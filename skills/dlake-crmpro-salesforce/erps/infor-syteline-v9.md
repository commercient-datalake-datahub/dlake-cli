---
name: dlake-crmpro-salesforce/erps/infor-syteline-v9
kind: erp-summary
description: >-
  Use it when standing up or reading an Infor SyteLine version 9 → Salesforce template set, when
  deciding which templates to import and activate, or when a run completes without pushing records
  and the answer is in the view or the configuration row. It extends dlake-crmpro, which covers
  operating CRMPro generally, and dlake-crmpro-salesforce, the destination skill this page is a
  child of, which carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Infor SyteLine version 9: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/infor-syteline-v9` (or `list_skills`) against
the Commercient admin plane. Existing customers who need access or help: contact
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
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Contact, Commercient Customer Managed Custom Object, Commercient Salesperson Managed Custom Object | customers, customer addresses, customer contacts, contact master records, salespeople |
| **CRM Opportunity and Line** | The templates push Opportunity, Opportunity line item to Salesforce. New records are created and existing ones updated; none are deleted. | Opportunity, Opportunity line item | customer orders, customer order lines |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created and existing ones updated; none are deleted. | Commercient Customer Address Managed Custom Object | customer addresses, customers |
| **Product** | ERP item master, item price, item warehouse (all sites) data becomes Commercient Item Managed Custom Object, Commercient Item Warehouse Managed Custom Object, Product in Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Item Managed Custom Object, Commercient Item Warehouse Managed Custom Object, Product | items (all sites), item warehouse quantities (all sites) |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created and existing ones updated; none are deleted. | Commercient Invoice Header Managed Custom Object, Commercient Invoice Line Managed Custom Object | invoice headers, invoice lines |
| **Pricebook** | ERP item price data becomes Price book entry, Price book entry in Salesforce. New records are created and existing ones updated; none are deleted. | Price book entry | item prices (all sites), item prices |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created and existing ones updated; none are deleted. | Commercient Customer Order Managed Custom Object, Commercient Customer Order Line Managed Custom Object | customer orders, customer order lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Get User | User | — | 0 |
| Sync Salesperson | Commercient Salesperson Managed Custom Object | Commercient external key (Infor package) | 1 |
| Sync Account | Account | Commercient AR customer code | 2 |
| Sync Ship to account | Account | Commercient AR customer code | 3 |
| Sync Customer | Commercient Customer Managed Custom Object | Commercient external key (Infor package) | 4 |
| Sync Customer to account lookup | Account | Commercient AR customer code | 5 |
| Sync Contact | Contact | External key (custom field) | 5 |
| Sync Address | Commercient Customer Address Managed Custom Object | Commercient external key (Infor package) | 6 |
| Sync Product object | Product | Commercient external key (earlier package) | 7 |
| Sync Item | Commercient Item Managed Custom Object | Commercient external key (Infor package) | 8 |
| Sync Item to product lookup | Product | Commercient external key (earlier package) | 9 |
| Sync Item warehouse | Commercient Item Warehouse Managed Custom Object | Commercient external key (Infor package) | 10 |
| Sync standard price book sync output (generic name) | Price book entry | External key (custom field) | 11 |
| Sync standard price book sync output (generic name) update | Price book entry | External key (custom field) | 12 |
| Sync Customer order | Commercient Customer Order Managed Custom Object | Commercient external key (Infor package) | 13 |
| Estimate | Opportunity | External key (custom field) | 14 |
| Sync Customer order line | Commercient Customer Order Line Managed Custom Object | Commercient external key (Infor package) | 14 |
| Estimate line | Opportunity line item | External key (custom field) | 15 |
| Sync invoice header | Commercient Invoice Header Managed Custom Object | Commercient external key (Infor package) | 15 |
| Sync Invoice line item | Commercient Invoice Line Managed Custom Object | Commercient external key (Infor package) | 16 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| salesperson feed | insert + update | salespeople |
| account feed | insert + update | customers, customer addresses |
| shipping account feed | insert + update | customers, customer addresses |
| customer feed | insert + update | customers, customer addresses |
| customer account lookup feed | insert + update | customers |
| contact feed | insert + update | customer contacts, contact master records |
| address feed | insert + update | customer addresses, customers |
| product feed | insert + update | items (all sites) |
| item feed | insert + update | items (all sites) |
| item product lookup feed | insert + update | items (all sites) |
| item warehouse feed | insert + update | item warehouse quantities (all sites) |
| standard price book feed | insert + update | item prices (all sites) |
| standard price book feed (changes) | insert + update | item prices |
| customer order feed | insert + update | customer orders |
| estimate feed | insert + update | customer orders |
| customer order line feed | insert + update | customer order lines |
| estimate line feed | insert + update | customer order lines |
| invoice feed | insert + update | invoice headers |
| invoice line item feed | insert + update | invoice lines |

## 4. Order of work

The templates set run sequence from 0 to 16. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 0 — Get User
- 1 — Sync Salesperson
- 2 — Sync Account
- 3 — Sync Ship to account
- 4 — Sync Customer
- 5 — Sync Customer to account lookup, Sync Contact
- 6 — Sync Address
- 7 — Sync Product object
- 8 — Sync Item
- 9 — Sync Item to product lookup
- 10 — Sync Item warehouse
- 11 — Sync standard price book sync output (generic name)
- 12 — Sync standard price book sync output (generic name) update
- 13 — Sync Customer order
- 14 — Estimate, Sync Customer order line
- 15 — Estimate line, Sync invoice header
- 16 — Sync Invoice line item

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- account feed reads user sync output
- shipping account feed reads user sync output, account sync output
- customer account lookup feed reads customer sync output
- contact feed reads shipping account sync output
- estimate feed reads account sync output, customer sync output, standard price book sync output
- estimate line feed reads estimate sync output, product sync output
- customer feed reads account sync output
- address feed reads account sync output, customer sync output
- item feed reads product sync output
- item warehouse feed reads product sync output, item sync output
- invoice feed reads account sync output, customer sync output
- invoice line item feed reads invoice sync output, item sync output
- standard price book feed reads product sync output
- standard price book feed (changes) reads product sync output
- item product lookup feed reads item sync output
- customer order feed reads account sync output, customer sync output
- customer order line feed reads customer order sync output, item sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Sync Salesperson | Commercient Salesperson Managed Custom Object | 13 | Site reference, Salesperson code → Commercient external key (Infor package), Site reference, Salesperson code → Name, Site reference → Commercient site, Salesperson code → Commercient salesperson code, outside → Commercient outside salesperson |
| Sync Account | Account | 12 | Site reference,ERP customer number,Customer sequence number → Commercient AR customer code, name → Name, city property → Billing city, state → Billing state, country → Billing country |
| Sync Ship to account | Account | 13 | Site reference,ERP customer number,Customer sequence number → Commercient AR customer code, Bill to name → Name, Bill to city → Billing city, Bill to state → Billing state, Bill to country → Billing country |
| Sync Customer | Commercient Customer Managed Custom Object | 110 | Site reference, ERP customer number, Customer sequence number → Commercient external key (Infor package), name → Commercient name, Site reference → Commercient site, ERP customer number → Commercient customer number, Customer sequence number → Commercient customer sequence |
| Sync Customer to account lookup | Account | 2 | Site reference, ERP customer number, Customer sequence number → Commercient AR customer code, the linked Salesforce record → Commercient customer (related record) |
| Sync Contact | Contact | 13 | first name property → first name property, last name property → last name property, Phone → Phone, Email → Email, Title → Title |
| Sync Address | Commercient Customer Address Managed Custom Object | 45 | Site reference,ERP customer number,Customer sequence number → Commercient external key (Infor package), name → Commercient name, city property → Commercient city, state → Commercient state, postal code property → Commercient postal code |
| Sync Product object | Product | 4 | Site reference, item → Commercient external key (earlier package), Site reference, item → Name, Site reference, item → Product code, description → Description |
| Sync Item | Commercient Item Managed Custom Object | 71 | Site reference, item → Commercient external key (Infor package), item → Name, Site reference → Commercient site, item → Commercient item, description → Commercient description |
| Sync Item to product lookup | Product | 2 | Site reference, item → Commercient external key (earlier package), the linked Salesforce record → Commercient Infor SyteLine item (related record) |
| Sync Item warehouse | Commercient Item Warehouse Managed Custom Object | 53 | Site reference,item,Warehouse → Commercient external key (Infor package), Site reference,item,Warehouse → Commercient name, Site reference → Commercient site, Warehouse → Commercient warehouse, Quantity on hand → Commercient quantity on hand |
| Sync standard price book sync output (generic name) | Price book entry | 5 | Site reference, item, Currency code → External key (custom field), the linked Salesforce record → price book lookup, the linked Salesforce record → product lookup, Active → Active, Unit price 1 → Unit price |
| Sync standard price book sync output (generic name) update | Price book entry | 3 | Site reference, item, Currency code → External key (custom field), Active → Active, Unit price 1 → Unit price |
| Sync Customer order | Commercient Customer Order Managed Custom Object | 54 | Site reference,Customer order number → Commercient external key (Infor package), Site reference,Customer order number → Commercient name, Site reference → Commercient site, type → Commercient type, Customer order number → Commercient customer order number |
| Estimate | Opportunity | 7 | Customer order number → External key (custom field), Customer order number → Name, price → Amount, Close date → close date property, Stage → Stage |
| Sync Customer order line | Commercient Customer Order Line Managed Custom Object | 80 | Site reference, Customer order number, Customer order line, Customer order release → Commercient external key (Infor package), Site reference, Customer order number, Customer order line, Customer order release → Commercient name, Site reference → Commercient site, Customer order number → Commercient customer order number, Customer order line → Commercient customer order line number |
| Estimate line | Opportunity line item | 5 | Customer order number, Customer order line, Customer order release → External key (custom field), item → product lookup, Quantity ordered → Quantity, price → Unit price, Customer order number → Opportunity |
| Sync invoice header | Commercient Invoice Header Managed Custom Object | 33 | Site reference, Invoice number, Invoice sequence → Commercient external key (Infor package), Site reference, Invoice number, Invoice sequence → Commercient name, Site reference → Commercient site, Invoice number → Commercient invoice number, Invoice sequence → Commercient invoice sequence |
| Sync Invoice line item | Commercient Invoice Line Managed Custom Object | 23 | Site reference,Invoice number,Invoice sequence,Invoice line number,Customer order number,Customer order line,Customer order release → Commercient external key (Infor package), Site reference,Invoice number,Invoice sequence,Invoice line number,Customer order number,Customer order line,Customer order release → Commercient name, Site reference → Commercient site, Invoice number → Commercient invoice number, Invoice sequence → Commercient invoice sequence |

## 6. Community templates

The catalogue carries 90 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 90
- Default operations: insert on 90, update on 90, delete on 90
- Marked as circular sync: 5
- Licence groups they span: 12
- Destination objects: Account, Product, Commercient Customer Order Line Managed Custom Object,
  Price book entry, Commercient Customer Order Managed Custom Object, Commercient Customer Address
  Managed Custom Object, Commercient Customer Managed Custom Object, Commercient Invoice Header
  Managed Custom Object, Commercient Invoice Line Managed Custom Object, Commercient Item Managed
  Custom Object, Commercient Item Warehouse Managed Custom Object, Commercient Salesperson Managed
  Custom Object, Contact, Commercient Account Matching Managed Custom Object, Credit Memo (custom
  object), Contract Price (custom object), Item Customer Price (custom object), Job Master (custom
  object), Opportunity, 5 more and 6 custom objects
- Object display names: Sync Address, Sync Customer, Sync Customer order, Sync Customer order line,
  Sync Customer to account lookup, Sync invoice header, Sync Invoice line item, Sync Item, Sync Item
  to product lookup, Sync Item warehouse, Sync Product object, Sync Salesperson, 25 more and 5
  further templates
- Template groups: Product, Account, Sales order, Invoice, Customer Multi Ship Addresses, CRM
  Opportunity and Line, Purchase Order

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/infor-syteline-v9`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Infor SyteLine version 9 → Salesforce templates set up. dlake-crmpro-salesforce is the
destination skill this page sits under: its own text is the authority for the Salesforce conventions
that hold across every ERP, and its ERP table lists this page alongside every sibling ERP page for
this destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/infor-syteline-v9`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
