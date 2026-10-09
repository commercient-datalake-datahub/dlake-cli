---
name: dlake-crmpro-salesforce/erps/infor-m3
kind: erp-summary
description: >-
  Use it when standing up or reading an Infor M3 → Salesforce template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-salesforce, the destination skill this page is a child of, which
  carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Infor M3: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/infor-m3` (or `list_skills`) against the
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
| **Infor M3 Shipping address** | The templates push Commercient Shipping Address Managed Custom Object to Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Shipping Address Managed Custom Object | customer addresses |
| **Product** | The templates push Product to Salesforce. New records are created and existing ones updated; none are deleted. | Product | item master records |
| **Infor M3 Item master** | The templates push Commercient Item Master Managed Custom Object to Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Item Master Managed Custom Object | item master records |
| **Infor M3 Item warehouse** | The templates push Commercient Item Warehouse Managed Custom Object to Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Item Warehouse Managed Custom Object | item warehouse records |
| **Product to Item Master Reverse Lookup** | The templates push Product to Salesforce. New records are created and existing ones updated; none are deleted. | Product | item master records |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created and existing ones updated; none are deleted. | Account, Contact, Commercient Customer Managed Custom Object, Commercient Code Table Managed Custom Object | customers, code tables |
| **CRM Ownership** | The templates push user to Salesforce. New records are created and existing ones updated; none are deleted. | user | — |
| **Invoice History Headers** | The Invoices from the ERP invoice module are synchronized to the Commercient Invoice Header (MCO) object in CRM. Customer service and sales people can visualize the status of the Invoice such as open, closed, as well as the balance remaining and the due date. New records are created and existing ones updated; none are deleted. | Commercient Invoice Header Managed Custom Object | invoice headers |
| **Open AR Invoice Header** | The detail lines on open invoices are visible too so that you are aware of what items you are awaiting payment on from your customer. New records are created and existing ones updated; none are deleted. | Commercient Invoice Line Managed Custom Object | invoice lines, invoice headers |
| **Sales order** | The templates push Commercient Sales Order Line Managed Custom Object, Commercient Sales Order Header Managed Custom Object to Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Sales Order Line Managed Custom Object, Commercient Sales Order Header Managed Custom Object | sales order lines, sales order headers |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Infor M3 Salesperson | Commercient Code Table Managed Custom Object | External key (custom field) | 1 |
| Account | Account | Commercient AR customer code | 2 |
| Infor M3 Customer | Commercient Customer Managed Custom Object | Commercient external key (Infor package) | 3 |
| Infor M3 Customer to account lookup | Account | Commercient AR customer code | 4 |
| Infor M3 Shipping address | Commercient Shipping Address Managed Custom Object | External key (custom field) | 5 |
| Infor M3 Sales order header | Commercient Sales Order Header Managed Custom Object | Commercient external key (Infor package) | 6 |
| Infor M3 Sales order line | Commercient Sales Order Line Managed Custom Object | Commercient external key (Infor package) | 7 |
| Infor M3 Invoice header | Commercient Invoice Header Managed Custom Object | Commercient external key (Infor package) | 8 |
| Infor M3 invoice line | Commercient Invoice Line Managed Custom Object | Commercient external key (Infor package) | 9 |
| Contact | Contact | External key (custom field) | 10 |
| Product | Product | Commercient external key (earlier package) | 11 |
| Infor M3 Item master | Commercient Item Master Managed Custom Object | Commercient external key (Infor package) | 12 |
| Infor M3 Item warehouse | Commercient Item Warehouse Managed Custom Object | Commercient external key (Infor package) | 13 |
| Product to Item Master Reverse Lookup | Product | Commercient external key (earlier package) | 17 |
| Get Users | user | AU sales rep number (custom field) | 20 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| salesperson feed | insert + update | code tables |
| account feed | insert + update | customers |
| customer feed | insert + update | customers |
| customer account lookup feed | insert + update | customers |
| shipping address feed | insert + update | customer addresses |
| sales order feed | insert + update | sales order headers |
| sales order line feed | insert + update | sales order lines |
| invoice feed | insert + update | invoice headers |
| invoice line feed | insert + update | invoice lines, invoice headers |
| product feed | insert + update | item master records |
| item master feed | insert + update | item master records |
| item warehouse feed | insert only | item warehouse records |
| product item master lookup feed | insert + update | item master records |

## 4. Order of work

The templates set run sequence from 1 to 20. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 1 — Infor M3 Salesperson
- 2 — Account
- 3 — Infor M3 Customer
- 4 — Infor M3 Customer to account lookup
- 5 — Infor M3 Shipping address
- 6 — Infor M3 Sales order header
- 7 — Infor M3 Sales order line
- 8 — Infor M3 Invoice header
- 9 — Infor M3 invoice line
- 10 — Contact
- 11 — Product
- 12 — Infor M3 Item master
- 13 — Infor M3 Item warehouse
- 17 — Product to Item Master Reverse Lookup
- 20 — Get Users

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- shipping address feed reads account sync output, customer sync output
- item master feed reads product sync output
- item warehouse feed reads product sync output, item master sync output
- product item master lookup feed reads item master sync output
- account feed reads salesperson sync output, user sync output
- customer account lookup feed reads customer sync output
- customer feed reads account sync output, salesperson sync output
- invoice feed reads account sync output, customer sync output
- invoice line feed reads invoice sync output
- sales order line feed reads sales order sync output
- sales order feed reads account sync output, customer sync output, salesperson sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Infor M3 Salesperson | Commercient Code Table Managed Custom Object | 16 | Code table company number, Code type, Code → External key (custom field), Code description → Name, Code table company number → Code table company number (custom field), Code table division → Code table division (custom field), Code type → Code type (custom field) |
| Account | Account | 15 | Customer company number, Customer number → Commercient AR customer code, Customer name → Name, Customer phone number → Phone, Customer address line 1, Customer address line 2, Customer address line 3 → Billing street, Customer city → Billing city |
| Infor M3 Customer | Commercient Customer Managed Custom Object | 110 | Customer company number, Customer number → Commercient external key (Infor package), Customer name → Name, the linked Salesforce record → Account, Customer address line 1 → Customer address line 1, Customer address line 2 → Customer address line 2 |
| Infor M3 Customer to account lookup | Account | 2 | Customer company number, Customer number → Commercient AR customer code, the linked Salesforce record → Commercient customer (related record) |
| Infor M3 Shipping address | Commercient Shipping Address Managed Custom Object | 63 | Address company number, Address customer number, Address type, Address identifier → External key (custom field), Address company number, Address customer number, Address type, Address identifier → Name, Address company number, Address customer number → Account, Address company number, Address customer number → Customer (custom field), Address change date → Address change date |
| Infor M3 Sales order header | Commercient Sales Order Header Managed Custom Object | 97 | Sales order company number, Sales order number → Commercient external key (Infor package), Sales order company number, Sales order number → Commercient name, Sales order company number, Sales order customer number → Account, Sales order company number, Sales order customer number → Commercient customer (related record), Sales order change date → Commercient sales order change date |
| Infor M3 Sales order line | Commercient Sales Order Line Managed Custom Object | 33 | Order line company number, Sales order line order number, Sales order line number, Sales order line suffix → Commercient external key (Infor package), Order line company number, Sales order line order number, Sales order line number, Sales order line suffix → Name, the linked Salesforce record → Commercient sales order header (related record), Sales order line change date → Sales order line change date, Sales order line confirmed date → Sales order line confirmed date |
| Infor M3 Invoice header | Commercient Invoice Header Managed Custom Object | 53 | Invoice company number, Invoice division, Invoice number, Invoice year → Commercient external key (Infor package), Invoice company number, Invoice division, Invoice number, Invoice year → Name, Invoice company number, Payer number → Commercient account (related record), Invoice company number, Payer number → Commercient customer (related record), Invoice entry date → Commercient invoice entry date |
| Infor M3 invoice line | Commercient Invoice Line Managed Custom Object | 41 | the linked Salesforce record → Commercient invoice header (related record), Invoice line change date → Commercient invoice line change date, Invoice line entry date → Commercient invoice line entry date, Invoice line entry time → Commercient entry time, Invoice line change date → Commercient invoice line change date (second field) |
| Contact | Contact | 1 | External key (custom field) → External key (custom field) |
| Product | Product | 5 | Item company number, Item number → Commercient external key (earlier package), Active → Active, Item name → Name, Item number → Product code, Item description → Description |
| Infor M3 Item master | Commercient Item Master Managed Custom Object | 208 | Item company number, Item number → Commercient external key (Infor package), Item name → Name, Item company number → Commercient item company number, Item status → Commercient item status, Item number → Commercient item number |
| Infor M3 Item warehouse | Commercient Item Warehouse Managed Custom Object | 152 | Item warehouse company number, Item warehouse item number, Warehouse code → Commercient external key (Infor package), Item warehouse company number, Item warehouse item number, Warehouse code → Name, Item warehouse company number → Item warehouse company number, Warehouse code → Warehouse code, Item warehouse item number → Item warehouse item number |
| Product to Item Master Reverse Lookup | Product | 2 | Item company number, Item number → Commercient external key (earlier package), the linked Salesforce record → Commercient item master (related record) |
| Get Users | user | 1 | — |

## 6. Community templates

The catalogue carries 18 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 18
- Default operations: insert on 18, update on 18, delete on 18
- Marked as circular sync: 0
- Licence groups they span: 11
- Destination objects: Account, Product, user, Commercient Item Warehouse Managed Custom Object,
  Commercient Item Master Managed Custom Object, Commercient Customer Managed Custom Object,
  Commercient Invoice Header Managed Custom Object, Commercient Invoice Line Managed Custom Object,
  Commercient Sales Order Header Managed Custom Object, Commercient Sales Order Line Managed Custom
  Object, Commercient Code Table Managed Custom Object, Commercient Shipping Address Managed Custom
  Object, Contact, Location, Product item
- Object display names: Get Users, Account, Contact, Get Location, Infor M3 Customer, Infor M3
  Customer to account lookup, Infor M3 Invoice header, Infor M3 invoice line, Infor M3 Item master,
  Infor M3 Item warehouse, Infor M3 Sales order header, Infor M3 Sales order line and 5 more
- Template groups: Account, Product, Invoice, Sales order, CRM Ownership, Customer Multi Ship
  Addresses

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/infor-m3`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Infor M3 → Salesforce templates set up. dlake-crmpro-salesforce is the destination skill
this page sits under: its own text is the authority for the Salesforce conventions that hold across
every ERP, and its ERP table lists this page alongside every sibling ERP page for this destination.
For the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/infor-m3`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
