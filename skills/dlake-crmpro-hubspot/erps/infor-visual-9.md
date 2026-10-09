---
name: dlake-crmpro-hubspot/erps/infor-visual-9
kind: erp-summary
description: >-
  Use it when standing up or reading an Infor Visual 9 → HubSpot template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-hubspot, the destination skill this page is a child of, which carries
  the HubSpot conventions that hold across every ERP.
---
# CRMPro → HubSpot — Infor Visual 9: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-hubspot/erps/infor-visual-9` (or `list_skills`) against the
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
| **create product** | ERP parts data becomes product in HubSpot. New records are created and existing ones updated; none are deleted. | product | parts |
| **create company** | ERP customers data becomes company in HubSpot. New records are created and existing ones updated; none are deleted. | company | customers |
| **create order** | The templates push deal to HubSpot. New records are created and existing ones updated; none are deleted. | deal | customer orders, customers |
| **create order detail** | The templates push line item to HubSpot. New records are created and existing ones updated; none are deleted. | line item | customer order lines, parts |
| **create quote** | The templates push deal to HubSpot. New records are created and existing ones updated; none are deleted. | deal | quotes, quote prices |
| **create quote detail** | The templates push line item to HubSpot. New records are created and existing ones updated; none are deleted. | line item | quote lines, parts, quote prices |
| **create contact** | ERP customers, customer contacts, contacts data becomes contact in HubSpot. New records are created and existing ones updated; none are deleted. | contact | customers, customer contacts, contacts |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Run sequence |
|---|---|---|
| create company | company | 5 |
| create contact | contact | 5 |
| create product | product | 10 |
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
| new customer feed | insert only | customers |
| new contact feed | insert only | customers, customer contacts, contacts |
| new item feed | insert only | parts |
| new order deal feed | insert only | customer orders, customers |
| new order line item feed | insert only | customer order lines, parts |
| new quote deal feed | insert only | quotes, quote prices |
| new quote line item feed | insert only | quote lines, parts, quote prices |

## 4. Order of work

The templates set run sequence to 5, 10, 13, 14, 15, 16. A run processes active rows in ascending
run sequence, which is the order the templates put them in:

- 5 — create company, create contact
- 10 — create product
- 13 — create order
- 14 — create order detail
- 15 — create quote
- 16 — create quote detail

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- new order deal feed reads new customer sync output, Order; no template in this set writes Order
- new order line item feed reads new item sync output, Order; no template in this set writes Order
- new quote deal feed reads new customer sync output, quote sync output (generic name); no template
  in this set writes quote sync output (generic name)
- new quote line item feed reads new item sync output, quote sync output (generic name); no template
  in this set writes quote sync output (generic name)
- new contact feed reads new customer sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → HubSpot pairs |
|---|---|---|---|
| create company | company | 9 | record identifier → Commercient AR customer code, Name → name, Address line 1 → address, Address line 2 → address line 2 property, City → city property |
| create contact | contact | 18 | First name → first name property, Last name → last name property, Email → email, Address line 1 → address, Address line 2 → address line 2 property |
| create product | product | 5 | ERP product code → stock keeping unit property, Manufacturer name → name, Description → description, Effective date price → price, record identifier → record key |
| create order | deal | 8 | record identifier → external identifier property, Desired ship date → close date property, Order date → create date property, Total amount ordered → amount, Contact first name, Contact last name, Name → deal name property |
| create order detail | line item | 6 | Unit price → price, Order quantity → quantity, record identifier → product lookup property, Description → name, Customer order identifier → deal association |
| create quote | deal | 8 | record identifier → external identifier property, Expiration date → close date property, Quote date → create date property, Unit price → amount, Contact first name, Contact last name, Name → deal name property |
| create quote detail | line item | 5 | the linked item → product lookup property, the linked deal → deal association, Quote price quantity → quantity, Quote price unit price → price |

## 6. Community templates

The catalogue carries 4 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 4
- Default operations: insert on 4, update on 4, delete on 4
- Marked as circular sync: 0
- Licence groups they span: 2
- Destination objects: company, contact
- Object display names: create company, create contact, update company, update contact
- Template groups: Account

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-hubspot/erps/infor-visual-9`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Infor Visual 9 → HubSpot templates set up. dlake-crmpro-hubspot is the destination skill
this page sits under: its own text is the authority for the HubSpot conventions that hold across
every ERP, and its ERP table lists this page alongside every sibling ERP page for this destination.
For the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-hubspot/erps/infor-visual-9`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
