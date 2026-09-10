---
name: dlake-crmpro-salesforce
description: >-
  Build a working CRMPro → Salesforce forward sync on a Commercient tenant, whatever the source ERP:
  the `CRM_Configuration` row shape the Salesforce engine dispatches on (standard objects
  `Account`/`Product2`/`PricebookEntry` alongside the managed package's `CommercientSF__*__c` custom
  objects, a namespace prefix/postfix pair, and an external-id field as `CRM_PK_API_Name`), the view
  contract the shipped templates use — a SINGLE-colon repository key, no `RecordKey` and no `SFDCID`
  output column, the key column named as the external id itself, and change detection through one
  join on the key AND the cursor — the id-chaining ladder that turns `Sync_Order` into a dependency
  order, the reverse-lookup and create/update process pairs, `CRM_FieldList`, and the checks to run
  when a run completes having pushed nothing. Use it when standing up or debugging any ERP →
  Salesforce Phase 1 sync, or when reading a Salesforce template's `CreateViewQuery` and wondering
  why its conventions differ from the HubSpot ones. It extends `dlake-crmpro`, which covers
  operating CRMPro generally, and it carries one child page per source ERP under `erps/`.
---

# CRMPro → Salesforce: the working configuration

> **Keep this skill current.** `dlake` ships updates often and this text is embedded in the CLI
> you have installed. Before relying on it, compare `dlake --version` with
> `npm view @commercient/dlake version`; if they differ, run `npm install -g @commercient/dlake@latest`
> and then `dlake skills install`, which overwrites the installed skill files with the current text.

**The source ERP has its own page under this skill.** `erps/<erp>.md` is a child file of this
skill and describes what the shipped templates for that ERP → Salesforce pair set up. §10 lists
every one of them and how to pick the right row; read this page first, then that one.

`dlake-crmpro` covers the tools and the general source-view contract. This skill gives the values the
Salesforce engine dispatches on, and they hold whichever ERP is the source. **Read it before assuming
any HubSpot habit carries over**: the Salesforce templates use a different key separator, a different
identity column, and a different way of getting the destination id into a record. What is
ERP-specific — which template groups ship, which clone tables they read, which view names and
prefixes they pair — lives in this skill's own `erps/` pages, one per source ERP. Where the catalogue
and a live install disagree, trust the install and say so.

## 1. How a run is structured

Phase 1 for a hosted tenant runs on Commercient's sync servers — not on the customer's own agent,
which only fills the `dbo` clone tables. Each run:

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

## 2. The `CRM_Configuration` values that matter

| Column | Value the templates use | Why |
|---|---|---|
| `CRM_Object_API_Name` | The Salesforce API name: `Account`, `Product2`, `PricebookEntry`, or a managed-package object `CommercientSF__<Name>__c`. A user read-back row writes `users`. | Case and namespace are part of the name. Copy it from the template or from `crmpro_crm_objects`, never retype it. |
| `CRM_PK_API_Name` | An external-id field, not the record id: `CommercientSF__ExternalKey__c` on most managed objects, `CommercientSF__Commercient_ArCustomerCode__c` on `Account`, `CommercientSF__ExternalKeys__c` (plural) on the inventory objects, `CommercientSF__TermsCodes__c`, `CommercientSF__Class__c`, `CommercientSF__Areas__c`, `CommercientSF__Branch__c` on the lookup objects, and a bare `ExternalKey__c` on `PricebookEntry`. | The upsert matches on this field. The plural and the un-namespaced forms are real — they are not typos to correct. Each ERP's page names the exact field its own templates set. |
| `Prefix_OF_Field_OR_Object` / `Postfix_OF_Field_OR_Object` | `'CommercientSF__'` and `'__c'` on managed-package rows; **both empty** on `Account`, `Product2` and `PricebookEntry`. | The engine composes destination field names from the view's column names plus this pair, which is how a view can name a column `SalesBudget1` and have it land on the namespaced field. Setting the pair on a standard object would namespace fields that have no namespace. |
| `SQL_Query` | `select * from <view>` — and for the sales-order legs `select * from Call_vw_… (nolock)`. | Whatever the query names must exist as a view in the gateway database. |
| `TimeStamp_Prefix` | **Not, by default, the view name.** The Salesforce templates routinely pair a view called `vw_<ERP>_Account` with the prefix `vw_Account`. | The prefix must equal the literal the view's own `TimeStampRepository` join builds its key from, case included — nothing else. It is also what every *downstream* view joins on, so it is effectively public API between processes. Each ERP's page lists the pairings its own templates use. |
| `View_Name_For_Field_Creation` | The actual view name. | Drives field creation where `Is_Create_Fields` is set. |
| `Sync_Operation_Type` | Left to the column default (`'1'`, upsert) on every pushed object; a user read-back row sets `'1'` explicitly. | |
| `Is_Active` | `0` in every template's insert. | A freshly imported process is inactive by design — you activate it after checking the view. |
| `IsAccountMatching` | `1` on the `Account` row only. | |
| `Is_Create_Fields` | `0` in the insert; the importer then sets it to `1` **only where the object name matches a custom-object pattern** — that is, on the managed-package objects, and not on `Account`, `Product2` or `PricebookEntry`. | Sourced from the Registration API import path. If you build a standard-object process by hand, do not expect field creation to fill in missing standard fields. |
| `Get_SOQL_Query` | Empty except on the read-back rows — typically a price-book query on the product row and a user query on the user row. | This is the read-back leg — see §8. |
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

