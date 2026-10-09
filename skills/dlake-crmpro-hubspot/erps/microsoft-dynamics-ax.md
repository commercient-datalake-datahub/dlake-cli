---
name: dlake-crmpro-hubspot/erps/microsoft-dynamics-ax
kind: erp-summary
description: >-
  Use it when standing up or reading a Microsoft Dynamics AX → HubSpot template set, when deciding
  which templates to import and activate, or when a run completes without pushing records and the
  answer is in the view or the configuration row. It extends dlake-crmpro, which covers operating
  CRMPro generally, and dlake-crmpro-hubspot, the destination skill this page is a child of, which
  carries the HubSpot conventions that hold across every ERP.
---
# CRMPro → HubSpot — Microsoft Dynamics AX: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-hubspot/erps/microsoft-dynamics-ax` (or `list_skills`) against
the Commercient admin plane. Existing customers who need access or help: contact
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
| **create quote** | The templates push deal to HubSpot. New records are created and existing ones updated; none are deleted. | deal | sales quotations, sales quotation lines |
| **create quote detail** | The templates push line item to HubSpot. New records are created and existing ones updated; none are deleted. | line item | sales quotation lines |
| **create product** | ERP items data becomes product in HubSpot. New records are created and existing ones updated; none are deleted. | product | items |
| **create company** | ERP customers, address book parties, party locations data becomes company in HubSpot. New records are created and existing ones updated; none are deleted. | company | customers, address book parties, party locations, postal addresses, states, counties |
| **create invoice** | The templates push deal to HubSpot. New records are created and existing ones updated; none are deleted. | deal | customer invoice headers |
| **create invoice detail** | The templates push line item to HubSpot. New records are created and existing ones updated; none are deleted. | line item | customer invoice lines |
| **create order** | The templates push deal to HubSpot. New records are created and existing ones updated; none are deleted. | deal | sales orders, sales order lines |
| **create order detail** | The templates push line item to HubSpot. New records are created and existing ones updated; none are deleted. | line item | sales order lines |

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
| create quote | deal | 15 |
| create quote detail | line item | 16 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| new customer feed | — | customers, address book parties, party locations, postal addresses, states |
| new item feed | insert only | items |
| new invoice deal feed | insert only | customer invoice headers |
| new invoice line item feed | insert only | customer invoice lines |
| new order deal feed | insert only | sales orders, sales order lines |
| new order line item feed | insert only | sales order lines |
| new quote deal feed | insert only | sales quotations, sales quotation lines |
| new quote line item feed | insert only | sales quotation lines |

## 4. Order of work

The templates set run sequence to 5, 10, 11, 12, 13, 14, 15, 16. A run processes active rows in
ascending run sequence, which is the order the templates put them in:

- 5 — create company
- 10 — create product
- 11 — create invoice
- 12 — create invoice detail
- 13 — create order
- 14 — create order detail
- 15 — create quote
- 16 — create quote detail

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- new quote deal feed reads new customer sync output, quote sync output (generic name); no template
  in this set writes quote sync output (generic name)
- new quote line item feed reads new item sync output, quote sync output (generic name); no template
  in this set writes quote sync output (generic name)
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
| create company | company | 8 | Company data area, Account number, Partition → Commercient AR customer code, Name → name, Address → address, Name → city property, Name → state |
| create product | product | 4 | Item identifier → stock keeping unit property, Search name → name, Item dimension cost price → price, Row timestamp → Timestamp |
| create invoice | deal | 8 | Invoice identifier,Invoice date → external key property, Due date → close date property, Invoice date → create date property, Invoice amount → amount, Delivery name → deal name property |
| create invoice detail | line item | 5 | Item identifier → product lookup property, Invoice identifier → deal association, Sales price → price, Quantity → quantity, Name → name |
| create order | deal | 8 | Fixed due date → close date property, Created date and time → create date property, Total amount → amount, Row timestamp → Timestamp, Delivery name → deal name property |
| create order detail | line item | 5 | Item identifier → product lookup property, Sales order number → deal association, Sales price → price, Quantity ordered → quantity, Name → name |
| create quote | deal | 8 | Quotation expiry date → close date property, Created date and time → create date property, Total amount → amount, pipeline → pipeline, deal stage property → deal stage property |
| create quote detail | line item | 6 | Sales price → price, Sales quantity → quantity, Item identifier → product lookup property, Name → name, Quotation identifier → deal association |

## 6. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-hubspot/erps/microsoft-dynamics-ax`.

## 7. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Microsoft Dynamics AX → HubSpot templates set up. dlake-crmpro-hubspot is the
destination skill this page sits under: its own text is the authority for the HubSpot conventions
that hold across every ERP, and its ERP table lists this page alongside every sibling ERP page for
this destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-hubspot/erps/microsoft-dynamics-ax`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
