---
name: dlake-crmpro-zohocrm/erps/acumatica
kind: erp-summary
description: >-
  Use it when standing up or reading an Acumatica → Zoho CRM template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-zohocrm, the destination skill this page is a child of, which carries
  the Zoho CRM conventions that hold across every ERP.
---
# CRMPro → Zoho CRM — Acumatica: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-zohocrm/erps/acumatica` (or `list_skills`) against the
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
| **Users** | The templates push users to Zoho CRM. New records are created and existing ones updated; none are deleted. | users | — |
| **Accounts** | The templates push Accounts to Zoho CRM. New records are created and existing ones updated; none are deleted. | Accounts | sales invoices, customers, billing contacts, sales reps, customer billing addresses, customer shipping addresses |
| **Contacts** | The templates push Contacts to Zoho CRM. New records are created and existing ones updated; none are deleted. | Contacts | contacts, customer main addresses |
| **Acumatica Customer** | The templates push Commercient Acumatica customer object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Acumatica customer object | customers |
| **Acumatica Opportunity** | The templates push Acumatica opportunity (custom object) to Zoho CRM. New records are created and existing ones updated; none are deleted. | Acumatica opportunity (custom object) | opportunities, sales reps |
| **Acumatica Contacts** | The templates push Commercient Acumatica customer contact object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Acumatica customer contact object | contacts, customer main addresses |
| **Acumatica Sales order** | The templates push Commercient Acumatica sales order header object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Acumatica sales order header object | sales orders |
| **Acumatica Sales order line** | The templates push Commercient Acumatica sales order detail object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Acumatica sales order detail object | sales order details |
| **Acumatica Invoice** | The templates push Commercient Acumatica invoice header object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Acumatica invoice header object | sales invoices |
| **Acumatica invoice line** | The templates push Commercient Acumatica invoice detail object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Acumatica invoice detail object | sales invoice lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Users | users | Commercient external key column | 0 |
| Accounts | Accounts | Commercient AR customer code (Zoho field) | 1 |
| Contacts | Contacts | Commercient external key column | 3 |
| Acumatica Customer | Commercient Acumatica customer object | Commercient external key column | 4 |
| Acumatica Opportunity | Acumatica opportunity (custom object) | Commercient external key column | 5 |
| Acumatica Contacts | Commercient Acumatica customer contact object | Commercient external key column | 6 |
| Acumatica Sales order | Commercient Acumatica sales order header object | Commercient external key column | 7 |
| Acumatica Sales order line | Commercient Acumatica sales order detail object | Commercient external key column | 8 |
| Acumatica Invoice | Commercient Acumatica invoice header object | Commercient external key column | 9 |
| Acumatica invoice line | Commercient Acumatica invoice detail object | Commercient external key column | 10 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| account feed | insert + update | sales invoices, customers, billing contacts, sales reps, customer billing addresses |
| contact feed | insert only | contacts, customer main addresses |
| customer feed | insert only | customers |
| opportunity feed | insert only | opportunities, sales reps |
| customer contact feed | insert only | contacts, customer main addresses |
| sales order feed | insert only | sales orders |
| sales order line feed | insert only | sales order details |
| invoice feed | insert only | sales invoices |
| invoice line feed | insert only | sales invoice lines |

## 4. Order of work

The templates set run sequence from 0 to 10. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 0 — Users
- 1 — Accounts
- 3 — Contacts
- 4 — Acumatica Customer
- 5 — Acumatica Opportunity
- 6 — Acumatica Contacts
- 7 — Acumatica Sales order
- 8 — Acumatica Sales order line
- 9 — Acumatica Invoice
- 10 — Acumatica invoice line

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- account feed reads Sales rep; no template in this set writes Sales rep
- contact feed reads account sync output, contact sync output; no template in this set writes
  contact sync output
- customer feed reads account sync output, customer sync output; no template in this set writes
  customer sync output
- opportunity feed reads account sync output, customer sync output, Sales rep, opportunity sync
  output; no template in this set writes customer sync output, Sales rep, opportunity sync output
- customer contact feed reads account sync output, customer sync output, customer contact sync
  output; no template in this set writes customer sync output, customer contact sync output
- sales order feed reads account sync output, customer sync output, sales order sync output; no
  template in this set writes customer sync output, sales order sync output
- sales order line feed reads sales order sync output, sales order line sync output; no template in
  this set writes sales order sync output, sales order line sync output
- invoice feed reads account sync output, customer sync output, invoice sync output; no template in
  this set writes customer sync output, invoice sync output
- invoice line feed reads invoice sync output, invoice line sync output; no template in this set
  writes invoice sync output, invoice line sync output

## 5. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-zohocrm/erps/acumatica`.

## 6. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Acumatica → Zoho CRM templates set up. dlake-crmpro-zohocrm is the destination skill
this page sits under: its own text is the authority for the Zoho CRM conventions that hold across
every ERP, and its ERP table lists this page alongside every sibling ERP page for this destination.
For the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-zohocrm/erps/acumatica`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
