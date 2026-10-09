---
name: dlake-crmpro-zohocrm/erps/microsoft-dynamics-ax
kind: erp-summary
description: >-
  Use it when standing up or reading a Microsoft Dynamics AX → Zoho CRM template set, when deciding
  which templates to import and activate, or when a run completes without pushing records and the
  answer is in the view or the configuration row. It extends dlake-crmpro, which covers operating
  CRMPro generally, and dlake-crmpro-zohocrm, the destination skill this page is a child of, which
  carries the Zoho CRM conventions that hold across every ERP.
---
# CRMPro → Zoho CRM — Microsoft Dynamics AX: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-zohocrm/erps/microsoft-dynamics-ax` (or `list_skills`) against
the Commercient admin plane. Existing customers who need access or help: contact
support@commercient.com. New customers: contact sales@commercient.com to become a customer and be
whitelisted.

dlake-crmpro is the parent skill and the authority for everything general: the CRMPro tools, process
configuration and field list, the sync history, how source data is selected, and what a run that
finds nothing does. Read it first; this page does not repeat it. dlake-crmpro-zohocrm is the
destination skill this page is a child of, and the authority for the Zoho CRM conventions that hold
across every ERP: read it first, then come back here for what this source's own templates set. This
page grows as the catalogue does.

## 1. What the templates deliver

