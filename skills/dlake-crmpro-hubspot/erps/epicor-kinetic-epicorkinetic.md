---
name: dlake-crmpro-hubspot/erps/epicor-kinetic-epicorkinetic
kind: erp-summary
description: >-
  Use it when standing up or reading an Epicor Kinetic (Epicor Kinetic) → HubSpot template set, when
  deciding which templates to import and activate, or when a run completes without pushing records
  and the answer is in the view or the configuration row. It extends dlake-crmpro, which covers
  operating CRMPro generally, and dlake-crmpro-hubspot, the destination skill this page is a child
  of, which carries the HubSpot conventions that hold across every ERP.
---
# CRMPro → HubSpot — Epicor Kinetic (Epicor Kinetic): what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-hubspot/erps/epicor-kinetic-epicorkinetic` (or `list_skills`)
against the Commercient admin plane. Existing customers who need access or help: contact
support@commercient.com. New customers: contact sales@commercient.com to become a customer and be
whitelisted.

dlake-crmpro is the parent skill and the authority for everything general: the CRMPro tools, process
configuration and field list, the sync history, how source data is selected, and what a run that
finds nothing does. Read it first; this page does not repeat it. dlake-crmpro-hubspot is the
destination skill this page is a child of, and the authority for the HubSpot conventions that hold
across every ERP: read it first, then come back here for what this source's own templates set. This
page grows as the catalogue does.

## 1. What the templates deliver

| Group | Business outcome | Objects | Source tables and views |
|---|---|---|---|
| **Create Customer** | The templates push company to HubSpot. New records are created and existing ones updated; none are deleted. | company | customers |
| **Create Product** | The templates push product to HubSpot. New records are created and existing ones updated; none are deleted. | product | parts |
| **create sales order** | The templates push deal to HubSpot. New records are created, existing ones updated, and records removed in the ERP are deleted. | deal | sales order headers, customers |
| **create order line** | The templates push line item to HubSpot. New records are created, existing ones updated, and records removed in the ERP are deleted. | line item | sales order lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Run sequence |
|---|---|---|
| create Customer | company | 1 |
| Create Product | product | 3 |
| create sales order | deal | 8 |
| create order line | line item | 9 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Emits the linked Salesforce record | Source tables |
|---|---|---|---|
| new customer feed | insert only | no | customers |
| new item feed | insert only | no | parts |
| new sales order feed | insert only | no | sales order headers, customers |
| new order line item feed | insert only | yes | sales order lines |

## 4. Order of work

The templates set run sequence to 1, 3, 8, 9. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 1 — create Customer
- 3 — Create Product
- 8 — create sales order
- 9 — create order line

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- new sales order feed reads new customer sync output, new contact sync output, Order; no template
  in this set writes new contact sync output, Order
- new order line item feed reads new order sync output, new item sync output, Order; no template in
  this set writes new order sync output, Order

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → HubSpot pairs |
|---|---|---|---|
| create Customer | company | 10 | Company, Customer identifier → Commercient AR customer code, Name → name, Address 1 → address, Address 2 → address line 2 property, City → city property |
| Create Product | product | 6 | Part number → stock keeping unit property, Part number → name, Part description → description, Company,Part number → record key, Company,Part number → external key property |
| create sales order | deal | 9 | Company, Order number → external key property, Order date → create date property, Order date → close date property, Company, Order number → deal name property, Order amount → amount |
| create order line | line item | 6 | Document unit price → price, Order quantity → quantity, Part number → product lookup property, Part number → name, Epicor line description → description |

## 6. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-hubspot/erps/epicor-kinetic-epicorkinetic`.

## 7. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Epicor Kinetic (Epicor Kinetic) → HubSpot templates set up. dlake-crmpro-hubspot is the
destination skill this page sits under: its own text is the authority for the HubSpot conventions
that hold across every ERP, and its ERP table lists this page alongside every sibling ERP page for
this destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-hubspot/erps/epicor-kinetic-epicorkinetic`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
