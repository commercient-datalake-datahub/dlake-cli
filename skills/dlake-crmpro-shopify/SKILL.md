---
name: dlake-crmpro-shopify
description: >-
  Build a working CRMPro → Shopify forward sync on a Commercient tenant, whatever the source ERP: the
  `CRM_Configuration` row shape the Shopify engine dispatches on (UPPER-CASE object names `CUSTOMER`,
  `CUSTOMERADDRESS`, `PRODUCT`, `PRODUCTQUANTITY`, `PRODUCTPRICE`, an empty `CRM_PK_API_Name`, and a
  display name that reads as an operation), the view contract the shipped templates use — `RecordKey`,
  a `::` repository key, an association carried as the parent's `SFDCID`, and insert-only change
  detection — and the two update legs, which are not cursor-driven at all but compare the ERP value
  against a mirrored Shopify value and push only the difference. Use it when standing up or debugging
  any ERP → Shopify Phase 1 sync, when a product's quantity or price is not moving, or when reading a
  Shopify template's `CreateViewQuery` and wondering why it looks unlike the other CRMs'. It extends
  `dlake-crmpro`, which covers operating CRMPro generally, and it carries one child page per source
  ERP under `erps/`.
---

# CRMPro → Shopify: the working configuration

> **Keep this skill current.** `dlake` ships updates often and this text is embedded in the CLI
> you have installed. Before relying on it, compare `dlake --version` with
> `npm view @commercient/dlake version`; if they differ, run `npm install -g @commercient/dlake@latest`
> and then `dlake skills install`, which overwrites the installed skill files with the current text.

**The source ERP has its own page under this skill.** `erps/<erp>.md` is a child file of this
skill and describes what the shipped templates for that ERP → Shopify pair set up. §10 lists every
one of them and how to pick the right row; read this page first, then that one.

`dlake-crmpro` covers the tools and the general source-view contract. This skill gives the values the
Shopify engine dispatches on, and they hold whichever ERP is the source. The Shopify template set is
small, and it is small because Shopify is an e-commerce destination rather than a CRM: it carries
customers, addresses and products, and it carries **two update legs that exist only to keep a Shopify
variant's quantity and price in step with the ERP**. What is ERP-specific — which groups ship, which
clone tables the views read, which view names and prefixes they pair — lives in this skill's own
`erps/` pages, one per source ERP. Where the catalogue and a live install disagree, trust the install
and say so.

## 1. How a run is structured

Phase 1 for a hosted tenant runs on Commercient's sync servers — not on the customer's own agent,
which only fills the `dbo` clone tables. Each run:

1. `TR_CreateUpdateView` — executes `CreateViewQuery` for rows flagged `IsViewNeedsToCreate`, then
   clears the flag.
2. Shopify token refresh.
3. One block per active `CRM_Configuration` row, in ascending `Sync_Order`.

The engine records each pushed record in `TimeStampRepository` keyed `<TimeStamp_Prefix>::<RecordKey>`
with the Shopify id in `SFDCID`. Those rows are the identity map, the change cursor for the create
legs, and the way the address view finds the customer it belongs to.

**The clone-table prefix the Shopify templates read is not always the one the other CRMs use.** Some
ERPs' Shopify templates read a per-ERP clone prefix rather than the `SF_ERP_Salesforce_Clone_*` names
the Salesforce and HubSpot templates read. Each ERP's page names the prefix its own templates expect;
check what the tenant actually has with `dlake admin crmpro_tables_views` before applying a template —
an import against the wrong prefix fails with a missing-table warning and leaves the view pending.

## 2. The `CRM_Configuration` values that matter