| Group | Business outcome | Objects | Source tables and views |
|---|---|---|---|
| **Microsoft Dynamics AX Payment term** | The templates push Commercient Dynamics AX Payment Terms object to Zoho CRM. | Commercient Dynamics AX Payment Terms object | payment terms |
| **Accounts** | The templates push Accounts to Zoho CRM. | Accounts | customers, extended customer details, address book parties, shipping address feed, sales advisor targets (custom table), customer aging |
| **Microsoft Dynamics AX Customer** | The templates push Commercient Dynamics AX Customer object to Zoho CRM. | Commercient Dynamics AX Customer object | customers, address book parties |
| **Products** | The templates push Products to Zoho CRM. | Products | items, ordered status (custom table), product translations, item tracking dimension groups, tracking dimension groups, item module settings |
| **Microsoft Dynamics AX Item** | The templates push Commercient Dynamics AX Item object to Zoho CRM. | Commercient Dynamics AX Item object | items, ordered status (custom table) |
| **Microsoft Dynamics AX Shipping Address** | The templates push Commercient Dynamics AX Shipping Address object to Zoho CRM. | Commercient Dynamics AX Shipping Address object | customers, address book parties, party locations, postal addresses, countries and regions, states |
| **Contacts** | The templates push Contacts to Zoho CRM. | Contacts | contact persons, customers, address book parties, person names, party locations, postal addresses |
| **Microsoft Dynamics AX Warehouse** | The templates push Commercient Dynamics AX Warehouse object to Zoho CRM. | Commercient Dynamics AX Warehouse object | warehouses |
| **Microsoft Dynamics AX Sales Quotation Header** | The templates push Commercient Dynamics AX Sales Quotation Header object to Zoho CRM. | Commercient Dynamics AX Sales Quotation Header object | sales quotations |
| **Microsoft Dynamics AX Sales Quotation Line** | The templates push Commercient Dynamics AX Sales Quotation Line object to Zoho CRM. | Commercient Dynamics AX Sales Quotation Line object | sales quotation lines |
| **Microsoft Dynamics AX Sales Order Header** | The templates push Commercient Dynamics AX Sales Order Header object to Zoho CRM. | Commercient Dynamics AX Sales Order Header object | sales orders |
| **Microsoft Dynamics AX Sales Order Detail** | The templates push Commercient Dynamics AX Sales Order Detail object to Zoho CRM. | Commercient Dynamics AX Sales Order Detail object | sales order lines |
| **Microsoft Dynamics AX Invoice Header** | The templates push Commercient Dynamics AX Invoice Header object to Zoho CRM. | Commercient Dynamics AX Invoice Header object | customer invoice headers |
| **Microsoft Dynamics AX Invoice Detail** | The templates push Commercient Dynamics AX Invoice Detail object to Zoho CRM. | Commercient Dynamics AX Invoice Detail object | customer invoice lines |
| **Dynamics AX Cust Pur History** | The templates push Dynamics AX Customer Purchase History (custom object) to Zoho CRM. | Dynamics AX Customer Purchase History (custom object) | customer purchase history (custom table), customers |
| **Product Price books** | The templates push Zoho product price book relation to Zoho CRM. | Zoho product price book relation | items, ordered status (custom table), price and discount agreements |
| **Dynamics AX Dispatch Shipping** | The templates push Dynamics AX Outbound Shipping (custom object) to Zoho CRM. | Dynamics AX Outbound Shipping (custom object) | packing slip lines, associated shipping guide lines (custom table), shipping guides (custom table), sales orders, postal addresses, states |
| **Dynamics AX Contact person** | The templates push Commercient Dynamics AX Salesperson object to Zoho CRM. | Commercient Dynamics AX Salesperson object | contact persons, customers, address book parties, party locations, postal addresses, countries and regions |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Microsoft Dynamics AX Payment term | Commercient Dynamics AX Payment Terms object | Commercient external key column | 1 |
| Accounts | Accounts | Commercient AR customer code (Zoho field) | 2 |
| Microsoft Dynamics AX Customer | Commercient Dynamics AX Customer object | Commercient external key column | 3 |
| Products | Products | Commercient external key column | 4 |
| Microsoft Dynamics AX Item | Commercient Dynamics AX Item object | Commercient external key column | 5 |
| Microsoft Dynamics AX Shipping Address | Commercient Dynamics AX Shipping Address object | Commercient external key column | 5 |
| Contacts | Contacts | Commercient external key column | 5 |
| Microsoft Dynamics AX Warehouse | Commercient Dynamics AX Warehouse object | Commercient external key column | 6 |
| Microsoft Dynamics AX Sales Quotation Header | Commercient Dynamics AX Sales Quotation Header object | Commercient external key column | 7 |
| Microsoft Dynamics AX Sales Quotation Line | Commercient Dynamics AX Sales Quotation Line object | Commercient external key column | 8 |
| Microsoft Dynamics AX Sales Order Header | Commercient Dynamics AX Sales Order Header object | Commercient external key column | 9 |
| Microsoft Dynamics AX Sales Order Detail | Commercient Dynamics AX Sales Order Detail object | Commercient external key column | 10 |
| Microsoft Dynamics AX Invoice Header | Commercient Dynamics AX Invoice Header object | Commercient external key column | 11 |
| Microsoft Dynamics AX Invoice Detail | Commercient Dynamics AX Invoice Detail object | Commercient external key column | 12 |
| Dynamics AX Cust Pur History | Dynamics AX Customer Purchase History (custom object) | Commercient external key column | 13 |
| Product Price books | Zoho product price book relation | Commercient external key column | 17 |
| Dynamics AX Dispatch Shipping | Dynamics AX Outbound Shipping (custom object) | Commercient external key column | 18 |
| Dynamics AX Contact person | Commercient Dynamics AX Salesperson object | Commercient external key column | 19 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| payment terms feed | insert only | payment terms |
| account feed | insert only | customers, extended customer details, address book parties, shipping address feed, sales advisor targets (custom table) |
| customer feed | insert only | customers, address book parties |
| product feed | insert only | items, ordered status (custom table), product translations, item tracking dimension groups, tracking dimension groups |
| item feed | insert only | items, ordered status (custom table) |
| delivery address feed | insert only | customers, address book parties, party locations, postal addresses, countries and regions |
| contact feed | insert only | contact persons, customers, address book parties, person names, party locations |
| warehouse feed | insert + update | warehouses |
| sales quotation header feed | insert only | sales quotations |
| sales quotation line feed | insert only | sales quotation lines |
| sales order header feed | insert only | sales orders |
| sales order detail feed | insert only | sales order lines |
| invoice feed | insert only | customer invoice headers |
| invoice line feed | insert only | customer invoice lines |
| customer purchase history feed | insert only | customer purchase history (custom table), customers |
| product price book feed | insert only | items, ordered status (custom table), price and discount agreements |
| outbound shipping feed | insert only | packing slip lines, associated shipping guide lines (custom table), shipping guides (custom table), sales orders, postal addresses |
| customer contact feed | insert only | contact persons, customers, address book parties, party locations, postal addresses |

## 4. Order of work

