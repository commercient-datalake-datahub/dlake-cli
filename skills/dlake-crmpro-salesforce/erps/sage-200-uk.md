---
name: dlake-crmpro-salesforce/erps/sage-200-uk
kind: erp-summary
description: >-
  Use it when standing up or reading a Sage 200 UK → Salesforce template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-salesforce, the destination skill this page is a child of, which
  carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Sage 200 UK: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/sage-200-uk` (or `list_skills`) against the
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
| **Sync Product colour** | ERP product group search category, product group search value, product group data becomes Sage 200 UK colour (custom object) in Salesforce. New records are created and existing ones updated; none are deleted. | Sage 200 UK colour (custom object) | product groups, stock items, product group search values, search values, product group search categories, stock item search category values |
| **Sync Product range** | ERP product group search category, product group search value, product group data becomes Sage 200 UK range (custom object) in Salesforce. New records are created and existing ones updated; none are deleted. | Sage 200 UK range (custom object) | product groups, stock items, product group search categories, search values, search categories, stock item search category values |
| **GET USER** | The templates push users to Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | users | — |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Contact, Commercient Sage 200 UK Customer Managed Custom Object | customer accounts, customer locations, customer delivery addresses, customer contacts, customer contact details, price bands |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created and existing ones updated; none are deleted. | Commercient Sage 200 UK Address Managed Custom Object, Commercient Sage 200 UK Customer Delivery Address Managed Custom Object, Commercient Sage 200 UK Sales Order Delivery Address Managed Custom Object | customer locations, customer delivery addresses, sales order delivery addresses |
| **Product** | ERP stock item, Warehouse, warehouse item data becomes Commercient Sage 200 UK Item Managed Custom Object, Commercient Sage 200 UK Warehouse Managed Custom Object, Commercient Sage 200 UK Warehouse Item Managed Custom Object in Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Sage 200 UK Item Managed Custom Object, Commercient Sage 200 UK Warehouse Managed Custom Object, Commercient Sage 200 UK Warehouse Item Managed Custom Object, Product group (custom object), Product | stock items, warehouses, warehouse items, product groups |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created and existing ones updated; none are deleted. | Commercient Sage 200 UK Invoice Header Managed Custom Object, Commercient Sage 200 UK Invoice Detail Managed Custom Object | invoices and credit notes, posted customer transactions, invoice and credit note lines, stock items |
| **Pricebook** | ERP price band, stock item, stock item price data becomes Price book object, Price book entry, Price book entry in Salesforce. New records are created and existing ones updated; none are deleted. | Price book object, Price book entry | price bands, stock items, stock item prices |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created and existing ones updated; none are deleted. | Commercient Sage 200 UK Sales Order Header Managed Custom Object, Commercient Sage 200 UK Sales Order Detail Managed Custom Object | sales orders and returns, warehouses, sales order and return lines, stock items |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| GET USER | users | — | 0 |
| Sync price band | Price book object | External key (custom field) | 0 |
| Account | Account | Commercient AR customer code | 1 |
| Address | Commercient Sage 200 UK Address Managed Custom Object | Commercient external key | 2 |
| Contacts | Contact | External key (custom field) | 3 |
| Customer | Commercient Sage 200 UK Customer Managed Custom Object | Commercient external key | 3 |
| Account Reverse lookup | Account | Commercient AR customer code | 4 |
| Sync Customer delivery address | Commercient Sage 200 UK Customer Delivery Address Managed Custom Object | Commercient external key | 4 |
| Sync Product colour | Sage 200 UK colour (custom object) | External key (custom field) | 5 |
| Sync Product range | Sage 200 UK range (custom object) | External key (custom field) | 5 |
| Sync product group | Product group (custom object) | External key (custom field) | 5 |
| Product | Product | Commercient external key (earlier package) | 5 |
| Sync Sales order delivery address | Commercient Sage 200 UK Sales Order Delivery Address Managed Custom Object | Commercient external key | 6 |
| Price Book Entry Create | Price book entry | External key (custom field) | 6 |
| Price Book Entry Update | Price book entry | External key (custom field) | 7 |
| Item Master | Commercient Sage 200 UK Item Managed Custom Object | Commercient external key | 8 |
| Sync Warehouse | Commercient Sage 200 UK Warehouse Managed Custom Object | Commercient external key | 9 |
| Item Warehouse | Commercient Sage 200 UK Warehouse Item Managed Custom Object | Commercient external key | 10 |
| Item Reverse lookup | Product | Commercient external key (earlier package) | 11 |
| Sales Order | Commercient Sage 200 UK Sales Order Header Managed Custom Object | Commercient external key | 12 |
| Sales Order Line | Commercient Sage 200 UK Sales Order Detail Managed Custom Object | Commercient external key | 13 |
| Invoice | Commercient Sage 200 UK Invoice Header Managed Custom Object | Commercient external key | 14 |
| Invoice Line | Commercient Sage 200 UK Invoice Detail Managed Custom Object | Commercient external key | 15 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| price band feed | insert + update | price bands |
| account feed | insert + update | customer accounts, customer locations, customer delivery addresses |
| address feed | insert only | customer locations |
| customer feed | insert only | customer accounts, price bands, customer contacts, customer contact details, customer locations |
| account reverse lookup feed | insert + update | customer accounts |
| customer delivery address feed | insert + update | customer delivery addresses |
| colour feed | — | product groups, stock items, product group search values, search values, product group search categories |
| range feed | insert + update | product groups, stock items, product group search categories, search values, search categories |
| product group feed | insert + update | product groups |
| product feed | insert + update | stock items, product groups |
| sales order delivery address feed | insert + update | sales order delivery addresses |
| price book feed (new entries) | insert only | stock items, stock item prices, price bands |
| price book feed (changes) | insert + update | stock items, stock item prices, price bands |
| item feed | insert only | stock items |
| warehouse feed | insert + update | warehouses |
| warehouse item feed | insert only | warehouse items, warehouses, stock items |
| item reverse lookup feed | insert + update | stock items |
| sales order feed | insert only | sales orders and returns, warehouses |
| sales order line feed | insert only | sales order and return lines, stock items |
| invoice feed | insert only | invoices and credit notes, posted customer transactions |
| invoice line feed | insert only | invoice and credit note lines, stock items |

## 4. Order of work

The templates set run sequence from 0 to 15. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 0 — GET USER, Sync price band
- 1 — Account
- 2 — Address
- 3 — Contacts, Customer
- 4 — Account Reverse lookup, Sync Customer delivery address
- 5 — Sync Product colour, Sync Product range, Sync product group, Product
- 6 — Sync Sales order delivery address, Price Book Entry Create
- 7 — Price Book Entry Update
- 8 — Item Master
- 9 — Sync Warehouse
- 10 — Item Warehouse
- 11 — Item Reverse lookup
- 12 — Sales Order
- 13 — Sales Order Line
- 14 — Invoice
- 15 — Invoice Line

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- account reverse lookup feed reads customer sync output
- customer feed reads address sync output, account sync output (generic name)
- address feed reads account sync output (generic name)
- customer delivery address feed reads account sync output (generic name)
- sales order delivery address feed reads sales order sync output
- item feed reads Product, product group sync output, colour sync output, range sync output
- warehouse item feed reads Product, item sync output, warehouse sync output
- invoice feed reads customer sync output, account sync output (generic name)
- invoice line feed reads item sync output, product group sync output, colour sync output, range
  sync output, invoice sync output
- price book feed (new entries) reads price band sync output, Product
- price book feed (changes) reads Product
- item reverse lookup feed reads item sync output
- sales order feed reads customer sync output, account sync output (generic name), warehouse sync
  output
- sales order line feed reads item sync output, product group sync output, colour sync output, range
  sync output, sales order sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Sync price band | Price book object | 5 | Price band identifier → External key (custom field), Name → Name, Description → Description, Record version stamp → Record version stamp, Active → Active |
| Account | Account | 10 | Customer account identifier → Commercient AR customer code, Customer account name → Name, Address line 1, Address line 2, Address line 3 → Billing street, City → Billing city, Country → Billing country |
| Address | Commercient Sage 200 UK Address Managed Custom Object | 14 | Address line 1 → Address line 1, Address line 2 → Address line 2, Address line 3 → Address line 3, Address line 4 → Address line 4, Postcode → Postcode |
| Contacts | Contact | 19 | Record number → Record number, Customer identifier → Customer identifier, Family name → Family name, Middle name → Middle name, Given name → Given name |
| Customer | Commercient Sage 200 UK Customer Managed Custom Object | 105 | Customer location identifier → Commercient address (related record), Customer account name → Name, Customer account identifier → Commercient account, Customer account identifier → Commercient external key, Customer account number → Customer account number |
| Account Reverse lookup | Account | 2 | Customer account identifier → Commercient AR customer code, the linked Salesforce record → Sage 200 UK customer (custom field) |
| Sync Customer delivery address | Commercient Sage 200 UK Customer Delivery Address Managed Custom Object | 19 | Customer delivery address identifier → Commercient external key, Description → Commercient name, Address line 1 → Commercient address line 1, Address line 2 → Commercient address line 2, Address line 3 → Commercient address line 3 |
| Sync Product colour | Sage 200 UK colour (custom object) | 4 | Code → External key (custom field), Name → Item description, Name → Name, Record version stamp → Record version stamp |
| Sync Product range | Sage 200 UK range (custom object) | 5 | Code → External key (custom field), Record version stamp → Row timestamp, Name → Item description, Name → Range, Description → Description |
| Sync product group | Product group (custom object) | 34 | Product group identifier → External key (custom field), Description → Name, Code → Code, Use description on documents → Use description on documents, Stock item type identifier → Stock item type identifier |
| Product | Product | 5 | Code → Commercient external key (earlier package), Name → Name, Code → Product code, Stock item status identifier → Active, Description → Description |
| Sync Sales order delivery address | Commercient Sage 200 UK Sales Order Delivery Address Managed Custom Object | 19 | Order or return identifier → Commercient Sage 200 UK Sales Order Header Managed Custom Object, Sales order delivery address identifier → Commercient external key, Description → Name, Postal name → Postal name, Address line 1 → Address line 1 |
| Price Book Entry Create | Price book entry | 4 | Code, Price band identifier → External key (custom field), Active → Active, Price → Unit price, Code → product lookup |
| Price Book Entry Update | Price book entry | 3 | Code → External key (custom field), Active → Active, Price → Unit price |
| Item Master | Commercient Sage 200 UK Item Managed Custom Object | 76 | Code → Commercient product, Stock item status identifier → Current status, Product group identifier → Product group (custom object), Code → Sage 200 UK colour (custom object), Code → Sage 200 UK range (custom object) |
| Sync Warehouse | Commercient Sage 200 UK Warehouse Managed Custom Object | 36 | Warehouse identifier → Commercient external key, Name → Name, Description → Description, Use for sales trading → Use for sales trading, Postal name → Postal name |
| Item Warehouse | Commercient Sage 200 UK Warehouse Item Managed Custom Object | 27 | Warehouse identifier → Commercient Sage 200 UK Warehouse Managed Custom Object, Item identifier → Commercient product, Item identifier → Commercient item, Warehouse item identifier → Name, Warehouse item identifier → external key column |
| Item Reverse lookup | Product | 2 | Code → Commercient external key (earlier package), the linked Salesforce record → Commercient Sage 200 UK Item Managed Custom Object |
| Sales Order | Commercient Sage 200 UK Sales Order Header Managed Custom Object | 100 | Customer identifier → Commercient customer (related record), Customer identifier → Commercient account, Warehouse identifier → Sage 200 UK warehouse (custom field), Order or return identifier → Name, Warehouse identifier → Warehouse name |
| Sales Order Line | Commercient Sage 200 UK Sales Order Detail Managed Custom Object | 102 | Order or return identifier → Commercient sales order header (related record), Item code → Sage 200 UK colour (custom object), Item code → Item code, Nominal account reference → Nominal account reference, Nominal cost centre → Nominal cost centre |
| Invoice | Commercient Sage 200 UK Invoice Header Managed Custom Object | 54 | Customer identifier → Commercient customer (related record), Customer identifier → Commercient account, Document status identifier → Document status (custom field), Invoice or credit type identifier → Invoice or credit type name (custom field), Invoice or credit identifier → Name |
| Invoice Line | Commercient Sage 200 UK Invoice Detail Managed Custom Object | 28 | Invoice or credit identifier → Commercient invoice header (related record), Print sequence number,Invoice or credit line identifier → Commercient external key, Despatch receipt numbers → Despatch receipt numbers, User name → User name, Order or return number → Order or return number |

## 6. Community templates

The catalogue carries 114 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 114
- Default operations: insert on 114, update on 114, delete on 114
- Marked as circular sync: 2
- Licence groups they span: 12
- Destination objects: Account, Commercient Sage 200 UK Address Managed Custom Object, Commercient
  Sage 200 UK Customer Managed Custom Object, Commercient Sage 200 UK Invoice Detail Managed Custom
  Object, Commercient Sage 200 UK Invoice Header Managed Custom Object, Commercient Sage 200 UK
  Sales Order Detail Managed Custom Object, Commercient Sage 200 UK Sales Order Header Managed
  Custom Object, Contact, Product, Sage 200 UK open AR invoice header (custom object), Price book
  entry, Commercient Sage 200 UK Item Managed Custom Object, Commercient Sage 200 UK Warehouse
  Managed Custom Object, Commercient Sage 200 UK Warehouse Item Managed Custom Object, Price book
  object, Commercient Account Managed Custom Object, Commercient Sage 200 UK Customer Delivery
  Address Managed Custom Object, Commercient Sage 200 UK Sales Order Delivery Address Managed Custom
  Object, 5 more and 15 custom objects
- Object display names: Account, Account Reverse lookup, Customer, Address, Invoice, Invoice Line,
  Sales Order Line, Sales Order, Contact, Sage 200 UK Open AR invoice Header, Contacts, Item Master,
  26 more and 13 further templates
- Template groups: Account, Invoice, Sales order, Product, Customer Multi Ship Addresses, Open AR
  Invoice Header, Purchase Order

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/sage-200-uk`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Sage 200 UK → Salesforce templates set up. dlake-crmpro-salesforce is the destination
skill this page sits under: its own text is the authority for the Salesforce conventions that hold
across every ERP, and its ERP table lists this page alongside every sibling ERP page for this
destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/sage-200-uk`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
