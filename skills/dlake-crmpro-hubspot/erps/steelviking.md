---
name: dlake-crmpro-hubspot/erps/steelviking
kind: erp-summary
description: >-
  Use it when standing up or reading a Steel Viking → HubSpot template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-hubspot, the destination skill this page is a child of, which carries
  the HubSpot conventions that hold across every ERP.
---
# CRMPro → HubSpot — Steel Viking: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-hubspot/erps/steelviking` (or `list_skills`) against the
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
| **create customer** | ERP customers data becomes company in HubSpot. New records are created and existing ones updated; none are deleted. | company | customers |
| **create item** | The templates push products to HubSpot. New records are created and existing ones updated; none are deleted. | products | parts |
| **create order** | The templates push deal to HubSpot. New records are created and existing ones updated; none are deleted. | deal | sales orders |
| **create order detail** | The templates push line item to HubSpot. New records are created and existing ones updated; none are deleted. | line item | sales order lines, parts |
| **create invoice** | The templates push deal to HubSpot. New records are created and existing ones updated; none are deleted. | deal | invoices |
| **create invoice detail** | The templates push line item to HubSpot. New records are created and existing ones updated; none are deleted. | line item | invoice lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Run sequence |
|---|---|---|
| create customer | company | 5 |
| create item | products | 10 |
| create order | deal | 11 |
| create order detail | line item | 12 |
| create invoice | deal | 13 |
| create invoice detail | line item | 14 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| new customer feed | insert only | customers |
| new item feed | insert only | parts |
| new order deal feed | insert only | sales orders |
| new order line item feed | insert only | sales order lines, parts |
| new invoice deal feed | insert only | invoices |
| new invoice line item feed | insert only | invoice lines |

## 4. Order of work

The templates set run sequence to 5, 10, 11, 12, 13, 14. A run processes active rows in ascending
run sequence, which is the order the templates put them in:

- 5 — create customer
- 10 — create item
- 11 — create order
- 12 — create order detail
- 13 — create invoice
- 14 — create invoice detail

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- new order deal feed reads new customer sync output, Order; no template in this set writes Order
- new order line item feed reads new item sync output, Order; no template in this set writes Order
- new invoice deal feed reads invoice customer sync output, invoice record sync output (generic
  name); no template in this set writes invoice customer sync output, invoice record sync output
  (generic name)
- new invoice line item feed reads new item sync output, invoice record sync output (generic name);
  no template in this set writes invoice record sync output (generic name)

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → HubSpot pairs |
|---|---|---|---|
| create customer | company | 9 | Customer identifier,Ship to identifier → Commercient AR customer code, Customer name,Shipping name → name, Bill to address line 1,Shipping address line 1 → address, Bill to address line 2,Shipping address line 2 → address line 2 property, Bill to address city,Shipping city → city property |
| create item | products | 4 | Part number (Steel Viking) → stock keeping unit property, Part name → name, Part identifier → external key property, Part identifier → record key |
| create order | deal | 7 | Order amount → amount, Order date → close date property, Order date → create date property, Sales order number (Steel Viking),Bill to name → deal name property, Customer identifier → company association |
| create order detail | line item | 6 | Unit price amount → price, Order quantity → quantity, the linked Salesforce record → product lookup property, Part name → name, the linked Salesforce record → deal association |
| create invoice | deal | 7 | Invoice amount → amount, Due date → close date property, Invoice date → create date property, Invoice number (Steel Viking),Bill to name → deal name property, Customer identifier → company association |
| create invoice detail | line item | 5 | Unit price amount → price, Invoice quantity → quantity, Part identifier → product lookup property, Invoice line description → name, returned invoice identifier → deal association |

## 6. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-hubspot/erps/steelviking`.

## 7. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Steel Viking → HubSpot templates set up. dlake-crmpro-hubspot is the destination skill
this page sits under: its own text is the authority for the HubSpot conventions that hold across
every ERP, and its ERP table lists this page alongside every sibling ERP page for this destination.
For the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-hubspot/erps/steelviking`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
