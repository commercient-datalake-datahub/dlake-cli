---
name: dlake-crmpro-hubspot/erps/infor-syteline-v9
kind: erp-summary
description: >-
  Use it when standing up or reading an Infor SyteLine version 9 → HubSpot template set, when
  deciding which templates to import and activate, or when a run completes without pushing records
  and the answer is in the view or the configuration row. It extends dlake-crmpro, which covers
  operating CRMPro generally, and dlake-crmpro-hubspot, the destination skill this page is a child
  of, which carries the HubSpot conventions that hold across every ERP.
---
# CRMPro → HubSpot — Infor SyteLine version 9: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-hubspot/erps/infor-syteline-v9` (or `list_skills`) against the
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
| **create product** | ERP items data becomes product in HubSpot. New records are created and existing ones updated; none are deleted. | product | items |
| **create company** | ERP customers, customer addresses data becomes company in HubSpot. New records are created and existing ones updated; none are deleted. | company | customers, customer addresses |
| **create invoice** | The templates push deal to HubSpot. New records are created and existing ones updated; none are deleted. | deal | invoice headers, customers |
| **create invoice detail** | The templates push line item to HubSpot. New records are created and existing ones updated; none are deleted. | line item | invoice lines |
| **create order** | The templates push deal to HubSpot. New records are created and existing ones updated; none are deleted. | deal | customer orders |
| **create order detail** | The templates push line item to HubSpot. New records are created and existing ones updated; none are deleted. | line item | customer order lines |

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
| new customer feed | insert only | customers, customer addresses |
| new item feed | insert only | items |
| new invoice deal feed | insert only | invoice headers, customers |
| new invoice line item feed | insert only | invoice lines |
| new order deal feed | insert only | customer orders |
| new order line item feed | insert only | customer order lines |

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
| create company | company | 9 | Site reference, ERP customer number, Customer sequence number → Commercient AR customer code, name → name, city property → city property, state → state, postal code property → postal code property |
| create product | product | 5 | ERP product code → stock keeping unit property, description → name, description → description, Unit cost → cost of goods sold property, Unit cost → price |
| create invoice | deal | 8 | Site reference, Invoice number, Invoice sequence → external key property, Ship date → close date property, Invoice date → create date property, price → amount, Site reference, Invoice number, Invoice sequence → deal name property |
| create invoice detail | line item | 5 | item → product lookup property, item → name, Quantity invoiced → quantity, price → price, Site reference, Invoice number, Invoice sequence → deal association |
| create order | deal | 8 | Site reference, Customer order number → external key property, Close date → close date property, Order date → create date property, price → amount, Site reference, Customer order number → deal name property |
| create order detail | line item | 6 | item → product lookup property, Customer order number → deal association, price → price, Quantity ordered → quantity, item → name |

## 6. Community templates

The catalogue carries 15 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 15
- Default operations: insert on 15, update on 15, delete on 15
- Marked as circular sync: 0
- Licence groups they span: 4
- Destination objects: company, deal, product, Commercient Deal Matching object, Commercient Product
  Matching object, contact, line item
- Object display names: upsert company, upsert product, account matching, create company, create
  order, create order detail, lookup company, Sync contact matching, Sync deal matching, Sync
  product matching, update order deal and 2 further templates
- Template groups: Product, Account, CRM Opportunity and Line

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-hubspot/erps/infor-syteline-v9`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Infor SyteLine version 9 → HubSpot templates set up. dlake-crmpro-hubspot is the
destination skill this page sits under: its own text is the authority for the HubSpot conventions
that hold across every ERP, and its ERP table lists this page alongside every sibling ERP page for
this destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-hubspot/erps/infor-syteline-v9`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
