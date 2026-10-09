---
name: dlake-crmpro-zohocrm/erps/sage-x3
kind: erp-summary
description: >-
  Use it when standing up or reading a Sage X3 → Zoho CRM template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-zohocrm, the destination skill this page is a child of, which carries
  the Zoho CRM conventions that hold across every ERP.
---
# CRMPro → Zoho CRM — Sage X3: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-zohocrm/erps/sage-x3` (or `list_skills`) against the Commercient
admin plane. Existing customers who need access or help: contact support@commercient.com. New
customers: contact sales@commercient.com to become a customer and be whitelisted.

dlake-crmpro is the parent skill and the authority for everything general: the CRMPro tools, process
configuration and field list, the sync history, how source data is selected, and what a run that
finds nothing does. Read it first; this page does not repeat it. dlake-crmpro-zohocrm is the
destination skill this page is a child of, and the authority for the Zoho CRM conventions that hold
across every ERP: read it first, then come back here for what this source's own templates set. This
page grows as the catalogue does.

## 1. What the templates deliver

| Group | Business outcome | Objects | Source tables and views |
|---|---|---|---|
| **Sage X3 Payment Term** | The templates push Commercient Sage X3 Payment Term object to Zoho CRM. | Commercient Sage X3 Payment Term object | payment terms |
| **Sage X3 Salesperson** | The templates push Commercient Sage X3 Salesperson object to Zoho CRM. | Commercient Sage X3 Salesperson object | sales reps, business partner addresses |
| **Parent Account** | The templates push Accounts to Zoho CRM. | Accounts | customers, customer delivery addresses, business partner addresses |
| **Child Account** | The templates push Accounts to Zoho CRM. | Accounts | customer delivery addresses, customers, payment terms, business partner addresses |
| **Sage X3 Customer** | The templates push Commercient Sage X3 Customer object to Zoho CRM. | Commercient Sage X3 Customer object | customers, customer delivery addresses, payment terms |
| **Sage X3 Address** | The templates push Commercient Sage X3 Address object to Zoho CRM. | Commercient Sage X3 Address object | business partner addresses, customers, customer delivery addresses, sales reps |
| **Sage X3 Item Master** | The templates push Commercient Sage X3 Item Master object to Zoho CRM. | Commercient Sage X3 Item Master object | item master records, item sales settings |
| **Sage X3 Sales Order Header** | The templates push Commercient Sage X3 Sales Order Header object to Zoho CRM. | Commercient Sage X3 Sales Order Header object | sales order headers, customers, sales reps |
| **Sage X3 Sales Order Detail** | The templates push Commercient Sage X3 Sales Order Detail object to Zoho CRM. | Commercient Sage X3 Sales Order Detail object | sales order line prices, sales order line quantities, sales order headers, customers, sales reps |
| **Sage X3 Invoice Header** | The templates push Commercient Sage X3 Invoice Header object to Zoho CRM. | Commercient Sage X3 Invoice Header object | sales invoices, customers, sales reps |
| **Sage X3 Invoice Detail** | The templates push Commercient Sage X3 Invoice Detail object to Zoho CRM. | Commercient Sage X3 Invoice Detail object | sales invoice lines, sales invoices, customers, sales reps |
| **Sage X3 Warehouse** | The templates push Commercient Sage X3 Warehouse object to Zoho CRM. | Commercient Sage X3 Warehouse object | warehouses |
| **Sage X3 Item Warehouse** | The templates push Commercient Sage X3 Item Warehouse object to Zoho CRM. | Commercient Sage X3 Item Warehouse object | item warehouse quantities, warehouses |
| **Sage X3 Payment Header** | The templates push Commercient Sage X3 Payment Header object to Zoho CRM. | Commercient Sage X3 Payment Header object | payment headers, customers, sales reps |
| **Sage X3 Payment Detail** | The templates push Commercient Sage X3 Payment Detail object to Zoho CRM. | Commercient Sage X3 Payment Detail object | payment lines, payment headers, sales invoices, customers, sales reps |
| **Products** | The templates push Products to Zoho CRM. | Products | item master records, price lists |
| **Sales Orders** | The templates push Sales orders to Zoho CRM. | Sales orders | sales order line prices, sales order line quantities, item master records, sales order headers, customers, business partner addresses |
| **Invoices** | The templates push Invoices to Zoho CRM. | Invoices | sales invoice lines, item master records, sales invoices, customers, business partner addresses, x |
| **CRM Ownership** | The templates push users to Zoho CRM. | users | — |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Sage X3 Payment Term | Commercient Sage X3 Payment Term object | Commercient external key column | 1 |
| Users | users | Commercient external key column | 1 |
| Sage X3 Salesperson | Commercient Sage X3 Salesperson object | Commercient external key column | 2 |
| Parent Account | Accounts | Commercient AR customer code (Zoho field) | 3 |
| Child Account | Accounts | Commercient AR customer code (Zoho field) | 5 |
| Sage X3 Customer | Commercient Sage X3 Customer object | Commercient external key column | 6 |
| Sage X3 Address | Commercient Sage X3 Address object | Commercient external key column | 7 |
| Products | Products | Commercient external key column | 7 |
| Sage X3 Item Master | Commercient Sage X3 Item Master object | Commercient external key column | 8 |
| Sage X3 Sales Order Header | Commercient Sage X3 Sales Order Header object | Commercient external key column | 9 |
| Sage X3 Sales Order Detail | Commercient Sage X3 Sales Order Detail object | Commercient external key column | 10 |
| Sage X3 Invoice Header | Commercient Sage X3 Invoice Header object | Commercient external key column | 11 |
| Sage X3 Invoice Detail | Commercient Sage X3 Invoice Detail object | Commercient external key column | 12 |
| Sage X3 Warehouse | Commercient Sage X3 Warehouse object | Commercient external key column | 13 |
| Sage X3 Item Warehouse | Commercient Sage X3 Item Warehouse object | Commercient external key column | 14 |
| Sage X3 Payment Header | Commercient Sage X3 Payment Header object | Commercient external key column | 15 |
| Sage X3 Payment Detail | Commercient Sage X3 Payment Detail object | Commercient external key column | 16 |
| Sales Orders | Sales orders | Commercient external key column | 17 |
| Invoices | Invoices | Commercient external key column | 18 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| payment term feed | insert + update | payment terms |
| sales rep feed | insert + update | sales reps, business partner addresses |
| account feed | insert + update | customers, customer delivery addresses, business partner addresses |
| child account feed | insert + update | customer delivery addresses, customers, payment terms, business partner addresses |
| customer feed | insert + update | customers, customer delivery addresses, payment terms |
| address feed | insert + update | business partner addresses, customers, customer delivery addresses, sales reps |
| product feed | insert only | item master records, price lists |
| item master feed | insert + update | item master records, item sales settings |
| sales order feed | insert + update | sales order headers, customers, sales reps |
| sales order line feed | insert + update | sales order line prices, sales order line quantities, sales order headers, customers, sales reps |
| invoice feed | insert + update | sales invoices, customers, sales reps |
| invoice line feed | insert + update | sales invoice lines, sales invoices, customers, sales reps |
| warehouse feed | insert + update | warehouses |
| item warehouse feed | insert + update | item warehouse quantities, warehouses |
| payment feed | insert + update | payment headers, customers, sales reps |
| payment line feed | insert + update | payment lines, payment headers, sales invoices, customers, sales reps |
| sales order feed (standard module) | insert only | sales order line prices, sales order line quantities, item master records, sales order headers, customers |
| invoice feed (standard module) | insert only | sales invoice lines, item master records, sales invoices, customers, business partner addresses |

