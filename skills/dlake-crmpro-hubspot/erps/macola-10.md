---
name: dlake-crmpro-hubspot/erps/macola-10
kind: erp-summary
description: >-
  Use it when standing up or reading a Macola 10 → HubSpot template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-hubspot, the destination skill this page is a child of, which carries
  the HubSpot conventions that hold across every ERP.
---
# CRMPro → HubSpot — Macola 10: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-hubspot/erps/macola-10` (or `list_skills`) against the
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
| **create order** | The templates push deal to HubSpot. New records are created and existing ones updated; none are deleted. | deal | order headers, accounts |
| **create order detail** | The templates push line item to HubSpot. New records are created and existing ones updated; none are deleted. | line item | order lines, order headers |
| **create invoice** | The templates push deal to HubSpot. New records are created and existing ones updated; none are deleted. | deal | order history headers |
| **create invoice detail** | The templates push line item to HubSpot. New records are created and existing ones updated; none are deleted. | line item | order history lines |
| **create quote** | The templates push deal to HubSpot. New records are created and existing ones updated; none are deleted. | deal | order headers, accounts |
| **create quote detail** | The templates push line item to HubSpot. New records are created and existing ones updated; none are deleted. | line item | order lines, order headers |
| **create customer** | ERP accounts data becomes company in HubSpot. New records are created and existing ones updated; none are deleted. | company | accounts |
| **create item** | The templates push products to HubSpot. New records are created and existing ones updated; none are deleted. | products | items |

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
| new customer feed | insert only | accounts |
| new item feed | insert only | items |
| new order deal feed | insert only | order headers, accounts |
| new order line item feed | insert only | order lines, order headers |
| new invoice deal feed | insert only | order history headers |
| new invoice line item feed | insert only | order history lines |
| new quote deal feed | insert only | order headers, accounts |
| new quote line item feed | insert only | order lines, order headers |

## 4. Order of work

The templates set run sequence to 5, 10, 11, 12, 13, 14, 15, 16. A run processes active rows in
ascending run sequence, which is the order the templates put them in:

- 5 — create customer
- 10 — create item
- 11 — create order
- 12 — create order detail
- 13 — create invoice
- 14 — create invoice detail
- 15 — create quote
- 16 — create quote detail

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- new order deal feed reads new customer sync output, Order; no template in this set writes Order
- new order line item feed reads new item sync output, Order; no template in this set writes Order
- new invoice deal feed reads new customer sync output, invoice record sync output (generic name);
  no template in this set writes invoice record sync output (generic name)
- new invoice line item feed reads new item sync output, invoice record sync output (generic name);
  no template in this set writes invoice record sync output (generic name)
- new quote deal feed reads new customer sync output, quote sync output (generic name); no template
  in this set writes quote sync output (generic name)
- new quote line item feed reads new item sync output, quote sync output (generic name); no template
  in this set writes quote sync output (generic name)

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → HubSpot pairs |
|---|---|---|---|
| create customer | company | 9 | Account code → Commercient AR customer code, Account name → name, Account address line 1 → address, Account address line 2 → address line 2 property, Account city → city property |
| create item | products | 5 | Item code → stock keeping unit property, Description → name, Long description → description, Standard cost price → cost of goods sold property, Sales package price → price |
| create order | deal | 8 | Total sales amount → amount, Shipping date → close date property, Order date → create date property, Order number → deal name property, Account name → deal name property |
| create order detail | line item | 6 | Unit price → price, Total quantity ordered → quantity, Item number → product lookup property, Item description 1 → name, Order number → deal association |
| create invoice | deal | 7 | Total sales amount → amount, Invoice date → close date property, Invoice date → create date property, Invoice number,Bill to name → deal name property, Customer number → company association |
| create invoice detail | line item | 5 | Item number → product lookup property, Customer number → deal association, Unit price → price, Total quantity ordered → quantity, Item description 1 → name |
| create quote | deal | 8 | Total sales amount → amount, Shipping date → close date property, Order date → create date property, Order number → deal name property, Account name → deal name property |
| create quote detail | line item | 5 | Unit price → price, Total quantity ordered → quantity, Item number → product lookup property, Item description 1 → name, record identifier → deal association |

## 6. Community templates

The catalogue carries 19 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 19
- Default operations: insert on 19, update on 19, delete on 19
- Marked as circular sync: 0
- Licence groups they span: 3
- Destination objects: company, contact, deal, line item, products, order
- Object display names: upsert contact, upsert customer, upsert item, upsert order, upsert order
  detail, upsert child company, upsert invoice, upsert invoice detail, upsert standard order and 2
  further templates
- Template groups: Account, CRM Order and Line

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-hubspot/erps/macola-10`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Macola 10 → HubSpot templates set up. dlake-crmpro-hubspot is the destination skill this
page sits under: its own text is the authority for the HubSpot conventions that hold across every
ERP, and its ERP table lists this page alongside every sibling ERP page for this destination. For
the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-hubspot/erps/macola-10`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