| Column | Value the templates use | Why |
|---|---|---|
| `CRM_Object_API_Name` | **UPPER CASE**, one word: `CUSTOMER`, `CUSTOMERADDRESS`, `PRODUCT`, `PRODUCTQUANTITY`, `PRODUCTPRICE`. | These are the tokens the module dispatches on. `CUSTOMERADDRESS` carries no separator; `PRODUCTQUANTITY` and `PRODUCTPRICE` are objects in their own right, not modes of `PRODUCT`. Copy them, do not derive them. |
| `CRM_Object_Display_Name` | Reads as an operation on most rows — `createcustomer`, `createproduct`, `updatequantity`, `updateprice` — while an address row tends to repeat the object name. | `dlake-crmpro` §7b notes that for the upsert operation type the process name selects the sync path. Treat the verb form as the shape to copy, and verify against a working install before renaming any of these. |
| `CRM_PK_API_Name` | `''` on every row. | Shopify processes in this catalogue do not match on a key property; identity comes from the repository row and, for the update legs, from the Shopify variant id carried in the view. |
| `SQL_Query` | `select * from <view>` — unqualified, matching the view's own name. | |
| `TimeStamp_Prefix` | The view name **without** the `vw_` prefix for the create legs; the object name itself for the two update legs. | It must equal the literal the view's own `TimeStampRepository` join builds its key from, case included — that is the only rule. The update views build no such key. |
| `Sync_Order` | Small numbers that **collide across groups**, because each group is authored on its own. | **Set a coherent order yourself** after import: customers, then addresses, then products, then the update legs. |
| `Is_Active` | `0` in every template's insert. | A freshly imported process is inactive by design. |
| `Is_Create_Entity`, `Is_Create_Fields`, `Is_Active_Get_Records`, `Is_Active_Delete_Records` | `0` throughout. | Nothing in this set creates destination schema or reads records back. |
| `Sync_Operation_Type`, `Sync_Batch_Size`, `IsAccountMatching` | Not written by these templates at all — the insert column list stops earlier. | They take the column defaults (`'1'` upsert, `200`). If a tenant's `CRM_Configuration` has no defaults on those columns, set them explicitly. |
| every nullable text column | `''` | The templates set `''` throughout. A NULL throws inside the engine, which catches it, so the object is skipped with no recorded error. |

## 3. The view contract

The templates create their views in the gateway `dbo` schema. Where you write one yourself,
`dlake admin create_view` / `alter_view` puts it in the tenant's working schema — either works
provided `SQL_Query` names it as it exists.

**The create legs** follow the `::` convention and are **insert-only**: the repository is
`LEFT OUTER JOIN`ed on `'<PREFIX>::' + <key>` and the view filters `WHERE repo.[Key] IS NULL`, so a
record that has already been created never appears again. What they provide:

- **`RecordKey`** — the source key, which becomes the repository key suffix.
- **`[Timestamp]`** — the clone table's rowversion. The templates alias it `Timestamp`, not
  `[TimeStamp]`; keep whichever spelling the tenant's working processes use.
- **`description` and `erp_code`** — extra columns on the create views, carrying the ERP customer
  code or the record's own description. They travel as ordinary mapped fields.
- **No `SFDCID` output column.** Unlike the HubSpot contract, these views do not emit one. The
  repository is joined only to decide whether the record has already been created.

**The association is the parent's `SFDCID`.** The address view INNER-joins the customer prefix and
emits that id as `customerId`, so an address appears in the view only once its customer has synced —
parents on one run, children on the next. It also emits `[Default] = 1`, and hard-codes the country;
adjust the country expression before using it outside its assumed region.

**The update legs do not use the cursor at all**, and this is the single most important thing to know
about this CRM. They join the ERP clone table to a **mirrored Shopify table** —
`Shopify_ProductVariant` for the variant and its current quantity and price, `Shopify_InventoryLevel`
for the location — and select only rows where the two sides differ. Three consequences:

1. **A variant must already exist in the mirror table.** The join is INNER on `SKU`, so a product the
   create leg has not yet pushed — or one Shopify has but the mirror has not been refreshed with —
   simply never appears. "Prices are not updating" is usually a mirror question first.
2. **The `RecordKey` embeds the values being compared**, so each distinct old/new pairing produces a
   distinct repository key. That is what stops the same difference being pushed twice, and it is why
   these keys are not stable identities the way a create leg's key is.
3. **The comparison is on integers.** The update views cast to `INT`, so a price difference smaller
   than a whole unit is invisible to them. Widen the cast in a copy of the view where that matters.

Each ERP's page carries a worked view from its own set, with the clone-table names that set reads.

## 4. Field ownership

There is no seed/upsert pair here, because the create legs are already insert-only: **everything a
create leg sends is sent once, at creation, and never overwritten.** Anything a merchant edits in
Shopify afterwards — a product title, a customer's marketing preferences, an address — stays as they
left it.

The corollary is that the only ERP-owned fields after creation are the two the update legs carry:
inventory quantity and price. If a customer expects a description or a title change to flow through,
that is a new update-leg process modelled on §3, not a flag on the create leg.

## 5. `CRM_FieldList` is required

