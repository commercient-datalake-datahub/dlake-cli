---
name: dlake-crmpro-hubspot
description: >-
  Build a working CRMPro → HubSpot forward sync on a Commercient tenant: the exact
  `CRM_Configuration` row shape the HubSpot engine dispatches on (lower-case object names, operation
  type `'1'` with a `syspro_*` PK property, an unbound CRM), the DLO view contract (`RecordKey`,
  `[TimeStamp]`, an `SFDCID` that is never NULL, the identity/change double-join, prefix = view name,
  the three view kinds and the `SavedTimeStamp` cursor rule that keeps a fresh install from freezing),
  the seed/upsert pair that gives CRM-owned and ERP-owned fields different lifetimes, the
  `CRM_FieldList` rows an object needs before it will push anything, and the checks to run when a
  sync completes having written no records. Use it when standing up or debugging a SYSPRO (or other
  ERP) → HubSpot Phase 1 sync — especially when a run reports `Total Data Sync : 0` with no error
  recorded anywhere. It extends `dlake-crmpro`, which covers operating CRMPro generally, with the
  HubSpot-specific values.
---

# CRMPro → HubSpot: the working configuration

> **Keep this skill current.** `dlake` ships updates often and this text is embedded in the CLI
> you have installed. Before relying on it, compare `dlake --version` with
> `npm view @commercient/dlake version`; if they differ, run `npm install -g @commercient/dlake@latest`
> and then `dlake skills install`, which overwrites the installed skill files with the current text.

`dlake-crmpro` covers the tools and the general source-view contract. This skill gives the values the
HubSpot engine dispatches on, because nearly every mistake here has the same shape: the run reports
success, each object logs `Start … END` in about 0.00 seconds, `Total Data Sync : 0`, and no error is
written to any error table. Match the values below and a process pushes; depart from any one of them
and you get that silent run.

## 1. How a run is structured

Phase 1 for a hosted tenant runs on Commercient's sync servers (`H:\Salesforce Sync Exes\<tenant>`,
log `SalesforceLog.txt`) — not on the customer's own agent, which only fills the clone tables. Each
run does the following in order:

1. `TR_CreateUpdateView` — executes `CreateViewQuery` for rows flagged `IsViewNeedsToCreate`, then
   clears the flag.
2. HubSpot token refresh.
3. Bootstrap sync of owners and pipelines, which lands `HUBSPOT_NEW_OWNER::` and
   `HUBSPOT_NEW_PIPELINE::` rows in `TimeStampRepository`. Their presence confirms the OAuth
   connection is working.
4. One `SYNC DATA` block per active `CRM_Configuration` row, in `Sync_Order`. Where
   `Is_Create_Fields` is set, missing HubSpot properties are created from
   `View_Name_For_Field_Creation` as string-typed properties.

The engine records each pushed record in `TimeStampRepository` keyed `<TimeStamp_Prefix>::<RecordKey>`
with the HubSpot object id in `SFDCID`. Those rows are both the identity map and the change cursor.

## 2. The `CRM_Configuration` values that matter

| Column | Value | Why |
|---|---|---|
| `CRM_Object_API_Name` | `company`, `contact`, `deal`, `product`, `line_item` | Lower case, matched case-sensitively against the engine's baked-in object dictionary. A misspelling or wrong case is skipped silently. Spell it from this list rather than copying an existing row — a template may carry `comapny`. |
| `Sync_Operation_Type` | `'1'` | The upsert path, which matches on the PK property and handles new and existing records alike. |
| `CRM_PK_API_Name` | the unique `syspro_*` property — `syspro_customer`, `syspro_stockcode`, `syspro_salesorder`, `syspro_order_line`, `syspro_invoice`; contacts use `email` | `crmpro_create_process` requires it; omitting it is the usual cause of an otherwise opaque `400`. |
| `CRMName` and `APIAuthConfigID` | both NULL | The hosted HubSpot engine uses the default registered-CRM path. Leave the Connection Manager entry unbound and do not set `CRMName`. |
| `SQL_Query` | `SELECT * FROM DLO.vw_COMPANY` | DLO-qualified is correct. The view name must match the view's own case exactly. |
| `View_Name_For_Field_Creation` | the view name | Drives property creation while `Is_Create_Fields = 1`. |
| `TimeStamp_Prefix` | the view name | The view joins `TimeStampRepository` on `'<viewname>::' + RecordKey`, so the prefix and the view name have to agree, case included. |
| `Is_Create_Fields` | `1` | Creates the `syspro_*` properties on the first run. |
| `IsAccountMatching` | `1` on company rows | The matching path for portals that already hold companies. |
| `Sync_Batch_Size` | `'200'` | |
| every nullable text column | `''` | `Prefix_OF_Field_OR_Object`, `Postfix_OF_Field_OR_Object`, `Developer_Comment`, `Document_Source_Path`, `File_Search_Pattern`, `File_Name_Separator`, `Delete_SQL_Query`, `Get_SOQL_Query` and the rest. A NULL throws `Object reference not set` inside the engine, which catches it — so the object is skipped without a recorded error. |

