---
name: dlake-crmpro-hubspot/erps/sage-50-us
kind: erp-summary
description: >-
  Use it when standing up or reading a Sage 50 US → HubSpot template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-hubspot, the destination skill this page is a child of, which carries
  the HubSpot conventions that hold across every ERP.
---
# CRMPro → HubSpot — Sage 50 US: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-hubspot/erps/sage-50-us` (or `list_skills`) against the
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
| **create invoice** | The templates push deal to HubSpot. New records are created and existing ones updated; none are deleted. | deal | journal headers, customers |
| **create invoice detail** | The templates push line item to HubSpot. New records are created and existing ones updated; none are deleted. | line item | journal lines, items |
| **create order** | The templates push deal to HubSpot. New records are created and existing ones updated; none are deleted. | deal | journal headers, customers |
| **create order detail** | The templates push line item to HubSpot. New records are created and existing ones updated; none are deleted. | line item | journal lines, items |
| **create quote** | The templates push deal to HubSpot. New records are created and existing ones updated; none are deleted. | deal | journal headers, customers |
| **create quote detail** | The templates push line item to HubSpot. New records are created and existing ones updated; none are deleted. | line item | journal lines, items |
| **create product** | ERP items data becomes product in HubSpot. New records are created and existing ones updated; none are deleted. | product | items |
| **create company** | ERP customers, addresses data becomes company in HubSpot. New records are created and existing ones updated; none are deleted. | company | customers, addresses |
| **create contact** | ERP contacts, customers, addresses data becomes contact in HubSpot. New records are created and existing ones updated; none are deleted. | contact | contacts, customers, addresses, employees |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Run sequence |
|---|---|---|
| create company | company | 5 |
| create contact | contact | 5 |
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
| new customer feed | — | customers, addresses |
| new contact feed | insert only | contacts, customers, addresses, employees |
| new item feed | insert only | items |
| new invoice deal feed | insert only | journal headers, customers |
| new invoice line item feed | insert only | journal lines, items |
| new order deal feed | insert only | journal headers, customers |
| new order line item feed | insert only | journal lines, items |
| new quote deal feed | insert only | journal headers, customers |
| new quote line item feed | insert only | journal lines, items |

## 4. Order of work

The templates set run sequence to 5, 10, 11, 12, 13, 14, 15, 16. A run processes active rows in
ascending run sequence, which is the order the templates put them in:

- 5 — create company, create contact
- 10 — create product
- 11 — create invoice
- 12 — create invoice detail
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

- new invoice deal feed reads new customer sync output, invoice record sync output (generic name);
  no template in this set writes invoice record sync output (generic name)
- new invoice line item feed reads new item sync output, invoice record sync output (generic name);
  no template in this set writes invoice record sync output (generic name)
- new order deal feed reads new customer sync output, Order; no template in this set writes Order
- new order line item feed reads new item sync output, Order; no template in this set writes Order
- new quote deal feed reads new customer sync output, quote sync output (generic name); no template
  in this set writes quote sync output (generic name)
- new quote line item feed reads new item sync output, quote sync output (generic name); no template
  in this set writes quote sync output (generic name)
- new contact feed reads new owner sync output, new customer sync output; no template in this set
  writes new owner sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → HubSpot pairs |
|---|---|---|---|
| create company | company | 9 | Customer identifier → Commercient AR customer code, Customer billing name → name, Address line 1 → address, Address line 2 → address line 2 property, City → city property |
| create contact | contact | 13 | the linked Salesforce record → company association, returned record identifier → record key, returned record identifier → external key property, Family name → last name property, Given name → first name property |
| create product | product | 4 | Item identifier → stock keeping unit property, Item description text → name, Labor cost → cost of goods sold property, Price level 1 amount → price |
| create invoice | deal | 8 | Posting order number → external key property, Ship date → close date property, Transaction date → create date property, Main amount → amount, Customer identifier → company association |
| create invoice detail | line item | 7 | Stocking unit cost → price, Stocking quantity → quantity, the linked Salesforce record → product lookup property, Item identifier → name, the linked Salesforce record → deal association |
| create order | deal | 8 | Posting order number → external identifier property, Ship date → close date property, Transaction date → create date property, Main amount → amount, Reference → deal name property |
| create order detail | line item | 6 | Stocking quantity → quantity, Stocking unit cost → price, the linked Salesforce record → product lookup property, Item identifier → name, Posting order number → deal association |
| create quote | deal | 8 | Ship date → close date property, Transaction date → create date property, Main amount → amount, Reference → deal name property, Customer identifier → company association |
| create quote detail | line item | 6 | Stocking quantity → quantity, Stocking unit cost → price, the linked Salesforce record → product lookup property, Item identifier → name, Posting order number → deal association |

## 6. Community templates

The catalogue carries 87 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 87
- Default operations: insert on 87, update on 87, delete on 87
- Marked as circular sync: 0
- Licence groups they span: 6
- Destination objects: deal, line item, company, product, Deal, contact, notes, Commercient Sage 50
  US contact matching object, Commercient Account Matching 2 object, Commercient Product Matching
  Managed Custom Object, Commercient HubSpot new contact object, Commercient HubSpot new customer
  object, Customer, line item and 3 custom objects
- Object display names: create invoice, create company, create invoice detail, create product,
  create deal, create order, create quote, get all companies, update deal, update deal quote, upsert
  company, upsert contact, 47 more and 6 further templates
- Template groups: Account, CRM Opportunity and Line, Product

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-hubspot/erps/sage-50-us`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Sage 50 US → HubSpot templates set up. dlake-crmpro-hubspot is the destination skill
this page sits under: its own text is the authority for the HubSpot conventions that hold across
every ERP, and its ERP table lists this page alongside every sibling ERP page for this destination.
For the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-hubspot/erps/sage-50-us`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
