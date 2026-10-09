---
name: dlake-crmpro-zohocrm/erps/microsoft-dynamics-gp-2017
kind: erp-summary
description: >-
  Use it when standing up or reading a Microsoft Dynamics GP 2017 → Zoho CRM template set, when
  deciding which templates to import and activate, or when a run completes without pushing records
  and the answer is in the view or the configuration row. It extends dlake-crmpro, which covers
  operating CRMPro generally, and dlake-crmpro-zohocrm, the destination skill this page is a child
  of, which carries the Zoho CRM conventions that hold across every ERP.
---
# CRMPro → Zoho CRM — Microsoft Dynamics GP 2017: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-zohocrm/erps/microsoft-dynamics-gp-2017` (or `list_skills`)
against the Commercient admin plane. Existing customers who need access or help: contact
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
| **Parent Account** | The templates push Accounts to Zoho CRM. | Accounts | customers, customer addresses, additional customer feed |
| **Child Account** | The templates push Accounts to Zoho CRM. | Accounts | customer addresses, customers, additional customer feed |
| **Dynamics GP Customer** | The templates push Commercient Dynamics GP Customer object to Zoho CRM. | Commercient Dynamics GP Customer object | customer addresses, customers |
| **Dynamics GP Customer address** | The templates push Commercient Dynamics GP Customer Address object to Zoho CRM. | Commercient Dynamics GP Customer Address object | customer addresses |
| **Product** | The templates push Products to Zoho CRM. | Products | items, internet addresses |
| **Dynamics GP Warehouse** | The templates push Commercient Dynamics GP Warehouse object to Zoho CRM. | Commercient Dynamics GP Warehouse object | warehouse sites |
| **Dynamics GP Item master** | The templates push Commercient Dynamics GP Item Master object to Zoho CRM. | Commercient Dynamics GP Item Master object | items, internet addresses |
| **Dynamics GP Item warehouse** | The templates push Commercient Dynamics GP Item Warehouse object to Zoho CRM. | Commercient Dynamics GP Item Warehouse object | item quantities |
| **Dynamics GP Sales order header** | The templates push Commercient Dynamics GP Sales Order Header object to Zoho CRM. | Commercient Dynamics GP Sales Order Header object | sales transaction headers |
| **Dynamics GP Sales order detail** | The templates push Commercient Dynamics GP Sales Order Detail object to Zoho CRM. | Commercient Dynamics GP Sales Order Detail object | sales transaction lines |
| **Dynamics GP Invoice header** | The templates push Commercient Dynamics GP Invoice Header object to Zoho CRM. | Commercient Dynamics GP Invoice Header object | sales transaction history headers |
| **Dynamics GP Invoice detail** | The templates push Commercient Dynamics GP Invoice Detail object to Zoho CRM. | Commercient Dynamics GP Invoice Detail object | sales transaction history lines |
| **Standard Price Book Create** | The templates push Zoho price books to Zoho CRM. | Zoho price books | price sheets |
| **Product Pricebook** | The templates push Zoho product price book relation to Zoho CRM. | Zoho product price book relation | price sheet lines, price sheets |
| **Sales orders** | The templates push Sales orders to Zoho CRM. | Sales orders | sales transaction lines, sales transaction headers, customers, x |
| **Invoices** | The templates push Invoices to Zoho CRM. | Invoices | sales transaction history lines, sales transaction history headers, customers, x |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Parent Account | Accounts | Commercient AR customer code (Zoho field) | 1 |
| Child Account | Accounts | Commercient AR customer code (Zoho field) | 2 |
| Dynamics GP Customer | Commercient Dynamics GP Customer object | Commercient external key column | 3 |
| Dynamics GP Customer address | Commercient Dynamics GP Customer Address object | Commercient external key column | 4 |
| Product | Products | Commercient external key column | 5 |
| Dynamics GP Warehouse | Commercient Dynamics GP Warehouse object | Commercient external key column | 6 |
| Dynamics GP Item master | Commercient Dynamics GP Item Master object | Commercient external key column | 7 |
| Dynamics GP Item warehouse | Commercient Dynamics GP Item Warehouse object | Commercient external key column | 8 |
| Dynamics GP Sales order header | Commercient Dynamics GP Sales Order Header object | Commercient external key column | 9 |
| Dynamics GP Sales order detail | Commercient Dynamics GP Sales Order Detail object | Commercient external key column | 10 |
| Dynamics GP Invoice header | Commercient Dynamics GP Invoice Header object | Commercient external key column | 11 |
| Dynamics GP Invoice detail | Commercient Dynamics GP Invoice Detail object | Commercient external key column | 12 |
| Standard Price Book Create | Zoho price books | Commercient external key column | 13 |
| Product Pricebook | Zoho product price book relation | Commercient external key column | 14 |
| Sales orders | Sales orders | Commercient external key column | 15 |
| Invoices | Invoices | Commercient external key column | 16 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| parent account feed | insert + update | customers, customer addresses, additional customer feed |
| child account feed | insert + update | customer addresses, customers, additional customer feed |
| customer feed | insert only | customer addresses, customers |
| customer address feed | insert only | customer addresses |
| product feed | insert + update | items, internet addresses |
| warehouse feed | insert only | warehouse sites |
| item master feed | insert only | items, internet addresses |
| item warehouse feed | insert + update | item quantities |
| sales order header feed | insert only | sales transaction headers |
| sales order detail feed | insert only | sales transaction lines |
| invoice header feed | insert only | sales transaction history headers |
| invoice detail feed | insert only | sales transaction history lines |
| standard price book feed | insert + update | price sheets |
| product price list feed | insert + update | price sheet lines, price sheets |
| sales order feed | insert only | sales transaction lines, sales transaction headers, customers, x |
| invoice feed | insert only | sales transaction history lines, sales transaction history headers, customers, x |

