---
name: dlake-crmpro-hubspot/erps/infor-csd
kind: erp-summary
description: >-
  Use it when standing up or reading an Infor CSD → HubSpot template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-hubspot, the destination skill this page is a child of, which carries
  the HubSpot conventions that hold across every ERP.
---
# CRMPro → HubSpot — Infor CSD: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-hubspot/erps/infor-csd` (or `list_skills`) against the
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
| **create customer** | ERP AR customers data becomes company in HubSpot. New records are created and existing ones updated; none are deleted. | company | AR customers |
| **create item** | The templates push products to HubSpot. New records are created and existing ones updated; none are deleted. | products | inventory products |
| **create invoice** | The templates push deal to HubSpot. New records are created and existing ones updated; none are deleted. | deal | AR transactions |
| **create invoice detail** | The templates push line item to HubSpot. New records are created and existing ones updated; none are deleted. | line item | order entry lines, order entry headers, AR transactions, inventory products |
| **create order** | The templates push deal to HubSpot. New records are created and existing ones updated; none are deleted. | deal | order entry headers, AR customers |
| **create order detail** | The templates push line item to HubSpot. New records are created and existing ones updated; none are deleted. | line item | order entry lines, order entry headers, inventory products |
| **create quote** | The templates push deal to HubSpot. New records are created and existing ones updated; none are deleted. | deal | order entry headers, AR customers |
| **create quote detail** | The templates push line item to HubSpot. New records are created and existing ones updated; none are deleted. | line item | order entry lines, order entry headers, inventory products |
| **create contact** | ERP contacts data becomes contact in HubSpot. New records are created and existing ones updated; none are deleted. | contact | contacts, customer shipping addresses, AR customers, salespeople |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Run sequence |
|---|---|---|
| create customer | company | 5 |
| create contact | contact | 5 |
| create contact | contact | 5 |
| create item | products | 10 |
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

| View | Change detection | Emits the linked Salesforce record | Source tables |
|---|---|---|---|
| new customer feed | insert only | no | AR customers |
| new contact feed | insert only | no | contacts |
| new contact feed | insert + update | yes | customer shipping addresses, AR customers, salespeople |
| new item feed | insert only | no | inventory products |
| new invoice deal feed | insert only | no | AR transactions |
| new invoice line item feed | insert only | no | order entry lines, order entry headers, AR transactions, inventory products |
| new order deal feed | insert only | no | order entry headers, AR customers |
| new order line item feed | insert only | no | order entry lines, order entry headers, inventory products |
| new quote deal feed | insert only | no | order entry headers, AR customers |
| new quote line item feed | insert only | no | order entry lines, order entry headers, inventory products |

## 4. Order of work

The templates set run sequence to 5, 10, 11, 12, 13, 14, 15, 16. A run processes active rows in
ascending run sequence, which is the order the templates put them in:

- 5 — create customer, create contact
- 10 — create item
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
- new quote deal feed reads new opportunity deal sync output, new customer sync output, quote sync
  output (generic name); no template in this set writes new opportunity deal sync output, quote sync
  output (generic name)
- new quote line item feed reads new item sync output, quote sync output (generic name); no template
  in this set writes quote sync output (generic name)
- new contact feed reads new customer sync output, new owner sync output; no template in this set
  writes new owner sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → HubSpot pairs |
|---|---|---|---|
| create customer | company | 9 | Customer number → Commercient AR customer code, name → name, Address → address, Address line 3 → address line 2 property, city property → city property |
| create item | products | 3 | ERP product → stock keeping unit property, Product description → name, Product description → description |
| create invoice | deal | 10 | Row pointer → record key, Row pointer → external key property, Due date → close date property, Invoice date → create date property, amount → amount |
| create invoice detail | line item | 5 | Shipped product → product lookup property, Quantity shipped → quantity, price → price, Product description → name, Row pointer → deal association |
| create order | deal | 9 | Total order amount → amount, Ship date → close date property, Created date → create date property, Ship to name → deal name property, Status type → deal stage property |
| create order detail | line item | 6 | Shipped product → product lookup property, Order number → deal association, Quantity ordered → quantity, price → price, Product description → name |
| create quote | deal | 7 | Company number, Customer number, Order number, Order suffix → external key property, Ship date → close date property, Created date → create date property, Total order amount → amount, Ship to name, Customer number, Order number, Invoice number, Transaction type → deal name property |
| create quote detail | line item | 5 | Shipped product → product lookup property, Company number → deal association, Quantity shipped → quantity, price → price, Product description → name |

## 6. Community templates

The catalogue carries 18 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 18
- Default operations: insert on 18, update on 18, delete on 18
- Marked as circular sync: 0
- Licence groups they span: 2
- Destination objects: deal, line item, company, contact, product and 2 custom objects
- Object display names: upsert customer, create contact, create vendor, lookup deal and vendor,
  upsert invoice, upsert invoice detail, upsert order, upsert order detail, upsert quote, upsert
  quote detail, upsert contact, upsert order and quote, 4 more and a further template
- Template groups: Account

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-hubspot/erps/infor-csd`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Infor CSD → HubSpot templates set up. dlake-crmpro-hubspot is the destination skill this
page sits under: its own text is the authority for the HubSpot conventions that hold across every
ERP, and its ERP table lists this page alongside every sibling ERP page for this destination. For
the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-hubspot/erps/infor-csd`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