One row per pushed column, with `Object_Name` equal to the `CRM_Object_API_Name` value —
`CUSTOMERADDRESS`, not `CUSTOMER ADDRESS`. `View_Field_Name` and `CRM_API_Name` are the view's column
name. **An object with no `CRM_FieldList` rows pushes nothing and records no error.**

The templates carry the intended list in `MappingJson`, and it is worth reading because it is
narrower than the view: a customer view can emit a `description` column the mapping omits, and a
product view can emit `SavedTimeStamp` and `RecordKey` columns that are mechanism rather than data.
**The Registration API's template import does not populate `CRM_FieldList`** — it runs the
`CreateViewQuery` and the `Insert_Query` and records the connection, nothing more. Read `MappingJson`
with `crmpro_templates`, then check `crmpro_field_mapping` on the created process before activating.

The update legs' mappings are almost entirely mechanism: the variant id, the inventory item id, the
location id, the old quantity and the new value. Those all need rows.

## 6. Building a process by hand

1. `crmpro_create_process` per process, minimal arguments only: `recordType ADDProcessFromERP`,
   `crmObjectApiName` (upper case, from §2), `selectedTable` (the clone-table key), `customViewName`,
   `displayName`, `crmPkApiName`. Optional arguments can return an opaque `400`; set those
   afterwards.
2. `crmpro_update_process` does not persist `createViewQuery`, `isViewNeedsToCreate` or a CRM rebind.
   For those, expose `CRM_Configuration` and `CRM_FieldList` with `set_entity_exposure`,
   `restart_dab` once, then write the rows with `dlake tool update_record`.
3. Create the views, `dbo`-qualifying the clone tables inside them, and use the same `::` literal in
   the view that you put in `TimeStamp_Prefix`.
4. Set every nullable text column to `''`, set an explicit `Sync_Order` across all the processes,
   and populate `CRM_FieldList`.
5. Activate — `crmpro_update_process_field --fieldName Is_Active --value true` (the CLI argument is
   `value`) — then `crmpro_set_sync_enabled --enabled true`. Where the flag row does not exist yet
   the first call seeds it at `0`, so re-read `crmpro_sync_status` and call again.

**Display names are the import's identity.** The import inserts a template's row only when no
`CRM_Configuration` row already carries that trimmed `CRM_Object_Display_Name`. Two processes sharing
a display name means the second never gets created — which matters here, where the display names are
short verbs.

## 7. When a run pushes no records

The log shows each object starting and ending in about 0.00 seconds and a zero total, with nothing in
the error tables. Check in this order; each of these produces exactly that result.

1. **Does the view return rows?** `dlake tool query "SELECT COUNT(*) FROM <schema>.<view>"`. For a
   create leg, zero means every source row already has a repository entry. For an update leg, zero
   means the ERP and the mirrored Shopify values already agree — or the mirror is empty.
2. **Is the mirror populated?** `SELECT COUNT(*) FROM dbo.Shopify_ProductVariant` and the same for
   `Shopify_InventoryLevel`. An empty mirror makes both update legs return nothing, permanently and
   silently.
3. **Do the clone tables use the prefix the view expects?** That prefix is on this ERP's own page.
4. **Is `CRM_Object_API_Name` UPPER CASE and spelled from the §2 list**, case-exact?
5. **Does `CRM_FieldList` have rows** for that `Object_Name`?
6. **Any NULL nullable text column** on the configuration row? Set it to `''`.
7. **Does `TimeStamp_Prefix` match the `'<prefix>::'` literal in the view**, case included?
8. **Is `Sync_Order` coherent?** Addresses ordered before customers, or update legs before the create
   leg, produce empty views on the first run and recover only on the next.

## 8. Shopify-side facts that shape the design

- **The connection is an OAuth flow whose callback lands on the website, not on a loopback
  listener**, and it needs the shop name (the `yourshop` part of the myshopify host). The CRM
  catalogue does not advertise Shopify as having an end-to-end CLI flow, so expect the browser leg to
  be completed by hand.
- **Shopify is one of the modules `dlake-crmpro` §8 flags as not necessarily honouring `Is_Active`.**
  If turning a process off does not stop it syncing, check that before looking for a platform fault.
- **Inventory lives on a location.** The quantity leg carries a location id out of the mirrored
  inventory-level table; a shop with more than one location needs that join narrowed deliberately
  rather than left to whichever row the join returns.
- **Quantity is summed across ERP warehouses.** Both the create leg and the quantity update leg sum
  on-hand across every warehouse row for a stock code. Where the merchant sells from one warehouse
  only, filter it in a copy of the view.
