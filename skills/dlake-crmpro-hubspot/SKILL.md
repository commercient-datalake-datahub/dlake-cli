---
name: dlake-crmpro-hubspot
description: >-
  Build a working CRMPro → HubSpot forward sync on a Commercient tenant: the exact
  `CRM_Configuration` row shape the HubSpot engine dispatches on (lower-case object names, operation
  type `'1'` with a `syspro_*` PK property, an unbound CRM), the DLO view contract (`RecordKey`,
  `[TimeStamp]`, an `SFDCID` that is never NULL, the identity/change double-join, prefix = view name),
  the seed/upsert pair that gives CRM-owned and ERP-owned fields different lifetimes, the
  `CRM_FieldList` rows an object needs before it will push anything, and the checks to run when a
  sync completes having written no records. Use it when standing up or debugging a SYSPRO (or other
  ERP) → HubSpot Phase 1 sync — especially when a run reports `Total Data Sync : 0` with no error
  recorded anywhere. It extends `dlake-crmpro`, which covers operating CRMPro generally, with the
  HubSpot-specific values.
---

# CRMPro → HubSpot: the working configuration

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
| `CRM_Object_API_Name` | `company`, `contact`, `deal`, `product`, `line_item` | Lower case, matched case-sensitively against the engine's object dictionary. A misspelling or wrong case is skipped silently. Spell it from this list rather than copying an existing row — a template may carry `comapny`. |
| `Sync_Operation_Type` | `'1'` | The upsert path, which matches on the PK property and handles new and existing records alike. |
| `CRM_PK_API_Name` | the unique `syspro_*` property — `syspro_customer`, `syspro_stockcode`, `syspro_salesorder`, `syspro_order_line`, `syspro_invoice`; contacts use `email` | `crmpro_create_process` requires it; omitting it is the usual cause of an otherwise opaque `400`. |
| `CRMName` and `APIAuthConfigID` | both NULL | The hosted HubSpot engine uses the default registered-CRM path. Leave the Connection Manager entry unbound and do not set `CRMName`. |
| `SQL_Query` | `SELECT * FROM DLO.vw_NL_COMPANY` | DLO-qualified is correct. The view name must match the view's own case exactly. |
| `View_Name_For_Field_Creation` | the view name | Drives property creation while `Is_Create_Fields = 1`. |
| `TimeStamp_Prefix` | the view name | The view joins `TimeStampRepository` on `'<viewname>::' + RecordKey`, so the prefix and the view name have to agree, case included. |
| `Is_Create_Fields` | `1` | Creates the `syspro_*` properties on the first run. |
| `IsAccountMatching` | `1` on company rows | The matching path for portals that already hold companies. |
| `Sync_Batch_Size` | `'200'` | |
| every nullable text column | `''` | `Prefix_OF_Field_OR_Object`, `Postfix_OF_Field_OR_Object`, `Developer_Comment`, `Document_Source_Path`, `File_Search_Pattern`, `File_Name_Separator`, `Delete_SQL_Query`, `Get_SOQL_Query` and the rest. Leave one NULL and the object is skipped without a recorded error. |

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
       ON idj.[Key] = 'vw_NL_X::' + CAST(v.RecordKey AS varchar(200))
LEFT JOIN dbo.TimeStampRepository chg
       ON chg.[Key] = 'vw_NL_X::' + CAST(v.RecordKey AS varchar(200))
      AND v.[TimeStamp] = ISNULL(chg.SavedTimeStamp, v.[TimeStamp])
WHERE chg.[Key] IS NULL
```

A **create-only** view — for seed processes and for contacts — drops the `chg` join and filters
`WHERE idj.[Key] IS NULL` instead, so a record that has synced once never reappears.

**Value conventions.** Booleans as `1`/`0` rather than `'true'`/`'false'`. Dates as
`CAST(col AS date)`. Currency normalised to an ISO code (`'$'` and `''` → `'USD'`). Resolve lookups
inside the view — the terms description rather than the terms code. Send the owner as the HubSpot
user's email in `hubspot_owner_id`, mapped from the ERP salesperson code through a mapping table such
as `DLO.nl_owner_map`, joined `LEFT` so an unmapped row sends NULL and leaves the CRM's owner
untouched.

**Associations** are columns carrying the parent's repository `SFDCID`, coalesced to `''`:
`associate_company`, `associate_deal`, and `hs_product_id` for a line item's product. Join the prefix
of the process that creates the parent — `'vw_NL_COMPANY_SEED::' + Customer` where a seed process
creates companies. An INNER join on the parent's repository row also sequences the work: children
appear in the view only once their parent has synced, so parents flow on one run and children on the
next. That is expected.

## 4. Field ownership: the seed/upsert pair

Where some fields belong to the CRM after first load — set once, never overwritten by the ERP — use
two processes on the same object:

1. **`vw_NL_COMPANY_SEED`** — create-only (`idj IS NULL`), lower `Sync_Order`, carrying the CRM-owned
   fields (phone, address block, postcode, account email, customer-since) plus the key and name. Once
   a record has a repository row it never reappears, so the CRM's copy stands.
2. **`vw_NL_COMPANY`** — the upsert view, carrying only ERP-owned fields, matched on the PK property.
   It runs whenever a row changes.

Both are `Sync_Operation_Type '1'` with the same `CRM_PK_API_Name`, and each has its own
`TimeStamp_Prefix` equal to its own view name.

## 5. `CRM_FieldList` is required

One row per pushed column, where `Object_Name` is the `CRM_Object_API_Name` value (`company`, not a
display name), and `View_Field_Name` and `CRM_API_Name` are the view's column name. **An object with
no `CRM_FieldList` rows pushes nothing and records no error.** Template imports populate this table;
a hand-built process needs it populated too — `dlake tool create_record --entity CRM_FieldList` once
the entity is exposed, or through the portal.

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
   the answer is in the view.
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
- **Deal pipelines are created in the HubSpot UI**, not by the sync. Once the customer has created
  one, read the `dealstage` and `pipeline` enumeration options — the internal ids, not the labels —
  and put those ids in the deal view's `CASE` expression before activating deals.
- **A large product set lands across several runs** — around 140 per run at batch size 200 — so a
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
