---
name: dlake-crmpro-salesforce/erps/sage-100-france
kind: erp-summary
description: >-
  Use it when standing up or reading a Sage 100 France → Salesforce template set, when deciding
  which templates to import and activate, or when a run completes without pushing records and the
  answer is in the view or the configuration row. It extends dlake-crmpro, which covers operating
  CRMPro generally, and dlake-crmpro-salesforce, the destination skill this page is a child of,
  which carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Sage 100 France: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/sage-100-france` (or `list_skills`) against the
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
| **GET USER** | The templates push users to Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | users | — |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Commercient Sage 100 French Third Party Account Managed Custom Object, Commercient Sage 100 France Customer Price Book (custom object), Commercient Collaborator Managed Custom Object | third party accounts, delivery addresses, customer items, items, collaborators |
| **CRM Opportunity and Line** | ERP third party account, document header, document line data becomes Opportunity in Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | Opportunity | document lines, document headers, third party accounts, calculated total amount |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Sage 100 France Shipping Address Managed Custom Object | delivery addresses |
| **Product** | ERP article, document line, supplier article data becomes Commercient Sage 100 France Item Managed Custom Object, Product, Price book entry in Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Sage 100 France Item Managed Custom Object, Product, Price book entry, Commercient Sage 100 France Vendor Price Book (custom object) | items, document lines, weighted average unit cost, supplier items, third party accounts |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Sage 100 French Invoice Header Managed Custom Object, Commercient Sage 100 French Invoice Line Managed Custom Object | document headers, delivery addresses, third party accounts, document lines |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Sage 100 French Sales Order Header Managed Custom Object, Commercient Sage 100 French Sales Order Line Managed Custom Object | document headers, document lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| GET USER | users | — | 0 |
| Sync Salesperson | Commercient Collaborator Managed Custom Object | Commercient external key | 1 |
| Account | Account | Commercient AR customer code | 2 |
| Customer Sync | Commercient Sage 100 French Third Party Account Managed Custom Object | Commercient external key | 3 |
| Customer to account lookup | Account | Commercient AR customer code | 4 |
| Ship to customer Sync | Commercient Sage 100 France Shipping Address Managed Custom Object | Commercient external key | 5 |
| Product | Product | Commercient external key (earlier package) | 6 |
| Sage 100 FR Item master | Commercient Sage 100 France Item Managed Custom Object | Commercient external key | 7 |
| Sales order Header Sync | Commercient Sage 100 French Sales Order Header Managed Custom Object | Commercient external key | 8 |
| Sales order line Sync | Commercient Sage 100 French Sales Order Line Managed Custom Object | Commercient external key | 9 |
| Invoice Header Sync | Commercient Sage 100 French Invoice Header Managed Custom Object | Commercient external key | 10 |
| Invoice Line Sync | Commercient Sage 100 French Invoice Line Managed Custom Object | Commercient external key | 11 |
| Sync Vendor | Account | Commercient AR customer code | 12 |
| Item TO Product object Lookup | Product | Commercient external key (earlier package) | 13 |
| Standard price book create | Price book entry | External key (custom field) | 14 |
| Standard price book update | Price book entry | External key (custom field) | 15 |
| Opportunity | Opportunity | External key (custom field) | 16 |
| Sage 100 fr Customer price book | Commercient Sage 100 France Customer Price Book (custom object) | External key (custom field) | 17 |
| Sage 100 FR Vendor price book | Commercient Sage 100 France Vendor Price Book (custom object) | External key (custom field) | 18 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| salesperson feed | insert + update | collaborators |
| account feed | insert + update | third party accounts, delivery addresses |
| customer feed | insert + update | third party accounts |
| customer account lookup feed | insert + update | third party accounts |
| shipping address feed | insert + update | delivery addresses |
| product feed | insert + update | document lines, items, weighted average unit cost |
| item master feed | insert + update | items |
| sales order feed | insert + update | document headers |
| sales order line feed | insert only | document lines, document headers |
| invoice feed | insert + update | document headers, delivery addresses, third party accounts |
| invoice line feed | insert + update | document lines, document headers |
| vendor feed | insert + update | third party accounts, delivery addresses |
| item reverse lookup feed | insert + update | items |
| price book feed (new entries) | insert only | items |
| price book feed (changes) | insert only | items |
| opportunity feed | insert + update | document lines, document headers, third party accounts, calculated total amount |
| customer price book feed | insert + update | customer items, third party accounts, items |
| vendor price book feed | insert + update | supplier items, third party accounts, items |

## 4. Order of work

The templates set run sequence from 0 to 18. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 0 — GET USER
- 1 — Sync Salesperson
- 2 — Account
- 3 — Customer Sync
- 4 — Customer to account lookup
- 5 — Ship to customer Sync
- 6 — Product
- 7 — Sage 100 FR Item master
- 8 — Sales order Header Sync
- 9 — Sales order line Sync
- 10 — Invoice Header Sync
- 11 — Invoice Line Sync
- 12 — Sync Vendor
- 13 — Item TO Product object Lookup
- 14 — Standard price book create
- 15 — Standard price book update
- 16 — Opportunity
- 17 — Sage 100 fr Customer price book
- 18 — Sage 100 FR Vendor price book

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- customer account lookup feed reads account sync output, customer sync output
- opportunity feed reads account sync output
- customer feed reads account sync output
- customer price book feed reads product sync output, account sync output
- shipping address feed reads account sync output, customer sync output
- item master feed reads product sync output
- invoice feed reads account sync output, customer sync output
- invoice line feed reads account sync output, customer sync output, invoice sync output, product
  sync output, item master sync output
- item reverse lookup feed reads product sync output, item master sync output
- price book feed (new entries) reads product sync output
- price book feed (changes) reads product sync output
- vendor price book feed reads product sync output, account sync output
- sales order feed reads account sync output, customer sync output
- sales order line feed reads account sync output, customer sync output, sales order sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Sync Salesperson | Commercient Collaborator Managed Custom Object | 12 | Sales rep number → Commercient sales rep number, Sales rep address → Commercient sales rep address, Sales rep postal code → Sales rep postal code, Sales rep city → Commercient sales rep city, Sales rep region → Sales rep region |
| Account | Account | 14 | Commercient AR customer code column → Commercient AR customer code, Commercient Salesperson Managed Custom Object → Commercient Salesperson Managed Custom Object, Type → Type, Phone → Phone, Billing street → Billing street |
| Customer Sync | Commercient Sage 100 French Third Party Account Managed Custom Object | 103 | account lookup → Commercient account (related record), Sales rep number → sales rep number (custom field), → indexed sales rep number (custom field), Warehouse number → warehouse number (custom field), → indexed warehouse number (custom field) |
| Customer to account lookup | Account | 2 | Commercient AR customer code column → Commercient AR customer code, Commercient Sage 100 client (related record) → Commercient Sage 100 client (related record) |
| Ship to customer Sync | Commercient Sage 100 France Shipping Address Managed Custom Object | 26 | Account → Account, Client → Commercient Client Managed Custom Object, Record creator → Record creator, →, Record flag → Commercient record flag |
| Product | Product | 6 | Commercient external key (earlier package) → Commercient external key (earlier package), Product code → Product code, Description → Description, Family → Family, Weighted average cost → weighted average cost (custom field) |
| Sage 100 FR Item master | Commercient Sage 100 France Item Managed Custom Object | 144 | product lookup → product lookup (custom field), Weight unit → weight unit (custom field), Bill of materials type → bill of materials type (custom field), Stock tracking method → stock tracking method (custom field), Item reference → Commercient item reference |
| Sales order Header Sync | Commercient Sage 100 French Sales Order Header Managed Custom Object | 93 | account lookup → Commercient account (related record), Sage 100 client (custom field) → Commercient Sage 100 client (related record), Sales rep number → sales rep number (custom field), → indexed sales rep number (custom field), Warehouse number → warehouse number (custom field) |
| Sales order line Sync | Commercient Sage 100 French Sales Order Line Managed Custom Object | 96 | Document line number → document line number (custom field), Sales rep number → sales rep number (custom field), → indexed sales rep number (custom field), First variant number → first variant number (custom field), Second variant number → second variant number (custom field) |
| Invoice Header Sync | Commercient Sage 100 French Invoice Header Managed Custom Object | 113 | account lookup → Commercient account (related record), Sage 100 client (custom field) → Commercient Sage 100 client (related record), Sales rep number → sales rep number (custom field), → indexed sales rep number (custom field), Warehouse number → warehouse number (custom field) |
| Invoice Line Sync | Commercient Sage 100 French Invoice Line Managed Custom Object | 97 | Sage 100 France invoice (custom field) → Commercient Sage 100 France invoice (related record), Document line number → document line number (custom field), Sales rep number → sales rep number (custom field), → indexed sales rep number (custom field), First variant number → first variant number (custom field) |
| Sync Vendor | Account | 14 | Commercient AR customer code column → Commercient AR customer code, Commercient Salesperson Managed Custom Object → Commercient Salesperson Managed Custom Object, Type → Type, Phone → Phone, Billing street → Billing street |
| Item TO Product object Lookup | Product | 2 | Commercient external key (earlier package) → Commercient external key (earlier package), Commercient France item (related record) → Commercient France item (related record) |
| Standard price book create | Price book entry | 4 | Active → Active, Unit price → Unit price, price book lookup → price book lookup, product lookup → product lookup |
| Standard price book update | Price book entry | 2 | Active → Active, Unit price → Unit price |
| Opportunity | Opportunity | 13 | account lookup → account lookup, Amount → Amount, close date property → close date property, Description → Description, Date became customer → date became customer (custom field) |
| Sage 100 fr Customer price book | Commercient Sage 100 France Customer Price Book (custom object) | 35 | Third party account name → third party account name (custom field), Item reference → item reference (custom field), → indexed item reference (custom field), Item designation → item designation (custom field), Price category → price category (custom field) |
| Sage 100 FR Vendor price book | Commercient Sage 100 France Vendor Price Book (custom object) | 37 | Item reference → item reference (custom field), → indexed item reference (custom field), Third party account number → third party account number (custom field), → indexed third party account number (custom field), Supplier item reference → supplier item reference (custom field) |

## 6. Community templates

The catalogue carries 18 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 18
- Default operations: insert on 18, update on 18, delete on 18
- Marked as circular sync: 0
- Licence groups they span: 9
- Destination objects: Account, Price book entry, Product, Commercient Sage 100 France Customer
  Price Book (custom object), Commercient Sage 100 France Vendor Price Book (custom object),
  Commercient Sage 100 France Item Managed Custom Object, Commercient Sage 100 France Shipping
  Address Managed Custom Object, Opportunity and 6 custom objects
- Object display names: Account, Customer Sync, Customer to account lookup, Invoice Header Sync,
  Invoice Line Sync, Item TO Product object Lookup, Opportunity, Product, Sage 100 fr Customer price
  book, Sage 100 FR Item master, Sage 100 FR Vendor price book, Sales order Header Sync and 6 more
- Template groups: Account, Product, Invoice, Sales order, CRM Opportunity and Line, Customer Multi
  Ship Addresses

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/sage-100-france`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Sage 100 France → Salesforce templates set up. dlake-crmpro-salesforce is the
destination skill this page sits under: its own text is the authority for the Salesforce conventions
that hold across every ERP, and its ERP table lists this page alongside every sibling ERP page for
this destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/sage-100-france`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