## 4. Order of work

The templates set run sequence from 1 to 18. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 1 — Sage X3 Payment Term, Users
- 2 — Sage X3 Salesperson
- 3 — Parent Account
- 5 — Child Account
- 6 — Sage X3 Customer
- 7 — Sage X3 Address, Products
- 8 — Sage X3 Item Master
- 9 — Sage X3 Sales Order Header
- 10 — Sage X3 Sales Order Detail
- 11 — Sage X3 Invoice Header
- 12 — Sage X3 Invoice Detail
- 13 — Sage X3 Warehouse
- 14 — Sage X3 Item Warehouse
- 15 — Sage X3 Payment Header
- 16 — Sage X3 Payment Detail
- 17 — Sales Orders
- 18 — Invoices

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- payment term feed reads payment term sync output; no template in this set writes payment term sync
  output
- sales rep feed reads sales rep sync output; no template in this set writes sales rep sync output
- account feed reads sales rep sync output, Users: account sync output; no template in this set
  writes sales rep sync output, Users: account sync output
- child account feed reads payment term sync output, sales rep sync output, user sync output
  (generic name), account sync output; no template in this set writes payment term sync output,
  sales rep sync output, user sync output (generic name), account sync output
- customer feed reads payment term sync output, account sync output, sales rep sync output, user
  sync output (generic name), customer sync output; no template in this set writes payment term sync
  output, account sync output, sales rep sync output, user sync output (generic name)
