---
name: dlake-crmpro-hubspot/erps/sage-100-us
kind: erp-summary
description: >-
  Use it when standing up or reading a Sage 100 (US) → HubSpot template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-hubspot, the destination skill this page is a child of, which carries
  the HubSpot conventions that hold across every ERP.
---
# CRMPro → HubSpot — Sage 100 (US): what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-hubspot/erps/sage-100-us` (or `list_skills`) against the
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
| **create item** | The templates push products to HubSpot. New records are created and existing ones updated; none are deleted. | products | items |
| **create order** | The templates push deal to HubSpot. New records are created and existing ones updated; none are deleted. | deal | sales order history headers |
| **create order detail** | The templates push line item to HubSpot. New records are created and existing ones updated; none are deleted. | line item | sales order history lines, items |
| **create invoice** | The templates push deal to HubSpot. New records are created and existing ones updated; none are deleted. | deal | invoice history headers |
| **create invoice detail** | The templates push line item to HubSpot. New records are created and existing ones updated; none are deleted. | line item | invoice history lines, items |
| **create quote** | The templates push deal to HubSpot. New records are created and existing ones updated; none are deleted. | deal | sales order headers |
| **create quote detail** | The templates push line item to HubSpot. New records are created and existing ones updated; none are deleted. | line item | sales order lines, items |
| **create open invoice** | The templates push deal to HubSpot. New records are created and existing ones updated; none are deleted. | deal | open invoices, customers |
| **create open invoice detail** | The templates push line item to HubSpot. New records are created and existing ones updated; none are deleted. | line item | invoice history lines, items |
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
| create open invoice | deal | 17 |
| create open invoice detail | line item | 18 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Emits the linked Salesforce record | Source tables |
|---|---|---|---|
| new customer feed | insert only | no | customers |
| new contact feed | insert only | no | customer contacts, customers |
| new item feed | insert only | no | items |
| new order deal feed | insert only | no | sales order history headers |
| new order line item feed | insert + update | yes | sales order history lines, items |
| new invoice deal feed | insert only | no | invoice history headers |
| new invoice line item feed | insert only | no | invoice history lines, items |
| new quote deal feed | insert only | no | sales order headers |
| new quote line item feed | insert only | no | sales order lines, items |
| new open invoice deal feed | insert only | no | open invoices, customers |
| new open invoice line item feed | insert only | no | invoice history lines, items |

## 4. Order of work

The templates set run sequence from 5 to 18. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 5 — create customer, create contact
- 10 — create item
- 11 — create order
- 12 — create order detail
- 13 — create invoice
- 14 — create invoice detail
- 15 — create quote
- 16 — create quote detail
- 17 — create open invoice
- 18 — create open invoice detail

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
- new open invoice deal feed reads new customer sync output, open invoice sync output; no template
  in this set writes open invoice sync output
- new open invoice line item feed reads new item sync output, open invoice sync output; no template
  in this set writes open invoice sync output
- new contact feed reads new customer sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → HubSpot pairs |
|---|---|---|---|
| create customer | company | 9 | Customer number → Commercient AR customer code, Customer name → name, Address line 1 → address, Address line 2 → address line 2 property, City → city property |
| create contact | contact | 11 | Email address → email, Contact name → first name property, Contact name → last name property, Telephone number 1 → phone, Telephone 2 → mobile phone property |
| create item | products | 5 | Item code → stock keeping unit property, Item description → name, Item description → description, Standard unit cost → cost of goods sold property, Standard unit price → price |
| create order | deal | 8 | Order date → close date property, Order date → create date property, Bill to name, returned sales order number → deal name property, returned sales order number → external key property, the linked Salesforce record → company association |
| create order detail | line item | 6 | Last unit price → price, Quantity on back order → quantity, the linked Salesforce record → product lookup property, Item code → name, the linked Salesforce record → deal association |
| create invoice | deal | 8 | Invoice number → external key property, Invoice due date → close date property, Invoice date → create date property, Bill to name, Invoice number → deal name property, the linked Salesforce record → company association |
| create invoice detail | line item | 5 | Unit price → price, Quantity ordered → quantity, the linked Salesforce record → product lookup property, Item code → name, the linked Salesforce record → deal association |
| create quote | deal | 8 | returned sales order number → external key property, Order date → close date property, Ship expire date → create date property, Bill to name, returned sales order number → deal name property, the linked Salesforce record → company association |
| create quote detail | line item | 6 | Unit price → price, Quantity on back order → quantity, the linked Salesforce record → product lookup property, Item code → name, the linked Salesforce record → deal association |
| create open invoice | deal | 9 | close date property → close date property, close date property → create date property, Customer name, Invoice number → deal name property, Invoice number → external key property, Invoice number → record key |
| create open invoice detail | line item | 6 | Unit price → price, Quantity ordered → quantity, the linked Salesforce record → product lookup property, Item code → name, the linked Salesforce record → deal association |

## 6. Community templates

The catalogue carries 306 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 306
- Default operations: insert on 306, update on 306, delete on 306
- Marked as circular sync: 0
- Licence groups they span: 10
- Destination objects: deal, line item, company, contact, products, Commercient Sage 100 AR customer
  object, order, Commercient Account Matching Managed Custom Object, product, Account, Commercient
  Sage 100 AR customer contact object, Commercient Contact Matching Managed Custom Object,
  Commercient Shipping Address Matching object, invoice, Commercient Sage 100 shipping address
  object, Commercient AR Customer Managed Custom Object, Commercient AR Customer Contact object,
  Commercient AR Products object, Commercient Contact Match object, Commercient Data Matching
  object, 7 more and 13 custom objects
- Object display names: upsert customer, upsert item, upsert invoice, upsert order, create customer,
  create invoice detail, upsert contact, upsert order detail, create order detail, create contact,
  upsert invoice detail, create invoice, 75 more and 25 further templates
- Template groups: Account, CRM Opportunity and Line, Product, Sales order, CRM Order and Line,
  Customer Multi Ship Addresses, CRM Quote and Line

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-hubspot/erps/sage-100-us`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Sage 100 (US) → HubSpot templates set up. dlake-crmpro-hubspot is the destination skill
this page sits under: its own text is the authority for the HubSpot conventions that hold across
every ERP, and its ERP table lists this page alongside every sibling ERP page for this destination.
For the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-hubspot/erps/sage-100-us`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