## 3. The view contract

Views live in **DLO** — create them with `dlake admin create_view` / `alter_view`; the engine reads
them there and no `dbo` copy is needed. Every view provides:

- **`RecordKey`** — the source key, trimmed. It becomes the repository key suffix.
- **`[TimeStamp]`** — the clone table's rowversion, aliased exactly `[TimeStamp]`.
- **`SFDCID`** — as `ISNULL(idj.SFDCID, '')`. A NULL here stops the whole object.
- **Data columns named exactly as the HubSpot property each one feeds**, with `CRM_FieldList` naming
  the same columns (§5).

The upsert shape. The view is the change detection: it returns rows that are new or changed, and the
engine pushes what it returns.

```sql
SELECT v.*, ISNULL(idj.SFDCID, '') AS SFDCID
FROM ( SELECT /* fields */, a.[TimeStamp] AS [TimeStamp]
       FROM dbo.SF_ERP_Salesforce_Clone_X a /* joins */ ) v
LEFT JOIN dbo.TimeStampRepository idj
       ON idj.[Key] = 'vw_X::' + CAST(v.RecordKey AS varchar(200))
LEFT JOIN dbo.TimeStampRepository chg
       ON chg.[Key] = 'vw_X::' + CAST(v.RecordKey AS varchar(200))
      AND chg.SavedTimeStamp = v.[TimeStamp]
WHERE chg.[Key] IS NULL
```

**The three view kinds.** Every source view is one of three. The `SELECT` list and the joins to the
source stay the same; only the repository join and the `WHERE` clause change.

| Kind | Repository joins | `WHERE` | Use for |
|---|---|---|---|
| **Insert-only** (create-once, seed) | `LEFT JOIN … idj` on the prefix key | `idj.[Key] IS NULL` | Seed processes carrying CRM-owned fields, contacts, and anything that must not overwrite the CRM after creation. A record that has synced once never reappears. |
| **Update-only** | `INNER JOIN … idj` on the prefix key | `idj.SavedTimeStamp <> v.[TimeStamp] OR idj.SavedTimeStamp IS NULL` | A leg that may only touch records the sync already created, never create one. Emit `idj.SFDCID` — the inner join means it is never NULL, but wrap it in `ISNULL(…, '')` anyway. |
| **Insert + update** (upsert) | `LEFT JOIN … idj` for the id, plus `LEFT JOIN … chg` on the key **and** `chg.SavedTimeStamp = v.[TimeStamp]` | `chg.[Key] IS NULL` | The everyday leg: rows that are new (no repository row) or changed (the cursor differs). |

In all three kinds a NULL `SavedTimeStamp` means **changed**, never unchanged.

**The upsert `chg` predicate is `chg.SavedTimeStamp = v.[TimeStamp]`, never
`v.[TimeStamp] = ISNULL(chg.SavedTimeStamp, v.[TimeStamp])`.** The engine writes `SavedTimeStamp`
only on the update path, and a record created through a create or seed path gets a repository row
with a NULL cursor. The `ISNULL` form reads that NULL as "unchanged", so such a record can never
qualify as changed, the update path never runs for it, and its cursor never populates — the records
created first are frozen for good. The direct-equality form treats a NULL cursor as changed; after
the first update pass the engine fills the cursors in and ordinary change detection takes over.

