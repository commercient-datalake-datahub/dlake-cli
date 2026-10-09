---
name: dlake-crmpro-salesforce/erps/acumatica-cloud
kind: erp-summary
description: >-
  Use it when standing up or reading an Acumatica Cloud → Salesforce template set, when deciding
  which templates to import and activate, or when a run completes without pushing records and the
  answer is in the view or the configuration row. It extends dlake-crmpro, which covers operating
  CRMPro generally, and dlake-crmpro-salesforce, the destination skill this page is a child of,
  which carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Acumatica Cloud: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/acumatica-cloud` (or `list_skills`) against the
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
| **Get User** | The templates push User to Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | User | — |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Contact, Commercient Acumatica Customer Managed Custom Object, Commercient Acumatica Employee Managed Custom Object | sales reps, salespeople, customers, customer locations, customer contacts, contacts |
| **AR Invoice Payments** | Commercient Syncs the payment details that are held in the receivables file and held against an invoice in the ERP. The amount received from a customer, the date of payment, the method of payment, whether partial or full payment, etc New records are created and existing ones updated; none are deleted. | Commercient Acumatica Payment Managed Custom Object, Commercient Acumatica Payment Detail Managed Custom Object | payments, payment details |
| **CRM Opportunity and Line** | ERP sales order, sales order detail data becomes Opportunity, Opportunity line item in Salesforce. New records are created and existing ones updated; none are deleted. | Opportunity, Opportunity line item | sales orders, sales order details |
| **CRM Quote and Line** | ERP sales order, sales order detail data becomes Quote, Quote line item in Salesforce. New records are created and existing ones updated; none are deleted. | Quote, Quote line item | sales orders, sales order details |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created and existing ones updated; none are deleted. | Commercient Acumatica Contact Managed Custom Object | contacts, customer contacts |
| **Product** | ERP stock item data becomes Commercient Stock Item Managed Custom Object, Product in Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Stock Item Managed Custom Object, Product | stock items |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created and existing ones updated; none are deleted. | Commercient Acumatica Sales Invoice Managed Custom Object, Commercient Acumatica Sales Invoice Detail Managed Custom Object | sales invoices, sales invoice lines |
| **Pricebook** | ERP stock item data becomes Price book entry in Salesforce. New records are created and existing ones updated; none are deleted. | Price book entry | stock items |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created and existing ones updated; none are deleted. | Commercient Acumatica Sales Order Managed Custom Object, Commercient Acumatica Sales Order Details Managed Custom Object | sales orders, sales order details |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Account | Account | Commercient AR customer code | 1 |
| Acumatica Salesperson | Commercient Acumatica Employee Managed Custom Object | Commercient external key (Acumatica and MYOB package) | 1 |
| Acumatica Cloud Customer | Commercient Acumatica Customer Managed Custom Object | Commercient external key (Acumatica and MYOB package) | 2 |
| Acumatica Cloud Address | Commercient Acumatica Contact Managed Custom Object | Commercient external key (Acumatica and MYOB package) | 3 |
| Product | Product | Commercient external key (earlier package) | 4 |
| Acumatica Cloud Item Master | Commercient Stock Item Managed Custom Object | Commercient external key (Acumatica and MYOB package) | 5 |
| Acumatica Cloud Sales order | Commercient Acumatica Sales Order Managed Custom Object | Commercient external key (Acumatica and MYOB package) | 6 |
| Acumatica Cloud Sales order detail | Commercient Acumatica Sales Order Details Managed Custom Object | Commercient external key (Acumatica and MYOB package) | 7 |
| Acumatica Cloud Invoice header | Commercient Acumatica Sales Invoice Managed Custom Object | Commercient external key (Acumatica and MYOB package) | 8 |
| Acumatica Cloud Invoice detail | Commercient Acumatica Sales Invoice Detail Managed Custom Object | Commercient external key (Acumatica and MYOB package) | 9 |
| Acumatica Cloud Payment | Commercient Acumatica Payment Managed Custom Object | Commercient external key (Acumatica and MYOB package) | 10 |
| Acumatica Cloud Payment detail | Commercient Acumatica Payment Detail Managed Custom Object | Commercient external key (Acumatica and MYOB package) | 11 |
| Create Standard Price Book | Price book entry | External key (custom field) | 12 |
| Update Standard Pricebook | Price book entry | External key (custom field) | 13 |
| Opportunity | Opportunity | External key (custom field) | 14 |
| Contact | Contact | Commercient external key (custom field) | 15 |
| Opportunity line item | Opportunity line item | External key (custom field) | 15 |
| Get User | User | Id | 17 |
| Quote | Quote | External key (custom field) | 22 |
| Quote Line | Quote line item | External key (custom field) | 23 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| account feed | insert + update | sales reps, salespeople, customers, customer locations, customer contacts |
| salesperson feed | insert + update | sales reps, salespeople |
| customer feed | insert + update | customers |
| address feed | insert + update | contacts, customer contacts |
| product feed | insert + update | stock items |
| item master feed | insert + update | stock items |
| sales order feed | insert + update | sales orders |
| sales order line feed | insert + update | sales order details |
| invoice feed | insert + update | sales invoices |
| invoice line feed | insert + update | sales invoice lines |
| payment feed | insert + update | payments |
| payment detail feed | insert + update | payment details |
| price book feed (new entries) | insert + update | stock items |
| price book feed (changes) | insert + update | stock items |
| opportunity feed | insert only | sales orders |
| contact feed | insert + update | contacts, customer contacts, sales reps |
| opportunity line feed | insert + update | sales order details |
| quote feed | insert + update | sales orders |
| quote line feed | insert + update | sales order details |

## 4. Order of work

The templates set run sequence from 1 to 23. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 1 — Account, Acumatica Salesperson
- 2 — Acumatica Cloud Customer
- 3 — Acumatica Cloud Address
- 4 — Product
- 5 — Acumatica Cloud Item Master
- 6 — Acumatica Cloud Sales order
- 7 — Acumatica Cloud Sales order detail
- 8 — Acumatica Cloud Invoice header
- 9 — Acumatica Cloud Invoice detail
- 10 — Acumatica Cloud Payment
- 11 — Acumatica Cloud Payment detail
- 12 — Create Standard Price Book
- 13 — Update Standard Pricebook
- 14 — Opportunity
- 15 — Contact, Opportunity line item
- 17 — Get User
- 22 — Quote
- 23 — Quote Line

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- account feed reads salesperson sync output, user sync output
- payment feed reads account sync output, customer sync output
- payment detail feed reads payment sync output
- contact feed reads account sync output
- opportunity feed reads account sync output
- opportunity line feed reads product sync output, opportunity sync output
- quote feed reads account sync output, opportunity sync output
- quote line feed reads Salesforce quote sync output (generic name), product sync output
- customer feed reads account sync output
- address feed reads account sync output, customer sync output
- item master feed reads product sync output
- invoice feed reads account sync output, customer sync output
- invoice line feed reads invoice sync output, item master sync output
- price book feed (new entries) reads product sync output, standard price book sync output (generic
  name); no template in this set writes standard price book sync output (generic name)
- price book feed (changes) reads product sync output, standard price book sync output (generic
  name); no template in this set writes standard price book sync output (generic name)
- sales order feed reads account sync output, customer sync output
- sales order line feed reads sales order sync output, item master sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Account | Account | 24 | Customer identifier → Commercient AR customer code, Customer name → Name, Customer identifier → ERP unique identifier (custom field), Display name → Doing business as name (custom field), Email → Account emails (custom field) |
| Acumatica Salesperson | Commercient Acumatica Employee Managed Custom Object | 5 | Commission → Commission (custom field), Employee identifier → Commercient employee identifier, Location identifier → Location identifier (custom field), Active → Is active (custom field), Location name → Location name (custom field) |
| Acumatica Cloud Customer | Commercient Acumatica Customer Managed Custom Object | 49 | Customer identifier → Commercient external key (Acumatica and MYOB package), Customer name → Name, Account reference → Account reference, Apply overdue charges → Apply overdue charges, Automatically apply payments → Automatically apply payments |
| Acumatica Cloud Address | Commercient Acumatica Contact Managed Custom Object | 67 | Contact identifier, Type → Commercient external key (Acumatica and MYOB package), Customer identifier → Account, Customer identifier → Commercient Acumatica customer (related record), Display name, Contact identifier, Type → Name, Active → Active (custom field) |
| Product | Product | 6 | Inventory identifier → Commercient external key (earlier package), Description → Name, Inventory identifier → Product code, Description → Description, Item status → Active |
| Acumatica Cloud Item Master | Commercient Stock Item Managed Custom Object | 74 | Inventory identifier → Commercient external key (Acumatica and MYOB package), the linked Salesforce record → Commercient product (related record), Inventory identifier → Commercient name, Inventory classification code → Commercient inventory classification code, Auto incremental value → Commercient auto incremental value |
| Acumatica Cloud Sales order | Commercient Acumatica Sales Order Managed Custom Object | 64 | Order number → Commercient external key (Acumatica and MYOB package), Customer identifier → Account, Customer identifier → Commercient Acumatica customer (related record), Order number → Name, Approved → Approved (custom field) |
| Acumatica Cloud Sales order detail | Commercient Acumatica Sales Order Details Managed Custom Object | 46 | Order number, Line number → Commercient external key (Acumatica and MYOB package), the linked Salesforce record → Commercient Acumatica sales order (related record), the linked Salesforce record → Commercient Acumatica item master (related record), Order number → Order number, account → account |
| Acumatica Cloud Invoice header | Commercient Acumatica Sales Invoice Managed Custom Object | 17 | Reference number → Commercient external key (Acumatica and MYOB package), Customer identifier → Commercient account identifier, Customer identifier → Commercient Acumatica Customer Managed Custom Object, Reference number → Name, Amount → Amount |
| Acumatica Cloud Invoice detail | Commercient Acumatica Sales Invoice Detail Managed Custom Object | 16 | Sales invoice number,Line number → Commercient external key (Acumatica and MYOB package), Sales invoice number,Line number → Commercient name, the linked Salesforce record → Commercient Acumatica sales invoice (related record), the linked Salesforce record → Commercient Acumatica item master (related record), Reference number → Commercient reference number |
| Acumatica Cloud Payment | Commercient Acumatica Payment Managed Custom Object | 21 | Reference number → Commercient external key (Acumatica and MYOB package), Customer identifier → Account (custom field), Customer identifier → Commercient Acumatica customer (related record), Reference number → Name, Application date → Application date |
| Acumatica Cloud Payment detail | Commercient Acumatica Payment Detail Managed Custom Object | 10 | Payment header reference number,Reference number → Commercient external key (Acumatica and MYOB package), the linked Salesforce record → Commercient Acumatica payment (related record), Payment header reference number,Reference number → Name, Amount paid → Amount paid, Balance write off → Balance write off |
| Create Standard Price Book | Price book entry | 5 | Inventory identifier → External key (custom field), price book lookup → price book lookup, the linked Salesforce record → product lookup, Active → Active, Current standard cost → Unit price |
| Update Standard Pricebook | Price book entry | 3 | Inventory identifier → External key (custom field), Active → Active, Current standard cost → Unit price |
| Opportunity | Opportunity | 8 | Order number → External key (custom field), Customer identifier → account lookup, Description → Name, Effective date → close date property, Free on board point → Free on board (custom field) |
| Contact | Contact | 20 | Commercient external key column → Commercient external key (custom field), account lookup → account lookup, Family name → Family name, Given name → Given name, Suffix → Suffix |
| Opportunity line item | Opportunity line item | 5 | Order number, Line number, Line type → External key (custom field), the linked Salesforce record → price book entry lookup, the linked Salesforce record → Opportunity, Unit price → Unit price, Order quantity → Quantity |
| Quote | Quote | 17 | Order number → External key (custom field), Order number → Name, the linked Salesforce record → Opportunity identifier, the linked Salesforce record → account lookup, the linked Salesforce record → price book lookup |
| Quote Line | Quote line item | 6 | Order number, Line number, Line type → External key (custom field), returned inventory identifier → product lookup, price book entry lookup → price book entry lookup, Order number → Quote, Order quantity → Quantity |

## 6. Community templates

The catalogue carries 170 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 170
- Default operations: insert on 170, update on 170, delete on 170
- Marked as circular sync: 0
- Licence groups they span: 12
- Destination objects: Account, Product, Price book entry, Commercient Acumatica Customer Managed
  Custom Object, Commercient Acumatica Sales Invoice Managed Custom Object, Commercient Acumatica
  Sales Invoice Detail Managed Custom Object, Commercient Acumatica Sales Order Managed Custom
  Object, Commercient Acumatica Sales Order Details Managed Custom Object, Commercient Stock Item
  Managed Custom Object, Commercient Acumatica Contact Managed Custom Object, Commercient Acumatica
  Employee Managed Custom Object, Contact, Acumatica item warehouse (custom object), Commercient
  Account Matching Managed Custom Object, Quote, Commercient Acumatica Payment Detail Managed Custom
  Object, Commercient Contact Matching Managed Custom Object, Opportunity, Acumatica customer credit
  card (custom object), Commercient Acumatica Payment Managed Custom Object and 16 more
- Object display names: Account, Acumatica Cloud Invoice detail, Acumatica Cloud Invoice header,
  Acumatica Cloud Customer, Acumatica Cloud Item Master, Product, Acumatica Cloud Sales order,
  Acumatica Cloud Sales order detail, Update Standard Pricebook, Acumatica Cloud Address, Create
  Standard Price Book, Sync Item to product lookup, 48 more and 2 further templates
- Template groups: Account, Product, Invoice, Sales order, CRM Quote and Line, Customer Multi Ship
  Addresses, CRM Opportunity and Line

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/acumatica-cloud`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Acumatica Cloud → Salesforce templates set up. dlake-crmpro-salesforce is the
destination skill this page sits under: its own text is the authority for the Salesforce conventions
that hold across every ERP, and its ERP table lists this page alongside every sibling ERP page for
this destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/acumatica-cloud`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
