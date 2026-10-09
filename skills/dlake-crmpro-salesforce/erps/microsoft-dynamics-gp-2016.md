---
name: dlake-crmpro-salesforce/erps/microsoft-dynamics-gp-2016
kind: erp-summary
description: >-
  Use it when standing up or reading a Microsoft Dynamics GP 2016 → Salesforce template set, when
  deciding which templates to import and activate, or when a run completes without pushing records
  and the answer is in the view or the configuration row. It extends dlake-crmpro, which covers
  operating CRMPro generally, and dlake-crmpro-salesforce, the destination skill this page is a
  child of, which carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Microsoft Dynamics GP 2016: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/microsoft-dynamics-gp-2016` (or `list_skills`)
against the Commercient admin plane. Existing customers who need access or help: contact
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
| **Sale User Define & Track No Work History** | ERP sales user defined work history, sales tracking number work history data becomes Commercient Sales User Defined Work History Managed Custom Object in Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Sales User Defined Work History Managed Custom Object | sales user defined fields, sales tracking numbers |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created and existing ones updated; none are deleted. | Account, Commercient Customer Master Managed Custom Object, Commercient Customer Master Address Managed Custom Object, Commercient Customer Master Summary Managed Custom Object, Commercient Customer Period Summary Managed Custom Object | customer code identifiers, customers, customer addresses, customer summaries, customer period summaries |
| **Product** | ERP item master, item quantity data becomes Commercient Item Master Managed Custom Object, Commercient Item Quantity Master Managed Custom Object, Product in Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Item Master Managed Custom Object, Commercient Item Quantity Master Managed Custom Object, Product | items, item quantities |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created and existing ones updated; none are deleted. | Commercient Invoicing Transaction Work Managed Custom Object, Commercient Invoicing Transaction Amounts Work Managed Custom Object, Commercient Invoicing Transaction History Managed Custom Object, Commercient Invoicing Transaction Amounts History Managed Custom Object, Commercient Invoicing Serial and Lot Number Work Managed Custom Object, Commercient Invoicing Line Comments Managed Custom Object, Commercient Invoicing Payments Work Managed Custom Object | invoicing work transactions, invoicing work transaction amounts, invoicing history transactions, invoicing history transaction amounts, invoicing work serial and lot numbers, invoicing work line comments |
| **Pricebook** | ERP item master, item price list data becomes Price book entry in Salesforce. New records are created and existing ones updated; none are deleted. | Price book entry | items, item price lists |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created and existing ones updated; none are deleted. | Commercient Sales Transaction Work Managed Custom Object, Commercient Sales Transaction Amounts Work Managed Custom Object, Commercient Sales Transaction History Managed Custom Object, Commercient Sales Transaction Amounts History Managed Custom Object | sales transaction headers, sales transaction lines, sales transaction history headers, sales transaction history lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Account | Account | Commercient AR customer code | 1 |
| Customer | Commercient Customer Master Managed Custom Object | Commercient customer number | 2 |
| Customer Master Address | Commercient Customer Master Address Managed Custom Object | Commercient external key (Dynamics NAV package) | 3 |
| Customer Master Summary | Commercient Customer Master Summary Managed Custom Object | Commercient customer number | 4 |
| Customer Period Summary | Commercient Customer Period Summary Managed Custom Object | Commercient external key (Dynamics NAV package) | 5 |
| Sales Transaction Works | Commercient Sales Transaction Work Managed Custom Object | Commercient external key (Dynamics NAV package) | 6 |
| Sales Transaction Amounts Work | Commercient Sales Transaction Amounts Work Managed Custom Object | Commercient external key (Dynamics NAV package) | 7 |
| Sales Transaction History | Commercient Sales Transaction History Managed Custom Object | Commercient external key (Dynamics NAV package) | 8 |
| Sales Transaction Amounts History | Commercient Sales Transaction Amounts History Managed Custom Object | Commercient external key (Dynamics NAV package) | 9 |
| Sale User Define & Track No Work History | Commercient Sales User Defined Work History Managed Custom Object | Commercient external key (Dynamics NAV package) | 10 |
| Invoicing Transaction Work | Commercient Invoicing Transaction Work Managed Custom Object | Commercient external key (Dynamics NAV package) | 11 |
| Invoicing Transaction Amounts Work | Commercient Invoicing Transaction Amounts Work Managed Custom Object | Commercient external key (Dynamics NAV package) | 12 |
| Invoicing Transaction History | Commercient Invoicing Transaction History Managed Custom Object | Commercient external key (Dynamics NAV package) | 13 |
| Invoicing Transaction Amounts History | Commercient Invoicing Transaction Amounts History Managed Custom Object | Commercient external key (Dynamics NAV package) | 14 |
| Invoicing Serial and Lot Number Work | Commercient Invoicing Serial and Lot Number Work Managed Custom Object | Commercient external key (Dynamics NAV package) | 15 |
| Invoicing Line Comments | Commercient Invoicing Line Comments Managed Custom Object | Commercient external key (Dynamics NAV package) | 16 |
| Invoicing Payments Work | Commercient Invoicing Payments Work Managed Custom Object | Commercient external key (Dynamics NAV package) | 17 |
| Product | Product | Commercient external key (earlier package) | 18 |
| Item Master | Commercient Item Master Managed Custom Object | Commercient external key (Dynamics NAV package) | 19 |
| Item Quantity Master | Commercient Item Quantity Master Managed Custom Object | Commercient external key (Dynamics NAV package) | 20 |
| Product Item reverse lookup | Product | Commercient external key (earlier package) | 21 |
| Account Customer Reverse Lookup | Account | Commercient AR customer code | 22 |
| Create Price Book | Price book entry | External key (custom field) | 23 |
| Update Price Book | Price book entry | External key (custom field) | 24 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| account extract feed | insert + update | customer code identifiers, customers, customer addresses |
| customer master feed | insert + update | customers |
| customer master address feed | insert + update | customer addresses |
| customer master summary feed | insert + update | customer summaries |
| customer period summary feed | insert + update | customer period summaries |
| sales transaction work feed | insert + update | sales transaction headers |
| sales transaction amounts work feed | insert + update | sales transaction lines |
| sales transaction history feed | insert + update | sales transaction history headers |
| sales transaction amounts history feed | insert + update | sales transaction history lines |
| sales user defined work history feed | insert + update | sales user defined fields, sales tracking numbers |
| invoicing transaction work feed | insert + update | invoicing work transactions |
| invoicing transaction amounts work feed | insert + update | invoicing work transaction amounts |
| invoicing transaction history feed | insert + update | invoicing history transactions |
| invoicing transaction amounts history feed | insert + update | invoicing history transaction amounts |
| invoicing serial and lot number work feed | insert + update | invoicing work serial and lot numbers |
| invoicing line comments feed | insert + update | invoicing work line comments |
| invoicing payments work feed | insert + update | invoicing work payments |
| product feed | insert + update | items |
| item master feed | insert + update | items |
| item quantity master feed | insert + update | item quantities |
| product feed | insert + update | items |
| account reverse lookup feed | insert + update | customers |
| price book feed (new entries) | insert only | items, item price lists |
| price book feed (changes) | insert + update | items, item price lists |

## 4. Order of work

The templates set run sequence from 1 to 24. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 1 — Account
- 2 — Customer
- 3 — Customer Master Address
- 4 — Customer Master Summary
- 5 — Customer Period Summary
- 6 — Sales Transaction Works
- 7 — Sales Transaction Amounts Work
- 8 — Sales Transaction History
- 9 — Sales Transaction Amounts History
- 10 — Sale User Define & Track No Work History
- 11 — Invoicing Transaction Work
- 12 — Invoicing Transaction Amounts Work
- 13 — Invoicing Transaction History
- 14 — Invoicing Transaction Amounts History
- 15 — Invoicing Serial and Lot Number Work
- 16 — Invoicing Line Comments
- 17 — Invoicing Payments Work
- 18 — Product
- 19 — Item Master
- 20 — Item Quantity Master
- 21 — Product Item reverse lookup
- 22 — Account Customer Reverse Lookup
- 23 — Create Price Book
- 24 — Update Price Book

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- sales user defined work history feed reads sales transaction work, sales transaction history
- account reverse lookup feed reads customer master
- customer master address feed reads Account: customer master
- customer master summary feed reads Account: customer master
- customer period summary feed reads Account: customer master
- item master feed reads product record sync output (generic name)
- item quantity master feed reads item master, product record sync output (generic name)
- invoicing transaction work feed reads Account: customer master
- invoicing transaction amounts work feed reads invoicing transaction work
- invoicing transaction history feed reads Account: customer master
- invoicing transaction amounts history feed reads invoicing transaction history
- invoicing serial and lot number work feed reads invoicing transaction amounts work
- invoicing line comments feed reads invoicing transaction work
- invoicing payments work feed reads invoicing transaction history
- price book feed (new entries) reads product record sync output (generic name)
- price book feed (changes) reads product record sync output (generic name)
- product feed reads product record sync output (generic name)
- sales transaction amounts work feed reads sales transaction work
- sales transaction history feed reads Account: customer master
- sales transaction amounts history feed reads sales transaction history

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Account | Account | 14 | returned customer number → Commercient AR customer code, Customer name → Name, Customer unique identifier → Commercient customer unique identifier, Address 1,Address 2,Address 3 → Billing street, City → Billing city |
| Customer | Commercient Customer Master Managed Custom Object | 98 | returned customer number → Commercient customer number, Customer name → Name, the linked Salesforce record → Commercient account (related record), Customer class → Customer class, Corporate customer number → Corporate customer number |
| Customer Master Address | Commercient Customer Master Address Managed Custom Object | 33 | returned customer number, Address code → Commercient external key (Dynamics NAV package), returned customer number, Address code → Commercient name, the linked Salesforce record → Commercient account (related record), the linked Salesforce record → Commercient Customer Master Managed Custom Object, Salesperson identifier → Commercient salesperson identifier |
| Customer Master Summary | Commercient Customer Master Summary Managed Custom Object | 81 | returned customer number → Commercient customer number, returned customer number → Name, Last aged date → Commercient last aged date, First invoice date → Commercient first invoice date, Last returned check date → Commercient last returned check date |
| Customer Period Summary | Commercient Customer Period Summary Managed Custom Object | 16 | returned customer number,Period,Year,History type → Commercient external key (Dynamics NAV package), returned customer number → Commercient account (related record), returned customer number → Commercient Customer Master Managed Custom Object, Period → Commercient period, Year → Commercient year |
| Sales Transaction Works | Commercient Sales Transaction Work Managed Custom Object | 91 | Sales document type,Sales document number → Commercient external key (Dynamics NAV package), returned customer number → Commercient account (related record), Original number → Commercient original number, Document identifier → Commercient document identifier, Document date → Commercient document date |
| Sales Transaction Amounts Work | Commercient Sales Transaction Amounts Work Managed Custom Object | 96 | Sales document type, Sales document number, Line item sequence, Component sequence → external key column, Sales document type, Sales document number, Line item sequence, Component sequence → Name, Sales document number → Sales document number, the linked Salesforce record → sales transaction work, Item number → Item number |
| Sales Transaction History | Commercient Sales Transaction History Managed Custom Object | 96 | Sales document type, Sales document number → Commercient external key (Dynamics NAV package), Sales document type, Sales document number → Commercient name, returned customer number → Account, returned customer number → customer master, Original number → Commercient original number |
| Sales Transaction Amounts History | Commercient Sales Transaction Amounts History Managed Custom Object | 95 | Sales document type, Sales document number, Line item sequence, Component sequence → Commercient external key (Dynamics NAV package), Sales document type, Sales document number, Line item sequence, Component sequence → Commercient name, the linked Salesforce record → Commercient Sales Transaction History Managed Custom Object, Item number → Commercient item number, Item description → Commercient item description |
| Sale User Define & Track No Work History | Commercient Sales User Defined Work History Managed Custom Object | 21 | Sales document type, Sales document number → Commercient external key (Dynamics NAV package), Sales document type, Sales document number → Commercient name, the linked Salesforce record → sales transaction work, the linked Salesforce record → sales transaction history, Sales document type → Commercient sales document type |
| Invoicing Transaction Work | Commercient Invoicing Transaction Work Managed Custom Object | 104 | Document type code,Invoice number → Commercient external key (Dynamics NAV package), returned customer number → Commercient account (related record), returned customer number → customer master, Batch number → Batch number, Batch source → Batch source |
| Invoicing Transaction Amounts Work | Commercient Invoicing Transaction Amounts Work Managed Custom Object | 60 | Document type code,Invoice number,Line item sequence,Component sequence → Commercient external key (Dynamics NAV package), Document type code,Invoice number,Line item sequence,Component sequence → Commercient name, the linked Salesforce record → Commercient Invoicing Transaction Work Managed Custom Object, Item number → Commercient item number, Item tax schedule → Commercient item tax schedule |
| Invoicing Transaction History | Commercient Invoicing Transaction History Managed Custom Object | 94 | Document type code,Invoice number → Commercient external key (Dynamics NAV package), Document type code,Invoice number → Commercient name, returned customer number → Commercient account (related record), returned customer number → customer master, Batch number → Batch number |
| Invoicing Transaction Amounts History | Commercient Invoicing Transaction Amounts History Managed Custom Object | 48 | Document type code,Invoice number,Line item sequence,Component sequence → Commercient external key (Dynamics NAV package), Document type code,Invoice number,Line item sequence,Component sequence → Commercient name, invoicing transaction history → Commercient Invoicing Transaction History Managed Custom Object, Item number → Commercient item number, Item tax schedule → Commercient item tax schedule |
| Invoicing Serial and Lot Number Work | Commercient Invoicing Serial and Lot Number Work Managed Custom Object | 14 | Invoice number,Document type code,Component sequence,Line item sequence,Quantity type,Serial or lot sequence number → Commercient external key (Dynamics NAV package), Invoice number,Document type code,Component sequence,Line item sequence,Quantity type,Serial or lot sequence number → Commercient name, the linked Salesforce record → Commercient Invoicing Transaction Amounts Work Managed Custom Object, Item number → Commercient item number, Serial or lot number → Commercient serial or lot number |
| Invoicing Line Comments | Commercient Invoicing Line Comments Managed Custom Object | 8 | Document type code,Invoice number,Line item sequence → Commercient external key (Dynamics NAV package), Document type code,Invoice number,Line item sequence → Commercient name, the linked Salesforce record → invoicing transaction work, Comment 1 → Comment 1, Comment 2 → Comment 2 |
| Invoicing Payments Work | Commercient Invoicing Payments Work Managed Custom Object | 16 | Document type code,Invoice number,Sequence number → Commercient external key (Dynamics NAV package), the linked Salesforce record → Commercient Invoicing Transaction History Managed Custom Object, Document number → Document number, Currency code → Currency code, Checkbook → Checkbook |
| Product | Product | 5 | Item number → Commercient external key (earlier package), Item description → Name, Item number → Product code, Item description → Description, Inactive → Active |
| Item Master | Commercient Item Master Managed Custom Object | 91 | Item number → Commercient external key (Dynamics NAV package), Item number → Name, the linked Salesforce record → Product, Item number → Item number, Item description → Item description |
| Item Quantity Master | Commercient Item Quantity Master Managed Custom Object | 81 | Item number, Location code, Record type → Commercient external key (Dynamics NAV package), Item number, Location code, Record type → Commercient name, the linked Salesforce record → Commercient item master (related record), the linked Salesforce record → Commercient product (related record), Location code → Commercient location code |
| Product Item reverse lookup | Product | 5 | Item number → Commercient external key (earlier package), Item description → Name, Item number → Product code, Item description → Description, Inactive → Active |
| Account Customer Reverse Lookup | Account | 2 | returned customer number → Commercient AR customer code, the linked Salesforce record → Commercient customer (related record) |
| Create Price Book | Price book entry | 5 | Price level, Item number → External key (custom field), the linked Salesforce record → price book lookup, the linked Salesforce record → product lookup, Active → Active, Unit of measure price → Unit price |
| Update Price Book | Price book entry | 3 | Price level, Item number → External key (custom field), Unit of measure price → Unit price, Active → Active |

## 6. Community templates

The catalogue carries 39 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 39
- Default operations: insert on 39, update on 39, delete on 39
- Marked as circular sync: 0
- Licence groups they span: 10
- Destination objects: Account, Price book entry, Product, Contact and 22 custom objects
- Object display names: Account, Account Customer Reverse Lookup, Customer, Customer Master Address,
  Customer Master Summary, Customer Period Summary, Sales Transaction Amounts History, Sales
  Transaction Amounts Work, Sales Transaction History, Sales Transaction Works, AR Invoice Payments,
  Create Price Book and 17 more
- Template groups: Account, Product, Customer Multi Ship Addresses, AR Invoice Payments, Invoice,
  Open AR Invoice Header

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/microsoft-dynamics-gp-2016`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Microsoft Dynamics GP 2016 → Salesforce templates set up. dlake-crmpro-salesforce is the
destination skill this page sits under: its own text is the authority for the Salesforce conventions
that hold across every ERP, and its ERP table lists this page alongside every sibling ERP page for
this destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/microsoft-dynamics-gp-2016`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
