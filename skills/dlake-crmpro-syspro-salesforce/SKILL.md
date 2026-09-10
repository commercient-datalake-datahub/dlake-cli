---
name: dlake-crmpro-syspro-salesforce
description: >-
  Build a working CRMPro → Salesforce forward sync for a SYSPRO tenant: the `CRM_Configuration` row
  shape the Salesforce engine dispatches on (standard objects `Account`/`Product2`/`PricebookEntry`
  alongside the managed package's `CommercientSF__*__c` custom objects, a namespace prefix/postfix
  pair, and an external-id field as `CRM_PK_API_Name`), the view contract the shipped templates use —
  a SINGLE-colon repository key, no `RecordKey` and no `SFDCID` output column, the key column named
  as the external id itself, and change detection through one join on the key AND the cursor — the
  id-chaining ladder that turns `Sync_Order` into a dependency order, the reverse-lookup and
  create/update process pairs, `CRM_FieldList`, and the checks to run when a run completes having
  pushed nothing. Use it when standing up or debugging a SYSPRO → Salesforce Phase 1 sync, or when
  reading a Salesforce template's `CreateViewQuery` and wondering why its conventions differ from the
  HubSpot ones. It extends `dlake-crmpro`, which covers operating CRMPro generally.
---

# CRMPro → Salesforce (SYSPRO): the working configuration

> **Keep this skill current.** `dlake` ships updates often and this text is embedded in the CLI
> you have installed. Before relying on it, compare `dlake --version` with
> `npm view @commercient/dlake version`; if they differ, run `npm install -g @commercient/dlake@latest`
> and then `dlake skills install`, which overwrites the installed skill files with the current text.

`dlake-crmpro` covers the tools and the general source-view contract. This skill gives the values the
Salesforce engine dispatches on for a SYSPRO source. **Read it before assuming any HubSpot habit
carries over**: the Salesforce templates use a different key separator, a different identity column,
and a different way of getting the destination id into a record. Every claim below is taken from the
shipped SYSPRO 7 → Salesforce template catalogue (`crmpro_templates --crmName Salesforce`) or from
the Registration API's template-import path; where the catalogue and a live install disagree, trust
the install and say so.

## 1. How a run is structured

Phase 1 for a hosted tenant runs on Commercient's sync servers — not on the customer's own agent,
which only fills the `dbo` clone tables (`SF_ERP_Salesforce_Clone_*` for a SYSPRO source). Each run:

1. `TR_CreateUpdateView` — executes `CreateViewQuery` for rows flagged `IsViewNeedsToCreate`, then
   clears the flag.
2. Salesforce token refresh.
3. One block per active `CRM_Configuration` row, in ascending `Sync_Order`. Rows whose
   `Is_Active_Get_Records` is set also run their `Get_SOQL_Query` against Salesforce, subject to the
   `Get_Run_Count` / `Get_Current_Run_Count` interval pair described in `dlake-crmpro`.

The engine records each pushed record in `TimeStampRepository` keyed `<TimeStamp_Prefix>:<key>` with
the Salesforce id in `SFDCID`. Those rows are the identity map, the change cursor, **and** the way
every downstream view resolves a lookup — which is why `Sync_Order` on this CRM is a dependency
order, not a preference.

### What the default catalogue delivers

Ten template groups ship for SYSPRO 7 → Salesforce as `Standard` templates. Business outcome first;
the insert/update/delete defaults come from each template's own `IsDefault*` flags.

