---
name: dlake-crmpro-salesforce/erps/microsoft-dynamics-sl-2015
kind: erp-summary
description: >-
  Use it when standing up or reading a Microsoft Dynamics SL 2015 → Salesforce template set, when
  deciding which templates to import and activate, or when a run completes without pushing records
  and the answer is in the view or the configuration row. It extends dlake-crmpro, which covers
  operating CRMPro generally, and dlake-crmpro-salesforce, the destination skill this page is a
  child of, which carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Microsoft Dynamics SL 2015: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/microsoft-dynamics-sl-2015` (or `list_skills`)
against the Commercient admin plane. Existing customers who need access or help: contact
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
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Commercient customer (related record) | customers, addresses, customer code identifiers |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient address | customer shipping addresses |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Dynamics SL Invoice Header Managed Custom Object, Commercient Dynamics SL Invoice Detail Managed Custom Object | AR documents, AR invoice lines |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Dynamics SL Sales Order Header Managed Custom Object, Commercient Dynamics SL Sales Order Detail Managed Custom Object | project invoice headers, project invoice lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Account | Account | Commercient AR customer code | 1 |
| Sync Customer | Commercient customer (related record) | Commercient external key (Dynamics NAV package) | 2 |
| Sync Customer to account lookup | Account | Commercient AR customer code | 3 |
| Sync Address | Commercient address | Commercient external key (Dynamics NAV package) | 4 |
| Sync Sales Order Header | Commercient Dynamics SL Sales Order Header Managed Custom Object | Commercient external key (Dynamics NAV package) | 5 |
| Sync Sales Order Detail | Commercient Dynamics SL Sales Order Detail Managed Custom Object | Commercient external key (Dynamics NAV package) | 6 |
| Sync invoice record sync output (generic name) Header | Commercient Dynamics SL Invoice Header Managed Custom Object | Commercient external key (Dynamics NAV package) | 7 |
| Sync invoice record sync output (generic name) Detail | Commercient Dynamics SL Invoice Detail Managed Custom Object | Commercient external key (Dynamics NAV package) | 8 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| account feed | insert + update | customers, addresses, customer code identifiers |
| customer feed | insert + update | customers |
| customer account lookup feed | insert + update | customers |
| address feed | insert + update | customer shipping addresses |
| sales order header feed | insert + update | project invoice headers |
| sales order detail feed | insert + update | project invoice lines |
| invoice header feed | insert + update | AR documents |
| invoice detail feed | insert + update | AR invoice lines, AR documents |

## 4. Order of work

The templates set run sequence to 1, 2, 3, 4, 5, 6, 7, 8. A run processes active rows in ascending
run sequence, which is the order the templates put them in:

- 1 — Account
- 2 — Sync Customer
- 3 — Sync Customer to account lookup
- 4 — Sync Address
- 5 — Sync Sales Order Header
- 6 — Sync Sales Order Detail
- 7 — Sync invoice record sync output (generic name) Header
- 8 — Sync invoice record sync output (generic name) Detail

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- customer account lookup feed reads account sync output, customer sync output
- customer feed reads account sync output
- address feed reads account sync output, customer sync output
- invoice header feed reads account sync output, customer sync output
- invoice detail feed reads account sync output, customer sync output, invoice header sync output
- sales order header feed reads account sync output, customer sync output
- sales order detail feed reads sales order header sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Account | Account | 11 | Commercient AR customer code column → Commercient AR customer code, Billing street → Billing street, Billing city → Billing city, Billing state → Billing state, Billing country → Billing country |
| Sync Customer | Commercient customer (related record) | 91 | Apply finance charges → Apply finance charges, Auto apply → Commercient auto apply, Bill through project → Bill through project, Consolidated invoicing → Commercient consolidated invoicing, Customer fill priority → Commercient customer fill priority |
| Sync Customer to account lookup | Account | 2 | Commercient AR customer code column → Commercient AR customer code, Commercient Dynamics SL customer (source column) → Commercient Dynamics SL customer (related record) |
| Sync Address | Commercient address | 67 | Address 1 → Commercient address line 1, Address 2 → Commercient address line 2, Attention → Commercient attention, City → Commercient city, Cost of goods sold account → Cost of goods sold account (custom field) |
| Sync Sales Order Header | Commercient Dynamics SL Sales Order Header Managed Custom Object | 71 | Invoice header user field 10 → Commercient invoice header user field 10, Currency effective date → Commercient currency effective date, Begin date → Commercient begin date, Created date and time → Commercient created date and time, End date → Commercient end date |
| Sync Sales Order Detail | Commercient Dynamics SL Sales Order Detail Managed Custom Object | 94 | Invoice detail user field 10 → Commercient invoice detail user field 10, Line number → Commercient line number, Last updated date and time → Commercient last updated date and time, Invoice detail user field 20 → Commercient invoice detail user field 20, Invoice detail user field 8 → Commercient invoice detail user field 8 |
| Sync invoice record sync output (generic name) Header | Commercient Dynamics SL Invoice Header Managed Custom Object | 91 | Current number → Commercient current number, Cycle → Cycle, Draft issued → Commercient draft issued, Installment number → Commercient installment number, Job counter → Job counter |
| Sync invoice record sync output (generic name) Detail | Commercient Dynamics SL Invoice Detail Managed Custom Object | 91 | Account distribution → Account distribution, →, Flat rate line number → Commercient flat rate line number, Installment number → Commercient installment number, Service call line number → Service call line number |

## 6. Community templates

The catalogue carries 8 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 8
- Default operations: insert on 8, update on 8, delete on 8
- Marked as circular sync: 0
- Licence groups they span: 5
- Destination objects: Account, Commercient address, Commercient customer (related record),
  Commercient Dynamics SL Invoice Detail Managed Custom Object, Commercient Dynamics SL Invoice
  Header Managed Custom Object, Commercient Dynamics SL Sales Order Detail Managed Custom Object,
  Commercient Dynamics SL Sales Order Header Managed Custom Object
- Object display names: Account, Sync Address, Sync Customer, Sync Customer to account lookup, Sync
  invoice record sync output (generic name) Detail, Sync invoice record sync output (generic name)
  Header, Sync Sales Order Detail, Sync Sales Order Header
- Template groups: Account, Invoice, Sales order, Customer Multi Ship Addresses

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/microsoft-dynamics-sl-2015`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Microsoft Dynamics SL 2015 → Salesforce templates set up. dlake-crmpro-salesforce is the
destination skill this page sits under: its own text is the authority for the Salesforce conventions
that hold across every ERP, and its ERP table lists this page alongside every sibling ERP page for
this destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/microsoft-dynamics-sl-2015`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
