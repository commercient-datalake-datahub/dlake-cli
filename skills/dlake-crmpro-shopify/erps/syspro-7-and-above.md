---
name: dlake-crmpro-shopify/erps/syspro-7-and-above
description: >-
  What the shipped CRMPro templates set up for a SYSPRO 7 and above source pushing into Shopify: the
  four Standard template groups and the business outcome of each, the clone-table prefix these views
  read, the view names and the `TimeStamp_Prefix` each is paired with, a worked create view and a
  worked update view from this set, and the split that makes creation and maintenance different
  processes on different objects. Use it alongside `dlake-crmpro-shopify` when standing up or
  debugging a SYSPRO → Shopify Phase 1 sync, or when a product's quantity or price is not moving. It
  extends `dlake-crmpro`, which covers operating CRMPro generally, and `dlake-crmpro-shopify`, the
  destination skill this page is a child of, which carries the Shopify conventions that hold across
  every ERP.
---

# CRMPro → Shopify — SYSPRO 7 and above: what the shipped templates set up

> **Keep this skill current.** `dlake` ships updates often and this text is embedded in the CLI
> you have installed. Before relying on it, compare `dlake --version` with
> `npm view @commercient/dlake version`; if they differ, run `npm install -g @commercient/dlake@latest`
> and then `dlake skills install`, which overwrites the installed skill files with the current text.

`dlake-crmpro` is the product parent and the authority for everything general: the `crmpro_*` tools,
`CRM_Configuration` and `CRM_FieldList`, `TimeStampRepository`, the three kinds of source view, the
NULL-cursor rule and the silent zero-record run. `dlake-crmpro-shopify` is the destination skill this
page is a child of, and the authority for the Shopify conventions that hold across every ERP — the
UPPER-CASE object tokens, the empty `CRM_PK_API_Name`, the `::` key, the insert-only create legs and
the mirror-comparison update legs. Read those first; this page does not repeat them. This page is
`dlake-crmpro-shopify/erps/syspro-7-and-above.md`, and that skill's ERP table is what points at it.

What follows is only what the shipped SYSPRO 7 → Shopify templates themselves set, read from the
template catalogue (`crmpro_templates --crmName Shopify`) and the Registration API's template-import
path. Where the catalogue and a live install disagree, trust the install and say so.

## 1. What the default catalogue delivers

Four template groups ship for SYSPRO 7 → Shopify. Business outcome first; the insert/update/delete
behaviour comes from each template's own `IsDefault*` flags.

| Group | Business outcome | Object | Source view |
|---|---|---|---|
| **CUSTOMER** | ERP customers become Shopify customers: the name is split into first/middle/last, the email and phone come across, and the ERP customer code travels with the record. New customers are created; existing ones are not updated and none are deleted. | `CUSTOMER` | `vw_SHOPIFY_NEW_CUSTOMER` |
| **CUSTOMER ADDRESS** | Each ERP customer's ship-to address becomes the default address on the matching Shopify customer. Created once per customer; not updated, not deleted. | `CUSTOMERADDRESS` | `vw_SHOPIFY_NEW_CUSTOMER_ADDRESS` |
| **PRODUCT** | ERP stock codes become Shopify products, with the ERP description as the title and body, the stock code as the SKU, the price from the ERP price table and the on-hand quantity summed across warehouses. Created once; not updated, not deleted. | `PRODUCT` | `vw_SHOPIFY_NEW_PRODUCT` |
| **PRODUCT UPDATE** | Keeps an existing Shopify variant in step with the ERP: one leg pushes the on-hand quantity when it differs from Shopify's, the other pushes the selling price when it differs. Update-only — neither leg creates or deletes anything. | `PRODUCTQUANTITY`, `PRODUCTPRICE` | `Shopify_ProductVariant_QuantyUpdate`, `Shopify_ProductVariant_PriceUpdate` |