| Group | Business outcome | Objects |
|---|---|---|
| **Account** | ERP customers become Salesforce Accounts, and the full AR customer record lands beside them in the managed package so sales sees terms, class, area, branch and salesperson from the ERP. Accounts and the AR objects are created and updated; nothing is deleted. | `Account`, `CommercientSF__ArCustomer__c`, `CommercientSF__ArCustomerPlus__c`, `CommercientSF__TblArTerms__c`, `CommercientSF__TblCustomerClass__c`, `CommercientSF__SalSalesPerson__c` |
| **Sal Area** | ERP sales areas become lookup records the AR customer points at. Created and updated, never deleted. | `CommercientSF__SalArea__c` |
| **Branch/Division Details** | Where the ERP tracks branches, divisions or locations on the AR customer master, those become records the Account hierarchy can be read through. Created and updated, never deleted. | `CommercientSF__SalBranch__c` |
| **Salesorder** | Sales orders and their lines arrive in full, so service and sales can see order status, requested ship dates, PO numbers, discounts and per-line quantities without leaving the CRM. Created and updated, never deleted. | `CommercientSF__SorMasterRep__c`, `CommercientSF__SorDetailRep__c`, `CommercientSF__SorDetailSerRep__c` |
| **Invoice** | Open and historical invoices, their payments and their transaction detail sync; a balance or terms change on an open invoice is reflected on the next run. Created and updated, never deleted. | `CommercientSF__ArPermReprint__c`, `CommercientSF__ArInvoice__c`, `CommercientSF__ArInvoicePay__c`, `CommercientSF__ArTrnDetail__c` |
| **Product** | ERP stock codes become Salesforce Products, with the inventory master and per-warehouse quantities alongside them. Created and updated, never deleted. | `Product2`, `CommercientSF__InvMaster__c`, `CommercientSF__InvWarehouse__c` |
| **Pricebook** | ERP selling prices become price book entries. Shipped as **create/update pairs** — the create leg inserts only, the update leg updates only — for both the standard price book and custom ones. | `PricebookEntry` |
| **Customer Multi Ship Addresses** | Every ship-to address on an AR customer appears as a related list on the Account. Created and updated, never deleted. | `CommercientSF__ArMultAddress__c` |
| **Sales Move** | ERP sales-movement history syncs for reporting on the Account. Created and updated, never deleted. | `CommercientSF__ArSalesMove__c` |
| **Get_Users** | Reads Salesforce users back so ERP salesperson codes can be resolved to a Salesforce owner. This is the one group whose work is a **read from** Salesforce, not a push. | `User` |

Two further `Standard` rows in the Salesorder group carry an **empty `CRM_Object_API_Name`**: their
job is to create the base sales-order views the `Call_vw_*` processes select from. The Registration
API's import skips any template with an empty API name, so treat those base views as something to
create yourself (§6) rather than something the import will produce.

## 2. The `CRM_Configuration` values that matter

| Column | Value the templates use | Why |
|---|---|---|
| `CRM_Object_API_Name` | The Salesforce API name: `Account`, `Product2`, `PricebookEntry`, or a managed-package object `CommercientSF__<Name>__c`. The user-read row writes `users`. | Case and namespace are part of the name. Copy it from the template or from `crmpro_crm_objects`, never retype it. |
| `CRM_PK_API_Name` | An external-id field, not the record id: `CommercientSF__ExternalKey__c` on most managed objects, `CommercientSF__Commercient_ArCustomerCode__c` on `Account`, `CommercientSF__ArCustomer_Code__c` on the AR customer, `CommercientSF__ExternalKeys__c` (plural) on the inventory objects, `CommercientSF__TermsCodes__c`, `CommercientSF__Class__c`, `CommercientSF__Areas__c`, `CommercientSF__Branch__c` on the lookup objects, and a bare `ExternalKey__c` on `PricebookEntry`. | The upsert matches on this field. The plural and the un-namespaced forms are real — they are not typos to correct. |
| `Prefix_OF_Field_OR_Object` / `Postfix_OF_Field_OR_Object` | `'CommercientSF__'` and `'__c'` on managed-package rows; **both empty** on `Account`, `Product2` and `PricebookEntry`. | The engine composes destination field names from the view's column names plus this pair, which is how a view can name a column `SalesBudget1` and have it land on the namespaced field. Setting the pair on a standard object would namespace fields that have no namespace. |
| `SQL_Query` | `select * from <view>` — and for the sales-order legs `select * from Call_vw_… (nolock)`. | Whatever the query names must exist as a view in the gateway database. |
| `TimeStamp_Prefix` | **Not the view name.** The templates pair view `vw_SYSPRO7_Account` with prefix `vw_Account`, `vw_SYSPRO7_Salesperson` with `vw_SalesPerson`, `vw_SYSPRO7_Product` with `vwProduct`. | The prefix must equal the literal the view's own `TimeStampRepository` join builds its key from, case included — nothing else. It is also what every *downstream* view joins on, so it is effectively public API between processes. |
| `View_Name_For_Field_Creation` | The actual view name. | Drives field creation where `Is_Create_Fields` is set. |
| `Sync_Operation_Type` | Left to the column default (`'1'`, upsert) on every pushed object; the user-read row sets `'1'` explicitly. | |
| `Is_Active` | `0` in every template's insert. | A freshly imported process is inactive by design — you activate it after checking the view. |
| `IsAccountMatching` | `1` on the `Account` row only. | |
| `Is_Create_Fields` | `0` in the insert; the importer then sets it to `1` **only where the object name matches a custom-object pattern** — that is, on the managed-package objects, and not on `Account`, `Product2` or `PricebookEntry`. | Sourced from the Registration API import path. If you build a standard-object process by hand, do not expect field creation to fill in missing standard fields. |
| `Get_SOQL_Query` | Empty except on two rows: the product row carries a price-book query, and the user-read row carries a user query. | This is the read-back leg — see §8. |
| every nullable text column | `''` | The templates set `''` throughout. A NULL throws inside the engine, which catches it, so the object is skipped with no recorded error. |