**Value conventions.** Booleans as `1`/`0` rather than `'true'`/`'false'`. Dates as
`CAST(col AS date)`. Currency normalised to an ISO code (`'$'` and `''` → `'USD'`). Resolve lookups
inside the view — the terms description rather than the terms code. Send the owner as the HubSpot
user's email in `hubspot_owner_id`, mapped from the ERP salesperson code through a mapping table such
as `DLO.owner_map` (`syspro_salesperson`, `salsalesperson_name`, `hubspot_user_email`), joined
`LEFT` so an unmapped row sends NULL and leaves the CRM's owner untouched. Non-ASCII text in a view
literal is safe as an `N'…'` literal — an em dash in a deal name survives `create_view` and the push
unchanged.

Three further rules the contract implies and a fresh build can miss:

- **A seed or create view carries every property the CRM requires at creation.** A deal needs
  `pipeline` and `dealstage` in the seed view — the internal ids, not the labels — or the record is
  rejected. A company needs `name`; a contact needs `email`.
- **`RecordKey` and `CRM_PK_API_Name` may differ, and that is correct.** The repository key is the
  stable source identity — contacts key on `Customer`, one primary contact per account — while the PK
  property is what the CRM matches on, which for contacts is `email`. Choose the repository key for
  stability and the PK property for the destination's uniqueness rule.
- **When a view joins N tables, emit the MAX rowversion across all of them as `[TimeStamp]`.** A
  cursor that tracks only the base table cannot see a change in a joined one — an `InvPrice` price
  change never resyncs a product whose view emits only `InvMaster`'s rowversion. The idiom, NULL-safe
  for `LEFT`-joined tables:

  ```sql
  (SELECT MAX(ts) FROM (VALUES (a.[TimeStamp]), (b.[TimeStamp]), (c.[TimeStamp])) x(ts)) AS [TimeStamp]
  ```

  For aggregated children — a deal amount summed over `SorDetail` lines — include
  `MAX(child.[TimeStamp])` from the aggregate in that `VALUES` list.

**Associations** are columns carrying the parent's repository `SFDCID`, coalesced to `''`:
`associate_company`, `associate_deal`, and `hs_product_id` for a line item's product. Join the prefix
of the process that creates the parent — `'vw_COMPANY_SEED::' + Customer` where a seed process
creates companies. An INNER join on the parent's repository row also sequences the work: children
appear in the view only once their parent has synced, so parents flow on one run and children on the
next. That is expected.

## 4. Field ownership: the seed/upsert pair

Where some fields belong to the CRM after first load — set once, never overwritten by the ERP — use
two processes on the same object:

1. **`vw_COMPANY_SEED`** — insert-only (`idj IS NULL`, the first kind in §3), lower `Sync_Order`,
   carrying the CRM-owned fields (phone, address block, postcode, account email, customer-since) plus
   the key and name. Once a record has a repository row it never reappears, so the CRM's copy stands.
2. **`vw_COMPANY`** — the upsert view, carrying only ERP-owned fields, matched on the PK property.
   It runs whenever a row changes.

Both are `Sync_Operation_Type '1'` with the same `CRM_PK_API_Name`, and each has its own
`TimeStamp_Prefix` equal to its own view name.

## 5. `CRM_FieldList` is required

One row per pushed column, where `Object_Name` is the `CRM_Object_API_Name` value (`company`, not a
display name), and `View_Field_Name` and `CRM_API_Name` are the view's column name. Association
columns (`associate_company`, `associate_deal`, `hs_product_id`) need rows like any other pushed
column. **An object with no `CRM_FieldList` rows pushes nothing and records no error.** Template imports populate this table;
a hand-built process needs it populated too — `dlake tool create_record --entity CRM_FieldList` once
the entity is exposed, or through the portal.

Where a seed and an upsert process share an object (§4), they share the one `Object_Name`-keyed list:
it holds the union of both views' columns, and a listed field that a view does not output is simply
not posted by that process.

## 6. Building a process by hand

Use this when `crmpro_apply_template` is not available for the ERP/CRM pair.

1. `crmpro_create_process` per process, with minimal arguments only: `recordType ADDProcessFromERP`,
   `crmObjectApiName`, `selectedTable` (the clone-table key), `customViewName`, `displayName`,
   `crmPkApiName`. Additional optional arguments — `syncOperationType`, `batchSize`, `connectionId` —
   can return an opaque `400`; set those afterwards.
