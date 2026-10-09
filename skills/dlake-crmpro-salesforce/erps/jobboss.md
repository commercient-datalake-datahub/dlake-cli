---
name: dlake-crmpro-salesforce/erps/jobboss
kind: erp-summary
description: >-
  Use it when standing up or reading a JobBOSS → Salesforce template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-salesforce, the destination skill this page is a child of, which
  carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — JobBOSS: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/jobboss` (or `list_skills`) against the
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
| **Get Owners** | The templates push User to Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | User | — |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Contact, Commercient Customer Managed Custom Object, Commercient Employee Managed Custom Object | addresses, customers, contacts, employees |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created and existing ones updated; none are deleted. | Commercient JobBoss Address Managed Custom Object | addresses |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created and existing ones updated; none are deleted. | Commercient Invoice Header Managed Custom Object, Commercient Invoice Detail Managed Custom Object | invoice headers, invoice details |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created and existing ones updated; none are deleted. | Commercient Sales Order Header Managed Custom Object, Commercient JobBoss Sales Order Detail Managed Custom Object | sales order headers, sales order details |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Get Owners | User | Commercient salesperson code | 0 |
| JobBoss Salesperson | Commercient Employee Managed Custom Object | Commercient external key (Exact package) | 1 |
| Accounts | Account | Commercient AR customer code | 2 |
| Sync Account | Account | Commercient AR customer code | 2 |
| JobBoss Customer | Commercient Customer Managed Custom Object | Commercient external key (Exact package) | 4 |
| JobBoss Customer To Account Reverse Lookup | Account | Commercient AR customer code | 5 |
| JobBoss Address | Commercient JobBoss Address Managed Custom Object | Commercient external key (Exact package) | 6 |
| JobBoss Contact | Contact | External key (custom field) | 7 |
| JobBoss Sales order header | Commercient Sales Order Header Managed Custom Object | Commercient external key (Exact package) | 7 |
| JobBoss Sales order detail | Commercient JobBoss Sales Order Detail Managed Custom Object | Commercient external key (Exact package) | 8 |
| JobBoss Invoice header | Commercient Invoice Header Managed Custom Object | Commercient external key (Exact package) | 9 |
| JobBoss Invoice detail | Commercient Invoice Detail Managed Custom Object | Commercient external key (Exact package) | 10 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| salesperson feed | insert + update | employees |
| account feed | insert + update | addresses, customers |
| account feed | insert + update | addresses, customers |
| customer feed | insert + update | customers |
| customer account lookup feed | insert + update | customers |
| address feed | insert + update | addresses |
| contact feed | insert + update | contacts |
| sales order feed | insert + update | sales order headers |
| sales order line feed | insert + update | sales order details |
| invoice feed | insert + update | invoice headers |
| invoice line feed | insert + update | invoice details |

## 4. Order of work

The templates set run sequence from 0 to 10. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 0 — Get Owners
- 1 — JobBoss Salesperson
- 2 — Accounts, Sync Account
- 4 — JobBoss Customer
- 5 — JobBoss Customer To Account Reverse Lookup
- 6 — JobBoss Address
- 7 — JobBoss Contact, JobBoss Sales order header
- 8 — JobBoss Sales order detail
- 9 — JobBoss Invoice header
- 10 — JobBoss Invoice detail

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- account feed reads salesperson sync output (generic name), account sync output (generic name); no
  template in this set writes salesperson sync output (generic name), account sync output (generic
  name)
- customer account lookup feed reads customer sync output
- account feed reads salesperson sync output, user sync output; no template in this set writes user
  sync output
- contact feed reads account sync output, customer sync output
- customer feed reads account sync output, salesperson sync output
- address feed reads account sync output, customer sync output
- invoice feed reads account sync output, customer sync output
- invoice line feed reads invoice sync output
- sales order feed reads account sync output, customer sync output
- sales order line feed reads sales order sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| JobBoss Salesperson | Commercient Employee Managed Custom Object | 28 | Employee → Commercient external key (Exact package), First name, Last name → Name, Employee → Commercient employee, Work center → Commercient work center, Address → Commercient Address Managed Custom Object |
| Accounts | Account | 14 | Customer → Commercient AR customer code, Name → Name, Sales representative → Commercient salesperson (related record), Phone → Phone, Address line 1, Address line 2 → Billing street |
| Sync Account | Account | 13 | Commercient AR customer code column → Commercient AR customer code, Phone → Phone, Billing street → Billing street, Billing city → Billing city, Billing state → Billing state |
| JobBoss Customer | Commercient Customer Managed Custom Object | 24 | Customer → Commercient external key (Exact package), the linked Salesforce record → Account, the linked Salesforce record → JobBoss salesperson, Name → Name, Email address → Email address |
| JobBoss Customer To Account Reverse Lookup | Account | 2 | Customer → Commercient AR customer code, the linked Salesforce record → Commercient Customer Managed Custom Object |
| JobBoss Address | Commercient JobBoss Address Managed Custom Object | 17 | Address key → Commercient external key (Exact package), Customer → Account, Customer → JobBoss customer, Name → Name, Address line 1 → Address line 1 |
| JobBoss Contact | Contact | 10 | account lookup → account lookup, JobBoss customer → JobBoss customer (custom field), Family name → Family name, Email address → Email address (custom field), Email → Email |
| JobBoss Sales order header | Commercient Sales Order Header Managed Custom Object | 30 | Sales order (single record) → Commercient external key (Exact package), Sales order (single record) → Commercient record name, Customer → Commercient account (related record), Customer → Commercient Customer Managed Custom Object, Sales order (single record) → Commercient sales order |
| JobBoss Sales order detail | Commercient JobBoss Sales Order Detail Managed Custom Object | 50 | Sales order detail key → Commercient external key (Exact package), Sales order (single record), Sales order line number → Commercient record name, the linked Salesforce record → Commercient sales order header (related record), Sales order detail (JobBoss table) → Commercient Sales Order Detail Managed Custom Object, Sales order (single record) → Commercient sales order |
| JobBoss Invoice header | Commercient Invoice Header Managed Custom Object | 32 | Document → Commercient external key (Exact package), Document → Commercient record name, Customer → Commercient account (related record), Customer → Commercient Customer Managed Custom Object, Document date → Commercient document date |
| JobBoss Invoice detail | Commercient Invoice Detail Managed Custom Object | 46 | Invoice detail key → Commercient external key (Exact package), Document, Document line → Commercient record name, Document → Commercient Invoice Header Managed Custom Object, Invoice detail (JobBoss table) → Commercient Invoice Line Managed Custom Object, Document → Commercient document |

## 6. Community templates

The catalogue carries 143 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 143
- Default operations: insert on 143, update on 143, delete on 143
- Marked as circular sync: 0
- Licence groups they span: 14
- Destination objects: Account, Opportunity, Contact, Opportunity line item, Product, Commercient
  Customer Managed Custom Object, Commercient JobBoss Quote Managed Custom Object, Price book entry,
  Commercient JobBoss Address Managed Custom Object, Commercient Employee Managed Custom Object,
  Commercient Invoice Detail Managed Custom Object, Commercient Invoice Header Managed Custom
  Object, Commercient JobBoss Sales Order Detail Managed Custom Object, Commercient Sales Order
  Header Managed Custom Object, Commercient Item Managed Custom Object, Commercient JobBoss Item
  Warehouse Managed Custom Object, Commercient JobBoss Quote Line Managed Custom Object, User,
  Commercient Job Managed Custom Object, 19 more and 10 custom objects
- Object display names: JobBoss Customer, JobBoss Customer To Account Reverse Lookup, Accounts,
  JobBoss Contact, JobBoss Invoice detail, JobBoss Invoice header, JobBoss quote sync output
  (generic name), Create Standard Price Book, JobBoss Address, JobBoss Salesperson, Opportunity
  Line, Sync Address, 54 more and 20 further templates
- Template groups: Account, CRM Opportunity and Line, Product, Invoice, Opportunity, Sales order,
  Customer Multi Ship Addresses, CRM Order and Line, Quote line number

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/jobboss`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped JobBOSS → Salesforce templates set up. dlake-crmpro-salesforce is the destination skill
this page sits under: its own text is the authority for the Salesforce conventions that hold across
every ERP, and its ERP table lists this page alongside every sibling ERP page for this destination.
For the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/jobboss`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