The templates set run sequence from 1 to 19. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 1 — Microsoft Dynamics AX Payment term
- 2 — Accounts
- 3 — Microsoft Dynamics AX Customer
- 4 — Products
- 5 — Microsoft Dynamics AX Item, Microsoft Dynamics AX Shipping Address, Contacts
- 6 — Microsoft Dynamics AX Warehouse
- 7 — Microsoft Dynamics AX Sales Quotation Header
- 8 — Microsoft Dynamics AX Sales Quotation Line
- 9 — Microsoft Dynamics AX Sales Order Header
- 10 — Microsoft Dynamics AX Sales Order Detail
- 11 — Microsoft Dynamics AX Invoice Header
- 12 — Microsoft Dynamics AX Invoice Detail
- 13 — Dynamics AX Cust Pur History
- 17 — Product Price books
- 18 — Dynamics AX Dispatch Shipping
- 19 — Dynamics AX Contact person

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- payment terms feed reads payment terms sync output; no template in this set writes payment terms
  sync output
- account feed reads account sync output; no template in this set writes account sync output
- customer feed reads account sync output, payment terms sync output, customer sync output; no
  template in this set writes account sync output, payment terms sync output, customer sync output
- product feed reads product sync output; no template in this set writes product sync output
- item feed reads product sync output, item sync output; no template in this set writes product sync
  output, item sync output
- delivery address feed reads account sync output, customer sync output, delivery address sync
  output; no template in this set writes account sync output, customer sync output, delivery address
  sync output
- contact feed reads account sync output, contact sync output; no template in this set writes
  account sync output, contact sync output
- warehouse feed reads warehouse sync output; no template in this set writes warehouse sync output
- sales quotation header feed reads account sync output, customer sync output, sales quotation
  header sync output; no template in this set writes account sync output, customer sync output,
  sales quotation header sync output
- sales quotation line feed reads sales quotation header sync output, item sync output, sales
  quotation line sync output; no template in this set writes sales quotation header sync output,
  item sync output, sales quotation line sync output
- sales order header feed reads account sync output, customer sync output, sales order header sync
  output; no template in this set writes account sync output, customer sync output, sales order
  header sync output
- sales order detail feed reads sales order header sync output, item sync output, sales order detail
  sync output; no template in this set writes sales order header sync output, item sync output,
  sales order detail sync output
- invoice feed reads account sync output, customer sync output, invoice sync output; no template in
  this set writes account sync output, customer sync output, invoice sync output
- invoice line feed reads invoice sync output, item sync output, invoice line sync output; no
  template in this set writes invoice sync output, item sync output, invoice line sync output
- customer purchase history feed reads account sync output, customer sync output, customer purchase
  history sync output; no template in this set writes account sync output, customer sync output,
  customer purchase history sync output
- product price book feed reads product sync output, price book sync output, product price book sync
  output; no template in this set writes product sync output, price book sync output, product price
  book sync output
- outbound shipping feed reads account sync output, customer sync output, outbound shipping sync
  output; no template in this set writes account sync output, customer sync output, outbound
  shipping sync output
- customer contact feed reads account sync output, customer sync output, customer contact sync
  output; no template in this set writes account sync output, customer sync output, customer contact
  sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Zoho CRM pairs |
