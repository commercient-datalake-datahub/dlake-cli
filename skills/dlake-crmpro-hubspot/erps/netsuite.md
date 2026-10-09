---
name: dlake-crmpro-hubspot/erps/netsuite
kind: erp-summary
description: >-
  Use it when standing up or reading a NetSuite → HubSpot template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-hubspot, the destination skill this page is a child of, which carries
  the HubSpot conventions that hold across every ERP.
---
# CRMPro → HubSpot — NetSuite: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-hubspot/erps/netsuite` (or `list_skills`) against the
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
| **create order** | The templates push deal to HubSpot. New records are created and existing ones updated; none are deleted. | deal | sales orders |
| **create order detail** | The templates push line item to HubSpot. New records are created and existing ones updated; none are deleted. | line item | sales order items |
| **create invoice** | The templates push deal to HubSpot. New records are created and existing ones updated; none are deleted. | deal | invoices |
| **create invoice detail** | The templates push line item to HubSpot. New records are created and existing ones updated; none are deleted. | line item | invoice items |
| **create quote** | The templates push deal to HubSpot. New records are created and existing ones updated; none are deleted. | deal | quotes |
| **create quote detail** | The templates push line item to HubSpot. New records are created and existing ones updated; none are deleted. | line item | quote items |
| **create opportunity** | The templates push deal to HubSpot. New records are created and existing ones updated; none are deleted. | deal | opportunities |
| **create opportunity detail** | The templates push line item to HubSpot. New records are created and existing ones updated; none are deleted. | line item | opportunity items |
| **create customer** | ERP customers, customer address book entries data becomes company in HubSpot. New records are created and existing ones updated; none are deleted. | company | customers, customer address book entries |
| **create item** | The templates push products to HubSpot. New records are created and existing ones updated; none are deleted. | products | items |
| **create contact** | ERP contacts, contact address book entries data becomes company in HubSpot. New records are created and existing ones updated; none are deleted. | company | contacts, contact address book entries |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Run sequence |
|---|---|---|
| create customer | company | 5 |
| create contact | company | 6 |
| create item | products | 10 |
| create order | deal | 11 |
| create order detail | line item | 12 |
| create invoice | deal | 13 |
| create invoice detail | line item | 14 |
| create quote | deal | 15 |
| create quote detail | line item | 16 |
| create opportunity | deal | 17 |
| create opportunity detail | line item | 18 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| new customer feed | insert only | customers, customer address book entries |
| new contact feed | insert only | contacts, contact address book entries |
| new item feed | insert only | items |
| new order deal feed | insert only | sales orders |
| new order line item feed | insert only | sales order items |
| new invoice deal feed | insert only | invoices |
| new invoice line item feed | insert only | invoice items |
| new quote deal feed | insert only | quotes |
| new quote line item feed | insert only | quote items |
| new opportunity deal feed | insert only | opportunities |
| new opportunity line item feed | insert only | opportunity items |

## 4. Order of work

The templates set run sequence from 5 to 18. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 5 — create customer
- 6 — create contact
- 10 — create item
- 11 — create order
- 12 — create order detail
- 13 — create invoice
- 14 — create invoice detail
- 15 — create quote
- 16 — create quote detail
- 17 — create opportunity
- 18 — create opportunity detail

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
- new opportunity deal feed reads new customer sync output, opportunity sync output (generic name);
  no template in this set writes opportunity sync output (generic name)
- new opportunity line item feed reads new item sync output, opportunity sync output (generic name);
  no template in this set writes opportunity sync output (generic name)
- new contact feed reads new customer sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → HubSpot pairs |
|---|---|---|---|
| create customer | company | 9 | Internal identifier → Commercient AR customer code, Entity identifier → name, Address 1 → address, Address 2 → address line 2 property, city property → city property |
| create contact | company | 12 | First name → first name property, Last name → last name property, email → email, phone → phone, Address 1 → address |
| create item | products | 5 | Item identifier → stock keeping unit property, Display name → name, Sales description → description, cost → cost of goods sold property, Total value → price |
| create order | deal | 7 | total → amount, Created date → create date property, Created date → close date property, Entity external identifier, Transaction number → deal name property, the linked Salesforce record → company association |
| create order detail | line item | 6 | Rate → price, quantity → quantity, Item external identifier → product lookup property, Item external identifier → name, Internal identifier → Deal identifier |
| create invoice | deal | 7 | total → amount, Due date → close date property, Created date → create date property, Entity external identifier, Transaction number → deal name property, the linked Salesforce record → company association |
| create invoice detail | line item | 6 | Rate → price, quantity → quantity, Item external identifier → product lookup property, Item external identifier → name, Internal identifier → Deal identifier |
| create quote | deal | 7 | total → amount, Expected close date → close date property, Created date → create date property, Entity external identifier, Transaction number → deal name property, the linked Salesforce record → company association |
| create quote detail | line item | 6 | Rate → price, quantity → quantity, Item external identifier → product lookup property, Item external identifier → name, Internal identifier → Deal identifier |
| create opportunity | deal | 7 | Projected total → amount, close date property → close date property, Created date → create date property, Entity external identifier, Transaction number → deal name property, the linked Salesforce record → company association |
| create opportunity detail | line item | 6 | Rate → price, quantity → quantity, Item external identifier → product lookup property, Item external identifier → name, Internal identifier → Deal identifier |

## 6. Community templates

The catalogue carries 29 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 29
- Default operations: insert on 29, update on 29, delete on 29
- Marked as circular sync: 0
- Licence groups they span: 4
- Destination objects: company, deal, contact, products, line item, Commercient NetSuite customer
  object, Commercient Contact Matching object, Owner and 2 custom objects
- Object display names: create customer, create item, upsert contact, Sync contact matching, upsert
  customer, upsert opportunity, create contact, create invoice, create invoice detail, create
  opportunity detail, lookup company, Sync Customer, 4 more and 3 further templates
- Template groups: Account, CRM Opportunity and Line, Product

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-hubspot/erps/netsuite`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped NetSuite → HubSpot templates set up. dlake-crmpro-hubspot is the destination skill this
page sits under: its own text is the authority for the HubSpot conventions that hold across every
ERP, and its ERP table lists this page alongside every sibling ERP page for this destination. For
the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-hubspot/erps/netsuite`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