## 3. The view contract

Views are created in the gateway `dbo` schema by the templates themselves. Where you write one
yourself, `dlake admin create_view` / `alter_view` puts it in the tenant's working schema (usually
`DLO`) — either location works provided `SQL_Query` names it the way it exists.

**Four differences from the HubSpot contract, all of them load-bearing.**

1. **The key separator is a SINGLE colon.** `'vw_Account:' + RTRIM(Customer)`. Composite keys use the
   same single colon between parts: `'vw_SalesPerson:' + Branch + ':' + Salesperson`. A `::` literal
   simply never matches a row the Salesforce engine wrote.
2. **There is no `RecordKey` column.** The identity column *is* the external-id field, named as the
   destination field: `CommercientSF__ExternalKey__c`, or `ExternalKey`, or the object's own code
   field. The engine posts it like any other mapped column.
3. **There is no `SFDCID` output column.** The repository's `SFDCID` is read to fill **lookup
   fields** — `OwnerId`, `Product2Id`, `Pricebook2Id`, `CommercientSF__SalSalesperson__c` and the
   rest — not to tell the engine which record to update. That job belongs to the external id.
4. **Change detection is one join, not two.** The templates join the repository on the key **and**
   `SavedTimeStamp = v.[TimeStamp]` in the same `ON` clause, then filter
   `WHERE TimeStampRepository.SavedTimeStamp IS NULL`.

The upsert shape, as the templates write it:

```sql
CREATE VIEW [dbo].[vw_SYSPRO7_Account] AS
SELECT a.[TimeStamp] AS [TimeStamp],
       a.Customer AS CommercientSF__Commercient_ArCustomerCode__c,
       a.Name AS Name,
       ISNULL(a.Telephone, '') AS Phone,
       owner.SFDCID AS OwnerId
FROM SF_ERP_Salesforce_Clone_ArCustomer a (nolock)
LEFT JOIN TimeStampRepository owner
       ON 'vw_SYSPRO7_User:' + LTRIM(RTRIM(a.Salesperson)) = owner.[Key]
LEFT OUTER JOIN dbo.TimeStampRepository AS TimeStampRepository
       ON 'vw_Account:' + LTRIM(RTRIM(CAST(a.Customer AS varchar(max)))) = TimeStampRepository.[Key]
      AND a.[TimeStamp] = TimeStampRepository.SavedTimeStamp
WHERE TimeStampRepository.SavedTimeStamp IS NULL
  AND ISNULL(a.Name, '') != ''
```

**The three view kinds, in the Salesforce dialect.**

| Kind | Repository join | `WHERE` | Where the templates use it |
|---|---|---|---|
| **Insert + update** (the default) | `LEFT JOIN` on the key **and** `SavedTimeStamp = v.[TimeStamp]` | `SavedTimeStamp IS NULL` | Every ordinary object leg. A row with no repository entry and a row whose cursor has moved both qualify. |
| **Insert-only** | `LEFT JOIN` on the key alone | `[Key] IS NULL` | The price-book **create** legs. Once an entry exists it is never re-created. |
| **Update-only** | `INNER JOIN` the parent's prefix for the id, `LEFT JOIN` self on key + cursor | `SavedTimeStamp IS NULL` on the self join | The reverse-lookup legs and the price-book **update** legs. |

A NULL `SavedTimeStamp` means **changed**, never unchanged — the same rule as everywhere else in
CRMPro. The Salesforce shape gets this right by construction: with the cursor equality inside the
`ON` clause, an unmatched join yields NULL and the row is selected.

**Filters the templates apply, worth keeping when you copy one.** The sales-order header view limits
itself to orders within the last twelve months by entry date; the account view excludes customers
with a blank `Name`, and the product view excludes a blank `StockCode`. Salesforce rejects records
missing a required standard field, so these filters are part of the contract rather than decoration.

**When a view joins N tables, emit the MAX rowversion across all of them** as `[TimeStamp]`, exactly
as in `dlake-crmpro` §7b. A cursor tracking only the base table cannot see a change in a joined one.

## 4. Lookups and the id-chaining ladder

Salesforce processes do not carry association columns the way HubSpot ones do. They carry **lookup
field values**, and each one is a `SFDCID` read out of the repository under another process's prefix:

