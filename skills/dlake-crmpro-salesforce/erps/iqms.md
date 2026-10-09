---
name: dlake-crmpro-salesforce/erps/iqms
kind: erp-summary
description: >-
  Use it when standing up or reading an IQMS → Salesforce template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-salesforce, the destination skill this page is a child of, which
  carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — IQMS: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/iqms` (or `list_skills`) against the Commercient
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
| **Get Users** | The templates push User to Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | User | — |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Contact, Commercient AR Customer Managed Custom Object, Commercient Salespeople Managed Custom Object | AR customers, shipping addresses, contacts, salespeople |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Ship To Managed Custom Object | shipping addresses |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient AR Invoice Managed Custom Object, Commercient AR Invoice Detail Managed Custom Object | AR invoices, AR invoice lines |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Order Detail Managed Custom Object, Commercient Order History Managed Custom Object, Commercient Order History Detail Managed Custom Object | sales order lines, order history headers, order history lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Get Users | User | — | 0 |
| IQMS Salesperson | Commercient Salespeople Managed Custom Object | Commercient external key (IQMS package) | 2 |
| Account | Account | Commercient AR customer code | 4 |
| IQMS Customer | Commercient AR Customer Managed Custom Object | Commercient external key (IQMS package) | 6 |
| IQMS Customer to account lookup | Account | Commercient AR customer code | 7 |
| IQMS Shipping address | Commercient Ship To Managed Custom Object | Commercient external key (IQMS package) | 8 |
| IQMS Sales order detail | Commercient Order Detail Managed Custom Object | Commercient external key (IQMS package) | 10 |
| IQMS Sales order history header | Commercient Order History Managed Custom Object | Commercient external key (IQMS package) | 11 |
| IQMS Sales order history detail | Commercient Order History Detail Managed Custom Object | Commercient external key (IQMS package) | 12 |
| IQMS Invoice header | Commercient AR Invoice Managed Custom Object | Commercient external key (IQMS package) | 13 |
| IQMS Invoice detail | Commercient AR Invoice Detail Managed Custom Object | Commercient external key (IQMS package) | 14 |
| Contact | Contact | External key (custom field) | 16 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| salesperson feed | insert + update | salespeople |
| account feed | insert + update | AR customers, shipping addresses |
| customer feed | insert + update | AR customers |
| customer account lookup feed | insert + update | AR customers |
| shipping address feed | insert + update | shipping addresses |
| sales order line feed | insert + update | sales order lines |
| sales order history feed | insert + update | order history headers |
| sales order history line feed | insert + update | order history lines |
| invoice feed | insert + update | AR invoices |
| invoice line feed | insert + update | AR invoice lines |
| contact feed | insert + update | contacts |

## 4. Order of work

The templates set run sequence from 0 to 16. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 0 — Get Users
- 2 — IQMS Salesperson
- 4 — Account
- 6 — IQMS Customer
- 7 — IQMS Customer to account lookup
- 8 — IQMS Shipping address
- 10 — IQMS Sales order detail
- 11 — IQMS Sales order history header
- 12 — IQMS Sales order history detail
- 13 — IQMS Invoice header
- 14 — IQMS Invoice detail
- 16 — Contact

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- account feed reads terms sync output, salesperson sync output, user sync output; no template in
  this set writes terms sync output
- customer account lookup feed reads customer sync output, account sync output
- contact feed reads account sync output
- customer feed reads account sync output, salesperson sync output, terms sync output; no template
  in this set writes terms sync output
- shipping address feed reads account sync output, customer sync output
- invoice feed reads account sync output, customer sync output
- invoice line feed reads invoice sync output
- sales order line feed reads sales order sync output; no template in this set writes sales order
  sync output
- sales order history feed reads account sync output, customer sync output
- sales order history line feed reads sales order history sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| IQMS Salesperson | Commercient Salespeople Managed Custom Object | 16 | record identifier → Commercient record identifier, Employee identifier → Commercient employee identifier, Sales code → Sales code, First name → Commercient first name, Last name → Commercient last name |
| Account | Account | 16 | Commercient AR customer code column → Commercient AR customer code, Phone → Phone, Billing street → Billing street, Billing city → Billing city, Billing state → Billing state |
| IQMS Customer | Commercient AR Customer Managed Custom Object | 178 | Account → Account, linked IQMS salesperson → Commercient IQMS salesperson (related record), linked IQMS terms → Commercient IQMS terms (related record), record identifier → Commercient record identifier, Terms identifier → Commercient terms identifier |
| IQMS Customer to account lookup | Account | 2 | Commercient AR customer code column → Commercient AR customer code, linked IQMS AR customer → Commercient IQMS AR customer (related record) |
| IQMS Shipping address | Commercient Ship To Managed Custom Object | 109 | Account → Account, linked AR customer → linked AR customer, record identifier → Commercient record identifier, AR customer identifier → AR customer identifier, Attention → Commercient attention |
| IQMS Sales order detail | Commercient Order Detail Managed Custom Object | 81 | linked IQMS order header → Commercient IQMS order header (related record), record identifier → Commercient record identifier, Order identifier → Commercient order identifier, Inventory item identifier → Commercient inventory item identifier, Order line sequence number → Commercient order line sequence number |
| IQMS Sales order history header | Commercient Order History Managed Custom Object | 130 | record identifier → Commercient record identifier, Account → Account, linked AR customer → linked AR customer, Order number → Commercient order number, Customer purchase order number → Commercient customer purchase order number |
| IQMS Sales order history detail | Commercient Order History Detail Managed Custom Object | 90 | linked order history header → Commercient Order History Managed Custom Object, record identifier → Commercient record identifier, Order identifier → Commercient order identifier, Inventory item identifier → Commercient inventory item identifier, Order line sequence number → Commercient order line sequence number |
| IQMS Invoice header | Commercient AR Invoice Managed Custom Object | 92 | Account → Account, linked AR customer → linked AR customer, record identifier → Commercient record identifier, AR general ledger account identifier → AR general ledger account identifier, AR customer identifier → AR customer identifier |
| IQMS Invoice detail | Commercient AR Invoice Detail Managed Custom Object | 76 | AR invoice → AR invoice, record identifier → Commercient record identifier, AR invoice identifier → AR invoice identifier, Shipment line identifier → Commercient shipment line identifier, Order line identifier → Commercient order line identifier |
| Contact | Contact | 12 | account lookup → account lookup, Given name → Given name, Family name → Family name, Email → Email, Phone → Phone |

## 6. Community templates

The catalogue carries 47 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 47
- Default operations: insert on 47, update on 47, delete on 47
- Marked as circular sync: 0
- Licence groups they span: 12
- Destination objects: Account, Commercient AR Customer Managed Custom Object, Commercient AR
  Invoice Managed Custom Object, Commercient AR Invoice Detail Managed Custom Object, Commercient
  Order History Detail Managed Custom Object, Commercient Order History Managed Custom Object,
  Commercient Ship To Managed Custom Object, Contact, Commercient Order Detail Managed Custom
  Object, Commercient Orders Managed Custom Object, Commercient Salespeople Managed Custom Object,
  Commercient Terms Managed Custom Object, Product, IQMS Inventory (custom object), IQMS Order
  Detail (custom object), IQMS Quote Detail (custom object), IQMS Quote Header (custom object) and 3
  custom objects
- Object display names: Account, IQMS Customer, IQMS Customer to account lookup, Sync Account, Sync
  Contact, Sync Customer, Sync Customer to account lookup, Sync invoice detail, Sync invoice header,
  Sync sales order detail, Sync Sales order header, Sync Sales order history detail, 19 more and 2
  further templates
- Template groups: Account, Sales order, Invoice, Product, Customer Multi Ship Addresses,
  Opportunity

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/iqms`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped IQMS → Salesforce templates set up. dlake-crmpro-salesforce is the destination skill
this page sits under: its own text is the authority for the Salesforce conventions that hold across
every ERP, and its ERP table lists this page alongside every sibling ERP page for this destination.
For the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/iqms`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