Note the split: **creation and maintenance are different processes on different objects.** Product
creation happens once through `PRODUCT`; everything afterwards is the two `PRODUCT UPDATE` legs. A
price change in the ERP does not flow through the create leg, because that leg never revisits a
product it has already created.

## 2. The clone tables and the prefixes

**The clone tables these Shopify templates read are named differently from the other CRMs'.** They
use a `SF_SYSPRO61_*` prefix — `SF_SYSPRO61_ArCustomer`, `SF_SYSPRO61_InvMaster`,
`SF_SYSPRO61_InvPrice`, `SF_SYSPRO61_InvWarehouse` — where the Salesforce and HubSpot templates read
`SF_ERP_Salesforce_Clone_*`. Check which prefix the tenant actually has with
`dlake admin crmpro_tables_views` before applying a template.

The `TimeStamp_Prefix` pairings this set uses:

| View | `TimeStamp_Prefix` |
|---|---|
| `vw_SHOPIFY_NEW_CUSTOMER` | `SHOPIFY_NEW_CUSTOMER` |
| `vw_SHOPIFY_NEW_CUSTOMER_ADDRESS` | `SHOPIFY_NEW_CUSTOMER_ADDRESS` |
| `vw_SHOPIFY_NEW_PRODUCT` | `SHOPIFY_NEW_PRODUCT` |
| `Shopify_ProductVariant_QuantyUpdate` | `PRODUCTQUANTITY` |
| `Shopify_ProductVariant_PriceUpdate` | `PRODUCTPRICE` |

`Sync_Order` in this set is `1` and `2` in the customer group, `1` in the product group, and `1` on
both update legs — values that collide across groups, so set a coherent order yourself after import.

## 3. A worked create view

```sql
CREATE VIEW [dbo].[vw_SHOPIFY_NEW_CUSTOMER] AS
SELECT ISNULL(a.Customer, '') AS RecordKey,
       ISNULL(a.Customer, '') AS erp_code,
       ISNULL(a.Email, '')    AS Email,
       ISNULL(a.Name, '')     AS Name,
       ISNULL(a.Telephone,'') AS Phone,
       a.[TimeStamp]          AS [Timestamp]
FROM SF_SYSPRO61_ArCustomer a
LEFT OUTER JOIN TimeStampRepository AS repo
       ON 'SHOPIFY_NEW_CUSTOMER::' + ISNULL(a.Customer, '') = repo.[Key]
WHERE repo.[Key] IS NULL
```

## 4. A worked update view

```sql
CREATE VIEW Shopify_ProductVariant_PriceUpdate AS
SELECT a.StockCode + ':' + CAST(d.Price AS VARCHAR(10)) AS RecordKey,
       d.ProductVariantId AS ProductVariantId,
       a.[TimeStamp]      AS [TimeStamp],
       CAST((SELECT TOP 1 SellingPrice FROM SF_SYSPRO61_InvPrice WHERE StockCode = a.StockCode) AS INT) AS Price
FROM SF_SYSPRO61_InvMaster AS a
INNER JOIN Shopify_ProductVariant AS d ON a.StockCode = d.SKU
WHERE CAST((SELECT TOP 1 SellingPrice FROM SF_SYSPRO61_InvPrice WHERE StockCode = a.StockCode) AS INT) <> d.Price
```

The mirror join, the value-bearing `RecordKey` and the integer cast are the three things
`dlake-crmpro-shopify` §3 warns about, and this is the view they are read from.

## 5. Where this sits

`dlake-crmpro` is the general operating surface, and `dlake-crmpro-shopify` is the destination skill
this page sits under: its own text is the authority for the Shopify conventions that hold across
every ERP, and its ERP table lists this page alongside every sibling ERP page for this destination.
For the extract leg that fills the clone tables, see `dlake-normalsync`; for the on-premises agent
that runs it, `dlake-syncagent`; for the writeback leg, `dlake-txdownloaderpro`; for standing an
integration up, `dlake-integration-setup`.