- the account view reads the **user** prefix to fill `OwnerId`;
- the AR customer view reads the account, terms, salesperson, branch, area and customer-class
  prefixes to fill its six lookup fields;
- the sales-order header view reads the account, AR customer and salesperson prefixes;
- the price-book entry views read the product and price-book prefixes to fill `Product2Id` and
  `Pricebook2Id`, and both are **INNER** joins — an entry cannot exist before its product does.

That is why `Sync_Order` reads as a ladder: users and the small lookup objects first, then `Account`,
then the AR customer, then orders, invoices, products and price books. **An INNER join on a parent's
repository row also sequences the work**: children appear in the view only once the parent has
synced, so parents flow on one run and children on the next. That is expected, not a fault.

**Reverse-lookup processes exist because the ladder has a cycle.** The Account is created before the
AR customer record exists, so the Account's lookup to that record cannot be filled on the way up. The
customer and product reverse-lookup templates are update-only processes that run afterwards and write
the child's id back onto the parent — which is exactly what their update-only `IsDefault*` flags say.
Keep their display names spelled as the catalogue has them, including the catalogue's own
misspellings: the import matches on display name.

**The price-book pair is a create/update pair, not a seed/upsert pair.** The create leg emits
`Product2Id` and `Pricebook2Id`; the update leg comments those columns out and emits only the price,
because a price-book entry's parents cannot be changed after creation.

**The user leg is the owner map.** Where HubSpot resolves an owner through a mapping table to an
email address, Salesforce resolves it through the repository: the user read-back writes each
Salesforce user id under the user prefix keyed by the ERP salesperson code, and views then read that
`SFDCID` straight into `OwnerId`. If owners are landing empty, the question is whether that read-back
has run, not whether the mapping is wrong.

**The price-book prefix is written by a process the Standard set does not contain.** The Standard
price-book entry templates INNER-join a price-book prefix, but the process that populates it ships as
a Community template. Import it, or create that process by hand, before expecting any price-book
entry to appear.

## 5. `CRM_FieldList` is required

One row per pushed column, with `Object_Name` equal to the `CRM_Object_API_Name` value —
`CommercientSF__ArInvoice__c`, not the display name `Invoice Header`. `View_Field_Name` and
`CRM_API_Name` are the view's column name. **An object with no `CRM_FieldList` rows pushes nothing
and records no error.**

Two Salesforce-specific points:

- **The template's `MappingJson` is the intended field list, but the Registration API's import does
  not write it.** The import executes the `CreateViewQuery` and the `Insert_Query` and records the
  connection; it does not populate `CRM_FieldList`. Read `MappingJson` off the template with
  `crmpro_templates`, and check `crmpro_field_mapping` on the created process before activating it.
- **Lookup columns need rows like any other pushed column** — `OwnerId`, `Product2Id`,
  `Pricebook2Id` and each `CommercientSF__*__c` lookup.

The catalogue's managed-object templates map a very wide field set: the AR customer, the order lines
and the inventory objects each carry most of their ERP table's columns. Where you do not want all of
it, prune `CRM_FieldList` rather than the view — a listed field the view does not output is simply
not posted, and a view column with no list row is simply not sent.

## 6. Building a process by hand

Use this when `crmpro_apply_template` is not available for the pair, or when the template you need
carries an empty `CRM_Object_API_Name` and the import therefore skips it.

1. **Create the base views first** where the process selects from a `Call_vw_*` wrapper. The wrapper
   selects from a plain base view (`vw_SYSPRO7_SorMasterRep`, `vw_SYSPRO7_SorDetailRep`); create the
   base view, then the wrapper.
2. `crmpro_create_process` per process, minimal arguments only: `recordType ADDProcessFromERP`,
   `crmObjectApiName`, `selectedTable` (the clone-table key), `customViewName`, `displayName`,
   `crmPkApiName`. Optional arguments can return an opaque `400`; set those afterwards.
3. `crmpro_update_process` does not persist `createViewQuery`, `isViewNeedsToCreate` or a CRM rebind.
   For those, expose `CRM_Configuration` and `CRM_FieldList` with `set_entity_exposure`,
   `restart_dab` once, then write the rows with `dlake tool update_record`.
4. Set the namespace pair on managed-package rows and leave it empty on standard objects; set every
   nullable text column to `''`; apply the §2 values; populate `CRM_FieldList`.
5. Give each process a `TimeStamp_Prefix` and use **that same literal** in the view's own join and in
   every downstream view that needs its ids.
