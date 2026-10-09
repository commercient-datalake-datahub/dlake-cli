---
name: dlake-crmpro-salesforce/erps/macola-10
kind: erp-summary
description: >-
  Use it when standing up or reading a Macola 10 → Salesforce template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-salesforce, the destination skill this page is a child of, which
  carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Macola 10: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/macola-10` (or `list_skills`) against the
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
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Commercient Macola 10 Customer Managed Custom Object | accounts, addresses |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Macola 10 Address Managed Custom Object | addresses, accounts |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Macola 10 Order Header Managed Custom Object, Commercient Macola 10 Order Detail Managed Custom Object, Commercient Macola 10 Order History Header Managed Custom Object, Commercient Macola 10 Order History Detail Managed Custom Object | order headers, accounts, addresses, order lines, order history headers |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Account | Account | Commercient AR customer code | 1 |
| Macola 10 customer | Commercient Macola 10 Customer Managed Custom Object | External key (custom field) | 2 |
| Macola 10 address | Commercient Macola 10 Address Managed Custom Object | External key (custom field) | 3 |
| Customer to account lookup | Account | Commercient AR customer code | 4 |
| Macola 10 sales order header | Commercient Macola 10 Order Header Managed Custom Object | External key (custom field) | 12 |
| Macola 10 sales order detail | Commercient Macola 10 Order Detail Managed Custom Object | External key (custom field) | 13 |
| Macola 10 sales order history header | Commercient Macola 10 Order History Header Managed Custom Object | External key (custom field) | 15 |
| Macola 10 sales order history detail | Commercient Macola 10 Order History Detail Managed Custom Object | External key (custom field) | 16 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| account feed | insert + update | accounts, addresses |
| customer feed | insert + update | accounts, addresses |
| address feed | insert only | addresses, accounts |
| customer account lookup feed | insert + update | accounts |
| sales order feed | insert + update | order headers, accounts, addresses |
| sales order line feed | insert + update | order lines, order headers |
| sales order history feed | insert + update | order history headers, accounts, addresses |
| sales order history line feed | insert + update | order history lines, order history headers |

## 4. Order of work

The templates set run sequence to 1, 2, 3, 4, 12, 13, 15, 16. A run processes active rows in
ascending run sequence, which is the order the templates put them in:

- 1 — Account
- 2 — Macola 10 customer
- 3 — Macola 10 address
- 4 — Customer to account lookup
- 12 — Macola 10 sales order header
- 13 — Macola 10 sales order detail
- 15 — Macola 10 sales order history header
- 16 — Macola 10 sales order history detail

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- customer account lookup feed reads customer sync output
- customer feed reads account sync output
- address feed reads account sync output, customer sync output
- sales order feed reads account sync output, customer sync output, address sync output
- sales order line feed reads sales order sync output
- sales order history feed reads account sync output, customer sync output, address sync output
- sales order history line feed reads sales order history sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Account | Account | 15 | Commercient AR customer code column → Commercient AR customer code, Region → Region (custom field), parent account lookup → parent account lookup, Phone → Phone, Billing street → Billing street |
| Macola 10 customer | Commercient Macola 10 Customer Managed Custom Object | 92 | account lookup value → Lookup to account (custom field), Account employee identifier → Account employee identifier (custom field), Account rating → Account rating (custom field), Account type code → Account type code (custom field), Acknowledge → Acknowledge (custom field) |
| Macola 10 address | Commercient Macola 10 Address Managed Custom Object | 62 | account lookup value → Lookup to account (custom field), Macola 10 customer → Macola 10 customer (custom field), Main → Main (custom field), Type → Type (custom field), → |
| Customer to account lookup | Account | 2 | Commercient AR customer code column → Commercient AR customer code, Macola 10 customer → Macola 10 customer (custom field) |
| Macola 10 sales order header | Commercient Macola 10 Order Header Managed Custom Object | 89 | Order type → Order type (custom field), Order number → Order number (custom field), status → Status (custom field), Entered date → Entered date (custom field), Order date → Order date (custom field) |
| Macola 10 sales order detail | Commercient Macola 10 Order Detail Managed Custom Object | 68 | Order type → Order type (custom field), Order number → Order number (custom field), Line sequence number → Line sequence number (custom field), Item number → Item number (custom field), Location → Location (custom field) |
| Macola 10 sales order history header | Commercient Macola 10 Order History Header Managed Custom Object | 96 | Order type → Order type (custom field), Shipping name → Shipping name (custom field), Order number → Order number (custom field), status → Status (custom field), Entered date → Entered date (custom field) |
| Macola 10 sales order history detail | Commercient Macola 10 Order History Detail Managed Custom Object | 81 | ERP identifier → ERP identifier (custom field), Order type → Order type (custom field), Order number → Order number (custom field), Line sequence number → Line sequence number (custom field), Item number → Item number (custom field) |

## 6. Community templates

The catalogue carries 110 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 110
- Default operations: insert on 110, update on 110, delete on 110
- Marked as circular sync: 0
- Licence groups they span: 10
- Destination objects: Content document, Account, Macola 10 Quote Detail (custom object), Macola 10
  Quote Header (custom object), Macola 10 Sales Order Detail (custom object), Macola 10 Sales Order
  Header (custom object), Macola 10 Sales Order History Detail (custom object), Macola 10 Sales
  Order History Header (custom object), Macola 10 Transaction (custom object), Opportunity,
  Commercient Macola 10 Order Detail Managed Custom Object, Commercient Macola 10 Address Managed
  Custom Object, Commercient Macola 10 Item Managed Custom Object, Commercient Macola 10 Order
  Header Managed Custom Object, Commercient Macola 10 Addresses Managed Custom Object, Commercient
  Macola 10 Customer Managed Custom Object, Commercient Macola 10 Invoice Header Managed Custom
  Object, Commercient Macola 10 Invoice Lines Managed Custom Object, Commercient Macola 10 Sales
  Order Header Managed Custom Object, Commercient Macola 10 Sales Order Lines Managed Custom Object,
  8 more and 6 custom objects
- Object display names: Document Sync - Macola 10 quote sync output (generic name), Document Sync -
  Macola 10 Sales Order, Document Sync - Macola 10 Sales Order History, Get Account, Sync Macola 10
  quote sync output (generic name) Detail, Sync Macola 10 quote sync output (generic name) Header,
  Sync Macola 10 Sales Order Detail, Sync Macola 10 Sales Order Header, Sync Macola 10 Sales Order
  History Detail, Sync Macola 10 Sales Order History Header, Sync Macola 10 Transactions, Sync
  opportunity sync output (generic name), 18 more and 5 further templates
- Template groups: Sales order, Account, Opportunity, CRM Opportunity and Line, Invoice, Customer
  Multi Ship Addresses, Product

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/macola-10`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Macola 10 → Salesforce templates set up. dlake-crmpro-salesforce is the destination
skill this page sits under: its own text is the authority for the Salesforce conventions that hold
across every ERP, and its ERP table lists this page alongside every sibling ERP page for this
destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/macola-10`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