## 4. Order of work

The templates set run sequence from 1 to 16. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 1 — Parent Account
- 2 — Child Account
- 3 — Dynamics GP Customer
- 4 — Dynamics GP Customer address
- 5 — Product
- 6 — Dynamics GP Warehouse
- 7 — Dynamics GP Item master
- 8 — Dynamics GP Item warehouse
- 9 — Dynamics GP Sales order header
- 10 — Dynamics GP Sales order detail
- 11 — Dynamics GP Invoice header
- 12 — Dynamics GP Invoice detail
- 13 — Standard Price Book Create
- 14 — Product Pricebook
- 15 — Sales orders
- 16 — Invoices

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- parent account feed reads account sync output; no template in this set writes account sync output
- child account feed reads account sync output; no template in this set writes account sync output
- customer feed reads account sync output, customer sync output; no template in this set writes
  account sync output, customer sync output
- customer address feed reads customer sync output, account sync output, customer address sync
  output; no template in this set writes customer sync output, account sync output, customer address
  sync output
- product feed reads product sync output; no template in this set writes product sync output
- warehouse feed reads warehouse sync output; no template in this set writes warehouse sync output
- item master feed reads product sync output, warehouse sync output, item master sync output; no
  template in this set writes product sync output, warehouse sync output, item master sync output
- item warehouse feed reads product sync output, item master sync output, warehouse sync output,
  item warehouse sync output; no template in this set writes product sync output, item master sync
  output, warehouse sync output, item warehouse sync output
- sales order header feed reads account sync output, customer sync output, sales order header sync
  output; no template in this set writes account sync output, customer sync output, sales order
  header sync output
- sales order detail feed reads sales order header sync output, sales order detail sync output; no
  template in this set writes sales order header sync output, sales order detail sync output
- invoice header feed reads account sync output, customer sync output, invoice header sync output;
  no template in this set writes account sync output, customer sync output, invoice header sync
  output
- invoice detail feed reads invoice header sync output, invoice detail sync output; no template in
  this set writes invoice header sync output, invoice detail sync output
- standard price book feed reads standard price book sync output; no template in this set writes
  standard price book sync output
- product price list feed reads product sync output, standard price book sync output, product price
  list sync output; no template in this set writes product sync output, standard price book sync
  output, product price list sync output
- sales order feed reads product sync output, account sync output, sales order sync output; no
  template in this set writes product sync output, account sync output, sales order sync output
- invoice feed reads product sync output, account sync output, invoice sync output; no template in
  this set writes product sync output, account sync output, invoice sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Zoho CRM pairs |