6. Activate in ladder order — `crmpro_update_process_field --fieldName Is_Active --value true` (the
   CLI argument is `value`) — then `crmpro_set_sync_enabled --enabled true`. Where the flag row does
   not exist yet the first call seeds it at `0`, so re-read `crmpro_sync_status` and call again.

**Display names are the import's identity.** The import inserts a template's row only when no
`CRM_Configuration` row already carries that trimmed `CRM_Object_Display_Name`, and returns the
existing row's id otherwise. Two processes sharing a display name means the second never gets
created.

## 7. When a run pushes no records

The log shows each object starting and ending in about 0.00 seconds and a zero total, with nothing in
the error tables. Check in this order; each of these produces exactly that result.

1. **Does the view return rows?** `dlake tool query "SELECT COUNT(*) FROM <schema>.<view>"`. Zero
   rows means the answer is in the view.
2. **Is the repository key separator a single colon**, in both the view's literal and the
   `TimeStamp_Prefix`? A `::` here matches nothing.
3. **Does `TimeStamp_Prefix` equal the literal the view builds its key from**, case included — and
   not, by reflex, the view's own name?
4. **Does `CRM_FieldList` have rows** for that `Object_Name`, spelled as the API name?
5. **Any NULL nullable text column** on the configuration row? Set it to `''`.
6. **Is `CRM_PK_API_Name` an external-id field that exists on the object**, with the right namespace
   and the right plural?
7. **Is the parent's prefix the one that actually holds the ids?** A lookup join or an INNER join
   against a prefix no process writes returns no rows, and the child object stays empty indefinitely.
8. **Are `CRMName` and `APIAuthConfigID` consistent with how this tenant targets Salesforce?** A
   process targets a named connection or the registered CRM, never both.

A useful signal while still at zero: if the managed-package custom fields have appeared in Salesforce,
the engine is processing the row, so the answer is in the data step rather than in dispatch.

## 8. Salesforce-side facts that shape the design

- **The managed package must be installed first.** Every `CommercientSF__*__c` object and every
  namespaced field belongs to it. Registration reflects this: the Salesforce flow is an OAuth
  loopback flow with a package-install stage ahead of the authorisation stage.
- **`Is_Create_Fields` only reaches the custom objects.** The import sets it on objects whose API
  name matches the custom-object pattern, so `Account`, `Product2` and `PricebookEntry` never have
  fields created for them. Standard-object fields must exist in the org already.
- **A price book entry needs its product and its price book.** The templates carry a product prefix
  and a price-book prefix and INNER-join both; there is no create-on-demand.
- **The read-back leg is real and separate.** `Get_SOQL_Query` on the user row reads active users
  carrying a salesperson code; on the product row it reads active price books. These run under
  `Is_Active_Get_Records` and the `Get_Run_Count` interval, so a row with a large interval looks
  idle for several runs by design.
- **The catalogue's user query names a differently-numbered package namespace from the rest of the
  catalogue.** Check the namespace in the tenant's org before enabling the user read-back, rather
  than assuming the shipped query matches the installed package.
- **One shipped lookup join in the AR customer view builds its key with `::` against a prefix the
  customer-class process writes with a single colon.** That join cannot match. Where the
  customer-class lookup lands empty, this is the first thing to read.

## 9. Verifying

```bash
# per-prefix counts; every synced record carries its Salesforce id
dlake tool query --profile <tenant> --sql "SELECT LEFT([Key], CHARINDEX(':',[Key])-1) AS prefix, COUNT(*) n, COUNT(NULLIF(SFDCID,'')) withId FROM dbo.TimeStampRepository WHERE CHARINDEX(':',[Key])>0 GROUP BY LEFT([Key], CHARINDEX(':',[Key])-1)"
```

`withId = n` for every prefix is the success condition. Because Salesforce keys are single-colon and
composite keys use the same colon, that `LEFT`/`CHARINDEX` split is on the FIRST colon — which is the
prefix boundary. Confirm in Salesforce itself by searching on the external-id field.

Then check the ladder held: a child prefix with rows but no ids, or a child prefix with no rows while
its parent has many, points at the lookup join in §4 rather than at the child's own view.

## 10. Where this sits

`dlake-crmpro` is the general operating surface — the `crmpro_*` tools, the tables, the field mapping,
and the source-view contract that applies to every CRM. This skill adds the Salesforce values for a
SYSPRO source; `dlake-crmpro-hubspot` and `dlake-crmpro-syspro-shopify` do the same for their
destinations, and the conventions genuinely differ between them. For the extract leg that fills the
clone tables, see `dlake-normalsync`; for the on-premises agent that runs it, `dlake-syncagent`; for
the writeback leg, `dlake-txdownloaderpro`.
