---
name: dlake-crmpro-salesforce/erps/syspro-6
kind: erp-summary
description: >-
  Use it when standing up or reading a SYSPRO 6 → Salesforce template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-salesforce, the destination skill this page is a child of, which
  carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — SYSPRO 6: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/syspro-6` (or `list_skills`) against the
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
| **Sal Area** | ERP sales areas data becomes Commercient Sales Area Managed Custom Object in Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Sales Area Managed Custom Object | sales areas |
| **Sales Move** | ERP AR sales movements data becomes Commercient AR Sales Movement Managed Custom Object in Salesforce. New records are created and existing ones updated; none are deleted. | Commercient AR Sales Movement Managed Custom Object | AR sales movements |
| **Get Users** | The templates push users to Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | users | — |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created and existing ones updated; none are deleted. | Account, Commercient AR Terms Managed Custom Object, Commercient Customer Class Managed Custom Object, Commercient AR Customer Managed Custom Object (earlier package), Commercient AR Customer Plus Managed Custom Object, Commercient Salesperson Managed Custom Object | customers, AR terms, customer classes, salespeople |
| **Branch or Division Details** | ERP sales branches data becomes Commercient Sales Branch Managed Custom Object in Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Sales Branch Managed Custom Object | sales branches |
| **CRM Opportunity and Line** | ERP quotes, quote lines data becomes Opportunity, Opportunity line item in Salesforce. New records are created and existing ones updated; none are deleted. | Opportunity, Opportunity line item | quotes, quote lines |
| **CRM Order and Line** | ERP customers, sales order master feed, sales order detail feed data becomes Order, Order product in Salesforce. New records are created and existing ones updated; none are deleted. | Order, Order product | sales order master feed, customers, sales order detail feed |
| **CRM Quote and Line** | ERP customers, quotes, quote lines data becomes Quote, Quote line item in Salesforce. New records are created and existing ones updated; none are deleted. | Quote, Quote line item | quotes, customers, quote lines |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created and existing ones updated; none are deleted. | Commercient AR Multiple Address Managed Custom Object | customer multiple addresses |
| **Product** | ERP inventory master table, inventory items, inventory warehouse quantities data becomes Commercient Inventory Master Managed Custom Object, Commercient Inventory Warehouse Managed Custom Object, Product in Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Inventory Master Managed Custom Object, Commercient Inventory Warehouse Managed Custom Object, Product | inventory items, inventory warehouse quantities |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created and existing ones updated; none are deleted. | Commercient AR Permanent Reprint Managed Custom Object, Commercient AR Invoice Managed Custom Object, Commercient AR Invoice Payment Managed Custom Object, Commercient AR Transaction Detail Managed Custom Object | permanent entry invoices, AR invoices (Syspro 6 table), AR terms, sales order masters (Syspro 6 table), AR invoice payments |
| **Pricebook** | ERP inventory prices, price books data becomes Price book object, Price book entry in Salesforce. New records are created and existing ones updated; none are deleted. | Price book object, Price book entry | inventory prices |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created and existing ones updated; none are deleted. | Commercient Sales Order Master Managed Custom Object, Commercient Sales Order Detail Managed Custom Object, Commercient Sales Order Serial Detail Managed Custom Object | sales order master feed, sales order masters (Syspro 6 table), delivery note headers (Syspro 6 table), AR invoices (Syspro 6 table), sales order detail feed |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| GET USER | users | — | 0 |
| Owner Cache | Commercient Salesperson Managed Custom Object | Commercient external key (earlier package) | 0 |
| Account | Account | Commercient AR customer code | 1 |
| Account Child | Account | Commercient AR customer code | 1 |
| Sal Area | Commercient Sales Area Managed Custom Object | Commercient areas | 2 |
| Sal Branch | Commercient Sales Branch Managed Custom Object | Commercient branch | 3 |
| Sales Person | Commercient Salesperson Managed Custom Object | Commercient external key (earlier package) | 4 |
| Ar Terms | Commercient AR Terms Managed Custom Object | Commercient terms codes | 5 |
| Customer Class | Commercient Customer Class Managed Custom Object | Commercient class | 6 |
| Customer | Commercient AR Customer Managed Custom Object (earlier package) | Commercient AR customer code (customer record) | 7 |
| Customer Reverse Lookup | Account | Commercient AR customer code | 8 |
| Sales order master | Commercient Sales Order Master Managed Custom Object | Commercient external key (earlier package) | 10 |
| Sales order detail | Commercient Sales Order Detail Managed Custom Object | Commercient external key (earlier package) | 11 |
| Sales order serial detail | Commercient Sales Order Serial Detail Managed Custom Object | Commercient external key (earlier package) | 12 |
| Permanent Entry Invoices | Commercient AR Permanent Reprint Managed Custom Object | Commercient external key (earlier package) | 13 |
| Price book object | Price book object | External key (custom field) | 14 |
| Product | Product | Commercient external key (earlier package) | 14 |
| Price book entry Standard Create | Price book entry | External key (custom field) | 16 |
| Price book entry Standard Update | Price book entry | External key (custom field) | 17 |
| Inventory Master | Commercient Inventory Master Managed Custom Object | Commercient external keys (earlier package) | 20 |
| Inventory Warehouse | Commercient Inventory Warehouse Managed Custom Object | Commercient external keys (earlier package) | 21 |
| Product Revserse Lookup | Product | Commercient external key (earlier package) | 22 |
| Ar Multipal Address | Commercient AR Multiple Address Managed Custom Object | Commercient external key (earlier package) | 23 |
| Invoice Header | Commercient AR Invoice Managed Custom Object | Commercient external key (earlier package) | 24 |
| Invoice Pay | Commercient AR Invoice Payment Managed Custom Object | Commercient external key (earlier package) | 25 |
| Ar Tran Details | Commercient AR Transaction Detail Managed Custom Object | Commercient external key (earlier package) | 26 |
| Sales Move | Commercient AR Sales Movement Managed Custom Object | Commercient external key (earlier package) | 27 |
| Opportunity | Opportunity | External key (custom field) | 28 |
| Opportunity line item | Opportunity line item | External key (custom field) | 29 |
| Quote | Quote | External key (custom field) | 30 |
| Quote line item | Quote line item | External key (custom field) | 31 |
| Customer + | Commercient AR Customer Plus Managed Custom Object | Commercient external key (earlier package) | 32 |
| Price book entry Custom Create | Price book entry | External key (custom field) | 33 |
| Price book entry Custom Update | Price book entry | External key (custom field) | 34 |
| Standard Order | Order | External key (custom field) | 45 |
| Standard order line | Order product | External key (custom field) | 46 |
| Standard Order Update | Order | External key (custom field) | 47 |
| Standard order line update | Order product | External key (custom field) | 48 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| salesperson feed | insert + update | salespeople |
| account feed | insert + update | customers |
| child account feed | insert + update | customers |
| sales area feed | insert + update | sales areas |
| sales branch feed | insert + update | sales branches |
| salesperson feed | insert + update | salespeople |
| AR terms feed | insert + update | AR terms |
| customer class feed | insert + update | customer classes |
| customer feed | insert + update | customers |
| customer reverse lookup feed | insert + update | customers |
| sales order reprint feed (called view) | insert + update | sales order master feed, sales order masters (Syspro 6 table), delivery note headers (Syspro 6 table), AR invoices (Syspro 6 table) |
| sales order detail reprint feed (called view) | insert + update | sales order detail feed, sales order masters, delivery note headers, AR invoices |
| sales order serial detail feed | insert + update | sales order serial details, sales order master feed |
| permanent entry invoice feed | insert + update | permanent entry invoices, AR invoices (Syspro 6 table) |
| price book feed | insert only | inventory prices |
| product feed | insert + update | inventory items |
| product price book feed | insert only | inventory prices |
| standard price book update feed | insert + update | inventory prices |
| inventory master feed | insert + update | inventory items |
| inventory warehouse feed | insert + update | inventory warehouse quantities, inventory items |
| product reverse lookup feed | insert + update | inventory items |
| AR multiple address feed | insert + update | customer multiple addresses |
| AR invoice feed | insert + update | AR invoices (Syspro 6 table), AR terms, sales order masters (Syspro 6 table) |
| AR invoice payment feed | insert + update | AR invoice payments, AR invoices (Syspro 6 table) |
| AR transaction detail feed | insert + update | AR transaction details |
| AR sales movement feed | insert + update | AR sales movements |
| opportunity feed | insert + update | quotes |
| opportunity line item feed | insert only | quote lines |
| quote feed | insert + update | quotes, customers |
| quote line item feed | insert + update | quote lines |
| custom price book create feed | insert only | inventory prices |
| custom price book update feed | insert + update | inventory prices |
| standard order header feed | insert + update | sales order master feed, customers |
| standard order line feed | insert + update | sales order detail feed |
| standard order header update feed | insert + update | sales order master feed, customers |
| standard order line update feed | insert + update | sales order detail feed |

## 4. Order of work

The templates set run sequence from 0 to 48. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 0 — GET USER, Owner Cache
- 1 — Account, Account Child
- 2 — Sal Area
- 3 — Sal Branch
- 4 — Sales Person
- 5 — Ar Terms
- 6 — Customer Class
- 7 — Customer
- 8 — Customer Reverse Lookup
- 10 — Sales order master
- 11 — Sales order detail
- 12 — Sales order serial detail
- 13 — Permanent Entry Invoices
- 14 — Price book object, Product
- 16 — Price book entry Standard Create
- 17 — Price book entry Standard Update
- 20 — Inventory Master
- 21 — Inventory Warehouse
- 22 — Product Revserse Lookup
- 23 — Ar Multipal Address
- 24 — Invoice Header
- 25 — Invoice Pay
- 26 — Ar Tran Details
- 27 — Sales Move
- 28 — Opportunity
- 29 — Opportunity line item
- 30 — Quote
- 31 — Quote line item
- 32 — Customer +
- 33 — Price book entry Custom Create
- 34 — Price book entry Custom Update
- 45 — Standard Order
- 46 — Standard order line
- 47 — Standard Order Update
- 48 — Standard order line update

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- AR sales movement feed reads customer sync output (generic name), AR invoice sync output (generic
  name), sales order master sync output (generic name)
- account feed reads user sync output
- child account feed reads user sync output
- customer reverse lookup feed reads customer sync output (generic name)
- opportunity feed reads account sync output (generic name)
- opportunity line item feed reads opportunity sync output (generic name), product sync output
  (generic name), product price book sync output (generic name)
- standard order header feed reads account sync output (generic name), contact sync output (generic
  name), opportunity sync output (generic name), quote sync output (generic name); no template in
  this set writes contact sync output (generic name)
- standard order line feed reads standard order header sync output, product sync output (generic
  name), product price book sync output (generic name), quote line item sync output (generic name)
- standard order header update feed reads account sync output (generic name), contact sync output
  (generic name), opportunity sync output (generic name), quote sync output (generic name); no
  template in this set writes contact sync output (generic name)
- standard order line update feed reads standard order header sync output, product sync output
  (generic name), product price book sync output (generic name), quote line item sync output
  (generic name)
- quote feed reads account sync output (generic name), opportunity sync output (generic name)
- quote line item feed reads product sync output (generic name), quote sync output (generic name),
  product price book sync output (generic name)
- customer feed reads account sync output (generic name), AR terms sync output (generic name),
  salesperson sync output (generic name), customer class sync output (generic name), sales area sync
  output (generic name), sales branch sync output (generic name)
- AR multiple address feed reads account sync output (generic name)
- inventory master feed reads product sync output (generic name)
- inventory warehouse feed reads product sync output (generic name)
- permanent entry invoice feed reads AR invoice sync output (generic name), account sync output
  (generic name)
- AR invoice feed reads account sync output (generic name), customer sync output (generic name), AR
  terms sync output (generic name), salesperson sync output (generic name), sales area sync output
  (generic name), sales branch sync output (generic name)
- AR invoice payment feed reads AR invoice sync output (generic name), account sync output (generic
  name)
- AR transaction detail feed reads account sync output (generic name), product record sync output
  (generic name); no template in this set writes product record sync output (generic name)
- product price book feed reads product sync output (generic name), price book sync output (generic
  name)
- custom price book create feed reads product sync output (generic name), price book sync output
  (generic name)
- custom price book update feed reads product sync output (generic name), price book sync output
  (generic name)
- product reverse lookup feed reads inventory master sync output (generic name)
- sales order reprint feed (called view) reads account sync output (generic name), customer sync
  output (generic name), salesperson sync output (generic name), Salesforce account extract sync
  output (generic name), Salesforce AR customer extract sync output (generic name), sales branch
  sync output (alternate spelling); no template in this set writes Salesforce account extract sync
  output (generic name), Salesforce AR customer extract sync output (generic name), sales branch
  sync output (alternate spelling), salesperson sync output (alternate spelling)
- sales order detail reprint feed (called view) reads sales order master sync output (generic name),
  account sync output (generic name)
- sales order serial detail feed reads sales order master sync output (generic name), account sync
  output (generic name), sales order detail sync output (generic name)
- salesperson feed reads salesperson sync output (generic name)

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Owner Cache | Commercient Salesperson Managed Custom Object | 29 | Branch, Salesperson → Commercient external key (earlier package), Branch, Salesperson → Name, Sales budget 1 → Sales budget 1, Sales budget 2 → Sales budget 2, Sales budget 3 → Sales budget 3 |
| Account | Account | 14 | Customer → Commercient AR customer code, Name → Name, Telephone number → Phone, Sold to address line 1, Sold to address line 2 → Billing street, Sold to address line 3 → Billing city |
| Account Child | Account | 15 | Customer → Commercient AR customer code, Name → Name, Telephone number → Phone, Sold to address line 1, Sold to address line 2 → Billing street, Sold to address line 3 → Billing city |
| Sal Area | Commercient Sales Area Managed Custom Object | 6 | Area → Commercient areas, Description → Name, Description → Description, Tax code → Tax code, Provincial tax flag → Provincial tax flag |
| Sal Branch | Commercient Sales Branch Managed Custom Object | 29 | Branch → Commercient branch, Description → Name, Description → Description, Branch address line 1 → Branch address line 1, Branch address line 2 → Branch address line 2 |
| Sales Person | Commercient Salesperson Managed Custom Object | 29 | Branch, Salesperson → Commercient external key (earlier package), Branch, Salesperson → Name, Sales budget 1 → Sales budget 1, Sales budget 2 → Sales budget 2, Sales budget 3 → Sales budget 3 |
| Ar Terms | Commercient AR Terms Managed Custom Object | 14 | Terms code → Commercient terms codes, Description → Description, Discount percent → Discount percent, Discount days → Discount days, Terms due days → Terms due days |
| Customer Class | Commercient Customer Class Managed Custom Object | 3 | Class → Commercient class, Description → Name, Description → Description |
| Customer | Commercient AR Customer Managed Custom Object (earlier package) | 96 | Customer → Commercient AR customer code (customer record), Name → Name, Name → Commercient name (earlier package), Short name → Short name, Exempt from finance charges → Exempt from finance charges |
| Customer Reverse Lookup | Account | 2 | Customer → Commercient AR customer code, the linked Salesforce record → Commercient Syspro customer record (related record) |
| Sales order master | Commercient Sales Order Master Managed Custom Object | 60 | external key column → Commercient external key (earlier package), Name → Name, Account → Account, Syspro customer record column → Syspro customer record (custom field), salesperson record column → Salesperson record (custom field) |
| Sales order detail | Commercient Sales Order Detail Managed Custom Object | 98 | Invoice, Sales order, Sales order line → Commercient external key (earlier package), Invoice, Sales order, Sales order line → Name, the linked Salesforce record → Sales order master, the linked Salesforce record → Commercient account (related record), Discount percent 1 → Discount percent 1 |
| Sales order serial detail | Commercient Sales Order Serial Detail Managed Custom Object | 15 | Sales order, Sales order line, Lot → Commercient external key (earlier package), Invoice, Sales order → Commercient Sales Order Master Managed Custom Object, Customer → Account, Invoice, Sales order, Sales order line, Lot → Commercient Sales Order Detail Managed Custom Object, Sales order, Sales order line, Lot → Name |
| Permanent Entry Invoices | Commercient AR Permanent Reprint Managed Custom Object | 29 | Customer, Invoice → Commercient external key (earlier package), Invoice → Commercient AR Invoice Managed Custom Object, Customer → Commercient account (related record), Document type → Commercient document type, Invoice date → Commercient invoice date |
| Price book object | Price book object | 3 | Price code → External key (custom field), Price code → Name, Row timestamp → Row timestamp |
| Product | Product | 7 | Stock code → Name, Stock code → Product code, Stock code → Commercient external key (earlier package), Stock code → Product stock code (custom field), Description → Description |
| Price book entry Standard Create | Price book entry | 6 | Stock code,Price code → External key (custom field), the linked Salesforce record → product lookup, Selling price → Unit price, the linked Salesforce record → price book lookup, Price code → Currency code |
| Price book entry Standard Update | Price book entry | 3 | Stock code,Price code → External key (custom field), Selling price → Unit price, Active → Active |
| Inventory Master | Commercient Inventory Master Managed Custom Object | 98 | Stock code → Name, the linked Salesforce record → Product, Stock code → external keys column, Description → Description, Long description → Long description |
| Inventory Warehouse | Commercient Inventory Warehouse Managed Custom Object | 96 | Stock code,Warehouse → Commercient external keys (earlier package), Stock code,Warehouse → Name, Stock code → Stock code, Warehouse → Warehouse, Default bin → Default bin |
| Product Revserse Lookup | Product | 2 | Stock code → Commercient external key (earlier package), the linked Salesforce record → Commercient Syspro inventory master (related record) |
| Ar Multipal Address | Commercient AR Multiple Address Managed Custom Object | 17 | Customer, Address code → Commercient external key (earlier package), Ship to address 1 → Ship to address 1, Ship to address 2 → Ship to address 2, Ship to address line 3 → Ship to address line 3, Ship to address line 4 → Ship to address line 4 |
| Invoice Header | Commercient AR Invoice Managed Custom Object | 47 | Customer → Commercient account (related record), Sales order → Commercient Sales Order Master Managed Custom Object, Salesperson → Commercient Salesperson Managed Custom Object, Branch → Commercient Sales Branch Managed Custom Object, Area → Commercient Sales Area Managed Custom Object |
| Invoice Pay | Commercient AR Invoice Payment Managed Custom Object | 23 | Customer, Invoice, Document type, Entry number → Commercient external key (earlier package), Customer, Invoice, Document type, Entry number → Name, Customer → Account, Invoice → AR invoice, Transaction type → Transaction type |
| Ar Tran Details | Commercient AR Transaction Detail Managed Custom Object | 82 | Detail line number,Invoice,Register,Summary line number,Transaction month,Transaction year → Commercient external key (earlier package), Detail line number,Invoice,Register,Summary line number,Transaction month,Transaction year → Name, Stock code → Commercient product (related record), Customer → Commercient account (related record), Invoice → Commercient invoice (related record) |
| Sales Move | Commercient AR Sales Movement Managed Custom Object | 30 | Customer → Commercient AR Customer Managed Custom Object (earlier package), Invoice → Commercient AR Invoice Managed Custom Object, Sales order → Commercient Sales Order Master Managed Custom Object, Customer, Register, Summary line number, Detail line number → external key column, Register, Summary line number, Detail line number → Name |
| Opportunity | Opportunity | 7 | Quote → External key (custom field), Quote description → Name, Quote description → Description, Quote status → Stage, Requested delivery date → close date property |
| Opportunity line item | Opportunity line item | 6 | Quote, Line → External key (custom field), Quantity → Quantity, Customer retail price → Unit price, Quote line description → Description, the linked Salesforce record → Opportunity |
| Quote | Quote | 17 | Quote → External key (custom field), Quote → Name, the linked Salesforce opportunity → Opportunity identifier, Status → Status, Requested delivery date → Expiry date |
| Quote line item | Quote line item | 7 | Line stock code (Syspro) → product lookup, Quote → Quote, Quote line quantity → Quantity, Quote line ship date → Service date, Line stock code (Syspro) → price book entry lookup |
| Customer + | Commercient AR Customer Plus Managed Custom Object | 1 | external key column → Commercient external key (earlier package) |
| Price book entry Custom Create | Price book entry | 5 | Stock code,Price code → External key (custom field), the linked Salesforce record → product lookup, Selling price → Unit price, the linked Salesforce record → price book lookup, Active → Active |
| Price book entry Custom Update | Price book entry | 3 | Stock code,Price code → External key (custom field), Selling price → Unit price, Active → Active |
| Standard Order | Order | 22 | returned invoice number, Sales order → External key (custom field), the linked Salesforce record → account lookup, Customer → Account number, Delivery note → Description, returned invoice number, Sales order → Name |
| Standard order line | Order product | 11 | Invoice, Sales order, Sales order line → External key (custom field), Invoice, Sales order → Order identifier, Stock code → product lookup, Stock code, Price code → price book entry lookup, Stock description → Description |
| Standard Order Update | Order | 24 | returned invoice number,Sales order → External key (custom field), the linked Salesforce record → account lookup, Customer → Account number, Delivery note → Description, returned invoice number,Sales order → Name |
| Standard order line update | Order product | 9 | Invoice, Sales order, Sales order line → External key (custom field), Stock description → Description, Customer request date → End date, Price → List price, Order quantity → Quantity |

## 6. Community templates

The catalogue carries 47 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 47
- Default operations: insert on 47, update on 47, delete on 47
- Marked as circular sync: 0
- Licence groups they span: 11
- Destination objects: Price book entry, Account, Product, Commercient AR Customer Managed Custom
  Object (earlier package), Commercient Inventory Master Managed Custom Object, Commercient
  Inventory Warehouse Managed Custom Object, Commercient AR Multiple Address Managed Custom Object,
  Commercient AR Permanent Reprint Managed Custom Object, Commercient AR Sales Movement Managed
  Custom Object, Commercient AR Transaction Detail Managed Custom Object, Commercient Sales Area
  Managed Custom Object, Commercient Sales Branch Managed Custom Object, Commercient Salesperson
  Managed Custom Object, Commercient Sales Order Detail Managed Custom Object, Commercient Sales
  Order Serial Detail Managed Custom Object, Commercient Sales Order Master Managed Custom Object,
  Commercient AR Terms Managed Custom Object, Commercient Customer Class Managed Custom Object,
  Opportunity, 5 more and 4 custom objects
- Object display names: Price book entry Standard Create, Price book entry Standard Update, Product,
  Account, Account Child, Ar Multipal Address, Ar Terms, Ar Tran Details, AR customer Update Only,
  Customer, Customer Balance, Customer Class, 24 more and 5 further templates
- Template groups: Product, Account, CRM Opportunity and Line, CRM Quote and Line, Customer Multi
  Ship Addresses, Invoice

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/syspro-6`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped SYSPRO 6 → Salesforce templates set up. dlake-crmpro-salesforce is the destination skill
this page sits under: its own text is the authority for the Salesforce conventions that hold across
every ERP, and its ERP table lists this page alongside every sibling ERP page for this destination.
For the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/syspro-6`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