|---|---|---|---|
| Parent Account | Accounts | 17 | returned customer number → Commercient AR customer code (Zoho field), Customer name → Account name, Address 1, Address 2, Address 3 → Billing street (Zoho field), City → Billing city (Zoho field), State → Billing state |
| Child Account | Accounts | 17 | returned customer number, Address code → Commercient AR customer code (Zoho field), Customer name → Account name, Address 1, Address 2, Address 3 → Billing street (Zoho field), City → Billing city (Zoho field), State → Billing state |
| Dynamics GP Customer | Commercient Dynamics GP Customer object | 105 | returned customer number, Address code → Commercient external key (custom field), Customer name, returned customer number, Address code → Name, Address 1 → Address 1, Address 2 → Address 2, Address 3 → Address 3 |
| Dynamics GP Customer address | Commercient Dynamics GP Customer Address object | 35 | returned customer number, Address code → Commercient external key (custom field), returned customer number, Address code → Name, Address 1 → Address 1, Address 2 → Address 2, Address 3 → Address 3 |
| Product | Products | 5 | Item number → Commercient external key (custom field), Internet information field 7, Item number → Product name (Zoho field), Item number → ERP product code, Item description → Description, Inactive → Active |
| Dynamics GP Warehouse | Commercient Dynamics GP Warehouse object | 35 | Location code → Commercient external key (custom field), Location description → Name, Address 1 → Address 1, Address 2 → Address 2, Address 3 → Address 3 |
| Dynamics GP Item master | Commercient Dynamics GP Item Master object | 91 | Item number → Commercient external key (custom field), Inventory classification code → ABC classification code (custom field), Alternate item 1 → Alternate item 1 (custom field), Alternate item 2 → Alternate item 2 (custom field), Allow back order → Allow back order (custom field) |
| Dynamics GP Item warehouse | Commercient Dynamics GP Item Warehouse object | 85 | Item number,Location code,Record type → Commercient external key (custom field), Item number,Location code,Record type → Name, Item number → Products, Item number → item master (related record), Location code → Warehouse |
| Dynamics GP Sales order header | Commercient Dynamics GP Sales Order Header object | 195 | Sales document type, Sales document number → Commercient external key column, Sales document type, Sales document number → Name, Account amount → Account amount (Zoho field), Actual ship date → Actual ship date (Zoho field), Address 1 → Address 1 |
| Dynamics GP Sales order detail | Commercient Dynamics GP Sales Order Detail object | 134 | external key column → Commercient external key (custom field), Name → Name, Actual ship date → Actual ship date (custom field), Address 1 → Address 1, Address 2 → Address 2 |
| Dynamics GP Invoice header | Commercient Dynamics GP Invoice Header object | 188 | Sales document type, Sales document number → external key column, Sales document type, Sales document number → Commercient external key (custom field), Sales document type, Sales document number → Name, Account amount → Account amount (Zoho field), Actual ship date → Actual ship date (Zoho field) |
| Dynamics GP Invoice detail | Commercient Dynamics GP Invoice Detail object | 125 | external key column → Commercient external key (custom field), Name → Name, Actual ship date → Actual ship date (custom field), Address 1 → Address 1 (custom field), Address 2 → Address 2 (custom field) |
| Standard Price Book Create | Zoho price books | 5 | Price sheet → Price book name (Zoho field), Price sheet → Commercient external key column, Start date → Start date (Zoho field), End date → End date |
| Product Pricebook | Zoho product price book relation | 4 | Price sheet, Item number → Commercient external key column, Price sheet item value → List price, the linked Salesforce record → Product lookup (Zoho field), the linked Salesforce record → Price book lookup (Zoho field) |
| Sales orders | Sales orders | 18 | Sales document number → Commercient external key (custom field), returned customer number → Account name, Sales document number → Name, Sales document number → Sales order number (Zoho field), Order date → ERP created date (custom field) |
| Invoices | Invoices | 19 | Sales document number → Commercient external key (custom field), returned customer number → Account name, Sales document number → Name, Sales document number → Invoice number (Zoho field), Created date → Invoice date |

## 6. Community templates

The catalogue carries 16 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 16
- Default operations: insert on 16, update on 16, delete on 16
- Marked as circular sync: 0
- Licence groups they span: 3
- Destination objects: Accounts, Commercient Dynamics GP Customer object, Commercient Dynamics GP
  Customer Address object, Commercient Dynamics GP Invoice Detail object, Commercient Dynamics GP
  Invoice Header object, Commercient Dynamics GP Item Master object, Commercient Dynamics GP Item
  Warehouse object, Commercient Dynamics GP Sales Order Detail object, Commercient Dynamics GP Sales
  Order Header object, Commercient Dynamics GP Warehouse object, Invoices, Zoho price books,
  Products, Sales orders and a custom object
- Object display names: Child Account, Invoices, Dynamics GP Customer, Dynamics GP Customer address,
  Dynamics GP Invoice detail, Dynamics GP Invoice header, Dynamics GP Item master, Dynamics GP Item
  warehouse, Dynamics GP Sales order detail, Dynamics GP Sales order header, Dynamics GP Warehouse,
  Parent Account and 4 more
- Template groups: Account, CRM Order and Line

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-zohocrm/erps/microsoft-dynamics-gp-2017`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Microsoft Dynamics GP 2017 → Zoho CRM templates set up. dlake-crmpro-zohocrm is the
destination skill this page sits under: its own text is the authority for the Zoho CRM conventions
that hold across every ERP, and its ERP table lists this page alongside every sibling ERP page for
this destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-zohocrm/erps/microsoft-dynamics-gp-2017`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
