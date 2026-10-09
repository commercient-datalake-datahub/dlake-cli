---
name: dlake-crmpro-salesforce/erps/sap-hana
kind: erp-summary
description: >-
  Use it when standing up or reading a SAP HANA → Salesforce template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-salesforce, the destination skill this page is a child of, which
  carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — SAP HANA: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/sap-hana` (or `list_skills`) against the
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
| **Get Users** | The templates push users to Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | users | — |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Contact, Commercient Customer Master Managed Custom Object, Commercient Sales Document Partner Managed Custom Object | customers, addresses, contact persons, email addresses, sales document partners |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Accounting Document Header Managed Custom Object, Commercient Journal Entry Line Managed Custom Object | accounting document headers, journal entry lines |
| **Invoice History Headers** | The Invoices from the ERP invoice module are synchronized to the Commercient Invoice Header (MCO) object in CRM. Customer service and sales people can visualize the status of the Invoice such as open, closed, as well as the balance remaining and the due date. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Billing Document Header Managed Custom Object, Commercient Billing Document Line Managed Custom Object | billing document headers, billing document lines |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Sales Order Header Managed Custom Object, Commercient Sales Order Line Managed Custom Object | sales order headers, delivery lines, sales order lines, delivery headers, post goods issue status |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| GET USER | users | — | 0 |
| Sync Salesperson | Commercient Sales Document Partner Managed Custom Object | Commercient external key (SAP package) | 0 |
| Sync Account | Account | Commercient AR customer code | 1 |
| Sync Customer | Commercient Customer Master Managed Custom Object | Commercient external key (SAP package) | 2 |
| Sync Customer to account lookup | Account | Commercient AR customer code | 3 |
| Sync Contact records | Contact | External key (custom field) | 4 |
| Sync Sales order header | Commercient Sales Order Header Managed Custom Object | Commercient external key (SAP package) | 5 |
| Sync sales order detail | Commercient Sales Order Line Managed Custom Object | Commercient external key (SAP package) | 6 |
| Sync open invoice sync output | Commercient Accounting Document Header Managed Custom Object | Commercient external key (SAP package) | 7 |
| Sync Invoice history header | Commercient Billing Document Header Managed Custom Object | Commercient external key (SAP package) | 8 |
| Sync Invoice history line | Commercient Billing Document Line Managed Custom Object | Commercient external key (SAP package) | 9 |
| Sync Invoice payment | Commercient Journal Entry Line Managed Custom Object | Commercient external key (SAP package) | 10 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| salesperson feed | insert only | sales document partners |
| account feed | insert + update | customers, addresses |
| customer feed | insert only | customers |
| account reverse lookup feed | insert + update | customers |
| contact feed | insert + update | contact persons, customers, email addresses |
| sales order feed | insert only | sales order headers |
| sales order line feed | insert only | sales order headers, delivery lines, sales order lines, delivery headers, post goods issue status |
| open invoice feed | insert only | accounting document headers |
| invoice history feed | insert only | billing document headers |
| invoice history line feed | insert only | billing document lines |
| invoice payment feed | insert only | journal entry lines |

## 4. Order of work

The templates set run sequence from 0 to 10. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 0 — GET USER, Sync Salesperson
- 1 — Sync Account
- 2 — Sync Customer
- 3 — Sync Customer to account lookup
- 4 — Sync Contact records
- 5 — Sync Sales order header
- 6 — Sync sales order detail
- 7 — Sync open invoice sync output
- 8 — Sync Invoice history header
- 9 — Sync Invoice history line
- 10 — Sync Invoice payment

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- account feed reads salesperson sync output, AR terms sync output, user sync output; no template in
  this set writes AR terms sync output
- account reverse lookup feed reads customer sync output, account sync output
- contact feed reads account sync output
- customer feed reads account sync output, salesperson sync output, terms sync output; no template
  in this set writes terms sync output
- open invoice feed reads account sync output, customer sync output
- invoice payment feed reads account sync output, customer sync output, invoice history sync output
- invoice history feed reads account sync output, customer sync output
- invoice history line feed reads invoice history sync output
- sales order feed reads account sync output, customer sync output
- sales order line feed reads sales order sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Sync Salesperson | Commercient Sales Document Partner Managed Custom Object | 23 | Client → Commercient client, Sales document number → Commercient sales document number, Item number → Commercient item number, Partner function → Commercient partner function, Vendor number → Commercient vendor number |
| Sync Account | Account | 15 | Commercient AR customer code column → Commercient AR customer code, Commercient SAP HANA salesperson (related record) → Commercient SAP HANA salesperson (related record), Commercient SAP HANA terms (related record) → Commercient SAP HANA terms (related record), Type → Type, Billing street → Billing street |
| Sync Customer | Commercient Customer Master Managed Custom Object | 232 | Account → Account, SAP HANA salesperson (custom field) → Commercient SAP HANA salesperson (related record), SAP HANA terms (custom field) → Commercient SAP HANA terms (related record), Rule exclusion → Rule exclusion, Alternative payer account → Commercient alternative payer account |
| Sync Customer to account lookup | Account | 2 | Commercient AR customer code column → Commercient AR customer code, Commercient SAP HANA customer (related record) → Commercient SAP HANA customer (related record) |
| Sync Contact records | Contact | 12 | Given name → Given name, Family name → Family name, Email → Email, Phone → Phone, Title → Title |
| Sync Sales order header | Commercient Sales Order Header Managed Custom Object | 210 | Account → Commercient account (related record), Customer master → Commercient Customer Master Managed Custom Object, Client → Commercient client, Sales document number → Commercient sales document number, Creation date → Commercient creation date |
| Sync sales order detail | Commercient Sales Order Line Managed Custom Object | 355 | Sales document header → Commercient Sales Order Header Managed Custom Object, Client → Commercient client, Sales document number → Commercient sales document number, Item number → Commercient item number, Material entered → Commercient material entered |
| Sync open invoice sync output | Commercient Accounting Document Header Managed Custom Object | 112 | Account → Commercient account (related record), Customer master → Commercient Customer Master Managed Custom Object, Client → Commercient client, Company code → Commercient company code, Accounting document number → Commercient accounting document number |
| Sync Invoice history header | Commercient Billing Document Header Managed Custom Object | 116 | Account → Commercient account (related record), Customer master → Commercient Customer Master Managed Custom Object, Pricing procedure → Commercient pricing procedure, Document condition number → Commercient document condition number, Shipping conditions → Commercient shipping conditions |
| Sync Invoice history line | Commercient Billing Document Line Managed Custom Object | 279 | Billing document header → Commercient Billing Document Header Managed Custom Object, Statistical values indicator → Commercient statistical values indicator, Pricing indicator → Commercient pricing indicator, Cash discount indicator → Commercient cash discount indicator, Cash discount base amount → Commercient cash discount base amount |
| Sync Invoice payment | Commercient Journal Entry Line Managed Custom Object | 415 | Billing document header → Billing document header (custom field), account lookup → Account identifier (custom field), Customer master → Customer master (custom field), Accounting document number → Commercient accounting document number, Document line number → Commercient document line number |

## 6. Community templates

The catalogue carries 50 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 50
- Default operations: insert on 50, update on 50, delete on 50
- Marked as circular sync: 0
- Licence groups they span: 12
- Destination objects: Account, Product, Commercient Customer Master Managed Custom Object,
  Commercient Sales Order Line Managed Custom Object, Contact, Commercient Accounting Document
  Header Managed Custom Object, Commercient Sales Order Header Managed Custom Object, Commercient
  Sales Document Partner Managed Custom Object, Commercient Billing Document Header Managed Custom
  Object, Commercient Billing Document Line Managed Custom Object, Commercient SAP HANA Address
  Managed Custom Object, Commercient Journal Entry Line Managed Custom Object and 11 custom objects
- Object display names: Sync Account, Sync Contact records, Sync Customer, Sync Customer to account
  lookup, SAP HANA Serial number, SAP HANA Customer Address, Sync AR terms, Sync Invoice history
  header, Sync Invoice history line, Sync Items, Sync Item to product lookup, Sync open invoice sync
  output, 17 more and a further template
- Template groups: Account, Product, Sales order, Invoice, Invoice History Headers, Customer Multi
  Ship Addresses

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/sap-hana`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped SAP HANA → Salesforce templates set up. dlake-crmpro-salesforce is the destination skill
this page sits under: its own text is the authority for the Salesforce conventions that hold across
every ERP, and its ERP table lists this page alongside every sibling ERP page for this destination.
For the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/sap-hana`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