- address feed reads account sync output, customer sync output, sales rep sync output, address sync
  output; no template in this set writes account sync output, customer sync output, sales rep sync
  output, address sync output
- item master feed reads product sync output, item master sync output; no template in this set
  writes product sync output, item master sync output
- sales order feed reads account sync output, customer sync output, sales order sync output; no
  template in this set writes account sync output, customer sync output, sales order sync output
- sales order line feed reads sales order sync output, sales order line sync output; no template in
  this set writes sales order sync output, sales order line sync output
- invoice feed reads account sync output, customer sync output, invoice sync output; no template in
  this set writes account sync output, customer sync output, invoice sync output
- invoice line feed reads invoice sync output, invoice line sync output; no template in this set
  writes invoice sync output, invoice line sync output
- warehouse feed reads warehouse sync output; no template in this set writes warehouse sync output
- item warehouse feed reads warehouse sync output, item master sync output, item warehouse sync
  output; no template in this set writes warehouse sync output, item master sync output, item
  warehouse sync output
- payment feed reads account sync output, customer sync output, payment sync output; no template in
  this set writes account sync output, customer sync output, payment sync output
- payment line feed reads payment sync output, payment line sync output; no template in this set
  writes payment sync output, payment line sync output
- product feed reads product sync output; no template in this set writes product sync output
- sales order feed (standard module) reads product sync output, account sync output, sales order
  sync output (standard module); no template in this set writes product sync output, account sync
  output, sales order sync output (standard module)
