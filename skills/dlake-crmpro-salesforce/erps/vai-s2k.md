---
name: dlake-crmpro-salesforce/erps/vai-s2k
kind: erp-summary
description: >-
  Use it when standing up or reading a VAI S2K → Salesforce template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-salesforce, the destination skill this page is a child of, which
  carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — VAI S2K: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/vai-s2k` (or `list_skills`) against the
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
| **Customer memo notes Sync** | ERP Customer memo notes data becomes Customer memo notes (custom object) in Salesforce. New records are created and existing ones updated; none are deleted. | Customer memo notes (custom object) | customer memo note records |
| **Customer other notes Sync** | ERP Customer other notes data becomes Customer other notes (custom object) in Salesforce. New records are created and existing ones updated; none are deleted. | Customer other notes (custom object) | customer other note records |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created and existing ones updated; none are deleted. | Account, Contact, Commercient AR Customer Managed Custom Object | customers, customer shipping addresses, prospects |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created and existing ones updated; none are deleted. | Commercient Ship To Address Managed Custom Object | customer shipping addresses |
| **Product** | ERP Item master table, Warehouse table, Item warehouse table data becomes Item (custom object), Warehouse (custom object), Item Warehouse (custom object) in Salesforce. New records are created and existing ones updated; none are deleted. | Item (custom object), Warehouse (custom object), Item Warehouse (custom object), Product | items, warehouse locations, item warehouse records |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created and existing ones updated; none are deleted. | Commercient Sales Order Header Managed Custom Object, Commercient Sales Order Detail Managed Custom Object | sales order headers, sales order lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Account | Account | Commercient AR customer code | 1 |
| Contact | Contact | External key (custom field) | 2 |
| Customer | Commercient AR Customer Managed Custom Object | Commercient external key | 3 |
| Account To Customer Reverse Lookup | Account | Commercient AR customer code | 4 |
| Shipping address | Commercient Ship To Address Managed Custom Object | Commercient external key | 5 |
| Sales order header | Commercient Sales Order Header Managed Custom Object | Commercient external key | 6 |
| Sales order detail | Commercient Sales Order Detail Managed Custom Object | Commercient external key | 7 |
| Product | Product | Commercient external key (earlier package) | 8 |
| Item | Item (custom object) | External key (custom field) | 9 |
| Product To Item Reverse Lookup | Product | Commercient external key (earlier package) | 10 |
| Warehouse | Warehouse (custom object) | External key (custom field) | 11 |
| Item Warehouse | Item Warehouse (custom object) | External key (custom field) | 12 |
| Customer memo notes Sync | Customer memo notes (custom object) | External key (custom field) | 15 |
| Customer other notes Sync | Customer other notes (custom object) | External key (custom field) | 16 |
| Prospect Account | Account | Commercient AR customer code | 17 |
| Prospect Contact | Contact | External key (custom field) | 18 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| account feed | insert + update | customers, customer shipping addresses |
| contact feed | insert + update | customers, customer shipping addresses |
| customer feed | insert + update | customers |
| customer account lookup feed | insert + update | customers |
| shipping address feed | insert + update | customer shipping addresses |
| sales order header feed | insert + update | sales order headers |
| sales order detail feed | insert + update | sales order lines |
| product feed | insert + update | items |
| item feed | insert + update | items |
| product item master lookup feed | insert + update | items |
| warehouse feed | insert + update | warehouse locations |
| item warehouse feed | insert + update | item warehouse records |
| customer memo notes feed | insert + update | customer memo note records |
| customer other notes feed | insert + update | customer other note records |
| prospect account feed | insert + update | prospects |
| prospect contact feed | insert + update | prospects |

## 4. Order of work

The templates set run sequence from 1 to 18. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 1 — Account
- 2 — Contact
- 3 — Customer
- 4 — Account To Customer Reverse Lookup
- 5 — Shipping address
- 6 — Sales order header
- 7 — Sales order detail
- 8 — Product
- 9 — Item
- 10 — Product To Item Reverse Lookup
- 11 — Warehouse
- 12 — Item Warehouse
- 15 — Customer memo notes Sync
- 16 — Customer other notes Sync
- 17 — Prospect Account
- 18 — Prospect Contact

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- customer memo notes feed reads account sync output (generic name), customer sync output (generic
  name)
- customer other notes feed reads account sync output (generic name), customer sync output (generic
  name)
- customer account lookup feed reads customer sync output (generic name)
- contact feed reads account sync output (generic name)
- prospect contact feed reads prospect account sync output (generic name)
- customer feed reads account sync output (generic name)
- shipping address feed reads account sync output (generic name), customer sync output (generic
  name)
- item feed reads product record sync output (generic name)
- item warehouse feed reads product record sync output (generic name), item sync output (generic
  name), warehouse sync output (generic name)
- product item master lookup feed reads item sync output (generic name)
- sales order header feed reads account sync output (generic name), customer sync output (generic
  name)
- sales order detail feed reads sales order header sync output (generic name)

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Account | Account | 14 | Company number,Customer number → Commercient AR customer code, Customer master name → Name, Customer phone → Phone, Customer type → Type, Customer address line 1,Customer address line 2,Customer address line 3 → Billing street |
| Customer | Commercient AR Customer Managed Custom Object | 10 | Company number, Customer number → Commercient external key, Customer master name → Name, Customer address line 1, Customer address line 2, Customer address line 3 → Billing street, Customer city → Billing city, Customer state → Billing state |
| Account To Customer Reverse Lookup | Account | 2 | Company number, Customer number → Commercient AR customer code, the linked Salesforce record → Commercient customer master (related record) |
| Shipping address | Commercient Ship To Address Managed Custom Object | 90 | Shipping address company number, Shipping address customer number, Shipping address code → Commercient external key, Shipping address company number, Shipping address customer number → Commercient account (related record), Shipping address company number, Shipping address customer number → Commercient customer master (related record), Shipping address name → Name, Shipping address delete code → Commercient shipping address delete code |
| Sales order header | Commercient Sales Order Header Managed Custom Object | 99 | Order header company number, Order header order number, Back order code → Commercient external key, Order header company number, Order header customer number → Commercient account (related record), Order header company number, Order header customer number → Commercient customer master (related record), Order header company number, Order header order number, Back order code → Name, Order delete code → Commercient order delete code |
| Sales order detail | Commercient Sales Order Detail Managed Custom Object | 97 | Order line company number, Order line order number, Order line back order code, Order line number → external key column, Order line company number, Order line order number, Order line back order code, Order line number → Name, Order line delete code → Order line delete code, Order line company number → Order line company number, Order line order number → Order line order number |
| Product | Product | 5 | Item company number, Inventory item → Commercient external key (earlier package), Inventory item → Product code, Item description 1, Item description 2, Item description line 3 → Description, Item description 1, Item description 2, Item description line 3, Inventory item → Name |
| Item | Item (custom object) | 99 | Item company number, Inventory item → External key (custom field), the linked Salesforce record → Product (custom field), Item description 1, Item description 2, Item description line 3, Inventory item → Name, Item delete code → Item delete code (custom field), Item company number → Item company number (custom field) |
| Product To Item Reverse Lookup | Product | 2 | Item company number, Inventory item → Commercient external key (earlier package), the linked Salesforce record → Item master (custom field) |
| Warehouse | Warehouse (custom object) | 99 | Warehouse company number, Warehouse location code → External key (custom field), Warehouse company number, Warehouse location code, Warehouse name → Name, Warehouse delete code → Warehouse delete code, Warehouse company number → Warehouse company number, Warehouse location code → Warehouse location code |
| Item Warehouse | Item Warehouse (custom object) | 101 | Item warehouse delete code, Item warehouse company number, Item warehouse location code, Item warehouse item number → External key (custom field), Item warehouse company number, Item warehouse item number → Product (custom field), Item warehouse company number, Item warehouse item number → Item master (custom field), Item warehouse company number, Item warehouse location code → Warehouse (custom field), Item warehouse delete code, Item warehouse company number, Item warehouse location code, Item warehouse item number → Name |
| Customer memo notes Sync | Customer memo notes (custom object) | 27 | Note company number, Note customer number, Note date, Note time, Note text line 1, Note text line 2, Note text line 3 → External key (custom field), Note company number, Note customer number → Account, Note company number, Note customer number → Customer master (custom field), Note company number, Note customer number, Note date, Note time → Name, Note delete code → Note delete code |
| Customer other notes Sync | Customer other notes (custom object) | 27 | Note company number, Note customer number, Note date, Note time, Note text line 1 → External key (custom field), Note company number, Note customer number → Account, Note company number, Note customer number → Customer master (custom field), Note company number, Note customer number, Note date, Note time → Name, Note delete code → Note delete code |
| Prospect Account | Account | 14 | Prospect company number, Prospect number → Commercient AR customer code, Prospect name → Name, Prospect phone → Phone, Type → Type, Prospect address line 1, Prospect address line 2, Prospect address line 3 → Billing street |

## 6. Community templates

The catalogue carries 68 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 68
- Default operations: insert on 68, update on 68, delete on 68
- Marked as circular sync: 7
- Licence groups they span: 14
- Destination objects: Account, Contact, Order, Price book entry, Product, Order product,
  Commercient Ship To Address Managed Custom Object, Commercient AR Customer Managed Custom Object,
  Commercient Sales Order Detail Managed Custom Object, Commercient Sales Order Header Managed
  Custom Object, Quote, Quote line item, Commercient Division Managed Custom Object, Commercient
  Division Class Managed Custom Object, Commercient Sales Order History Detail Managed Custom
  Object, Commercient Sales Order History Header Managed Custom Object, Commercient Salesperson
  Managed Custom Object, Commercient Item Managed Custom Object, Commercient Item Warehouse Managed
  Custom Object, Commercient Warehouse Managed Custom Object, 10 more and 6 custom objects
- Object display names: Account, Account To Customer Reverse Lookup, Contact, Customer, Item, Item
  Warehouse, Product, Product To Item Reverse Lookup, Sales order detail, Sales order header,
  Shipping address, Standard Pricebook Create, 28 more and 15 further templates
- Template groups: Account, Product, CRM Order and Line, Sales order, CRM Quote and Line, Customer
  Multi Ship Addresses, CRM Opportunity and Line, Opportunity

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/vai-s2k`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped VAI S2K → Salesforce templates set up. dlake-crmpro-salesforce is the destination skill
this page sits under: its own text is the authority for the Salesforce conventions that hold across
every ERP, and its ERP table lists this page alongside every sibling ERP page for this destination.
For the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/vai-s2k`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
