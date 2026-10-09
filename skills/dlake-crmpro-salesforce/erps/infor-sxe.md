---
name: dlake-crmpro-salesforce/erps/infor-sxe
kind: erp-summary
description: >-
  Use it when standing up or reading an Infor SXe → Salesforce template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-salesforce, the destination skill this page is a child of, which
  carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Infor SXe: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/infor-sxe` (or `list_skills`) against the
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
| **Get Users** | The templates push User to Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | User | — |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Contact, Commercient AR Customer Managed Custom Object, Commercient Salesperson Managed Custom Object | AR customers, customer shipping addresses, contacts, salespeople |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created and existing ones updated; none are deleted. | Commercient Shipping Address Managed Custom Object | customer shipping addresses |
| **Product** | The templates push Commercient Item Master Managed Custom Object, Commercient Warehouse Managed Custom Object, Commercient Item Warehouse Managed Custom Object to Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Item Master Managed Custom Object, Commercient Warehouse Managed Custom Object, Commercient Item Warehouse Managed Custom Object, Product | inventory products, warehouses, item warehouses |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Invoice Header Managed Custom Object, Commercient Invoice Detail Managed Custom Object, Commercient Invoice Payment Managed Custom Object | AR transactions, order entry lines, order entry headers, AR transaction records |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created and existing ones updated; none are deleted. | Commercient Order Header Managed Custom Object, Commercient Order Line Managed Custom Object | order entry headers, order entry lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Get Users | User | Commercient salesperson code | 1 |
| Account | Account | Commercient AR customer code | 1 |
| Sync Salesperson | Commercient Salesperson Managed Custom Object | Commercient external key (Infor package) | 1 |
| Customer Master | Commercient AR Customer Managed Custom Object | Commercient external key (Infor package) | 2 |
| Customer To Account Reverse Lookup | Account | Commercient AR customer code | 3 |
| Sales Order Header | Commercient Order Header Managed Custom Object | Commercient external key (Infor package) | 4 |
| Sales Order Detail Line | Commercient Order Line Managed Custom Object | Commercient external key (Infor package) | 5 |
| Product | Product | Commercient external key (earlier package) | 6 |
| Sync Contact | Contact | External key (custom field) | 7 |
| Item Master | Commercient Item Master Managed Custom Object | Commercient external key (Infor package) | 7 |
| Product to Item Master Reverse Lookup | Product | Commercient external key (earlier package) | 8 |
| Warehouse | Commercient Warehouse Managed Custom Object | Commercient external key (Infor package) | 9 |
| Item Warehouse | Commercient Item Warehouse Managed Custom Object | Commercient external key (Infor package) | 10 |
| Ship To Address | Commercient Shipping Address Managed Custom Object | Commercient external key (Infor package) | 11 |
| Sync invoice header | Commercient Invoice Header Managed Custom Object | Commercient external key (Infor package) | 15 |
| Sync invoice detail | Commercient Invoice Detail Managed Custom Object | Commercient external key (Infor package) | 16 |
| Sync Invoice payment | Commercient Invoice Payment Managed Custom Object | Commercient external key (Infor package) | 17 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| account feed | insert + update | AR customers, customer shipping addresses |
| salesperson feed | insert + update | salespeople |
| customer feed | insert + update | AR customers |
| customer account lookup feed | insert + update | AR customers |
| sales order feed | insert + update | order entry headers |
| sales order line feed | insert + update | order entry lines |
| product feed | insert + update | inventory products |
| contact feed | insert + update | contacts |
| item master feed | insert + update | inventory products |
| product item master lookup feed | insert + update | inventory products |
| warehouse feed | insert + update | warehouses |
| item warehouse feed | insert + update | item warehouses |
| shipping address feed | insert + update | customer shipping addresses |
| invoice feed | insert + update | AR transactions |
| invoice line feed | insert + update | AR transactions, order entry lines, order entry headers, AR transaction records |
| invoice payment feed | insert + update | AR transactions, AR transaction records |

## 4. Order of work

The templates set run sequence from 1 to 17. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 1 — Get Users, Account, Sync Salesperson
- 2 — Customer Master
- 3 — Customer To Account Reverse Lookup
- 4 — Sales Order Header
- 5 — Sales Order Detail Line
- 6 — Product
- 7 — Sync Contact, Item Master
- 8 — Product to Item Master Reverse Lookup
- 9 — Warehouse
- 10 — Item Warehouse
- 11 — Ship To Address
- 15 — Sync invoice header
- 16 — Sync invoice detail
- 17 — Sync Invoice payment

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- account feed reads salesperson sync output, user sync output
- customer account lookup feed reads customer sync output (generic name)
- contact feed reads account sync output; no template in this set writes account sync output
- customer feed reads account sync output (generic name)
- shipping address feed reads account sync output (generic name), customer sync output (generic
  name)
- item master feed reads product record sync output (generic name)
- item warehouse feed reads item sync output (generic name), warehouse sync output (generic name),
  product record sync output (generic name)
- invoice feed reads salesperson sync output, account sync output, customer sync output; no template
  in this set writes account sync output, customer sync output
- invoice line feed reads invoice sync output, product sync output, item sync output; no template in
  this set writes product sync output, item sync output
- invoice payment feed reads account sync output, customer sync output, invoice sync output; no
  template in this set writes account sync output, customer sync output
- product item master lookup feed reads item sync output (generic name)
- sales order feed reads account sync output (generic name), customer sync output (generic name)
- sales order line feed reads sales order header sync output (generic name), item sync output
  (generic name)

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Account | Account | 15 | Company number, Customer number → Commercient AR customer code, Name, Company number, Customer number → Name, Phone number → Phone, Address → Billing street, City → Billing city |
| Sync Salesperson | Commercient Salesperson Managed Custom Object | 50 | Company number → Commercient company number, Sales rep → Commercient sales rep, Address → Commercient address, city property → Commercient city, state → Commercient state |
| Customer Master | Commercient AR Customer Managed Custom Object | 5 | Company number, Customer number → Commercient external key (Infor package), the linked Salesforce record → Account, Company number → Commercient company number, Customer number → Commercient customer number, Status type → Commercient status type |
| Customer To Account Reverse Lookup | Account | 2 | Company number, Customer number → Commercient AR customer code, the linked Salesforce record → Commercient Infor SXe customer (related record) |
| Sales Order Header | Commercient Order Header Managed Custom Object | 4 | Company number,Order number → Commercient external key (Infor package), Order number → Name, Company number,Customer number → Account, Company number,Customer number → the linked Infor SXe customer |
| Sales Order Detail Line | Commercient Order Line Managed Custom Object | 3 | Company number,Order number,Source line number,Transaction type → Commercient external key (Infor package), Order number → Name, the linked Salesforce record → the linked Infor SXe sales order header |
| Product | Product | 6 | Company number, ERP product → Commercient external key (earlier package), Status type → Active, ERP product → Name, Company number, ERP product → Product code, Product description → Description |
| Sync Contact | Contact | 15 | Title → Title, Given name → Given name, Salutation → Salutation, Family name → Family name, Email → Email |
| Item Master | Commercient Item Master Managed Custom Object | 3 | Company number,ERP product → Commercient external key (Infor package), the linked Salesforce record → Product, ERP product → Name |
| Product to Item Master Reverse Lookup | Product | 2 | Company number,ERP product → Commercient external key (earlier package), the linked Salesforce record → Commercient Infor SXe item master (related record) |
| Warehouse | Commercient Warehouse Managed Custom Object | 3 | Company number, Warehouse → Commercient external key (Infor package), Company number → Commercient company number, Warehouse → Commercient warehouse |
| Item Warehouse | Commercient Item Warehouse Managed Custom Object | 5 | Company number,ERP product,Warehouse → Commercient external key (Infor package), Company number,ERP product,Warehouse → Name, the linked Salesforce record → the linked Infor SXe item master, the linked Salesforce record → Commercient warehouse (related record), the linked Salesforce record → Product |
| Ship To Address | Commercient Shipping Address Managed Custom Object | 3 | Company number, Customer number, Ship to code → Commercient external key (Infor package), the linked Salesforce record → Account, the linked Salesforce record → the linked Infor SXe customer |
| Sync invoice header | Commercient Invoice Header Managed Custom Object | 80 | Company number → Commercient company number, Customer number → Commercient customer number, Status type → Commercient status type, Invoice number → Commercient invoice number, Invoice suffix → Commercient invoice suffix |
| Sync invoice detail | Commercient Invoice Detail Managed Custom Object | 236 | Order number → Commercient order number, Order suffix → Commercient order suffix, Warehouse → Commercient warehouse, Transaction type → Commercient transaction type, ship to → Commercient ship to code |
| Sync Invoice payment | Commercient Invoice Payment Managed Custom Object | 81 | Company number → Commercient company number, Customer number → Commercient customer number, Status type → Commercient status type, Invoice number → Commercient invoice number, Invoice suffix → Commercient invoice suffix |

## 6. Community templates

The catalogue carries 37 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 37
- Default operations: insert on 37, update on 37, delete on 37
- Marked as circular sync: 3
- Licence groups they span: 10
- Destination objects: Account, Product, Commercient Invoice Header Managed Custom Object,
  Commercient AR Customer Managed Custom Object, Commercient Shipping Address Managed Custom Object,
  Commercient Warehouse Managed Custom Object, Commercient Item Master Managed Custom Object,
  Commercient Item Warehouse Managed Custom Object, Commercient Order Header Managed Custom Object,
  Commercient Order Line Managed Custom Object, Commercient Invoice Detail Managed Custom Object,
  Commercient Salesperson Managed Custom Object, Contact, Price book entry, Commercient Invoice
  Payment Managed Custom Object, User
- Object display names: Account, Account Hierarchy, Account Without Customer Segment, Contact, Get
  Salesforce Users, Infor SXe Customer, Infor SXe Customer to account lookup, Infor SXe Invoice
  header, Infor SXe invoice line, Infor SXe Item master, Infor SXe Item warehouse, Infor SXe Product
  to item reverse lookup and 25 more
- Template groups: Account, Product, Invoice, Sales order, Customer Multi Ship Addresses

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/infor-sxe`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Infor SXe → Salesforce templates set up. dlake-crmpro-salesforce is the destination
skill this page sits under: its own text is the authority for the Salesforce conventions that hold
across every ERP, and its ERP table lists this page alongside every sibling ERP page for this
destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/infor-sxe`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
