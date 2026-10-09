---
name: dlake-crmpro-hubspot/erps/exact-max
kind: erp-summary
description: >-
  Use it when standing up or reading an Exact MAX → HubSpot template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-hubspot, the destination skill this page is a child of, which carries
  the HubSpot conventions that hold across every ERP.
---
# CRMPro → HubSpot — Exact MAX: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-hubspot/erps/exact-max` (or `list_skills`) against the
Commercient admin plane. Existing customers who need access or help: contact
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
| **create product** | ERP part master records data becomes product in HubSpot. New records are created and existing ones updated; none are deleted. | product | part master records |
| **create invoice** | The templates push deal to HubSpot. New records are created and existing ones updated; none are deleted. | deal | invoice master records, invoice details |
| **create invoice detail** | The templates push line item to HubSpot. New records are created and existing ones updated; none are deleted. | line item | invoice details, part master records |
| **create order** | The templates push deal to HubSpot. New records are created and existing ones updated; none are deleted. | deal | sales order master records, sales order details |
| **create order detail** | The templates push line item to HubSpot. New records are created and existing ones updated; none are deleted. | line item | sales order details, part master records |
| **create company** | ERP customer master records data becomes company in HubSpot. New records are created and existing ones updated; none are deleted. | company | customer master records |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Run sequence |
|---|---|---|
| create company | company | 5 |
| create product | product | 10 |
| create invoice | deal | 11 |
| create invoice detail | line item | 12 |
| create order | deal | 13 |
| create order detail | line item | 14 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| new customer feed | insert only | customer master records |
| new item feed | — | part master records |
| new invoice deal feed | insert only | invoice master records, invoice details |
| new invoice line item feed | insert only | invoice details, part master records |
| new order deal feed | insert only | sales order master records, sales order details |
| new order line item feed | insert only | sales order details, part master records |

## 4. Order of work

The templates set run sequence to 5, 10, 11, 12, 13, 14. A run processes active rows in ascending
run sequence, which is the order the templates put them in:

- 5 — create company
- 10 — create product
- 11 — create invoice
- 12 — create invoice detail
- 13 — create order
- 14 — create order detail

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- new invoice deal feed reads new customer sync output, invoice record sync output (generic name);
  no template in this set writes invoice record sync output (generic name)
- new invoice line item feed reads new item sync output, invoice record sync output (generic name);
  no template in this set writes invoice record sync output (generic name)
- new order deal feed reads new customer sync output, Order; no template in this set writes Order
- new order line item feed reads new item sync output, Order; no template in this set writes Order

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → HubSpot pairs |
|---|---|---|---|
| create company | company | 10 | Customer identifier → Commercient AR customer code, Customer name → name, Customer address line 1 → address, Customer address line 2 → address line 2 property, Customer city → city property |
| create product | product | 5 | Part commodity code → stock keeping unit property, Part description 1 → name, Part description 1, Part description 2 → description, Part cost → cost of goods sold property, Part price → price |
| create invoice | deal | 9 | Invoice order number,Invoice number → external key property, → close date property, Invoice date → create date property, Invoice line cost of goods sold → amount, Row timestamp → Timestamp |
| create invoice detail | line item | 6 | Invoice line part number → product lookup property, Invoice line order number → deal association, Invoice line price → price, Invoiced quantity → quantity, Part description 1 → name |
| create order | deal | 8 | Order number → external key property, → close date property, Order date → create date property, Order line price → amount, Order name → deal name property |
| create order detail | line item | 6 | Order line part number → product lookup property, Order line quantity ordered → quantity, Order line price → price, Part description 1 → name, Part number → name |

## 6. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-hubspot/erps/exact-max`.

## 7. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Exact MAX → HubSpot templates set up. dlake-crmpro-hubspot is the destination skill this
page sits under: its own text is the authority for the HubSpot conventions that hold across every
ERP, and its ERP table lists this page alongside every sibling ERP page for this destination. For
the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-hubspot/erps/exact-max`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
