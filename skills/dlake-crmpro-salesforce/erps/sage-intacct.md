---
name: dlake-crmpro-salesforce/erps/sage-intacct
kind: erp-summary
description: >-
  Use it when standing up or reading a Sage Intacct → Salesforce template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-salesforce, the destination skill this page is a child of, which
  carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Sage Intacct: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/sage-intacct` (or `list_skills`) against the
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
| **Employee Sync** | ERP Employee data becomes Commercient Sage Intacct Employee Managed Custom Object in Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Sage Intacct Employee Managed Custom Object | employees |
| **Warenouse Master** | ERP Warehouse data becomes Commercient Sage Intacct Warehouse Managed Custom Object in Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Sage Intacct Warehouse Managed Custom Object | warehouses |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Commercient Sage Intacct AR Term Managed Custom Object, Contact, Commercient Sage Intacct Customer Managed Custom Object | customers, the writeback transaction log, AR terms, contacts, employees |
| **CRM Ownership** | The templates push User to Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | User | — |
| **Product** | ERP Item, warehouse details per item, Warehouse data becomes Commercient Sage Intacct Item Managed Custom Object, Commercient Sage Intacct Item Warehouse Information Managed Custom Object, Product in Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Sage Intacct Item Managed Custom Object, Commercient Sage Intacct Item Warehouse Information Managed Custom Object, Product | items, item warehouse information, warehouses |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Sage Intacct AR Invoice Managed Custom Object, Commercient Sage Intacct AR Invoice Item Managed Custom Object, Commercient Sage Intacct AR Invoice Payment Managed Custom Object | AR invoices, AR invoice lines, items, AR invoice payments |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Sage Intacct Sales Order Document Managed Custom Object, Commercient Sage Intacct Sales Order Document Entry Managed Custom Object | sales order documents, sales order document lines, items |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Get Account | Account | Commercient AR customer code | 1 |
| Employee Sync | Commercient Sage Intacct Employee Managed Custom Object | Commercient external key | 2 |
| Account | Account | Commercient AR customer code | 3 |
| Term | Commercient Sage Intacct AR Term Managed Custom Object | Commercient external key | 4 |
| Customer Master | Commercient Sage Intacct Customer Managed Custom Object | Commercient external key | 5 |
| Warenouse Master | Commercient Sage Intacct Warehouse Managed Custom Object | Commercient external key | 6 |
| Product | Product | Commercient external key (earlier package) | 7 |
| Item Master | Commercient Sage Intacct Item Managed Custom Object | Commercient external key | 11 |
| Itemwise Warehouse | Commercient Sage Intacct Item Warehouse Information Managed Custom Object | Commercient external key | 12 |
| Sales order Header | Commercient Sage Intacct Sales Order Document Managed Custom Object | Commercient external key | 13 |
| Sales order Line | Commercient Sage Intacct Sales Order Document Entry Managed Custom Object | Commercient external key | 14 |
| Invoice Header | Commercient Sage Intacct AR Invoice Managed Custom Object | Commercient external key | 15 |
| Invoice Line | Commercient Sage Intacct AR Invoice Item Managed Custom Object | Commercient external key | 16 |
| Invoice Payment | Commercient Sage Intacct AR Invoice Payment Managed Custom Object | Commercient external key | 17 |
| Customer To Account Reverse lookup | Account | Commercient AR customer code | 18 |
| Item To Product Reverse Lookup | Product | Commercient external key (earlier package) | 19 |
| Contact | Contact | Commercient external key | 20 |
| Get User | User | Id | 22 |
| (unnamed) | Account | id | 23 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| employee feed | insert + update | employees |
| account feed | insert + update | customers |
| AR terms feed | insert + update | AR terms |
| customer feed | insert + update | customers, employees |
| warehouse feed | insert + update | warehouses |
| product feed | insert + update | items |
| item feed | insert + update | items |
| item warehouse feed | insert + update | item warehouse information, warehouses, items |
| sales order feed | insert + update | sales order documents |
| sales order line feed | insert + update | sales order document lines, items |
| invoice feed | insert + update | AR invoices |
| invoice line feed | insert + update | AR invoice lines, items, AR invoices |
| invoice payment feed | insert + update | AR invoice payments, AR invoices |
| account reverse lookup feed | insert only | customers |
| product reverse lookup feed | insert + update | items |
| contact feed | insert + update | contacts, customers |
| account customer code clearing feed | insert + update | customers, the writeback transaction log |

## 4. Order of work

The templates set run sequence from 1 to 23. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 1 — Get Account
- 2 — Employee Sync
- 3 — Account
- 4 — Term
- 5 — Customer Master
- 6 — Warenouse Master
- 7 — Product
- 11 — Item Master
- 12 — Itemwise Warehouse
- 13 — Sales order Header
- 14 — Sales order Line
- 15 — Invoice Header
- 16 — Invoice Line
- 17 — Invoice Payment
- 18 — Customer To Account Reverse lookup
- 19 — Item To Product Reverse Lookup
- 20 — Contact
- 22 — Get User

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- account feed reads retrieved user sync output
- account reverse lookup feed reads account sync output, customer sync output
- contact feed reads account sync output
- customer feed reads account sync output, AR terms sync output, employee sync output
- item feed reads product sync output
- item warehouse feed reads item sync output, warehouse sync output
- invoice feed reads account sync output, customer sync output
- invoice line feed reads invoice sync output, item sync output
- invoice payment feed reads account sync output, customer sync output, invoice sync output
- product reverse lookup feed reads item sync output
- sales order feed reads account sync output, customer sync output
- sales order line feed reads sales order sync output, item sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Get Account | Account | 18 | Commercient AR customer code column → Commercient AR customer code, Billing street → Billing street, Billing country → Billing country, Billing city → Billing city, Billing state → Billing state |
| Employee Sync | Commercient Sage Intacct Employee Managed Custom Object | 76 | Record number → Record number, Employee identifier → Commercient employee identifier, Social security number → Commercient social security number, Job title → Commercient title, Location identifier → Location identifier |
| Account | Account | 18 | Commercient AR customer code column → Commercient AR customer code, Billing street → Billing street, Billing country → Billing country, Billing city → Billing city, Billing state → Billing state |
| Term | Commercient Sage Intacct AR Term Managed Custom Object | 19 | Description → Description, Status → Commercient status, Record number → Record number, When modified → Commercient last modified date, When created → When created |
| Customer Master | Commercient Sage Intacct Customer Managed Custom Object | 93 | Account → Account, Sage Intacct term (related record) → Sage Intacct term (related record), Sage Intacct salesperson (related record) → Sage Intacct salesperson (related record), Record number → Record number, Customer identifier → Commercient customer identifier |
| Warenouse Master | Commercient Sage Intacct Warehouse Managed Custom Object | 21 | Record number → Record number, Location identifier → Location identifier, Parent key → Commercient parent key, parent account lookup → Commercient parent identifier, Parent name → Commercient parent name |
| Product | Product | 11 | Product code → Product code, Commercient external key (earlier package) → Commercient external key (earlier package), Description → Description, Family → Family, Category 1 → Category 1 (custom field) |
| Item Master | Commercient Sage Intacct Item Managed Custom Object | 70 | Product → Product (custom field), Record number → Record number, Item identifier → Commercient item identifier, Status → Commercient status, Monthly recurring revenue → Commercient monthly recurring revenue |
| Itemwise Warehouse | Commercient Sage Intacct Item Warehouse Information Managed Custom Object | 30 | Sage Intacct item (related record) → Sage Intacct item (related record), Sage Intacct warehouse (custom field) → Sage Intacct warehouse (custom field), Record number → Record number, Item identifier → Commercient item identifier, Warehouse identifier → Commercient warehouse identifier |
| Sales order Header | Commercient Sage Intacct Sales Order Document Managed Custom Object | 49 | Account → Account, Sage Intacct customer (related record) → Sage Intacct customer (related record), Record number → Record number, Document number (Epicor Eagle) → Document number (Epicor Eagle), Document identifier → Document identifier |
| Sales order Line | Commercient Sage Intacct Sales Order Document Entry Managed Custom Object | 88 | Record number → Record number, Sage Intacct item (related record) → Sage Intacct item (related record), Sage Intacct sales order header (related record) → Sage Intacct sales order header (related record), Document header number → Document header number, Document header identifier → Document header identifier |
| Invoice Header | Commercient Sage Intacct AR Invoice Managed Custom Object | 92 | Account → Account, Sage Intacct customer (related record) → Sage Intacct customer (related record), Record number → Record number, Record type → Record type, Record identifier → Record identifier |
| Invoice Line | Commercient Sage Intacct AR Invoice Item Managed Custom Object | 83 | Record number → Record number, Sage Intacct invoice header (related record) → Sage Intacct invoice header (related record), Sage Intacct item (related record) → Sage Intacct item (related record), record key → record key, Account key → Account key |
| Invoice Payment | Commercient Sage Intacct AR Invoice Payment Managed Custom Object | 20 | Sage Intacct invoice header (related record) → Sage Intacct invoice header (related record), Account → Account, Sage Intacct customer (related record) → Sage Intacct customer (related record), Record number → Record number, Payment key → Commercient payment key |
| Customer To Account Reverse lookup | Account | 2 | Commercient AR customer code column → Commercient AR customer code, Commercient Sage Intacct customer (related record) → Commercient Sage Intacct customer (related record) |
| Item To Product Reverse Lookup | Product | 2 | Commercient external key (earlier package) → Commercient external key (earlier package), Sage Intacct item (related record) → Sage Intacct item (custom field) |
| Contact | Contact | 12 | account lookup → account lookup, Commercient external key → Commercient external key, Family name → Family name, Given name → Given name, Email → Email |
| (unnamed) | Account | 2 | Commercient AR customer code column → Commercient AR customer code, id → id |

## 6. Community templates

The catalogue carries 57 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 57
- Default operations: insert on 57, update on 57, delete on 57
- Marked as circular sync: 0
- Licence groups they span: 9
- Destination objects: Account, Product, Commercient Sage Intacct AR Invoice Managed Custom Object,
  Commercient Sage Intacct Customer Managed Custom Object, Contact, Commercient Sage Intacct AR
  Invoice Payment Managed Custom Object, Commercient Sage Intacct Employee Managed Custom Object,
  Commercient Sage Intacct Item Managed Custom Object, Commercient Sage Intacct AR Invoice Item
  Managed Custom Object, Commercient Sage Intacct Item Warehouse Information Managed Custom Object,
  Commercient Sage Intacct Sales Order Document Managed Custom Object, Commercient Sage Intacct
  Sales Order Document Entry Managed Custom Object, Commercient Sage Intacct Warehouse Managed
  Custom Object, Commercient Sage Intacct AR Payment Managed Custom Object, Commercient Sage Intacct
  AR Term Managed Custom Object, Customer (custom object), Price book entry, Product Matching
  (custom object)
- Template groups: Account, Product, Invoice, Sales order

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/sage-intacct`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Sage Intacct → Salesforce templates set up. dlake-crmpro-salesforce is the destination
skill this page sits under: its own text is the authority for the Salesforce conventions that hold
across every ERP, and its ERP table lists this page alongside every sibling ERP page for this
destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/sage-intacct`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
