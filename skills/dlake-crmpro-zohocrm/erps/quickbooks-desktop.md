---
name: dlake-crmpro-zohocrm/erps/quickbooks-desktop
kind: erp-summary
description: >-
  Use it when standing up or reading a QuickBooks Desktop → Zoho CRM template set, when deciding
  which templates to import and activate, or when a run completes without pushing records and the
  answer is in the view or the configuration row. It extends dlake-crmpro, which covers operating
  CRMPro generally, and dlake-crmpro-zohocrm, the destination skill this page is a child of, which
  carries the Zoho CRM conventions that hold across every ERP.
---
# CRMPro → Zoho CRM — QuickBooks Desktop: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-zohocrm/erps/quickbooks-desktop` (or `list_skills`) against the
Commercient admin plane. Existing customers who need access or help: contact
support@commercient.com. New customers: contact sales@commercient.com to become a customer and be
whitelisted.

dlake-crmpro is the parent skill and the authority for everything general: the CRMPro tools, process
configuration and field list, the sync history, how source data is selected, and what a run that
finds nothing does. Read it first; this page does not repeat it. dlake-crmpro-zohocrm is the
destination skill this page is a child of, and the authority for the Zoho CRM conventions that hold
across every ERP: read it first, then come back here for what this source's own templates set. This
page grows as the catalogue does.

## 1. What the templates deliver

| Group | Business outcome | Objects | Source tables and views |
|---|---|---|---|
| **QuickBooks Terms** | The templates push Commercient QuickBooks Terms object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient QuickBooks Terms object | terms |
| **Quickbook Sales rep** | The templates push Commercient QuickBooks Salespeople object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient QuickBooks Salespeople object | sales reps |
| **Account** | The templates push Accounts to Zoho CRM. New records are created and existing ones updated; none are deleted. | Accounts | customers |
| **Child Account** | The templates push Accounts to Zoho CRM. New records are created and existing ones updated; none are deleted. | Accounts | — |
| **QuickBooks Customers** | The templates push Commercient QuickBooks Customer object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient QuickBooks Customer object | customers |
| **Contacts** | The templates push Contacts to Zoho CRM. New records are created and existing ones updated; none are deleted. | Contacts | contacts |
| **Products** | The templates push Products to Zoho CRM. New records are created and existing ones updated; none are deleted. | Products | inventory items, inventory assembly items, non inventory items |
| **QuickBooks Item** | The templates push Commercient QuickBooks Item object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient QuickBooks Item object | inventory items, inventory assembly items, non inventory items |
| **QuickBooks Sales order header** | The templates push Commercient QuickBooks Sales Order Header object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient QuickBooks Sales Order Header object | sales orders |
| **QuickBooks Sales order detail** | The templates push Commercient QuickBooks Sales Order Detail object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient QuickBooks Sales Order Detail object | sales order lines |
| **QuickBooks Invoice header** | The templates push Commercient QuickBooks Invoice Header object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient QuickBooks Invoice Header object | invoices |
| **QuickBooks Invoice detail** | The templates push Commercient QuickBooks Invoice Detail object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient QuickBooks Invoice Detail object | invoice lines |
| **QuickBooks Credit Memo** | The templates push Commercient QuickBooks Invoice Header object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient QuickBooks Invoice Header object | credit memos, invoices |
| **QuickBooks Credit Memo Line** | The templates push Commercient QuickBooks Invoice Detail object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient QuickBooks Invoice Detail object | credit memo lines, invoice lines |
| **CRM Ownership** | The templates push users to Zoho CRM. New records are created and existing ones updated; none are deleted. | users | — |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Users | users | Commercient external key column | 0 |
| QuickBooks Terms | Commercient QuickBooks Terms object | Commercient external key column | 1 |
| Quickbook Sales rep | Commercient QuickBooks Salespeople object | Commercient external key column | 2 |
| Account | Accounts | Commercient AR customer code (QuickBooks package field) | 3 |
| Child Account | Accounts | Commercient AR customer code (Zoho field) | 4 |
| QuickBooks Customers | Commercient QuickBooks Customer object | Commercient external key column | 5 |
| Contacts | Contacts | Commercient external key column | 6 |
| Products | Products | Commercient external key column | 7 |
| QuickBooks Item | Commercient QuickBooks Item object | Commercient external key column | 8 |
| QuickBooks Sales order header | Commercient QuickBooks Sales Order Header object | Commercient external key column | 9 |
| QuickBooks Sales order detail | Commercient QuickBooks Sales Order Detail object | Commercient external key column | 10 |
| QuickBooks Invoice header | Commercient QuickBooks Invoice Header object | Commercient external key column | 11 |
| QuickBooks Invoice detail | Commercient QuickBooks Invoice Detail object | Commercient external key column | 12 |
| QuickBooks Credit Memo | Commercient QuickBooks Invoice Header object | Commercient external key column | 14 |
| QuickBooks Credit Memo Line | Commercient QuickBooks Invoice Detail object | Commercient external key column | 15 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| terms feed | insert only | terms |
| sales rep feed | insert + update | sales reps |
| account feed | — | customers |
| customer feed | insert + update | customers |
| contact feed | insert + update | contacts |
| product feed | insert + update | inventory items, inventory assembly items, non inventory items |
| item feed | insert + update | inventory items, inventory assembly items, non inventory items |
| sales order header feed | insert + update | sales orders |
| sales order detail feed | insert + update | sales order lines |
| invoice header feed | insert + update | invoices |
| invoice detail feed | insert + update | invoice lines |
| credit memo feed | insert + update | credit memos, invoices |
| credit memo line feed | insert + update | credit memo lines, invoice lines |

