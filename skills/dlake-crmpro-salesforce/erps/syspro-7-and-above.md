---
name: dlake-crmpro-salesforce/erps/syspro-7-and-above
description: >-
  What the shipped CRMPro templates set up for a SYSPRO 7 and above source pushing into Salesforce:
  the ten Standard template groups and the business outcome of each, the clone-table prefix their
  views read, the view-name / `TimeStamp_Prefix` pairings the set uses, a worked view showing the
  single-colon key and the cursor-inside-the-join shape, the lookup ladder each view reads and the
  order that implies, the two Standard rows whose empty `CRM_Object_API_Name` makes the import skip
  them, and the three places in this set that do not work as shipped. Use it alongside
  `dlake-crmpro-salesforce` when standing up or debugging a SYSPRO → Salesforce Phase 1 sync, or
  when reading one of these templates' `CreateViewQuery`. It extends `dlake-crmpro`, which covers
  operating CRMPro generally, and `dlake-crmpro-salesforce`, the destination skill this page is a
  child of, which carries the Salesforce conventions that hold across every ERP.
---

# CRMPro → Salesforce — SYSPRO 7 and above: what the shipped templates set up

> **Keep this skill current.** `dlake` ships updates often and this text is embedded in the CLI
> you have installed. Before relying on it, compare `dlake --version` with
> `npm view @commercient/dlake version`; if they differ, run `npm install -g @commercient/dlake@latest`
> and then `dlake skills install`, which overwrites the installed skill files with the current text.

`dlake-crmpro` is the product parent and the authority for everything general: the `crmpro_*` tools,
`CRM_Configuration` and `CRM_FieldList`, `TimeStampRepository`, the three kinds of source view, the
NULL-cursor rule and the silent zero-record run. `dlake-crmpro-salesforce` is the destination skill
this page is a child of, and the authority for the Salesforce conventions that hold across every ERP
— the single-colon key, the external-id identity column, the namespace pair, the id-chaining ladder.
Read those first; this page does not repeat them. This page is
`dlake-crmpro-salesforce/erps/syspro-7-and-above.md`, and that skill's ERP table is what points at it.

What follows is only what the shipped SYSPRO 7 → Salesforce templates themselves set, read from the
template catalogue (`crmpro_templates --crmName Salesforce`) and the Registration API's
template-import path. Where the catalogue and a live install disagree, trust the install and say so.

## 1. What the default catalogue delivers

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

The views in this set read the `SF_ERP_Salesforce_Clone_*` clone tables — for example
`SF_ERP_Salesforce_Clone_ArCustomer`. Check what the tenant actually has with
`dlake admin crmpro_tables_views` before applying a template: an import against the wrong prefix
fails with a missing-table warning and leaves the view pending.

## 2. The view-name and `TimeStamp_Prefix` pairings

`dlake-crmpro-salesforce` §2 makes the general point that the prefix is not the view name. These are
the pairings this set actually uses, and they are the literals every downstream view joins on:

| View | `TimeStamp_Prefix` |
|---|---|
| `vw_SYSPRO7_Account` | `vw_Account` |
| `vw_SYSPRO7_Salesperson` | `vw_SalesPerson` |
| `vw_SYSPRO7_Product` | `vwProduct` |

Read the rest off the templates with `dlake admin crmpro_templates`; the shape is the same — a
`vw_SYSPRO7_*` view paired with a shorter prefix literal, case-exact.

Per-object external-id fields in this set follow the destination skill's list, with
`CommercientSF__ArCustomer_Code__c` on the AR customer object.

## 3. A worked view from this set

The upsert shape, as these templates write it — a single-colon key, the cursor equality inside the
`ON` clause, no `RecordKey` and no `SFDCID` output column:

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

The filters in this set are worth keeping when you copy a view: the sales-order header view limits
itself to orders within the last twelve months by entry date, the account view excludes customers
with a blank `Name`, and the product view excludes a blank `StockCode`.

## 4. The lookup ladder in this set

Which view reads which prefix, and therefore the order the processes have to be activated in:

- the account view reads the **user** prefix to fill `OwnerId`;
- the AR customer view reads the account, terms, salesperson, branch, area and customer-class
  prefixes to fill its six lookup fields;
- the sales-order header view reads the account, AR customer and salesperson prefixes;
- the price-book entry views read the product and price-book prefixes to fill `Product2Id` and
  `Pricebook2Id`, and both are **INNER** joins.

So: users and the small lookup objects, then `Account`, then the AR customer, then orders, invoices,
products and price books. The reverse-lookup processes in the Account and Product groups run after
the child exists and write its id back onto the parent.

**The price-book prefix is written by a process this Standard set does not contain.** The Standard
price-book entry templates INNER-join a price-book prefix, but the process that populates it ships as
a Community template. Import it, or create that process by hand, before expecting any price-book
entry to appear.

## 5. Base views the import will not create

Two further `Standard` rows in the Salesorder group carry an **empty `CRM_Object_API_Name`**: their
job is to create the base sales-order views the `Call_vw_*` processes select from. The Registration
API's import skips any template with an empty API name, so treat those base views as something to
create yourself — `vw_SYSPRO7_SorMasterRep` and `vw_SYSPRO7_SorDetailRep`, then the `Call_vw_*`
wrapper on top — following the by-hand sequence in `dlake-crmpro-salesforce` §6.

## 6. Two things in this set that do not work as written

- **The catalogue's user query names a differently-numbered package namespace from the rest of the
  catalogue.** Check the namespace in the tenant's org before enabling the user read-back, rather
  than assuming the shipped query matches the installed package.
- **One shipped lookup join in the AR customer view builds its key with `::` against a prefix the
  customer-class process writes with a single colon.** That join cannot match. Where the
  customer-class lookup lands empty, this is the first thing to read.

## 7. Where this sits

`dlake-crmpro` is the general operating surface, and `dlake-crmpro-salesforce` is the destination
skill this page sits under: its own text is the authority for the Salesforce conventions that hold
across every ERP, and its ERP table lists this page alongside every sibling ERP page for this
destination. For the extract leg that fills the clone tables, see `dlake-normalsync`; for the
on-premises agent that runs it, `dlake-syncagent`; for the writeback leg, `dlake-txdownloaderpro`;
for standing an integration up, `dlake-integration-setup`.
