---
name: dlake-crmpro-salesforce/erps/ifs
kind: erp-summary
description: >-
  Use it when standing up or reading an IFS → Salesforce template set, when deciding which templates
  to import and activate, or when a run completes without pushing records and the answer is in the
  view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro generally,
  and dlake-crmpro-salesforce, the destination skill this page is a child of, which carries the
  Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — IFS: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/ifs` (or `list_skills`) against the Commercient
admin plane. Existing customers who need access or help: contact support@commercient.com. New
customers: contact sales@commercient.com to become a customer and be whitelisted.

dlake-crmpro is the parent skill and the authority for everything general: the CRMPro tools, process
configuration and field list, the sync history, how source data is selected, and what a run that
finds nothing does. Read it first; this page does not repeat it. dlake-crmpro-salesforce is the
destination skill this page is a child of, and the authority for the Salesforce conventions that
hold across every ERP: read it first, then come back here for what this source's own templates set.
This page grows as the catalogue does.

## 1. What the templates deliver

| Group | Business outcome | Objects | Source tables and views |
|---|---|---|---|
| **IFS Part** | ERP inventory part, sales part data becomes Commercient IFS Part Managed Custom Object in Salesforce. New records are created and existing ones updated; none are deleted. | Commercient IFS Part Managed Custom Object | inventory parts, sales parts |
| **Get User** | The templates push User to Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | User | — |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created and existing ones updated; none are deleted. | Account, Commercient IFS Customer Managed Custom Object | customer information addresses, customer address types, customer information records |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created and existing ones updated; none are deleted. | Commercient IFS Address Managed Custom Object | customer information addresses |
| **Product** | ERP inventory part in stock data becomes Commercient IFS Item Warehouse Managed Custom Object in Salesforce. New records are created and existing ones updated; none are deleted. | Commercient IFS Item Warehouse Managed Custom Object | inventory parts in stock |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created and existing ones updated; none are deleted. | Commercient IFS Invoice Header Managed Custom Object, Commercient IFS Invoice Item Managed Custom Object | invoices, invoice items |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created and existing ones updated; none are deleted. | Commercient IFS Order Managed Custom Object, Commercient IFS Order Line Managed Custom Object | customer orders, customer order lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Account | Account | Commercient AR customer code | 1 |
| Child Account | Account | Commercient AR customer code | 2 |
| IFS Customer | Commercient IFS Customer Managed Custom Object | Commercient external key (IFS package) | 3 |
| Customer to Account Reverse Lookup | Account | Commercient AR customer code | 4 |
| IFS Address | Commercient IFS Address Managed Custom Object | Commercient external key (IFS package) | 5 |
| IFS Sales order header | Commercient IFS Order Managed Custom Object | Commercient external key (IFS package) | 6 |
| IFS Sales order detail | Commercient IFS Order Line Managed Custom Object | Commercient external key (IFS package) | 7 |
| IFS Invoice header | Commercient IFS Invoice Header Managed Custom Object | Commercient external key (IFS package) | 8 |
| IFS Invoice detail | Commercient IFS Invoice Item Managed Custom Object | Commercient external key (IFS package) | 9 |
| IFS Part | Commercient IFS Part Managed Custom Object | Commercient external key (IFS package) | 10 |
| IFS Item warehouse | Commercient IFS Item Warehouse Managed Custom Object | Commercient external key (IFS package) | 11 |
| Get User | User | Id | 17 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| account feed | insert + update | customer information addresses, customer address types, customer information records |
| child account feed | insert + update | customer information addresses, customer address types, customer information records |
| customer feed | insert + update | customer information records |
| customer account lookup feed | insert + update | customer information records |
| address feed | insert + update | customer information addresses |
| sales order feed | insert + update | customer orders |
| sales order line feed | insert + update | customer order lines |
| invoice feed | insert + update | invoices |
| invoice line feed | insert + update | invoice items |
| part feed | insert + update | inventory parts, sales parts |
| item warehouse feed | insert + update | inventory parts in stock |

## 4. Order of work

The templates set run sequence from 1 to 17. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 1 — Account
- 2 — Child Account
- 3 — IFS Customer
- 4 — Customer to Account Reverse Lookup
- 5 — IFS Address
- 6 — IFS Sales order header
- 7 — IFS Sales order detail
- 8 — IFS Invoice header
- 9 — IFS Invoice detail
- 10 — IFS Part
- 11 — IFS Item warehouse
- 17 — Get User

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- customer account lookup feed reads customer sync output (generic name)
- customer feed reads account sync output (generic name)
- address feed reads customer sync output (generic name)
- item warehouse feed reads part sync output (generic name)
- invoice feed reads account sync output (generic name), customer sync output (generic name)
- invoice line feed reads invoice sync output (generic name)
- sales order feed reads account sync output (generic name), customer sync output (generic name)
- sales order line feed reads sales order header sync output (generic name)

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Account | Account | 13 | returned customer identifier → Commercient AR customer code, Name → Name, Address 1, Address 2 → Billing street, City → Billing city, State → Billing state |
| Child Account | Account | 14 | returned customer identifier,Address identifier → Commercient AR customer code, Name,Address identifier → Name, the linked Salesforce record → parent account lookup, Address 1,Address 2 → Billing street, City → Billing city |
| IFS Customer | Commercient IFS Customer Managed Custom Object | 23 | returned customer identifier → Commercient external key (IFS package), Name → Name, the linked Salesforce record → Account, Name → Commercient name, Creation date → Commercient creation date |
| Customer to Account Reverse Lookup | Account | 2 | returned customer identifier → Commercient AR customer code, the linked Salesforce record → Commercient customer (related record) |
| IFS Address | Commercient IFS Address Managed Custom Object | 29 | returned customer identifier,Address identifier → Commercient external key (IFS package), returned customer identifier,Address identifier → Name, the linked Salesforce record → Customer, returned customer identifier → Commercient customer identifier, Address identifier → Commercient address identifier |
| IFS Sales order header | Commercient IFS Order Managed Custom Object | 141 | Order number, Customer number (IFS) → Commercient external key (IFS package), Order number, Customer number (IFS) → Commercient name, Customer number (IFS) → Account, Customer number (IFS) → Customer, Order number → Commercient order number |
| IFS Sales order detail | Commercient IFS Order Line Managed Custom Object | 221 | Order number, Line number, Release number, Line item number, Contract → Commercient external key (IFS package), Order number, Line number, Release number, Line item number, Contract → Commercient name, Order number → Commercient order, Order number → Commercient order number, Line number → Commercient line number |
| IFS Invoice header | Commercient IFS Invoice Header Managed Custom Object | 153 | Invoice identifier → Commercient external key (IFS package), Invoice identifier → Commercient name, the linked Salesforce record → Commercient account (related record), the linked Salesforce record → Commercient customer (related record), company → Commercient company |
| IFS Invoice detail | Commercient IFS Invoice Item Managed Custom Object | 88 | Invoice identifier, Item identifier → Commercient external key (IFS package), Invoice identifier, Item identifier → Commercient name, the linked Salesforce record → Commercient invoice header (related record), company → Commercient company, Identity → Commercient identity |
| IFS Part | Commercient IFS Part Managed Custom Object | 116 | Contract, Inventory part number → Commercient external key (IFS package), Contract, Inventory part number → Name, Contract → Commercient contract (site), Inventory part number → Commercient part number, Accounting group → Commercient accounting group |
| IFS Item warehouse | Commercient IFS Item Warehouse Managed Custom Object | 44 | the linked Salesforce record → Commercient IFS Part Managed Custom Object, Contract → Commercient contract (site), Inventory part number → Commercient part number, Configuration identifier → Commercient configuration identifier, Location number → Commercient location number |

## 6. Community templates

The catalogue carries 52 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 52
- Default operations: insert on 52, update on 52, delete on 52
- Marked as circular sync: 0
- Licence groups they span: 9
- Destination objects: Account, Commercient IFS Customer Managed Custom Object, Commercient IFS Item
  Warehouse Managed Custom Object, Opportunity, Price book entry, Commercient IFS Address Managed
  Custom Object, Commercient IFS Invoice Header Managed Custom Object, Commercient IFS Invoice Item
  Managed Custom Object, Commercient IFS Order Managed Custom Object, Commercient IFS Order Line
  Managed Custom Object, Commercient IFS Part Managed Custom Object, Commercient IFS Supplier
  Managed Custom Object, Price book object, Product
- Object display names: Account, Child Account, Customer to Account Reverse Lookup, IFS Customer,
  IFS Item warehouse, IFS Address, IFS Invoice detail, IFS Invoice header, IFS Part, IFS Sales order
  detail, IFS Sales order header, IFS Supplier, 7 more and a further template
- Template groups: Account, Product, Invoice, Sales order, CRM Opportunity and Line, Customer Multi
  Ship Addresses

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/ifs`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped IFS → Salesforce templates set up. dlake-crmpro-salesforce is the destination skill this
page sits under: its own text is the authority for the Salesforce conventions that hold across every
ERP, and its ERP table lists this page alongside every sibling ERP page for this destination. For
the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/ifs`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
