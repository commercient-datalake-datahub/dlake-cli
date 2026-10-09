---
name: dlake-crmpro-hubspot/erps/epicor-10
kind: erp-summary
description: >-
  Use it when standing up or reading an Epicor 10 → HubSpot template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-hubspot, the destination skill this page is a child of, which carries
  the HubSpot conventions that hold across every ERP.
---
# CRMPro → HubSpot — Epicor 10: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-hubspot/erps/epicor-10` (or `list_skills`) against the
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
| **create order** | The templates push deal to HubSpot. New records are created and existing ones updated; none are deleted. | deal | sales order headers, customers |
| **create order detail** | The templates push line item to HubSpot. New records are created and existing ones updated; none are deleted. | line item | sales order lines |
| **create invoice** | The templates push deal to HubSpot. New records are created and existing ones updated; none are deleted. | deal | invoice headers, customers |
| **create invoice detail** | The templates push line item to HubSpot. New records are created and existing ones updated; none are deleted. | line item | invoice lines |
| **create quote** | The templates push deal to HubSpot. New records are created and existing ones updated; none are deleted. | deal | quote headers, customers |
| **create quote detail** | The templates push line item to HubSpot. New records are created and existing ones updated; none are deleted. | line item | quote lines |
| **create customer** | ERP customers data becomes company in HubSpot. New records are created and existing ones updated; none are deleted. | company | customers |
| **create item** | The templates push products to HubSpot. New records are created and existing ones updated; none are deleted. | products | parts, part costs |
| **create contact** | ERP customer contacts, customers data becomes contact in HubSpot. New records are created and existing ones updated; none are deleted. | contact | customer contacts, customers |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Run sequence |
|---|---|---|
| create customer | company | 5 |
| create contact | contact | 5 |
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
| new customer feed | insert only | customers |
| new contact feed | insert only | customer contacts, customers |
| new item feed | insert only | parts, part costs |
| new order deal feed | insert only | sales order headers, customers |
| new order line item feed | insert only | sales order lines |
| new invoice deal feed | insert only | invoice headers, customers |
| new invoice line item feed | insert only | invoice lines |
| new quote deal feed | insert only | quote headers, customers |
| new quote line item feed | insert only | quote lines |

## 4. Order of work

The templates set run sequence to 5, 10, 11, 12, 13, 14, 15, 16. A run processes active rows in
ascending run sequence, which is the order the templates put them in:

- 5 — create customer, create contact
- 10 — create item
- 11 — create order
- 12 — create order detail
- 13 — create invoice
- 14 — create invoice detail
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
- new invoice deal feed reads new customer sync output, invoice record sync output (generic name);
  no template in this set writes invoice record sync output (generic name)
- new invoice line item feed reads new item sync output, invoice record sync output (generic name);
  no template in this set writes invoice record sync output (generic name)
- new quote deal feed reads new customer sync output, quote sync output (generic name); no template
  in this set writes quote sync output (generic name)
- new quote line item feed reads new item sync output, quote sync output (generic name); no template
  in this set writes quote sync output (generic name)
- new contact feed reads new customer sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → HubSpot pairs |
|---|---|---|---|
| create customer | company | 9 | Customer identifier → Commercient AR customer code, Name → name, Address 1 → address, Address 2 → address line 2 property, City → city property |
| create contact | contact | 15 | Company, Customer number, Shipping address number, Contact number → external key property, Given name → first name property, Family name → last name property, Email address → email, Phone number → phone |
| create item | products | 5 | Part number → stock keeping unit property, Part description → name, Search word → description, Unit price → price |
| create order | deal | 7 | Order number → deal name property, Order amount → amount, Order date → close date property, Order date → create date property, Customer identifier → company association |
| create order detail | line item | 6 | Unit price → price, Order quantity → quantity, Part number → product lookup property, Part number → name, Order number → deal association |
| create invoice | deal | 7 | Invoice number → deal name property, Invoice amount → amount, Closed date → close date property, Invoice date → create date property, Customer identifier → company association |
| create invoice detail | line item | 6 | Unit price → price, Order quantity in our unit of measure → quantity, the linked Salesforce record → product lookup property, Part number → name, the linked Salesforce record → deal association |
| create quote | deal | 7 | Quote amount → amount, Closed date → close date property, Date quoted → create date property, Quote number, Name → deal name property, Customer identifier → company association |
| create quote detail | line item | 6 | List price → price, Order quantity → quantity, the linked Salesforce record → product lookup property, Part number → name, the linked Salesforce record → deal association |

## 6. Community templates

The catalogue carries 57 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 57
- Default operations: insert on 57, update on 57, delete on 57
- Marked as circular sync: 0
- Licence groups they span: 5
- Destination objects: deal, line item, company, contact, products, product, orders, Commercient
  Epicor 10 customer object, Commercient Account Matching object, Commercient Contact Matching
  object and 5 custom objects
- Object display names: upsert contact, upsert customer, create contact, create customer, create
  item, upsert invoice, upsert invoice detail, upsert item, upsert order, upsert quote, upsert ship
  to company, create company, 25 more and 7 further templates
- Template groups: Account, CRM Order and Line, CRM Opportunity and Line

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-hubspot/erps/epicor-10`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Epicor 10 → HubSpot templates set up. dlake-crmpro-hubspot is the destination skill this
page sits under: its own text is the authority for the HubSpot conventions that hold across every
ERP, and its ERP table lists this page alongside every sibling ERP page for this destination. For
the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-hubspot/erps/epicor-10`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