## 4. Order of work

The templates set run sequence from 0 to 15. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 0 — Users
- 1 — QuickBooks Terms
- 2 — Quickbook Sales rep
- 3 — Account
- 4 — Child Account
- 5 — QuickBooks Customers
- 6 — Contacts
- 7 — Products
- 8 — QuickBooks Item
- 9 — QuickBooks Sales order header
- 10 — QuickBooks Sales order detail
- 11 — QuickBooks Invoice header
- 12 — QuickBooks Invoice detail
- 14 — QuickBooks Credit Memo
- 15 — QuickBooks Credit Memo Line

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- terms feed reads terms sync output; no template in this set writes terms sync output
- sales rep feed reads sales rep sync output; no template in this set writes sales rep sync output
- account feed reads sales rep sync output, account sync output; no template in this set writes
  sales rep sync output, account sync output
- customer feed reads account sync output, sales rep sync output, customer sync output; no template
  in this set writes account sync output, sales rep sync output, customer sync output
- contact feed reads account sync output, customer sync output, contact sync output; no template in
  this set writes account sync output, customer sync output, contact sync output
- item feed reads product sync output, item sync output; no template in this set writes item sync
  output
- sales order header feed reads account sync output, customer sync output, sales order header sync
  output; no template in this set writes account sync output, customer sync output, sales order
  header sync output
- sales order detail feed reads sales order header sync output, item sync output, sales order detail
  sync output; no template in this set writes sales order header sync output, item sync output,
  sales order detail sync output
- invoice header feed reads account sync output, customer sync output, invoice header sync output;
  no template in this set writes account sync output, customer sync output, invoice header sync
  output
- invoice detail feed reads invoice header sync output, item sync output, invoice detail sync
  output; no template in this set writes invoice header sync output, item sync output, invoice
  detail sync output
- credit memo feed reads account sync output, customer sync output, credit memo sync output; no
  template in this set writes account sync output, customer sync output, credit memo sync output
- credit memo line feed reads credit memo sync output, item sync output, credit memo line sync
  output; no template in this set writes credit memo sync output, item sync output, credit memo line
  sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Zoho CRM pairs |
|---|---|---|---|
| Users | users | 1 | — |
| QuickBooks Terms | Commercient QuickBooks Terms object | 14 | returned list identifier → Commercient external key (custom field), Day of month due → Day of month due (Zoho field), Discount day of month → Discount day of month (Zoho field), Discount percent → Discount percent (Zoho field), Due next month days → Due next month days (Zoho field) |
| Quickbook Sales rep | Commercient QuickBooks Salespeople object | 9 | returned list identifier → Commercient external key (custom field), Sales rep full name → Name, returned list identifier → List identifier (Zoho field), Time created → Time created (Zoho field), Time modified → Time modified (Zoho field) |
| Child Account | Accounts | 1 | Commercient AR customer code (Zoho field) → Commercient AR customer code (Zoho field) |
| Contacts | Contacts | 13 | the linked Salesforce record → Account name, the linked Salesforce record → QuickBooks customer (related record), Salutation → Salutation, Given name → First name, Family name → Last name |
| Products | Products | 21 | returned list identifier → Commercient external key column, Name → ERP product code, Full name → Description, Name → Product name (Zoho field), Quantity on hand → Quantity in stock (Zoho field) |
| QuickBooks Item | Commercient QuickBooks Item object | 100 | returned list identifier → Commercient external key (custom field), Asset account full name → Asset account reference full name (Zoho field), Asset account list identifier → Asset account reference list identifier (Zoho field), Average cost → Average cost (Zoho field), Barcode value → Bar code value (Zoho field) |

## 6. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-zohocrm/erps/quickbooks-desktop`.

## 7. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped QuickBooks Desktop → Zoho CRM templates set up. dlake-crmpro-zohocrm is the destination
skill this page sits under: its own text is the authority for the Zoho CRM conventions that hold
across every ERP, and its ERP table lists this page alongside every sibling ERP page for this
destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-zohocrm/erps/quickbooks-desktop`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
