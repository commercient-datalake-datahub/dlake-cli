---
name: dlake-crmpro-salesforce/erps/infor-m3-qm
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
`dlake admin get_skill dlake-crmpro-salesforce/erps/infor-m3-qm` (or `list_skills`) against the
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
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Contact, Commercient Customer Managed Custom Object, Commercient Salesperson Managed Custom Object | customers, customer addresses, shipping addresses, sales orders, shipping address details, customer salespeople |
| **CRM Ownership** | The templates push User to Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | User | — |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Shipping Address Managed Custom Object | shipping addresses, sales orders |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Invoice Header Managed Custom Object, Commercient Invoice Line Managed Custom Object | invoices, invoice lines |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Sales Order Header Managed Custom Object, Commercient Sales Order Line Managed Custom Object | sales orders, sales order lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Get Salesforce Users | User | Commercient salesperson code | 0 |
| Infor M3 Salesperson | Commercient Salesperson Managed Custom Object | Commercient external key (Infor package) | 1 |
| Account | Account | Commercient AR customer code | 2 |
| Infor M3 Customer | Commercient Customer Managed Custom Object | Commercient external key (Infor package) | 3 |
| Infor M3 Customer to account lookup | Account | Commercient AR customer code | 4 |
| Infor M3 Ship to | Commercient Shipping Address Managed Custom Object | Commercient external key (Infor package) | 5 |
| Infor M3 Sales order header | Commercient Sales Order Header Managed Custom Object | Commercient external key (Infor package) | 6 |
| Infor M3 Sales order line | Commercient Sales Order Line Managed Custom Object | Commercient external key (Infor package) | 7 |
| Infor M3 Invoice header | Commercient Invoice Header Managed Custom Object | Commercient external key (Infor package) | 8 |
| Infor M3 invoice line | Commercient Invoice Line Managed Custom Object | Commercient external key (Infor package) | 9 |
| Contact | Contact | External key (custom field) | 10 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| salesperson feed | insert + update | salespeople |
| account feed | insert + update | customers, customer addresses, shipping addresses, sales orders, shipping address details |
| customer feed | insert + update | customers |
| customer account lookup feed | insert + update | customers |
| shipping address feed | insert + update | shipping addresses, sales orders |
| sales order feed | insert + update | sales orders |
| sales order line feed | insert + update | sales order lines |
| invoice feed | insert + update | invoices |
| invoice line feed | insert + update | invoice lines |
| contact feed | insert + update | contacts, contact addresses, customer contact keys |

## 4. Order of work

The templates set run sequence from 0 to 10. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 0 — Get Salesforce Users
- 1 — Infor M3 Salesperson
- 2 — Account
- 3 — Infor M3 Customer
- 4 — Infor M3 Customer to account lookup
- 5 — Infor M3 Ship to
- 6 — Infor M3 Sales order header
- 7 — Infor M3 Sales order line
- 8 — Infor M3 Invoice header
- 9 — Infor M3 invoice line
- 10 — Contact

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- account feed reads salesperson sync output, retrieved Salesforce user sync output (Prophet 21
  name); no template in this set writes retrieved Salesforce user sync output (Prophet 21 name)
- customer account lookup feed reads account sync output, customer sync output
- contact feed reads account sync output
- customer feed reads account sync output
- shipping address feed reads account sync output, customer sync output
- invoice feed reads account sync output, customer sync output
- invoice line feed reads invoice sync output
- sales order feed reads account sync output, customer sync output
- sales order line feed reads sales order sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Infor M3 Salesperson | Commercient Salesperson Managed Custom Object | 17 | Sales rep identifier → Commercient sales rep identifier, address → Commercient address, phone → Commercient phone, Territory → Commercient territory, → |
| Account | Account | 10 | Commercient AR customer code column → Commercient AR customer code, Phone → Phone, Billing street → Billing street, Billing city → Billing city, Billing state → Billing state |
| Infor M3 Customer | Commercient Customer Managed Custom Object | 41 | Account → Account, Customer identifier → Commercient customer identifier, phone → Commercient phone, Bill to → Commercient bill to, Ship via → Commercient ship via |
| Infor M3 Customer to account lookup | Account | 2 | Commercient AR customer code column → Commercient AR customer code, Commercient Infor M3 customer (related record) → Commercient Infor M3 customer (related record) |
| Infor M3 Ship to | Commercient Shipping Address Managed Custom Object | 46 | Account → Account, Customer record link → Commercient Customer Managed Custom Object, date → Commercient date, type → Commercient type, via → Commercient via |
| Infor M3 Sales order header | Commercient Sales Order Header Managed Custom Object | 71 | Account → Account, Customer record link → Commercient Customer Managed Custom Object, date → Commercient date, Sold to → Commercient sold to, Booking date → Commercient booking date |
| Infor M3 Sales order line | Commercient Sales Order Line Managed Custom Object | 41 | Sales order record link → Commercient Sales Order Header Managed Custom Object, Parent key → Commercient parent key, Sales order identifier → Commercient sales order identifier, Item value → Commercient item value, → |
| Infor M3 Invoice header | Commercient Invoice Header Managed Custom Object | 47 | Account → Account, Customer record link → Commercient Customer Managed Custom Object, Customer (source column) → Commercient customer, po → Commercient PO, Source document → Source document |
| Infor M3 invoice line | Commercient Invoice Line Managed Custom Object | 18 | Invoice record link → Commercient Invoice Header Managed Custom Object, Parent key → Commercient parent key, AR record identifier → Commercient AR record identifier, Item value → Commercient item value, → |
| Contact | Contact | 10 | account lookup → account lookup, Given name → Given name, Family name → Family name, Title → Title, Fax → Fax |

## 6. Community templates

The catalogue carries 11 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 11
- Default operations: insert on 11, update on 11, delete on 11
- Marked as circular sync: 0
- Licence groups they span: 8
- Destination objects: Account, Commercient Invoice Header Managed Custom Object, Commercient
  Invoice Line Managed Custom Object, Commercient Customer Managed Custom Object, Commercient
  Salesperson Managed Custom Object, Commercient Shipping Address Managed Custom Object, Commercient
  Sales Order Header Managed Custom Object, Commercient Sales Order Line Managed Custom Object,
  Contact, User
- Object display names: Account, Contact, Get Salesforce Users, Infor M3 Customer, Infor M3 Customer
  to account lookup, Infor M3 Invoice header, Infor M3 invoice line, Infor M3 Sales order header,
  Infor M3 Sales order line, Infor M3 Salesperson, Infor M3 Ship to
- Template groups: Account, Invoice, Sales order, Customer Multi Ship Addresses

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/infor-m3-qm`.

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
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/infor-m3-qm`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
