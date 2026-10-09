---
name: dlake-crmpro-salesforce/erps/eci-m1
kind: erp-summary
description: >-
  Use it when standing up or reading an ECi M1 → Salesforce template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-salesforce, the destination skill this page is a child of, which
  carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — ECi M1: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/eci-m1` (or `list_skills`) against the
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
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Child Account | companies, company locations |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient ECi M1 AR Invoices Managed Custom Object, Commercient ECi M1 AR Invoice Lines Managed Custom Object | AR invoices, AR invoice lines |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient ECi M1 Sales Orders Managed Custom Object, Commercient ECi M1 Sales Order Lines Managed Custom Object | sales order headers, sales order lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Account | Account | Commercient AR customer code | 1 |
| Child Account | Child Account | Commercient AR customer code | 2 |
| ECi M1 Customer to account lookup | Account | Commercient AR customer code | 4 |
| ECi M1 Sales order header | Commercient ECi M1 Sales Orders Managed Custom Object | Commercient external key (ECi M1 package) | 6 |
| ECi M1 Sales order detail | Commercient ECi M1 Sales Order Lines Managed Custom Object | Commercient external key (ECi M1 package) | 7 |
| ECi M1 Invoice header | Commercient ECi M1 AR Invoices Managed Custom Object | Commercient external key (ECi M1 package) | 8 |
| ECi M1 Invoice detail | Commercient ECi M1 AR Invoice Lines Managed Custom Object | Commercient external key (ECi M1 package) | 9 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| account feed | insert + update | companies, company locations |
| child account feed | insert + update | company locations, companies |
| customer account lookup feed | insert + update | companies |
| sales order feed | insert + update | sales order headers |
| sales order line feed | insert + update | sales order lines |
| invoice feed | insert + update | AR invoices |
| invoice line feed | insert + update | AR invoice lines |

## 4. Order of work

The templates set run sequence to 1, 2, 4, 6, 7, 8, 9. A run processes active rows in ascending run
sequence, which is the order the templates put them in:

- 1 — Account
- 2 — Child Account
- 4 — ECi M1 Customer to account lookup
- 6 — ECi M1 Sales order header
- 7 — ECi M1 Sales order detail
- 8 — ECi M1 Invoice header
- 9 — ECi M1 Invoice detail

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- child account feed reads account sync output
- customer account lookup feed reads customer sync output, account sync output; no template in this
  set writes customer sync output
- invoice feed reads account sync output, customer sync output; no template in this set writes
  customer sync output
- invoice line feed reads invoice sync output
- sales order feed reads account sync output, customer sync output; no template in this set writes
  customer sync output
- sales order line feed reads sales order sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Account | Account | 13 | Commercient AR customer code column → Commercient AR customer code, Phone → Phone, Billing street → Billing street, Billing city → Billing city, Billing country → Billing country |
| Child Account | Child Account | 14 | Commercient AR customer code column → Commercient AR customer code, Phone → Phone, Billing street → Billing street, Billing city → Billing city, Billing country → Billing country |
| ECi M1 Customer to account lookup | Account | 2 | Commercient AR customer code column → Commercient AR customer code, Commercient ECi M1 customer (related record) → Commercient ECi M1 customer (related record) |
| ECi M1 Sales order header | Commercient ECi M1 Sales Orders Managed Custom Object | 86 | Account → Account, company lookup value → Commercient company (related record), Sales order identifier → Commercient sales order identifier, Plant identifier → Commercient plant identifier, Plant department identifier → Commercient plant department identifier |
| ECi M1 Sales order detail | Commercient ECi M1 Sales Order Lines Managed Custom Object | 67 | Sales orders → Commercient sales order (related record), Line sales order identifier → Commercient line sales order identifier, Sales order line identifier → Commercient sales order line identifier, Order line part identifier → Commercient order line part identifier, Customer part identifier → Commercient customer part identifier |
| ECi M1 Invoice header | Commercient ECi M1 AR Invoices Managed Custom Object | 93 | Account → Account, company lookup value → Commercient company (related record), AR invoice identifier → AR invoice identifier, AR invoice type → AR invoice type, Credit AR invoice identifier → Credit AR invoice identifier |
| ECi M1 Invoice detail | Commercient ECi M1 AR Invoice Lines Managed Custom Object | 94 | AR invoice (related record) → AR invoice (related record), Line AR invoice identifier → Line AR invoice identifier, AR invoice line identifier → AR invoice line identifier, Invoice line type → Commercient invoice line type, Invoice line part identifier → Commercient invoice line part identifier |

## 6. Community templates

The catalogue carries 27 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 27
- Default operations: insert on 27, update on 27, delete on 27
- Marked as circular sync: 0
- Licence groups they span: 7
- Destination objects: Account, Child Account, Commercient ECi M1 AR Invoice Lines Managed Custom
  Object, Commercient ECi M1 Company Locations Managed Custom Object, Commercient ECi M1 Sales Order
  Lines Managed Custom Object, Commercient ECi M1 Sales Orders Managed Custom Object, Account
  Matching (custom object), Contact, Contact Matching (custom object), User and 2 custom objects
- Object display names: Child Account, Account, ECi M1 Customer, ECi M1 Customer to account lookup,
  ECi M1 Invoice detail, ECi M1 Invoice header, ECi M1 Sales order detail, ECi M1 Sales order
  header, ECi M1 Shipping address, Contact, Get Users, Sync account matching and 1 more
- Template groups: Account, Invoice, Sales order, Customer Multi Ship Addresses

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/eci-m1`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped ECi M1 → Salesforce templates set up. dlake-crmpro-salesforce is the destination skill
this page sits under: its own text is the authority for the Salesforce conventions that hold across
every ERP, and its ERP table lists this page alongside every sibling ERP page for this destination.
For the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/eci-m1`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