2. `crmpro_update_process` does not persist `createViewQuery`, `isViewNeedsToCreate`, or a CRM
   rebind. For those, expose `CRM_Configuration` and `CRM_FieldList` with `set_entity_exposure`,
   `restart_dab` once, then write the rows with `dlake tool update_record`.
3. Create the views in DLO with `create_view` / `alter_view`, passing the inner `SELECT` and
   `dbo`-qualifying the clone tables inside it.
4. Set every nullable text column to `''`, apply the §2 values, and populate `CRM_FieldList`.
5. Activate the rows — `crmpro_update_process_field --fieldName Is_Active --value true` (the argument
   is `value`) — then `crmpro_set_sync_enabled --enabled true`. Where the flag row does not exist yet
   the first call seeds it at `0`, so re-read `crmpro_sync_status` and call again.

## 7. When a run pushes no records

The log shows `Start … END : 0.00` per object and `Total Data Sync : 0`, with nothing in the error
tables. Check in this order; each of these produces exactly that result.

1. **Does the view return rows?** `dlake tool query "SELECT COUNT(*) FROM DLO.vw_X"`. Zero rows means
   the answer is in the view — and where every repository row for that prefix carries a NULL
   `SavedTimeStamp`, zero rows from an upsert view is the cursor rule in §3, not an absence of
   changes.
2. **Is `SFDCID` NULL?** It must be `ISNULL(…, '')`.
3. **Is `CRM_Object_API_Name` lower case and spelled from the §2 list**, case-exact?
4. **Does `CRM_FieldList` have rows** for that `Object_Name`?
5. **Any NULL nullable text column** on the configuration row? Set it to `''`.
6. **Are `CRMName` and `APIAuthConfigID` both NULL?**
7. **Is `Sync_Operation_Type` `'1'` with the unique PK property set?**
8. **Do `SQL_Query`, `TimeStamp_Prefix`, the view's name and the view's own `'<prefix>::'` literal all
   agree, case included?**

A useful signal while still at zero records: if the `syspro_*` properties have appeared in HubSpot,
the engine is processing the row — field creation ran — so the answer is in the data step (2, 4–8)
rather than in dispatch.

Two things not to chase. The recurring
`Cannot insert duplicate key … (HUBSPOT_GET_DATA_HISTORY_INDAYS)` log line needs no action, and
`Loaded 0 object schemas from HubSpot` simply means the portal has no custom objects. A Normal Sync
resync does not change a zero-record push either — that gate is configuration, not a rowversion.

## 8. HubSpot portal facts that shape the design

- **Custom objects need an Enterprise portal.** On Standard or Professional a `p_*` custom object —
  invoices, for instance — cannot exist. Build the process, leave it inactive, and note why.
- **Deal pipelines are created in the HubSpot UI**, not by the sync and not by the HubSpot MCP
  connector. Once the customer has created one, read the `dealstage` and `pipeline` enumeration
  options — the internal ids, not the labels — and put those ids in the deal view's `CASE`
  expression before activating deals.
- **A large product set lands across several runs**, so a
  partial count after one run is expected.
- **Owners and pipelines sync on every run**, which is why `HUBSPOT_NEW_OWNER` and
  `HUBSPOT_NEW_PIPELINE` rows in `TimeStampRepository` are a quick confirmation that the connection
  itself is healthy.

## 9. Verifying

```bash
# per-prefix counts; every synced record carries its HubSpot id
dlake tool query --profile <tenant> --sql "SELECT LEFT([Key], CHARINDEX('::',[Key])-1) AS prefix, COUNT(*) n, COUNT(NULLIF(SFDCID,'')) withId FROM dbo.TimeStampRepository WHERE CHARINDEX('::',[Key])>0 GROUP BY LEFT([Key], CHARINDEX('::',[Key])-1)"
```

`withId = n` for every prefix is the success condition. Confirm in HubSpot itself by searching on the
`syspro_*` key property.

## 10. Where this sits

`dlake-crmpro` is the general operating surface — the `crmpro_*` tools, the tables, the field mapping,
and the source-view contract that applies to every CRM. This skill adds the HubSpot values. For the
extract leg that fills the clone tables, see `dlake-normalsync`; for the on-premises agent that runs
it, `dlake-syncagent`; for the writeback leg, `dlake-txdownloaderpro`.