**The three view kinds, in the Salesforce dialect.**

| Kind | Repository join | `WHERE` | Where the templates use it |
|---|---|---|---|
| **Insert + update** (the default) | `LEFT JOIN` on the key **and** `SavedTimeStamp = v.[TimeStamp]` | `SavedTimeStamp IS NULL` | Every ordinary object leg. A row with no repository entry and a row whose cursor has moved both qualify. |
| **Insert-only** | `LEFT JOIN` on the key alone | `[Key] IS NULL` | The price-book **create** legs. Once an entry exists it is never re-created. |
| **Update-only** | `INNER JOIN` the parent's prefix for the id, `LEFT JOIN` self on key + cursor | `SavedTimeStamp IS NULL` on the self join | The reverse-lookup legs and the price-book **update** legs. |

A NULL `SavedTimeStamp` means **changed**, never unchanged — the same rule as everywhere else in
CRMPro. The Salesforce shape gets this right by construction: with the cursor equality inside the
`ON` clause, an unmatched join yields NULL and the row is selected.

**Filters the templates apply, worth keeping when you copy one.** A sales-order header view typically
limits itself to orders within a trailing window by entry date; an account view excludes customers
with a blank `Name`, and a product view excludes a blank stock code. Salesforce rejects records
missing a required standard field, so these filters are part of the contract rather than decoration.
Each ERP's page shows the filters its own templates carry.

**When a view joins N tables, emit the MAX rowversion across all of them** as `[TimeStamp]`, exactly
as in `dlake-crmpro` §7b. A cursor tracking only the base table cannot see a change in a joined one.

## 4. Lookups and the id-chaining ladder

Salesforce processes do not carry association columns the way HubSpot ones do. They carry **lookup
field values**, and each one is a `SFDCID` read out of the repository under another process's prefix.
The shape is the same in every ERP's template set: the account view reads the user prefix to fill
`OwnerId`; the AR-customer view reads the account, terms, salesperson, branch, area and
customer-class prefixes to fill its lookup fields; the order header reads the account, AR customer
and salesperson prefixes; the price-book entry views read the product and price-book prefixes to fill
`Product2Id` and `Pricebook2Id`, and both are **INNER** joins — an entry cannot exist before its
product does.

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

**A price-book pair is a create/update pair, not a seed/upsert pair.** The create leg emits
`Product2Id` and `Pricebook2Id`; the update leg comments those columns out and emits only the price,
because a price-book entry's parents cannot be changed after creation.

**The user leg is the owner map.** Where HubSpot resolves an owner through a mapping table to an
email address, Salesforce resolves it through the repository: the user read-back writes each
Salesforce user id under the user prefix keyed by the ERP salesperson code, and views then read that
`SFDCID` straight into `OwnerId`. If owners are landing empty, the question is whether that read-back
has run, not whether the mapping is wrong.

## 5. `CRM_FieldList` is required

One row per pushed column, with `Object_Name` equal to the `CRM_Object_API_Name` value —
`CommercientSF__ArInvoice__c`, not a display name like `Invoice Header`. `View_Field_Name` and
`CRM_API_Name` are the view's column name. **An object with no `CRM_FieldList` rows pushes nothing
and records no error.**

Two Salesforce-specific points:

- **The template's `MappingJson` is the intended field list, but the Registration API's import does
  not write it.** The import executes the `CreateViewQuery` and the `Insert_Query` and records the
  connection; it does not populate `CRM_FieldList`. Read `MappingJson` off the template with
  `crmpro_templates`, and check `crmpro_field_mapping` on the created process before activating it.
- **Lookup columns need rows like any other pushed column** — `OwnerId`, `Product2Id`,
  `Pricebook2Id` and each `CommercientSF__*__c` lookup.

The managed-object templates map a very wide field set: the AR customer, the order lines and the
inventory objects each carry most of their ERP table's columns. Where you do not want all of it,
prune `CRM_FieldList` rather than the view — a listed field the view does not output is simply not
posted, and a view column with no list row is simply not sent.

## 6. Building a process by hand

Use this when `crmpro_apply_template` is not available for the pair, or when the template you need
carries an empty `CRM_Object_API_Name` and the import therefore skips it.

