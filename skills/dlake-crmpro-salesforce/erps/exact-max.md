---
name: dlake-crmpro-salesforce/erps/exact-max
kind: erp-summary
description: >-
  Use it when standing up or reading an Exact MAX → Salesforce template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-salesforce, the destination skill this page is a child of, which
  carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Exact MAX: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/exact-max` (or `list_skills`) against the
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
| **Commodity Codes** | ERP commodity code list data becomes Commercient Commodity Codes Managed Custom Object in Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Commodity Codes Managed Custom Object | commodity codes |
| **Get User** | The templates push User to Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | User | — |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created and existing ones updated; none are deleted. | Account, Commercient Exact Max Customer Master Managed Custom Object, Commercient Sales Rep Master Managed Custom Object | customer master records, shipping addresses, sales reps, code master records |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created and existing ones updated; none are deleted. | Commercient Shipping Master Managed Custom Object | shipping addresses |
| **Product** | ERP part master, part sales, part stock data becomes Commercient Part Master Managed Custom Object, Commercient Part Stock Managed Custom Object, Commercient Part Sales Managed Custom Object in Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Part Master Managed Custom Object, Commercient Part Stock Managed Custom Object, Commercient Part Sales Managed Custom Object, Product, Commercient Product Structure Managed Custom Object | part master records, part sales records, part stock records, product structures |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created and existing ones updated; none are deleted. | Commercient Invoice Master Managed Custom Object, Commercient Invoice Line Managed Custom Object | invoice master records, invoice details |
| **Pricebook** | ERP part master, part sales data becomes Price book entry, Price book entry in Salesforce. New records are created and existing ones updated; none are deleted. | Price book entry | part master records, part sales records |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created and existing ones updated; none are deleted. | Commercient Sales Order Master Managed Custom Object, Commercient Sales Order Detail Managed Custom Object | sales order master records, sales order details |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Sales Person | Commercient Sales Rep Master Managed Custom Object | Commercient external key (Exact package) | 1 |
| Account | Account | Commercient AR customer code | 4 |
| Customer Master | Commercient Exact Max Customer Master Managed Custom Object | Commercient external key (Exact package) | 5 |
| Customer Reverse Lookup Account | Account | Commercient AR customer code | 6 |
| Shipping Master | Commercient Shipping Master Managed Custom Object | Commercient external key (Exact package) | 7 |
| Commodity Codes | Commercient Commodity Codes Managed Custom Object | Commercient external key (Exact package) | 8 |
| Part Master | Commercient Part Master Managed Custom Object | Commercient external key (Exact package) | 9 |
| Sales Order Header | Commercient Sales Order Master Managed Custom Object | Commercient external key (Exact package) | 10 |
| Sales Order Line | Commercient Sales Order Detail Managed Custom Object | Commercient external key (Exact package) | 11 |
| Invoice Header | Commercient Invoice Master Managed Custom Object | Commercient external key (Exact package) | 12 |
| Invoice Line | Commercient Invoice Line Managed Custom Object | Commercient external key (Exact package) | 13 |
| Product | Product | Commercient external key (earlier package) | 14 |
| Product Reverse Lookup | Commercient Part Master Managed Custom Object | Commercient external key (Exact package) | 15 |
| Create Standard Price Book | Price book entry | External key (custom field) | 16 |
| Get User | User | Id | 17 |
| Part Stock | Commercient Part Stock Managed Custom Object | Commercient external key (Exact package) | 19 |
| Update Standard Pricebook | Price book entry | External key (custom field) | 20 |
| Part Sales | Commercient Part Sales Managed Custom Object | Commercient external key (Exact package) | 21 |
| Product Structure | Commercient Product Structure Managed Custom Object | Commercient external key (Exact package) | 22 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| salesperson feed | insert + update | sales reps |
| account feed | insert + update | customer master records, shipping addresses, sales reps, code master records |
| customer feed | insert + update | customer master records |
| customer account lookup sync output (generic name) | insert + update | customer master records |
| shipping address feed | insert + update | shipping addresses |
| commodity code feed | insert + update | commodity codes |
| part master feed | insert + update | part master records |
| sales order feed | insert + update | sales order master records |
| sales order line feed | insert + update | sales order details |
| invoice feed | insert + update | invoice master records |
| invoice line feed | insert + update | invoice details |
| product feed | insert + update | part master records, part sales records |
| product reverse lookup feed (generic name) | insert + update | part master records, part sales records |
| standard price book feed (new entries) | insert only | part master records, part sales records |
| part stock feed | insert + update | part stock records |
| standard price book feed (changes) | insert + update | part master records, part sales records |
| part sales feed | insert + update | part sales records |
| product structure feed | insert + update | product structures |

## 4. Order of work

The templates set run sequence from 1 to 22. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 1 — Sales Person
- 4 — Account
- 5 — Customer Master
- 6 — Customer Reverse Lookup Account
- 7 — Shipping Master
- 8 — Commodity Codes
- 9 — Part Master
- 10 — Sales Order Header
- 11 — Sales Order Line
- 12 — Invoice Header
- 13 — Invoice Line
- 14 — Product
- 15 — Product Reverse Lookup
- 16 — Create Standard Price Book
- 17 — Get User
- 19 — Part Stock
- 20 — Update Standard Pricebook
- 21 — Part Sales
- 22 — Product Structure

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- customer account lookup sync output (generic name) reads customer sync output (generic name)
- account feed reads salesperson sync output (generic name), user sync output
- customer feed reads salesperson sync output (generic name), account sync output (generic name)
- shipping address feed reads customer sync output (generic name), account sync output (generic
  name)
- part master feed reads commodity code sync output (generic name)
- product reverse lookup feed (generic name) reads product record sync output (generic name)
- part stock feed reads part master sync output (generic name)
- part sales feed reads part master sync output (generic name), product record sync output (generic
  name)
- invoice feed reads account sync output (generic name), customer sync output (generic name),
  salesperson sync output (generic name)
- invoice line feed reads invoice sync output (generic name), part master sync output (generic name)
- standard price book feed (new entries) reads product record sync output (generic name)
- standard price book feed (changes) reads product record sync output (generic name)
- product feed reads part master sync output (generic name)
- product structure feed reads part master sync output (generic name), product record sync output
  (generic name)
- sales order feed reads account sync output (generic name), customer sync output (generic name),
  salesperson sync output (generic name)
- sales order line feed reads sales order sync output (generic name), part master sync output
  (generic name)

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Sales Person | Commercient Sales Rep Master Managed Custom Object | 25 | Sales rep → Commercient external key (Exact package), Salesperson name → Name, Sales territory → Commercient sales territory, Company code (sales rep master) → Commercient company code, Site code (sales rep master) → Commercient site code |
| Account | Account | 17 | Customer identifier → Commercient AR customer code, Customer name → Name, Customer phone → Phone, the linked Salesforce record → Commercient Exact Max sales rep (related record), Customer type → Type |
| Customer Master | Commercient Exact Max Customer Master Managed Custom Object | 74 | Customer identifier → Commercient external key (Exact package), Customer identifier → Commercient account (related record), Discount rate → Commercient discount rate, Credit limit → Commercient credit limit, Sales month to date → Commercient sales month to date |
| Customer Reverse Lookup Account | Account | 2 | Customer identifier → Commercient AR customer code, the linked Salesforce record → Commercient Exact Max customer master (related record) |
| Shipping Master | Commercient Shipping Master Managed Custom Object | 36 | Customer identifier (shipping master),Shipping address code → Commercient external key (Exact package), Customer identifier (shipping master),Shipping address code → Commercient record name, Customer identifier (shipping master) → Account, Customer identifier (shipping master) → Commercient Exact Max Customer Master Managed Custom Object, Shipping address name → Commercient shipping address name |
| Commodity Codes | Commercient Commodity Codes Managed Custom Object | 15 | Commodity code (commodity master) → Commercient external key (Exact package), Commodity code (commodity master) → Commercient record name, Commodity code (commodity master) → Commercient commodity code, Commodity description → Commercient commodity description, User defined field key → Commercient user defined key |
| Part Master | Commercient Part Master Managed Custom Object | 131 | Part number → Commercient external key (Exact package), Part number → Name, Part type → Commercient part type, Part commodity code → Commercient Exact Max commodity code (related record), Part class code → Commercient part class code |
| Sales Order Header | Commercient Sales Order Master Managed Custom Object | 71 | Order number → Commercient external key (Exact package), Commission split 1 → Commercient commission split 1, Commission split 2 → Commercient commission split 2, Commission split 3 → Commercient commission split 3, Order commission → Commercient order commission |
| Sales Order Line | Commercient Sales Order Detail Managed Custom Object | 76 | Order number (sales order line),Line number (sales order line),Delivery number (sales order line) → Commercient external key (Exact package), Order number (sales order line),Line number (sales order line),Delivery number (sales order line) → Commercient record name, Delivery number (sales order line) → Commercient delivery number, Order line status → Commercient line status, Customer identifier (sales order line) → Commercient customer identifier (sales order line) |
| Invoice Header | Commercient Invoice Master Managed Custom Object | 83 | Invoice order number,Invoice number → Commercient external key (Exact package), Invoice order number,Invoice number → Commercient record name, Customer identifier (invoice header) → Account, Customer identifier (invoice header) → Commercient Exact Max customer master (related record), Sales rep 1 (invoice header) → Commercient Exact Max sales rep (related record) |
| Invoice Line | Commercient Invoice Line Managed Custom Object | 60 | Invoice line order number,Line number (invoice line),Delivery number (invoice line),Invoice number (invoice line) → Commercient external key (Exact package), Invoice line order number,Line number (invoice line),Delivery number (invoice line),Invoice number (invoice line) → Commercient record name, the linked Salesforce record → Commercient Invoice Master Managed Custom Object, Invoice line price → Commercient invoice line price, Original quantity (invoice line) → Commercient original quantity |
| Product | Product | 6 | Part number → Commercient external key (earlier package), Part description 1 → Name, Part number → Product code, Part description 2 → Description, Active → Active |
| Product Reverse Lookup | Commercient Part Master Managed Custom Object | 2 | Part number → Commercient external key (Exact package), the linked Salesforce record → Commercient Product Managed Custom Object |
| Create Standard Price Book | Price book entry | 5 | Part number → External key (custom field), the linked Salesforce record → price book lookup, the linked Salesforce record → product lookup, Active → Active, Price (part sales) → Unit price |
| Part Stock | Commercient Part Stock Managed Custom Object | 39 | Part stock record identifier → Commercient external key (Exact package), the linked Salesforce record → Commercient Part Master Managed Custom Object, Part stock record identifier → Name, Part number (part stock) → Part number (part stock), Stockroom → Stockroom |
| Update Standard Pricebook | Price book entry | 3 | Part number → External key (custom field), Active → Active, Price (part sales) → Unit price |
| Part Sales | Commercient Part Sales Managed Custom Object | 84 | Part number (part sales) → Commercient external key (Exact package), Part number (part sales) → Name, Sales category → Sales category, Part description 1 (part sales) → Part description 1 (part sales), Part description 2 (part sales) → Part description 2 (part sales) |
| Product Structure | Commercient Product Structure Managed Custom Object | 33 | Parent part number → Commercient parent part number, name → Commercient record name, Component part number → Commercient component part number, Effective date → Commercient effective date, Filler field → Commercient filler field |

## 6. Community templates

The catalogue carries 44 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 44
- Default operations: insert on 44, update on 44, delete on 44
- Marked as circular sync: 0
- Licence groups they span: 8
- Destination objects: Account, Commercient Part Master Managed Custom Object, Commercient Sales
  Order Detail Managed Custom Object, Commercient Commodity Codes Managed Custom Object, Commercient
  Exact Max Customer Master Managed Custom Object, Commercient Invoice Line Managed Custom Object,
  Commercient Invoice Master Managed Custom Object, Commercient Part Sales Managed Custom Object,
  Commercient Sales Rep Master Managed Custom Object, Commercient Shipping Master Managed Custom
  Object, Commercient Sales Order Master Managed Custom Object, Price book entry, Product, account,
  Commercient Code Master Managed Custom Object, Commercient Matching Managed Custom Object,
  Commercient Part Stock Managed Custom Object, Commercient Product Structure Managed Custom Object,
  Customer Part Data (custom object), 3 more and 2 custom objects
- Object display names: Account, Commodity Codes, Create Standard Price Book, Customer Master,
  Invoice Header, Invoice Line, Part Master, Part Sales, Product, Product Reverse Lookup, Sales
  Order Header, Sales Order Line, 15 more and 2 further templates
- Template groups: Account, Product, Sales order, Invoice, Customer Multi Ship Addresses

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/exact-max`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Exact MAX → Salesforce templates set up. dlake-crmpro-salesforce is the destination
skill this page sits under: its own text is the authority for the Salesforce conventions that hold
across every ERP, and its ERP table lists this page alongside every sibling ERP page for this
destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/exact-max`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