- **Price comes from a single price code.** The create leg takes the first selling price it finds;
  the customer's actual price list may not be that one.

## 9. Verifying

```bash
# per-prefix counts; every synced record carries its Shopify id
dlake tool query --profile <tenant> --sql "SELECT LEFT([Key], CHARINDEX('::',[Key])-1) AS prefix, COUNT(*) n, COUNT(NULLIF(SFDCID,'')) withId FROM dbo.TimeStampRepository WHERE CHARINDEX('::',[Key])>0 GROUP BY LEFT([Key], CHARINDEX('::',[Key])-1)"
```

`withId = n` for every prefix is the success condition for the create legs. The update legs
accumulate one repository row per pushed difference rather than one per product, so their counts grow
over time and are not a product count — read them as a change log, not an inventory.

Confirm in Shopify itself: a customer by the ERP customer code carried in `erp_code`, a product by
its SKU.

## 10. The source ERP’s own page

One row per source ERP the catalogue ships Standard Shopify templates for. Each page is a child
file of this skill, addressed as `dlake-crmpro-shopify/erps/<erp>` — `dlake skills show
dlake-crmpro-shopify/erps/<erp>` prints one, and `dlake skills install` writes them beside this
file.

<!-- ERP-TABLE:BEGIN dlake-crmpro-shopify -->
| ERP | Page | What its templates deliver |
|---|---|---|
| Acumatica | [`erps/acumatica.md`](erps/acumatica.md) | 4 Standard templates in 4 groups, pushing `CUSTOMER`, `CUSTOMERADDRESS`, `PRODUCT`, `PRODUCTPRICE`; `::` repository keys |
| Acumatica Cloud | [`erps/acumatica-cloud.md`](erps/acumatica-cloud.md) | 4 Standard templates in 4 groups, pushing `CUSTOMER`, `CUSTOMERADDRESS`, `PRODUCT`, `PRODUCTPRICE`; `::` repository keys |
| Dynamics Business Central | [`erps/dynamics-business-central.md`](erps/dynamics-business-central.md) | 2 Standard templates in 2 groups, pushing `CUSTOMER`, `CUSTOMERADDRESS`; `::` repository keys |
| Epicor 10 | [`erps/epicor-10.md`](erps/epicor-10.md) | 5 Standard templates in 4 groups, pushing `CUSTOMER`, `CUSTOMERADDRESS`, `PRODUCT`, `PRODUCTQUANTITY` and more; `::` repository keys |
| Epicor 10 Cloud | [`erps/epicor-10-cloud.md`](erps/epicor-10-cloud.md) | 5 Standard templates in 4 groups, pushing `CUSTOMER`, `CUSTOMERADDRESS`, `PRODUCT`, `PRODUCTQUANTITY` and more; `::` repository keys |
| Epicor 9 and 9.5 | [`erps/epicor-9-and-9-5.md`](erps/epicor-9-and-9-5.md) | 5 Standard templates in 4 groups, pushing `CUSTOMER`, `CUSTOMERADDRESS`, `PRODUCT`, `PRODUCTQUANTITY` and more; `::` repository keys |
| Epicor Prophet 21 (P21) | [`erps/epicor-prophet-21-p21.md`](erps/epicor-prophet-21-p21.md) | 4 Standard templates in 3 groups, pushing `CUSTOMER`, `CUSTOMERADDRESS`, `PRODUCTQUANTITY`, `PRODUCTPRICE`; `::` repository keys |
| Microsoft Business Central | [`erps/microsoft-business-central.md`](erps/microsoft-business-central.md) | 2 Standard templates in 2 groups, pushing `CUSTOMER`, `CUSTOMERADDRESS`; `::` repository keys |
| Microsoft Dynamics GP 2013 | [`erps/microsoft-dynamics-gp-2013.md`](erps/microsoft-dynamics-gp-2013.md) | 5 Standard templates in 4 groups, pushing `PRODUCTPRICE`, `CUSTOMER`, `CUSTOMERADDRESS`, `PRODUCT` and more; `::` repository keys |
| Microsoft Dynamics GP 2016 | [`erps/microsoft-dynamics-gp-2016.md`](erps/microsoft-dynamics-gp-2016.md) | 5 Standard templates in 4 groups, pushing `PRODUCTPRICE`, `CUSTOMER`, `CUSTOMERADDRESS`, `PRODUCT` and more; `::` repository keys |
| Microsoft Dynamics GP 2017 | [`erps/microsoft-dynamics-gp-2017.md`](erps/microsoft-dynamics-gp-2017.md) | 5 Standard templates in 4 groups, pushing `CUSTOMER`, `CUSTOMERADDRESS`, `PRODUCT`, `PRODUCTQUANTITY` and more; `::` repository keys |
| QuickBooks Desktop | [`erps/quickbooks-desktop.md`](erps/quickbooks-desktop.md) | 5 Standard templates in 4 groups, pushing `CUSTOMER`, `CUSTOMERADDRESS`, `PRODUCT`, `PRODUCTQUANTITY` and more; `::` repository keys |
| Sage 100 (US) | [`erps/sage-100-us.md`](erps/sage-100-us.md) | 12 Standard templates in 11 groups, pushing `CUSTOMER`, `CUSTOMERADDRESS`, `PRODUCT`, `ORDER` and more; `::` repository keys |
| Sage 100 2013 V5 | [`erps/sage-100-2013-v5.md`](erps/sage-100-2013-v5.md) | 7 Standard templates in 7 groups, pushing `CUSTOMER`, `CUSTOMERADDRESS`, `PRODUCT`, `ORDER` and more; `::` repository keys |
| Sage 100 2015 | [`erps/sage-100-2015.md`](erps/sage-100-2015.md) | 9 Standard templates in 8 groups, pushing `CUSTOMER`, `CUSTOMERADDRESS`, `PRODUCT`, `ORDER` and more; `::` repository keys |
| Sage 100 2016 | [`erps/sage-100-2016.md`](erps/sage-100-2016.md) | 9 Standard templates in 8 groups, pushing `CUSTOMER`, `CUSTOMERADDRESS`, `PRODUCT`, `ORDER` and more; `::` repository keys |
| Sage 100 2017 | [`erps/sage-100-2017.md`](erps/sage-100-2017.md) | 12 Standard templates in 11 groups, pushing `CUSTOMER`, `CUSTOMERADDRESS`, `PRODUCT`, `ORDER` and more; `::` repository keys |
| Sage 100 2018 | [`erps/sage-100-2018.md`](erps/sage-100-2018.md) | 9 Standard templates in 8 groups, pushing `CUSTOMER`, `CUSTOMERADDRESS`, `PRODUCT`, `ORDER` and more; `::` repository keys |
| Sage 50 UK | [`erps/sage-50-uk.md`](erps/sage-50-uk.md) | 5 Standard templates in 4 groups, pushing `CUSTOMER`, `CUSTOMERADDRESS`, `PRODUCT`, `PRODUCTQUANTITY` and more; `::` repository keys |
| Sage 50 US | [`erps/sage-50-us.md`](erps/sage-50-us.md) | 5 Standard templates in 4 groups, pushing `CUSTOMER`, `CUSTOMERADDRESS`, `PRODUCT`, `PRODUCTQUANTITY` and more; `::` repository keys |
| Sage Intacct | [`erps/sage-intacct.md`](erps/sage-intacct.md) | 1 Standard template in 1 group, pushing `PRODUCTQUANTITY`; `:` repository keys |
| SYSPRO 6 | [`erps/syspro-6.md`](erps/syspro-6.md) | 5 Standard templates in 4 groups, pushing `CUSTOMER`, `CUSTOMERADDRESS`, `PRODUCT`, `PRODUCTQUANTITY` and more; `::` repository keys |
| SYSPRO 7 and above | [`erps/syspro-7-and-above.md`](erps/syspro-7-and-above.md) | Hand-written: the full Standard template set for this source, its clone-table prefix, view and prefix pairings, and the places it does not work as shipped |
| Traverse 11 | [`erps/traverse-11.md`](erps/traverse-11.md) | 5 Standard templates in 4 groups, pushing `CUSTOMER`, `CUSTOMERADDRESS`, `PRODUCT`, `PRODUCTQUANTITY` and more; `::` repository keys |
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
and the source-view contract that applies to every destination. This skill adds the Shopify values,
and its `erps/` pages add what each source ERP's own templates set up. `dlake-crmpro-hubspot` and
`dlake-crmpro-salesforce` do the same for theirs, and the conventions genuinely differ between them.
For the extract leg that fills the clone tables, see `dlake-normalsync`; for the on-premises agent
that runs it, `dlake-syncagent`; for the writeback leg, `dlake-txdownloaderpro`.