1. **Create the base views first** where the process selects from a `Call_vw_*` wrapper. The wrapper
   selects from a plain base view; create the base view, then the wrapper. Each ERP's page names the
   base views its own set expects.
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
9. **Do the clone tables the view reads carry the prefix this ERP's templates expect?** That is on
   the ERP's own page, and it differs between source ERPs.

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
- **The read-back leg is real and separate.** `Get_SOQL_Query` on a user row reads active users
  carrying a salesperson code; on a product row it reads active price books. These run under
  `Is_Active_Get_Records` and the `Get_Run_Count` interval, so a row with a large interval looks
  idle for several runs by design.
- **Check the package namespace in the tenant's org before enabling a read-back query**, rather than
  assuming a shipped query's namespace matches the installed package.

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

## 10. The source ERP’s own page

One row per source ERP the catalogue ships Standard Salesforce templates for. Each page is a child
file of this skill, addressed as `dlake-crmpro-salesforce/erps/<erp>` — `dlake skills show
dlake-crmpro-salesforce/erps/<erp>` prints one, and `dlake skills install` writes them beside this
file.

<!-- ERP-TABLE:BEGIN dlake-crmpro-salesforce -->
| ERP | Page | What its templates deliver |
|---|---|---|
| Acumatica | [`erps/acumatica.md`](erps/acumatica.md) | 9 Standard templates in 4 groups, pushing `Account`, `Contact`, `CommercientSF24__ACUMATICA_Customer__c`, `CommercientSF24__CUSTOMERTOACCOUNTLOOKUP__c` and more; `:` repository keys |
| Acumatica Cloud | [`erps/acumatica-cloud.md`](erps/acumatica-cloud.md) | 20 Standard templates in 10 groups, pushing `User`, `Account`, `CommercientSF24__ACUMATICA_Payment__c`, `CommercientSF24__ACUMATICA_PaymentDetail__c` and more; `:` repository keys |
| Aptean Encompix | [`erps/aptean-encompix.md`](erps/aptean-encompix.md) | 8 Standard templates in 4 groups, pushing `Account`, `CommercientSF21__Aptean_Encompix_customer__c`, `CommercientSF21__Aptean_Encompix_custship__c`, `CommercientSF21__Aptean_Encompix_invoice__c` and more; `:` repository keys |
| Aptean Intuitive | [`erps/aptean-intuitive.md`](erps/aptean-intuitive.md) | 9 Standard templates in 4 groups, pushing `Account`, `Contact`, `CommercientSF21__Customer__c`, `CommercientSF21__CustomerShipTo__c` and more; `:` repository keys |
| Aptean Made2Manage | [`erps/aptean-made2manage.md`](erps/aptean-made2manage.md) | 11 Standard templates in 5 groups, pushing `Account`, `CommercientSF21__Aptean_M2M_UTTERMS__c`, `Contact`, `CommercientSF21__Aptean_M2M_SLCDPM__c` and more; `:` repository keys |
| Aptean Ross | [`erps/aptean-ross.md`](erps/aptean-ross.md) | 10 Standard templates in 3 groups, pushing `Account`, `CommercientSF21__CUSTOMERS__c`, `CommercientSF21__CUSTOMER_ADDRESSES__c`, `CommercientSF21__SALES_ORDER_INVOICES__c` and more; `:` repository keys |
| Deltek Vantagepoint | [`erps/deltek-vantagepoint.md`](erps/deltek-vantagepoint.md) | 4 Standard templates in 1 group, pushing `Account`, `Contact`, `Commercient38__CL__c`; `:` repository keys |
| Deltek Vision | [`erps/deltek-vision.md`](erps/deltek-vision.md) | 5 Standard templates in 2 groups, pushing `Pricebook2`, `Account`, `Contact`, `Commercient38__CL__c` and more; `:` repository keys |
| Dynamics Business Central | [`erps/dynamics-business-central.md`](erps/dynamics-business-central.md) | 9 Standard templates in 4 groups, pushing `DYNAMICSBUSINESSCENTRAL_Employee__c`, `Account`, `Contact`, `CommercientSF9__MSDYNAMICNAV_CUSTOMER__c` and more; `:` repository keys |
| E-solver | [`erps/e-solver.md`](erps/e-solver.md) | 11 Standard templates in 5 groups, pushing `Account`, `account`, `User`, `CommercientSF8_ESOLVER_CUSTOMER__c` and more; `:` repository keys |
| ECi e-automate | [`erps/eci-e-automate.md`](erps/eci-e-automate.md) | 5 Standard templates in 2 groups, pushing `Account`, `ECI_E_AUTOMATE_Customer__c`, `ECI_E_AUTOMATE_Invoice_Header__c`, `ECI_E_AUTOMATE_Invoice_Detail__c`; `:` repository keys |
| ECi M1 | [`erps/eci-m1.md`](erps/eci-m1.md) | 7 Standard templates in 3 groups, pushing `Account`, `Child Account`, `Commercient37__ECIM1_ARinvoices__c`, `Commercient37__ECIM1_ARInvoiceLines__c` and more; `:` repository keys |
| Epicor 10 | [`erps/epicor-10.md`](erps/epicor-10.md) | 25 Standard templates in 10 groups, pushing `user`, `Account`, `Contact`, `Opportunity` and more; `:` repository keys |
| Epicor 10 Cloud | [`erps/epicor-10-cloud.md`](erps/epicor-10-cloud.md) | 10 Standard templates in 4 groups, pushing `Account`, `Contact`, `CommercientSF10__EPICOR10_Customer__c`, `CommercientSF10__EPICOR10_ShipTo__c` and more; `:` repository keys |
| Epicor 11 Kinetic | [`erps/epicor-11-kinetic.md`](erps/epicor-11-kinetic.md) | 11 Standard templates in 5 groups, pushing `User`, `Account`, `Contact`, `CommercientSF10__EPICOR10_Customer__c` and more; `:` repository keys |
| Epicor 9 and 9.5 | [`erps/epicor-9-and-9-5.md`](erps/epicor-9-and-9-5.md) | 9 Standard templates in 4 groups, pushing `account`, `Contact`, `CommercientSF10__Customer__c`, `CommercientSF10__ShipTo__c` and more; `:` repository keys |
| Epicor Prophet 21 (P21) | [`erps/epicor-prophet-21-p21.md`](erps/epicor-prophet-21-p21.md) | 28 Standard templates in 13 groups, pushing `CommercientSF10__EPICOREP21_ShipToAddress__c`, `CommercientSF10__EPICOREP21_InvMaster__c`, `product2`, `CommercientSF10__EPICOREP21_Warehouse__c` and more; `:` repository keys |
| Exact Globe Next | [`erps/exact-globe-next.md`](erps/exact-globe-next.md) | 12 Standard templates in 5 groups, pushing `User`, `Account`, `Contact`, `CommercientSF19__MACOLAGLOBE_Customer__c` and more; `:` repository keys |
| Exact MAX | [`erps/exact-max.md`](erps/exact-max.md) | 19 Standard templates in 8 groups, pushing `CommercientSF19__Commodity_Codes__c`, `User`, `Account`, `CommercientSF19__Customer_Master__c` and more; `:` repository keys |
| Famous | [`erps/famous.md`](erps/famous.md) | 13 Standard templates in 5 groups, pushing `User`, `Account`, `FAMOUS_Customer__c`, `FAMOUS_ShipToAddress__c` and more; `:` repository keys |
| GlobalShop | [`erps/globalshop.md`](erps/globalshop.md) | 10 Standard templates in 5 groups, pushing `User`, `Account`, `Commercient33__CUSTOMER_MASTER__c`, `Commercient33__CUSTOMER_SHIPTO__c` and more; `:` repository keys |
| GlobalShop 2020 | [`erps/globalshop-2020.md`](erps/globalshop-2020.md) | 11 Standard templates in 6 groups, pushing `Commercient33__SALESPEOPLE__c`, `Account`, `Contact`, `user` and more; `:` repository keys |
| IFS | [`erps/ifs.md`](erps/ifs.md) | 12 Standard templates in 7 groups, pushing `CommercientSF20__IFS_PART__c`, `User`, `Account`, `CommercientSF20__IFS_CUSTOMER__c` and more; `:` repository keys |
| Infor A+ | [`erps/infor-a.md`](erps/infor-a.md) | 9 Standard templates in 3 groups, pushing `Account`, `CommercientSF18__CUSMS__c`, `CommercientSF18__ADDR__c`, `CommercientSF18__ORHED__c` and more; `:` repository keys |
| Infor CloudSuite | [`erps/infor-cloudsuite.md`](erps/infor-cloudsuite.md) | 8 Standard templates in 4 groups, pushing `User`, `Accounts`, `CommercientSF18__customer_all__c`, `CommercientSF18__custaddr__c` and more; `:` repository keys |
| Infor Fourth Shift | [`erps/infor-fourth-shift.md`](erps/infor-fourth-shift.md) | 12 Standard templates in 5 groups, pushing `Account`, `CommercientSF18__SA_Customer__c`, `CommercientSF18__SA_ShipToDeliveryLocation__c`, `CommercientSF18__SA_Item__c` and more; `:` repository keys |
| Infor LN 10 | [`erps/infor-ln-10.md`](erps/infor-ln-10.md) | 5 Standard templates in 1 group, pushing `Contact`, `CommercientSF14__BAAN_CUSTOMER__c`, `Account`, `CommercientSF14__BAAN_CUSTOMERADDRESS__c`; `:` repository keys |
| Infor LN 6 | [`erps/infor-ln-6.md`](erps/infor-ln-6.md) | 10 Standard templates in 4 groups, pushing `Account`, `CommercientSF14__BAAN_CUSTOMER__c`, `CommercientSF14__BAAN_CUSTOMERADDRESS__c`, `CommercientSF14__BAAN_INVOICEHEADER__c` and more; `:` repository keys |
| Infor M3 | [`erps/infor-m3.md`](erps/infor-m3.md) | 15 Standard templates in 10 groups, pushing `CommercientSF18_OCUSAD_ShipAdd__c`, `Product2`, `CommercientSF18__MITMAS__c`, `CommercientSF18__MITBAL__c` and more; `:` repository keys |
| Infor M3 QM | [`erps/infor-m3-qm.md`](erps/infor-m3-qm.md) | 11 Standard templates in 5 groups, pushing `Account`, `Contact`, `User`, `CommercientSF18__custsf__c` and more; `:` repository keys |
| Infor SXe | [`erps/infor-sxe.md`](erps/infor-sxe.md) | 17 Standard templates in 6 groups, pushing `User`, `Account`, `Contact`, `CommercientSF18__arsc__c` and more; `:` repository keys |
| Infor SyteLine V7 and V8 | [`erps/infor-syteline-v7-and-v8.md`](erps/infor-syteline-v7-and-v8.md) | 12 Standard templates in 6 groups, pushing `User`, `account`, `Contact`, `Opportunity` and more; `:` repository keys |
| Infor SyteLine V9 | [`erps/infor-syteline-v9.md`](erps/infor-syteline-v9.md) | 20 Standard templates in 8 groups, pushing `User`, `Account`, `Contact`, `Opportunity` and more; `:` repository keys |
| Infor Visual | [`erps/infor-visual.md`](erps/infor-visual.md) | 11 Standard templates in 6 groups, pushing `CommercientSF18__InforVisual_RECEIVABLE__c`, `CommercientSF18__InforVisual_RECEIVABLE_LINE__c`, `User`, `Account` and more; `:` repository keys |
| Infor Visual 7.1.2 | [`erps/infor-visual-7-1-2.md`](erps/infor-visual-7-1-2.md) | 11 Standard templates in 6 groups, pushing `CommercientSF18__InforVisual_RECEIVABLE__c`, `CommercientSF18__InforVisual_RECEIVABLE_LINE__c`, `User`, `Account` and more; `:` repository keys |
| Infor Visual 9 | [`erps/infor-visual-9.md`](erps/infor-visual-9.md) | 11 Standard templates in 6 groups, pushing `User`, `CommercientSF18__InforVisual_RECEIVABLE__c`, `CommercientSF18__InforVisual_RECEIVABLE_LINE__c`, `Account` and more; `:` repository keys |
| Infor XA | [`erps/infor-xa.md`](erps/infor-xa.md) | 10 Standard templates in 5 groups, pushing `User`, `Account`, `CommercientSF18__CUSMAS__c`, `CommercientSF18__MBS2REP__c` and more; `:` repository keys |
| Infor10 Distribution Business | [`erps/infor10-distribution-business.md`](erps/infor10-distribution-business.md) | 10 Standard templates in 4 groups, pushing `User`, `Account`, `Contact`, `CommercientSF18__arsc__c` and more; `:` repository keys |
| IQMS | [`erps/iqms.md`](erps/iqms.md) | 12 Standard templates in 5 groups, pushing `User`, `Account`, `Contact`, `Commercient31__ARCUSTO__c` and more; `:` repository keys |
| JD Edwards | [`erps/jd-edwards.md`](erps/jd-edwards.md) | 14 Standard templates in 5 groups, pushing `User`, `Account`, `CommercientSF17__F03012__c`, `CommercientSF17_F4006__c` and more; `:` repository keys |
| JobBOSS | [`erps/jobboss.md`](erps/jobboss.md) | 12 Standard templates in 5 groups, pushing `User`, `Account`, `Contact`, `CommercientSF19__JobBoss_Customer__c` and more; `:` repository keys |
| Macola 10 | [`erps/macola-10.md`](erps/macola-10.md) | 8 Standard templates in 3 groups, pushing `Account`, `CommercientSF19_MACOLA10_Customer__c`, `CommercientSF19_MACOLA10_Address__c`, `CommercientSF19_MACOLA10_SO_HEADER__c` and more; `:` repository keys |
| Microsoft Business Central | [`erps/microsoft-business-central.md`](erps/microsoft-business-central.md) | 11 Standard templates in 5 groups, pushing `User`, `Account`, `Contact`, `CommercientSF9__MSBusinessCentral_Customer__c` and more; `:` repository keys |
| Microsoft Dynamics AX | [`erps/microsoft-dynamics-ax.md`](erps/microsoft-dynamics-ax.md) | 20 Standard templates in 9 groups, pushing `CommercientSF9__SALESQUOTATIONTABLE__c`, `User`, `Account`, `CommercientSF9__MSDYNAMICAX_PaymentTerms__c` and more; `:` repository keys |
| Microsoft Dynamics GP 2016 | [`erps/microsoft-dynamics-gp-2016.md`](erps/microsoft-dynamics-gp-2016.md) | 24 Standard templates in 6 groups, pushing `CommercientSF9__SOP10106__c`, `Account`, `CommercientSF9__RM00101__c`, `CommercientSF9__RM00102__c` and more; `:` repository keys |
| Microsoft Dynamics GP 2017 | [`erps/microsoft-dynamics-gp-2017.md`](erps/microsoft-dynamics-gp-2017.md) | 12 Standard templates in 8 groups, pushing `Dynamics_GP__c`, `CommercientSF9__SOP30200__c`, `CommercientSF9__SOP30300__c`, `users` and more; `:` repository keys |
| Microsoft Dynamics NAV | [`erps/microsoft-dynamics-nav.md`](erps/microsoft-dynamics-nav.md) | 11 Standard templates in 4 groups, pushing `Account`, `Contact`, `CommercientSF9__MSDYNAMICNAV_CUSTOMER__c`, `Commercient_MSDYNAMICSNAV_ShipToAddress__c` and more; `:` repository keys |
| Microsoft Dynamics SL 2015 | [`erps/microsoft-dynamics-sl-2015.md`](erps/microsoft-dynamics-sl-2015.md) | 8 Standard templates in 4 groups, pushing `Account`, `CommercientSF9__Customer__c`, `CommercientSF9__Address__c`, `CommercientSF9__Dynamics_SL_Invoice_Header__c` and more; `:` repository keys |
| MYOB AccountRight | [`erps/myob-accountright.md`](erps/myob-accountright.md) | 16 Standard templates in 7 groups, pushing `users`, `Account`, `Contact`, `CommercientSF24__MYOX_Customers__c` and more; `:` repository keys |
| MYOB Advanced | [`erps/myob-advanced.md`](erps/myob-advanced.md) | 7 Standard templates in 3 groups, pushing `Account`, `CommercientSF24__Customer__c`, `CommercientSF24__SalesInvoice__c`, `CommercientSF24__SalesInvoiceDetail__c` and more; `:` repository keys |
| NetSuite | [`erps/netsuite.md`](erps/netsuite.md) | 19 Standard templates in 7 groups, pushing `users`, `Account`, `Contact`, `CommercientSF13__NETSUITE_CUSTOMERS__c` and more; `:` repository keys |
| Process PRO | [`erps/process-pro.md`](erps/process-pro.md) | 15 Standard templates in 9 groups, pushing `CommercientSF7__ICLOCT__c`, `users`, `Account`, `CommercientSF7__ARCUST__c` and more; `:` repository keys |
| QAD | [`erps/qad.md`](erps/qad.md) | 11 Standard templates in 5 groups, pushing `users`, `Account`, `Contact`, `CommercientSF18__QAD_Customer__c` and more; `:` repository keys |
| QuickBooks Desktop | [`erps/quickbooks-desktop.md`](erps/quickbooks-desktop.md) | 16 Standard templates in 7 groups, pushing `users`, `account`, `CommercientSF11__Terms__c`, `Contact` and more; `:` repository keys |
| QuickBooks Desktop (QUICKBOOKS) | [`erps/quickbooks-desktop-quickbooks.md`](erps/quickbooks-desktop-quickbooks.md) | 16 Standard templates in 7 groups, pushing `users`, `account`, `CommercientSF11__Terms__c`, `Contact` and more; `:` repository keys |
| QuickBooks Online | [`erps/quickbooks-online.md`](erps/quickbooks-online.md) | 10 Standard templates in 5 groups, pushing `CommercientSF11__Quickbook_Online_Payment__c`, `CommercientSF11__Quickbook_Online_PaymentLine__c`, `Account`, `Contact` and more; `:` repository keys |
| Sage 100 (US) | [`erps/sage-100-us.md`](erps/sage-100-us.md) | 27 Standard templates in 11 groups, pushing `users`, `Account`, `CommercientSF8__AR_TRANSACTIONPAYMENTHISTORY__c`, `CommercientSF8__SAGE100_TermsCode__c` and more; `:` repository keys |
| Sage 100 2013 V5 | [`erps/sage-100-2013-v5.md`](erps/sage-100-2013-v5.md) | 22 Standard templates in 8 groups, pushing `users`, `Account`, `CommercientSF8__AR_CUSTOMER__c`, `CommercientSF8__SO_SHIPTOADDRESS__c` and more; `:` repository keys |
| Sage 100 2014 | [`erps/sage-100-2014.md`](erps/sage-100-2014.md) | 21 Standard templates in 8 groups, pushing `users`, `Account`, `Contact`, `Opportunity` and more; `:` repository keys |
| Sage 100 2016 | [`erps/sage-100-2016.md`](erps/sage-100-2016.md) | 11 Standard templates in 5 groups, pushing `users`, `Account`, `CommercientSF8__AR_CUSTOMER__c`, `CommercientSF8__SO_SHIPTOADDRESS__c` and more; `:` repository keys |
| Sage 100 2017 | [`erps/sage-100-2017.md`](erps/sage-100-2017.md) | 32 Standard templates in 17 groups, pushing `users`, `Account`, `CommercientSF8__AR_TRANSACTIONPAYMENTHISTORY__c`, `Contact` and more; `:` repository keys |
| Sage 100 France | [`erps/sage-100-france.md`](erps/sage-100-france.md) | 19 Standard templates in 7 groups, pushing `users`, `Account`, `Opportunity`, `CommercientSF8__SAGE100_FRENCH_F_COMPTET__c` and more; `:` repository keys |
| Sage 100 Premium | [`erps/sage-100-premium.md`](erps/sage-100-premium.md) | 22 Standard templates in 8 groups, pushing `users`, `Account`, `Contact`, `Opportunity` and more; `:` repository keys |
| Sage 100 US (SAGE100US) | [`erps/sage-100-us-sage100us.md`](erps/sage-100-us-sage100us.md) | 28 Standard templates in 11 groups, pushing `users`, `Account`, `CommercientSF8__AR_TRANSACTIONPAYMENTHISTORY__c`, `CommercientSF8__SAGE100_TermsCode__c` and more; `:` repository keys |
| Sage 200 Evolution | [`erps/sage-200-evolution.md`](erps/sage-200-evolution.md) | 9 Standard templates in 4 groups, pushing `users`, `Account`, `CommercientSF8__Client__c`, `CommercientSF8__bvInvNumARFull_InvoiceHeader__c` and more; `:` repository keys |
| Sage 200 UK | [`erps/sage-200-uk.md`](erps/sage-200-uk.md) | 23 Standard templates in 9 groups, pushing `SAGE200UK_Colour__c`, `SAGE200UK_Range__c`, `users`, `Account` and more; `:` repository keys |
| Sage 200 US | [`erps/sage-200-us.md`](erps/sage-200-us.md) | 28 Standard templates in 14 groups, pushing `APVEND__c`, `CommercientSF7__ICLOCT__c`, `WOMAST__c`, `WOTRAN__c` and more; `:` repository keys |
| Sage 300 | [`erps/sage-300.md`](erps/sage-300.md) | 12 Standard templates in 5 groups, pushing `users`, `Account`, `CommercientSF8__SAGE300_InvoicePayment__c`, `Contacts` and more; `:` repository keys |
| Sage 300 CRE | [`erps/sage-300-cre.md`](erps/sage-300-cre.md) | 10 Standard templates in 6 groups, pushing `CommercientSF8__ACTIVE_CNC_CONTRACT__c`, `CommercientSF8__MASTER_JCM_JOB_1__c`, `Account`, `Contact` and more; `:` repository keys |
| Sage 50 Canada | [`erps/sage-50-canada.md`](erps/sage-50-canada.md) | 10 Standard templates in 5 groups, pushing `users`, `Account`, `Contact`, `CommercientSF8__SAGE50CA_Customer__c` and more; `:` repository keys |
| Sage 50 UK | [`erps/sage-50-uk.md`](erps/sage-50-uk.md) | 17 Standard templates in 9 groups, pushing `CommercientSF8__SAGE50UK_AUDIT_HEADER__c`, `CommercientSF8__SAGE50UK_AUDIT_SPLIT__c`, `users`, `Account` and more; `:` repository keys |
| Sage 50 US | [`erps/sage-50-us.md`](erps/sage-50-us.md) | 24 Standard templates in 10 groups, pushing `users`, `PricebookEntry`, `Account`, `CommercientSF8__SAGE50US_ARTerms__c` and more; `:` repository keys |
| Sage 500 | [`erps/sage-500.md`](erps/sage-500.md) | 25 Standard templates in 11 groups, pushing `CommercientSF8__SAGE500_ShipMethod__c`, `CommercientSF8__SAGE500_CustSalesHist__c`, `CommercientSF8__SAGE500_UnitMeasure__c`, `users` and more; `:` repository keys |
| Sage BusinessWorks 2013/2015 | [`erps/sage-businessworks-2013-2015.md`](erps/sage-businessworks-2013-2015.md) | 17 Standard templates in 8 groups, pushing `CommercientSF8__ICPart__c`, `Account`, `CommercientSF8__SAGEBUSINESSWORK_ARTerms__c`, `CommercientSF8__SAGEBUSINESSWORK_ARCustomer__c` and more; `:` repository keys |
| Sage Intacct | [`erps/sage-intacct.md`](erps/sage-intacct.md) | 19 Standard templates in 7 groups, pushing `CommercientSF8__SAGEINTACCT_employee__c`, `CommercientSF8__SAGEINTACCT_warehouse__c`, `Account`, `CommercientSF8__SAGEINTACCT_arterm__c` and more; `:` repository keys |
| Sage MAS 90 | [`erps/sage-mas-90.md`](erps/sage-mas-90.md) | 10 Standard templates in 6 groups, pushing `users`, `account`, `CommercientSF8__AR_TRANSACTIONPAYMENTHISTORY__c`, `CommercientSF8__AR_CUSTOMER__c` and more; `:` repository keys |
| Sage X3 | [`erps/sage-x3.md`](erps/sage-x3.md) | 14 Standard templates in 6 groups, pushing `users`, `Account`, `CommercientSF8__SAGEX3_PaymentHeader__c`, `CommercientSF8__SAGEX3_PaymentDetail__c` and more; `:` repository keys |
| SAP B1 | [`erps/sap-b1.md`](erps/sap-b1.md) | 21 Standard templates in 9 groups, pushing `users`, `Account`, `Contact`, `CommercientSF16__SAP_Customer__c` and more; `:` repository keys |
| SAP HANA | [`erps/sap-hana.md`](erps/sap-hana.md) | 12 Standard templates in 5 groups, pushing `users`, `Account`, `Contact`, `CommercientSF16__KNA1__c` and more; `:` repository keys |
| SouthWare | [`erps/southware.md`](erps/southware.md) | 11 Standard templates in 5 groups, pushing `User`, `Account`, `CommercientSF40__RCUST__c`, `CommercientSF40__RSHIP__c` and more; `:` repository keys |
| Steelviking | [`erps/steelviking.md`](erps/steelviking.md) | 9 Standard templates in 5 groups, pushing `CommercientSF14__Part__c`, `account`, `CommercientSF14__Customer__c`, `CommercientSF14__Invoice__c` and more; `:` repository keys |
| SYSPRO 6 | [`erps/syspro-6.md`](erps/syspro-6.md) | 38 Standard templates in 13 groups, pushing `CommercientSF__SalArea__c`, `CommercientSF__ArSalesMove__c`, `users`, `Account` and more; `:` repository keys |
| SYSPRO 7 and above | [`erps/syspro-7-and-above.md`](erps/syspro-7-and-above.md) | Hand-written: the full Standard template set for this source, its clone-table prefix, view and prefix pairings, and the places it does not work as shipped |
| Traverse 11 | [`erps/traverse-11.md`](erps/traverse-11.md) | 13 Standard templates in 8 groups, pushing `users`, `Account`, `CommercientSF7__tblArCust__c`, `CommercientSF7__tblArShipTo__c` and more; `:` repository keys |
| Traverse Process PRO Global | [`erps/traverse-process-pro-global.md`](erps/traverse-process-pro-global.md) | 16 Standard templates in 10 groups, pushing `User`, `CommercientSF7__tblSoTransHeader__c`, `CommercientSF7__tblSoTransDetail__c`, `CommercientSF7__tblArHistHeader__c` and more; `:` repository keys |
| VAI S2K | [`erps/vai-s2k.md`](erps/vai-s2k.md) | 16 Standard templates in 6 groups, pushing `VCMENOT__c`, `VCNENOT__c`, `Account`, `Contact` and more; `:` repository keys |
| Workday | [`erps/workday.md`](erps/workday.md) | 7 Standard templates in 3 groups, pushing `Account`, `CommercientSF22__WorkDay_CustomerMaster__c`, `CommercientSF22__WorkDay_CustomerAddress__c`, `CommercientSF22__WorkDay_SalesInvoice__c` and more; `:` repository keys |
| Xero | [`erps/xero.md`](erps/xero.md) | 12 Standard templates in 6 groups, pushing `User`, `ContentDocument`, `CommercientSF15__InvoicesCreditNotes__c`, `Account` and more; `:` repository keys |
<!-- ERP-TABLE:END -->

**Work out which row applies before reading one.** The source is the ERP the tenant was registered
with: `dlake register erps` lists the catalogue’s names and codes, and `dlake admin
crmpro_templates` shows what that tenant can actually import. Match that ERP to a row above, then
read its page alongside this one — this page for the conventions that hold across every source,
that page for what this source’s own templates set. If no row matches the tenant’s ERP, this skill
alone applies: the catalogue ships no Standard templates for that pair, so there is nothing
ERP-specific to read and nothing to import.

## 11. Where this sits

`dlake-crmpro` is the general operating surface — the `crmpro_*` tools, the tables, the field mapping,
and the source-view contract that applies to every CRM. This skill adds the Salesforce values, and
its `erps/` pages add what each source ERP's own templates set up. `dlake-crmpro-hubspot` and
`dlake-crmpro-shopify` do the same for their destinations, and the conventions genuinely differ
between them. For the extract leg that fills the clone tables, see `dlake-normalsync`; for the
on-premises agent that runs it, `dlake-syncagent`; for the writeback leg, `dlake-txdownloaderpro`.