|---|---|---|---|
| Microsoft Dynamics AX Payment term | Commercient Dynamics AX Payment Terms object | 17 | Company data area, Payment terms code, Partition → Commercient external key (custom field), Cash payment → Cash payment, Credit card credit check → Credit card credit check (Zoho field), Company data area → Company code (Zoho field), Description → Description |
| Accounts | Accounts | 51 | Company data area, Account number, Partition → Commercient AR customer code (Zoho field), Name → Account name, Address → Billing street (Zoho field), Zip code → Billing postal code, Country or region → Billing country |
| Microsoft Dynamics AX Customer | Commercient Dynamics AX Customer object | 66 | Company data area, Account number, Partition → Commercient external key (custom field), Account number → Commercient account number, Name → Name, Account statement → Account statement (Zoho field), Agency location code → Agency location code (Zoho field) |
| Products | Products | 13 | Company data area, Item identifier → Commercient external key column, Name → Product name (Zoho field), Company data area, Item identifier → ERP product code, Item identifier → Item (Zoho field), Description → Description |
| Microsoft Dynamics AX Item | Commercient Dynamics AX Item object | 38 | Company data area, Item identifier → Commercient external key (custom field), Company data area, Item identifier → Name, Batch number group → Batch number group (Zoho field), Bill of materials manual receipt → Bill of materials manual receipt (Zoho field), Bill of materials unit → Bill of materials unit (Zoho field) |
| Microsoft Dynamics AX Shipping Address | Commercient Dynamics AX Shipping Address object | 17 | Record identifier → Commercient external key (custom field), Record identifier → Name, the linked Salesforce record → account (related record), the linked Salesforce record → customer (related record), Delivery role → Delivery address flag (Zoho field) |
| Contacts | Contacts | 15 | Contact person identifier,Company data area,Partition → Commercient external key column, Contact person identifier → Contact identifier (Zoho field), First name → First name, Last name → Last name, Middle name → Middle name (Zoho field) |
| Microsoft Dynamics AX Warehouse | Commercient Dynamics AX Warehouse object | 72 | Company data area,Warehouse code,Partition → Commercient external key (custom field), Name → Name, Activity type, Russian localization → Activity type, Russian localization (Zoho field), Allow labor standards → Allow labor standards (Zoho field), Allow marking reservation removal → Allow marking reservation removal (Zoho field) |
| Microsoft Dynamics AX Sales Quotation Header | Commercient Dynamics AX Sales Quotation Header object | 113 | Customer account → Account, Customer account → Dynamics AX customer (related record), Company data area, Quotation identifier, Partition → Commercient external key (custom field), Company data area, Quotation identifier, Partition → Name, Address reference record → Address reference (Zoho quotation field) |
| Microsoft Dynamics AX Sales Quotation Line | Commercient Dynamics AX Sales Quotation Line object | 66 | the linked quotation header → Dynamics AX sales quotation header (related record), the linked item → Dynamics AX item (related record), company code, Inventory transaction identifier, Partition → Commercient external key (custom field), company code, Inventory transaction identifier, Partition → Name, address reference record identifier → Address reference record (Zoho field) |
| Microsoft Dynamics AX Sales Order Header | Commercient Dynamics AX Sales Order Header object | 128 | the linked Salesforce record → account (related record), the linked Salesforce record → customer (related record), Company data area, Sales order number, Partition → Commercient external key (custom field), Company data area, Sales order number, Partition → Name, Address reference record → Address reference record (Zoho field) |
| Microsoft Dynamics AX Sales Order Detail | Commercient Dynamics AX Sales Order Detail object | 94 | Company data area,Inventory transaction identifier,Partition → Commercient external key (custom field), Company data area,Inventory transaction identifier,Partition → Name, Activity number → Activity number (Zoho field), Name → Name 1, Address reference record → Address reference record (Zoho field) |
| Microsoft Dynamics AX Invoice Header | Commercient Dynamics AX Invoice Header object | 81 | Company data area → account (related record), Invoice account → customer (related record), Invoice identifier,Invoice date → Commercient external key (custom field), Invoice identifier,Invoice date → Name, Back order → Back order (Zoho field) |
| Microsoft Dynamics AX Invoice Detail | Commercient Dynamics AX Invoice Detail object | 79 | Invoice identifier, Invoice date, ERP line number → Commercient external key (custom field), Invoice identifier, Invoice date, ERP line number → Name, Asset book → Asset book (Zoho field), Name → Name 1, Fixed asset → Fixed asset (Zoho field) |
| Dynamics AX Cust Pur History | Dynamics AX Customer Purchase History (custom object) | 30 | Average purchase → Average purchase (Zoho field), Customer account reference → Customer account reference, Invoicing, month 1 → Invoicing, month 1, Invoicing, month 2 → Invoicing, month 2, Invoicing, month 3 → Invoicing, month 3 |
| Product Price books | Zoho product price book relation | 6 | the linked Salesforce record, the linked Salesforce record → Commercient external key column, the linked Salesforce record → Product lookup (Zoho field), the linked Salesforce record → Price book lookup (Zoho field), Item identifier → Item (Zoho field), Amount → List price |
| Dynamics AX Dispatch Shipping | Dynamics AX Outbound Shipping (custom object) | 15 | the linked Salesforce record → Client, the linked Salesforce record → Dynamics AX customer (related record), Record identifier → Commercient external key column, Base sales order number → Name, Base sales order number → Sales order reference (Zoho field) |
| Dynamics AX Contact person | Commercient Dynamics AX Salesperson object | 9 | Contact person identifier, Company data area, Partition → Commercient external key (custom field), Created by → Created by, Name → Last name, Name → First name, Created date and time → Created date and time (Zoho field) |

## 6. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-zohocrm/erps/microsoft-dynamics-ax`.

## 7. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Microsoft Dynamics AX → Zoho CRM templates set up. dlake-crmpro-zohocrm is the
destination skill this page sits under: its own text is the authority for the Zoho CRM conventions
that hold across every ERP, and its ERP table lists this page alongside every sibling ERP page for
this destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-zohocrm/erps/microsoft-dynamics-ax`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