- invoice feed (standard module) reads product sync output, account sync output, sales order sync
  output (standard module), invoice sync output (standard module); no template in this set writes
  product sync output, account sync output, sales order sync output (standard module), invoice sync
  output (standard module)

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Zoho CRM pairs |
|---|---|---|---|
| Sage X3 Payment Term | Commercient Sage X3 Payment Term object | 15 | Payment term code, Payment term line → Commercient external key (custom field), Payment term code, Payment term line → Name, Minimum due date amount → Commercient minimum due date amount, Due date percentage → Due date percentage (custom field), Month end indicator → Month end (custom field) |
| Users | users | 1 | Commercient external key column → Commercient external key column |
| Sage X3 Salesperson | Commercient Sage X3 Salesperson object | 13 | Sales rep code → Commercient external key (custom field), Associated user code → User code (custom field), Address code → Default address (custom field), Company code → Company, Currency → Sales rep currency (custom field) |
| Parent Account | Accounts | 14 | Row identifier → Commercient AR customer code (Sage X3 package field), Sales representative → Commercient salesperson (related record), Business partner customer name → Account name, Business partner address line 1 → Billing street (Zoho field), Business partner city → Billing city (Zoho field) |
| Child Account | Accounts | 23 | Business partner customer number, Address code → Commercient AR customer code (Zoho field), Business partner customer name, Address code → Account name, Business partner address line 1, Business partner address line 2 → Shipping street, Business partner address line 2 → Shipping street 2, Address line 3 → Shipping street 3 |
| Sage X3 Customer | Commercient Sage X3 Customer object | 51 | Business partner customer number, Address code → Commercient external key (custom field), Business partner customer name, Address code → Name, Business partner customer name, Address code → Sage X3 customer name (custom field), the linked account → account (related record), the linked sales rep → salesperson (related record) |
| Sage X3 Address | Commercient Sage X3 Address object | 28 | Address entity type, Address business partner number, Address code → Commercient external key (custom field), Address entity type, Address business partner number, Address code → Sage X3 address name (custom field), Address business partner number → account (related record), Address business partner number → customer (related record), Sales representative → sales rep (related record) |
| Products | Products | 13 | Item reference → Commercient external key column, Item description 1, Item description 2 → Products name (custom field), Item description 1, Item description 2 → Product name (Zoho field), Item reference → ERP product code, Item description 1 → Description |
| Sage X3 Item Master | Commercient Sage X3 Item Master object | 102 | Item reference → Commercient external key (custom field), Item reference → Name, Item description 1,Item description 2 → Sage X3 item master name (custom field), Item status → Product status (custom field), Stock management mode → Management mode (custom field) |
| Sage X3 Sales Order Header | Commercient Sage X3 Sales Order Header object | 71 | Sales order header number → Commercient external key (custom field), Sales order header number → Name, Sales order header number → Sage X3 sales order header name (custom field), Bill to customer number → account (related record), Bill to customer number → customer (related record) |
| Sage X3 Sales Order Detail | Commercient Sage X3 Sales Order Detail object | 61 | Sales order header number,Sales order line number,Sales order line sequence number → Commercient external key (custom field), Sales order header number,Sales order line number,Sales order line sequence number → Name, Sales order header number,Sales order line number,Sales order line sequence number → Sage X3 sales order detail name (custom field), the linked Salesforce record → sales order header (related record), Sold to customer number → Customer |
| Sage X3 Invoice Header | Commercient Sage X3 Invoice Header object | 40 | Document number → Commercient external key (custom field), Document number → external key column, Document number → Name, Document number → Sage X3 invoice header name (custom field), Business partner code → account (related record) |
| Sage X3 Invoice Detail | Commercient Sage X3 Invoice Detail object | 49 | Document number, Invoice line number → Commercient external key (custom field), Document number, Invoice line number → Name, Document number → Document number (custom field), Invoice line number → Invoice line (custom field), Sales order header number → Sales order number (custom field) |
| Sage X3 Warehouse | Commercient Sage X3 Warehouse object | 8 | Row identifier → Commercient external key (custom field), Warehouse → Warehouse code (custom field), Warehouse name → Name, Stock site → Stock factory (custom field), Export number → Export number (custom field) |
| Sage X3 Item Warehouse | Commercient Sage X3 Item Warehouse object | 65 | Row identifier → Commercient external key (custom field), Item reference, Warehouse → Name, Creation date and time → Change date (custom field), Created by user → Created user (custom field), Cycle count code → Count code (custom field) |
| Sage X3 Payment Header | Commercient Sage X3 Payment Header object | 42 | Document number → Commercient external key (custom field), Document number → Name, Business partner code → account (related record), Business partner code → customer (related record), Document number → Payment number (custom field) |
| Sage X3 Payment Detail | Commercient Sage X3 Payment Detail object | 28 | Document number,Line number → Commercient external key (custom field), Document number,Line number → Name, the linked Salesforce record → payment header (related record), Document number → Payment header identifier (custom field), Source document number → Invoice header identifier (custom field) |
| Sales Orders | Sales orders | 25 | Sales order header number → external key column, Sales order header number → Commercient external key (custom field), the linked Salesforce record → Account name, Sales order header number → Name, Sales order header number → Sales order number (Zoho field) |
| Invoices | Invoices | 20 | Document number → Commercient external key (custom field), the linked Salesforce record → Account name, Document number → Name, Document number → Invoice number (Zoho field), Creation date → Invoice date |

## 6. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-zohocrm/erps/sage-x3`.

## 7. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Sage X3 → Zoho CRM templates set up. dlake-crmpro-zohocrm is the destination skill this
page sits under: its own text is the authority for the Zoho CRM conventions that hold across every
ERP, and its ERP table lists this page alongside every sibling ERP page for this destination. For
the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-zohocrm/erps/sage-x3`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
