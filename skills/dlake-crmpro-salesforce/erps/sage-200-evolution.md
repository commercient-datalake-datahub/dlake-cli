---
name: dlake-crmpro-salesforce/erps/sage-200-evolution
kind: erp-summary
description: >-
  Use it when standing up or reading a Sage 200 Evolution → Salesforce template set, when deciding
  which templates to import and activate, or when a run completes without pushing records and the
  answer is in the view or the configuration row. It extends dlake-crmpro, which covers operating
  CRMPro generally, and dlake-crmpro-salesforce, the destination skill this page is a child of,
  which carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Sage 200 Evolution: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/sage-200-evolution` (or `list_skills`) against
the Commercient admin plane. Existing customers who need access or help: contact
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
| **GET USER** | The templates push users to Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | users | — |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created and existing ones updated; none are deleted. | Account, Commercient Client Managed Custom Object, Commercient Sales Rep Managed Custom Object | customers, sales reps |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created and existing ones updated; none are deleted. | Commercient Invoice Header Managed Custom Object, Commercient Invoice Line Managed Custom Object | invoice and order headers, C, S, invoice and order lines |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created and existing ones updated; none are deleted. | Commercient Sales Order Header Managed Custom Object, Commercient Sales Order Line Managed Custom Object | invoice and order headers, C, S, invoice and order lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| GET USER | users | — | 0 |
| Sales Person | Commercient Sales Rep Managed Custom Object | Commercient external key | 1 |
| Account | Account | Commercient AR customer code | 2 |
| Customer | Commercient Client Managed Custom Object | Commercient external key | 3 |
| Customer to Account Reverse Lookup | Account | Commercient AR customer code | 4 |
| Sales Order Header | Commercient Sales Order Header Managed Custom Object | Commercient external key | 5 |
| Sales Order Detail | Commercient Sales Order Line Managed Custom Object | Commercient external key | 6 |
| Invoice Header | Commercient Invoice Header Managed Custom Object | Commercient external key | 7 |
| Invoice Detail | Commercient Invoice Line Managed Custom Object | Commercient external key | 8 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| salesperson feed | insert + update | sales reps |
| account feed | insert + update | customers |
| customer feed | insert + update | customers |
| customer account lookup feed | insert + update | customers |
| sales order feed | insert + update | invoice and order headers, C, S |
| sales order line feed | insert + update | invoice and order lines |
| invoice feed | insert + update | invoice and order headers, C, S |
| invoice line feed | insert + update | invoice and order lines |

## 4. Order of work

The templates set run sequence from 0 to 8. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 0 — GET USER
- 1 — Sales Person
- 2 — Account
- 3 — Customer
- 4 — Customer to Account Reverse Lookup
- 5 — Sales Order Header
- 6 — Sales Order Detail
- 7 — Invoice Header
- 8 — Invoice Detail

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- account feed reads salesperson sync output (generic name), user sync output
- customer account lookup feed reads customer sync output (generic name)
- customer feed reads salesperson sync output (generic name), account sync output (generic name)
- invoice feed reads salesperson sync output (generic name), account sync output (generic name),
  customer sync output (generic name)
- invoice line feed reads invoice sync output (generic name)
- sales order feed reads salesperson sync output (generic name), account sync output (generic name),
  customer sync output (generic name)
- sales order line feed reads sales order header sync output (generic name)

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Sales Person | Commercient Sales Rep Managed Custom Object | 30 | Sales rep identifier → Commercient external key, Name → Commercient name, Code → Commercient code, Method → Commercient method, Target 1 → Commercient target 1 |
| Account | Account | 13 | Customer account link → Commercient AR customer code, Name, Account → Name, Telephone number → Phone, Assigned sales rep → Commercient Sage 200 Evolution salesperson, Physical address line 1, Physical address line 2 → Billing street |
| Customer | Commercient Client Managed Custom Object | 104 | Customer account link → Commercient external key, Assigned sales rep → Sage 200 Evolution salesperson (custom field), Customer account link → Account lookup (custom field), Name → Commercient name, Title → Title |
| Customer to Account Reverse Lookup | Account | 2 | Customer account link → Commercient AR customer code, the linked Salesforce record → Commercient Sage 200 Evolution customer (related record) |
| Sales Order Header | Commercient Sales Order Header Managed Custom Object | 319 | Document record number → Commercient external key, Order number → Name, Assigned sales rep → Sage 200 Evolution salesperson (custom field), Customer account link → Account lookup (custom field), Account → Sage 200 Evolution customer (custom field) |
| Sales Order Detail | Commercient Sales Order Line Managed Custom Object | 211 | Invoice identifier, Invoice line identifier → Commercient external key, Invoice identifier, Invoice line identifier → Name, the linked Salesforce record → Sage 200 Evolution sales order header (custom field), Invoice line identifier → Invoice line identifier, Invoice identifier → Invoice identifier |
| Invoice Header | Commercient Invoice Header Managed Custom Object | 6 | Document record number → Commercient external key, Invoice number → Name, Assigned sales rep → Sage 200 Evolution salesperson (custom field), Customer account link → Account lookup (custom field), Account → Sage 200 Evolution customer (custom field) |
| Invoice Detail | Commercient Invoice Line Managed Custom Object | 208 | Invoice identifier,Invoice line identifier → Commercient external key, Invoice identifier,Invoice line identifier → Name, the linked Salesforce record → Sage 200 Evolution invoice header (custom field), Invoice line identifier → Invoice line identifier, Invoice identifier → Invoice identifier |

## 6. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/sage-200-evolution`.

## 7. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Sage 200 Evolution → Salesforce templates set up. dlake-crmpro-salesforce is the
destination skill this page sits under: its own text is the authority for the Salesforce conventions
that hold across every ERP, and its ERP table lists this page alongside every sibling ERP page for
this destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/sage-200-evolution`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
