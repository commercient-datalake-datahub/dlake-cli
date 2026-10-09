---
name: dlake-crmpro-salesforce/erps/myob-advanced
kind: erp-summary
description: >-
  Use it when standing up or reading a MYOB Advanced → Salesforce template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-salesforce, the destination skill this page is a child of, which
  carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — MYOB Advanced: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/myob-advanced` (or `list_skills`) against the
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
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Commercient Customer Managed Custom Object | customers, customer contacts, contacts |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Sales Invoice Managed Custom Object, Commercient Sales Invoice Detail Managed Custom Object | sales invoices, sales invoice lines |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Sales Order Managed Custom Object, Commercient Sales Order Detail Managed Custom Object | sales orders, sales order details |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Account | Account | Commercient AR customer code | 1 |
| MYOB Advanced Customer | Commercient Customer Managed Custom Object | Commercient external key (Acumatica and MYOB package) | 2 |
| MYOB Advanced Customer To Account Lookup | Account | Commercient AR customer code | 3 |
| MYOB Advanced Sales Order Header | Commercient Sales Order Managed Custom Object | Commercient external key (Acumatica and MYOB package) | 4 |
| MYOB Advanced Sales Order Detail | Commercient Sales Order Detail Managed Custom Object | Commercient external key (Acumatica and MYOB package) | 5 |
| MYOB Advanced Invoice Header | Commercient Sales Invoice Managed Custom Object | Commercient external key (Acumatica and MYOB package) | 6 |
| MYOB Advanced Invoice Detail | Commercient Sales Invoice Detail Managed Custom Object | Commercient external key (Acumatica and MYOB package) | 7 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| account feed | insert + update | customers, customer contacts, contacts |
| customer feed | insert + update | customers |
| customer account lookup feed | insert + update | customers |
| sales order header feed | insert + update | sales orders |
| sales order detail feed | insert + update | sales order details |
| invoice header feed | insert + update | sales invoices |
| invoice detail feed | insert + update | sales invoice lines |

## 4. Order of work

The templates set run sequence to 1, 2, 3, 4, 5, 6, 7. A run processes active rows in ascending run
sequence, which is the order the templates put them in:

- 1 — Account
- 2 — MYOB Advanced Customer
- 3 — MYOB Advanced Customer To Account Lookup
- 4 — MYOB Advanced Sales Order Header
- 5 — MYOB Advanced Sales Order Detail
- 6 — MYOB Advanced Invoice Header
- 7 — MYOB Advanced Invoice Detail

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- customer account lookup feed reads customer sync output
- customer feed reads account sync output
- invoice header feed reads account sync output, customer sync output
- invoice detail feed reads invoice header sync output
- sales order header feed reads account sync output, customer sync output
- sales order detail feed reads sales order header sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Account | Account | 14 | Commercient AR customer code column → Commercient AR customer code, Billing street → Billing street, Billing city → Billing city, Billing state → Billing state, Billing country → Billing country |
| MYOB Advanced Customer | Commercient Customer Managed Custom Object | 49 | Account → Account, Account reference → Account reference, Apply overdue charges → Apply overdue charges, Automatically apply payments → Commercient auto apply payments, Billing address same as main → Commercient billing address same as main |
| MYOB Advanced Customer To Account Lookup | Account | 2 | Commercient AR customer code column → Commercient AR customer code, Commercient MYOB Advanced customer (source column) → Commercient MYOB Advanced customer (related record) |
| MYOB Advanced Sales Order Header | Commercient Sales Order Managed Custom Object | 64 | Account → Account, Customer → Commercient Customer Managed Custom Object, Approved → Commercient approved, Base currency → Base currency, Billing address line 1 → Commercient bill to address line 1 |
| MYOB Advanced Sales Order Detail | Commercient Sales Order Detail Managed Custom Object | 44 | Sales order → Commercient Sales Order Managed Custom Object, Order number → Commercient order number, Order type → Commercient order type, account → account, Alternate identifier → Commercient alternate identifier |
| MYOB Advanced Invoice Header | Commercient Sales Invoice Managed Custom Object | 22 | Account → Account, Customer → Commercient Customer Managed Custom Object, Amount → Commercient amount, Balance → Balance, Cash discount → Cash discount |
| MYOB Advanced Invoice Detail | Commercient Sales Invoice Detail Managed Custom Object | 14 | Sales invoice → Sales invoice, Reference number → Reference number, Sales invoice number → Sales invoice number, Amount → Commercient amount, Branch → Branch |

## 6. Community templates

The catalogue carries 7 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 7
- Default operations: insert on 7, update on 7, delete on 7
- Marked as circular sync: 0
- Licence groups they span: 4
- Destination objects: Account, Commercient Customer Managed Custom Object, Commercient Sales
  Invoice Managed Custom Object, Commercient Sales Invoice Detail Managed Custom Object, Commercient
  Sales Order Managed Custom Object, Commercient Sales Order Detail Managed Custom Object
- Object display names: Account, MYOB Advanced Customer, MYOB Advanced Customer To Account Lookup,
  MYOB Advanced Invoice Detail, MYOB Advanced Invoice Header, MYOB Advanced Sales Order Detail, MYOB
  Advanced Sales Order Header
- Template groups: Account, Invoice, Sales order

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/myob-advanced`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped MYOB Advanced → Salesforce templates set up. dlake-crmpro-salesforce is the destination
skill this page sits under: its own text is the authority for the Salesforce conventions that hold
across every ERP, and its ERP table lists this page alongside every sibling ERP page for this
destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/myob-advanced`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
