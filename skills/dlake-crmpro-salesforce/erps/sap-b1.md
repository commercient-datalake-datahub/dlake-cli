---
name: dlake-crmpro-salesforce/erps/sap-b1
kind: erp-summary
description: >-
  Use it when standing up or reading a SAP Business One → Salesforce template set, when deciding
  which templates to import and activate, or when a run completes without pushing records and the
  answer is in the view or the configuration row. It extends dlake-crmpro, which covers operating
  CRMPro generally, and dlake-crmpro-salesforce, the destination skill this page is a child of,
  which carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — SAP Business One: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/sap-b1` (or `list_skills`) against the
Commercient admin plane. Existing customers who need access or help: contact
support@commercient.com. New customers: contact sales@commercient.com to become a customer and be
whitelisted.

dlake-crmpro is the parent skill and the authority for everything general: the CRMPro tools, process
configuration and field list, the sync history, how source data is selected, and what a run that
finds nothing does. Read it first; this page does not repeat it. dlake-crmpro-salesforce is the
destination skill this page is a child of, and the authority for the Salesforce conventions that
hold across every ERP: read it first, then come back here for what this source's own templates set.
This page grows as the catalogue does.

## 1. What the templates deliver

| Group | Business outcome | Objects | Source tables and views |
|---|---|---|---|
| **Get Users** | The templates push users to Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | users | — |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Contact, Commercient SAP Customer Managed Custom Object | business partners, business partner addresses, business partner contacts |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created and existing ones updated; none are deleted. | Commercient SAP Address Managed Custom Object | business partner addresses |
| **Product** | ERP item warehouse, item master data becomes Commercient Item Master Managed Custom Object, Commercient Item Warehouse Managed Custom Object, Product in Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Item Master Managed Custom Object, Commercient Item Warehouse Managed Custom Object, Product | items, item warehouse quantities |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created and existing ones updated; none are deleted. | Commercient SAP Invoice Managed Custom Object, Commercient SAP Invoice Detail Managed Custom Object | AR invoice documents, invoice lines |
| **Pricebook** | ERP item price data becomes Price book entry, Price book entry in Salesforce. New records are created and existing ones updated; none are deleted. | Price book entry | item prices |
| **Purchase Order** | The templates push Commercient Purchase Order Managed Custom Object to Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Purchase Order Managed Custom Object | purchase order headers |
| **Purchase Order Line** | The templates push Commercient Purchase Order Line Managed Custom Object to Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Purchase Order Line Managed Custom Object | purchase order lines, purchase order headers |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created and existing ones updated; none are deleted. | Commercient SAP Sales Order Managed Custom Object, Commercient SAP Sales Order Line Managed Custom Object, Commercient Sales Credit Header Managed Custom Object, Commercient Sales Credit Line Managed Custom Object, Commercient Sales Delivery Header Managed Custom Object, Commercient Sales Delivery Line Managed Custom Object | sales orders, sales order lines, sales credit headers, sales credit lines, delivery headers |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| GET USER | users | — | 0 |
| Account | Account | Commercient AR customer code | 1 |
| SAP Business One Customer | Commercient SAP Customer Managed Custom Object | Commercient external key (SAP package) | 2 |
| Account Reverse lookup | Account | Commercient AR customer code | 3 |
| SAP Business One Address | Commercient SAP Address Managed Custom Object | Commercient external key (SAP package) | 4 |
| SAP Business One Sales Order | Commercient SAP Sales Order Managed Custom Object | Commercient external key (SAP package) | 5 |
| SAP Business One Sales Order Line | Commercient SAP Sales Order Line Managed Custom Object | Commercient external key (SAP package) | 6 |
| Sync Contact | Contact | External key (custom field) | 7 |
| SAP Business One Invoice | Commercient SAP Invoice Managed Custom Object | Commercient external key (SAP package) | 7 |
| SAP Business One Invoice Line | Commercient SAP Invoice Detail Managed Custom Object | Commercient external key (SAP package) | 8 |
| Product | Product | Commercient external key (earlier package) | 9 |
| Price Book Entry Create | Price book entry | External key (custom field) | 10 |
| Price Book Entry Update | Price book entry | External key (custom field) | 11 |
| SAP Business One Purchase Order | Commercient Purchase Order Managed Custom Object | Commercient external key (SAP package) | 12 |
| SAP Business One Purchase Order Line | Commercient Purchase Order Line Managed Custom Object | Commercient external key (SAP package) | 13 |
| SAP Business One Item Master | Commercient Item Master Managed Custom Object | Commercient external key (SAP package) | 14 |
| SAP Business One Item Warehouse | Commercient Item Warehouse Managed Custom Object | Commercient external key (SAP package) | 15 |
| SAP Business One Sales Credit Header | Commercient Sales Credit Header Managed Custom Object | Commercient external key (SAP package) | 16 |
| SAP Business One Sales Credit Detail | Commercient Sales Credit Line Managed Custom Object | Commercient external key (SAP package) | 17 |
| SAP Business One Sales Delivery Header | Commercient Sales Delivery Header Managed Custom Object | Commercient external key (SAP package) | 18 |
| SAP Business One Sales Delivery Detail | Commercient Sales Delivery Line Managed Custom Object | Commercient external key (SAP package) | 19 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| account feed | insert + update | business partners, business partner addresses |
| customer feed | insert + update | business partners |
| account reverse lookup feed | insert + update | business partners |
| address feed | insert + update | business partner addresses |
| sales order feed | insert + update | sales orders |
| sales order line feed | insert + update | sales order lines, sales orders |
| contact feed | insert + update | business partner contacts |
| invoice feed | insert + update | AR invoice documents |
| invoice line feed | insert + update | invoice lines, AR invoice documents |
| product feed | insert + update | items |
| price book feed (new entries) | insert only | item prices |
| price book feed (changes) | insert + update | item prices |
| purchase order feed | insert + update | purchase order headers |
| purchase order line feed | insert + update | purchase order lines, purchase order headers |
| item master feed | insert + update | items |
| item warehouse feed | insert + update | item warehouse quantities |
| sales credit feed | insert + update | sales credit headers |
| sales credit line feed | insert + update | sales credit lines, sales credit headers |
| sales delivery feed | insert + update | delivery headers |
| sales delivery line feed | insert + update | delivery lines, delivery headers |

## 4. Order of work

The templates set run sequence from 0 to 19. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 0 — GET USER
- 1 — Account
- 2 — SAP Business One Customer
- 3 — Account Reverse lookup
- 4 — SAP Business One Address
- 5 — SAP Business One Sales Order
- 6 — SAP Business One Sales Order Line
- 7 — Sync Contact, SAP Business One Invoice
- 8 — SAP Business One Invoice Line
- 9 — Product
- 10 — Price Book Entry Create
- 11 — Price Book Entry Update
- 12 — SAP Business One Purchase Order
- 13 — SAP Business One Purchase Order Line
- 14 — SAP Business One Item Master
- 15 — SAP Business One Item Warehouse
- 16 — SAP Business One Sales Credit Header
- 17 — SAP Business One Sales Credit Detail
- 18 — SAP Business One Sales Delivery Header
- 19 — SAP Business One Sales Delivery Detail

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- account reverse lookup feed reads customer sync output
- item master feed reads Product
- item warehouse feed reads Product, item master sync output
- invoice line feed reads invoice sync output
- price book feed (new entries) reads Product
- purchase order line feed reads purchase order sync output
- sales order line feed reads sales order sync output
- sales credit line feed reads sales credit sync output
- sales delivery line feed reads sales delivery sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Account | Account | 14 | Business partner code → Commercient AR customer code, Business partner name → Name, Phone 1 → Phone, Business partner type → Type, Street → Billing street |
| SAP Business One Customer | Commercient SAP Customer Managed Custom Object | 11 | Business partner code → Commercient external key (SAP package), Business partner name → Name, Address → Billing street, City → Billing city, Country → Billing country |
| Account Reverse lookup | Account | 2 | Business partner code → Commercient AR customer code, the linked Salesforce record → Commercient customer (related record) |
| SAP Business One Address | Commercient SAP Address Managed Custom Object | 32 | Address,Business partner code,Address type → Commercient external key (SAP package), Address → Name, Street → Street, Block → Block, Zip code → Zip code |
| SAP Business One Sales Order | Commercient SAP Sales Order Managed Custom Object | 3 | returned document entry → Commercient external key (SAP package), Business partner code → Commercient account (related record), Business partner code → Commercient customer (related record) |
| SAP Business One Sales Order Line | Commercient SAP Sales Order Line Managed Custom Object | 3 | returned document entry, ERP line number → Commercient external key (SAP package), document number, ERP line number → Name, the linked Salesforce record → Commercient SAP Sales Order Managed Custom Object |
| Sync Contact | Contact | 7 | Given name → Given name, Family name → Family name, Fax → Fax, Phone → Phone, mobile phone property → mobile phone property |
| SAP Business One Invoice | Commercient SAP Invoice Managed Custom Object | 24 | returned document entry → Commercient external key (SAP package), Business partner code → Commercient account (related record), Business partner code → Commercient customer (related record), document number → Name, Document type code → Document type code |
| SAP Business One Invoice Line | Commercient SAP Invoice Detail Managed Custom Object | 3 | returned document entry, ERP line number → Commercient external key (SAP package), document number, ERP line number → Name, the linked Salesforce record → Commercient invoice (related record) |
| Product | Product | 6 | Item code → Commercient external key (earlier package), Item name → Name, Item code → Product code, Item name → Description, Item class → Family |
| Price Book Entry Create | Price book entry | 5 | Item code → External key (custom field), Price → Unit price, the linked Salesforce record → price book lookup, the linked Salesforce record → product lookup |
| Price Book Entry Update | Price book entry | 3 | Item code → External key (custom field), Price → Unit price |
| SAP Business One Purchase Order | Commercient Purchase Order Managed Custom Object | 4 | returned document entry → Commercient external key (SAP package), document number → Name, Business partner code → Commercient account (related record), Business partner code → Commercient SAP Customer Managed Custom Object |
| SAP Business One Purchase Order Line | Commercient Purchase Order Line Managed Custom Object | 3 | returned document entry,ERP line number → Commercient external key (SAP package), document number,ERP line number → Name, the linked Salesforce record → Commercient SAP purchase order (related record) |
| SAP Business One Item Master | Commercient Item Master Managed Custom Object | 3 | Item code → Commercient external key (SAP package), Item name → Name, the linked Salesforce record → Commercient product (related record) |
| SAP Business One Item Warehouse | Commercient Item Warehouse Managed Custom Object | 77 | Item code, Warehouse code → Commercient external key (SAP package), Item code, Warehouse code → Name, the linked Salesforce record → Product (custom field), the linked Salesforce record → Commercient SAP item master (related record), Item code → Item code |
| SAP Business One Sales Credit Header | Commercient Sales Credit Header Managed Custom Object | 3 | returned document entry → Commercient external key (SAP package), Business partner code → Commercient account (related record), Business partner code → Commercient SAP Customer Managed Custom Object |
| SAP Business One Sales Credit Detail | Commercient Sales Credit Line Managed Custom Object | 2 | returned document entry, ERP line number → Commercient external key (SAP package), the linked Salesforce record → Commercient SAP Business One sales credit header (related record) |
| SAP Business One Sales Delivery Header | Commercient Sales Delivery Header Managed Custom Object | 4 | returned document entry → Commercient external key (SAP package), Business partner code → Commercient account (related record), Business partner code → Commercient SAP Customer Managed Custom Object, returned document entry → Commercient SAP sales order (related record) |
| SAP Business One Sales Delivery Detail | Commercient Sales Delivery Line Managed Custom Object | 282 | returned document entry,ERP line number → Commercient external key (SAP package), document number,ERP line number → Name, the linked Salesforce record → Commercient SAP sales delivery header (related record), returned document entry → returned document entry, ERP line number → ERP line number |

## 6. Community templates

The catalogue carries 437 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 437
- Default operations: insert on 437, update on 437, delete on 437
- Marked as circular sync: 0
- Licence groups they span: 15
- Destination objects: Account, Product, Commercient SAP Customer Managed Custom Object, Commercient
  SAP Invoice Managed Custom Object, Commercient SAP Sales Order Managed Custom Object, Price book
  entry, Commercient SAP Address Managed Custom Object, Commercient SAP Invoice Detail Managed
  Custom Object, Commercient SAP Sales Order Line Managed Custom Object, Commercient Item Master
  Managed Custom Object, Commercient Item Warehouse Managed Custom Object, Contact, Commercient SAP
  Salesperson Managed Custom Object, Commercient Sales Credit Header Managed Custom Object, User,
  Commercient Purchase Order Managed Custom Object, Quote line item, Commercient Purchase Order Line
  Managed Custom Object, Opportunity, 20 more and 14 custom objects
- Object display names: Account, SAP Business One Invoice, SAP Business One Sales Order, SAP
  Business One Invoice Line, SAP Business One Sales Order Line, Price Book Entry Create, Product,
  SAP Business One Address, SAP Business One Customer, Account Reverse lookup, SAP Business One Item
  Master, SAP Business One Item Warehouse, 89 more and 16 further templates
- Template groups: Account, Product, Invoice, Sales order, Customer Multi Ship Addresses,
  Opportunity, CRM Quote and Line, Purchase Order, CRM Opportunity and Line, CRM Ownership

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/sap-b1`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped SAP Business One → Salesforce templates set up. dlake-crmpro-salesforce is the
destination skill this page sits under: its own text is the authority for the Salesforce conventions
that hold across every ERP, and its ERP table lists this page alongside every sibling ERP page for
this destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/sap-b1`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
