---
name: dlake-crmpro-hubspot/erps/syspro-7-and-above
kind: erp-summary
description: >-
  Use it when standing up or reading a SYSPRO 7 and above → HubSpot template set, when deciding
  which templates to import and activate, or when a run completes without pushing records and the
  answer is in the view or the configuration row. It extends dlake-crmpro, which covers operating
  CRMPro generally, and dlake-crmpro-hubspot, the destination skill this page is a child of, which
  carries the HubSpot conventions that hold across every ERP.
---
# CRMPro → HubSpot — SYSPRO 7 and above: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-hubspot/erps/syspro-7-and-above` (or `list_skills`) against the
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
| **create product** | ERP inventory items, inventory prices data becomes product in HubSpot. New records are created and existing ones updated; none are deleted. | product | inventory items, inventory prices |
| **create sales order** | The templates push deal to HubSpot. New records are created and existing ones updated; none are deleted. | deal | sales orders, sales order lines |
| **create sales order line** | The templates push line item to HubSpot. New records are created and existing ones updated; none are deleted. | line item | sales order lines |
| **create quote** | The templates push deal to HubSpot. New records are created and existing ones updated; none are deleted. | deal | quotes, quote lines |
| **create quote line** | The templates push line item to HubSpot. New records are created and existing ones updated; none are deleted. | line item | quote lines |
| **create invoice** | The templates push deal to HubSpot. New records are created and existing ones updated; none are deleted. | deal | AR invoices |
| **create invoice line** | The templates push line item to HubSpot. New records are created and existing ones updated; none are deleted. | line item | AR transaction lines |
| **create contact** | ERP customers data becomes contact in HubSpot. New records are created and existing ones updated; none are deleted. | contact | customers |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Run sequence |
|---|---|---|
| create contact | contact | 5 |
| create customer | company | 11 |
| create product | product | 11 |
| create sales order | deal | 11 |
| create sales order line | line item | 11 |
| create quote | deal | 11 |
| create quote line | line item | 11 |
| create invoice | deal | 11 |
| create invoice line | line item | 11 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Emits the linked Salesforce record | Source tables |
|---|---|---|---|
| new contact feed | insert only | no | customers |
| new customer feed | insert only | yes | customers |
| new item feed | insert only | yes | inventory items, inventory prices |
| new sales order deal feed | insert only | no | sales orders, sales order lines |
| new sales order line item feed | insert only | no | sales order lines |
| new quote deal feed | insert only | no | quotes, quote lines |
| new quote line item feed | insert only | no | quote lines |
| new invoice deal feed | insert only | no | AR invoices |
| new invoice line item feed | insert only | no | AR transaction lines |

## 4. Order of work

The templates set run sequence to 5, 11. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 5 — create contact
- 11 — create customer, create product, create sales order, create sales order line, create quote,
  create quote line, create invoice, create invoice line

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- new sales order deal feed reads new customer sync output
- new sales order line item feed reads new deal sync output, new item sync output
- new quote deal feed reads new customer sync output
- new quote line item feed reads new deal sync output, new item sync output
- new invoice deal feed reads new customer sync output
- new invoice line item feed reads new deal sync output, new item sync output
- new contact feed reads new customer sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → HubSpot pairs |
|---|---|---|---|
| create contact | contact | 10 | Contact → last name property, Contact → first name property, Email → email, Telephone number, Telephone extension → phone, Sold to address line 1, Sold to address line 2 → address |
| create customer | company | 9 | Customer → Commercient AR customer code, Name → name, Sold to address line 1 → address, Sold to address line 2 → address line 2 property, Sold to address line 3 → city property |
| create product | product | 5 | Stock code → stock keeping unit property, Description → name, Long description → description, Material cost → cost of goods sold property, Selling price → price |
| create sales order | deal | 9 | Order line price → amount, Requested ship date → close date property, Order date → create date property, Customer name → deal name property, the linked Salesforce record → company association |
| create sales order line | line item | 5 | Line stock code (Syspro) → product lookup property, Line stock description (Syspro) → name, Order line price → price, Order line quantity → quantity, Sales order → deal association |
| create quote | deal | 9 | Customer retail price → amount, Expiry date → close date property, Enquiry date → create date property, Customer name → deal name property, Quote → external key property |
| create quote line | line item | 5 | Line stock code (Syspro) → product lookup property, Line stock code (Syspro) → name, Customer retail price → price, Quote line quantity → quantity, Quote → deal association |
| create invoice | deal | 9 | Currency value → amount, Invoice date → close date property, Invoice date → create date property, Customer, Invoice → deal name property, the linked Salesforce record → company association |
| create invoice line | line item | 6 | Warehouse amount → price, Quantity invoiced → quantity, Stock code → product lookup property, Stock code → name, Invoice → deal association |

## 6. Community templates

The catalogue carries 170 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 170
- Default operations: insert on 170, update on 170, delete on 170
- Marked as circular sync: 0
- Licence groups they span: 9
- Destination objects: company, line item, deal, product, contact, invoice, ticket, Products,
  Commercient Account Matching object (earlier package), Commercient AR Customer Managed Custom
  Object (earlier package), Commercient Product Matching object (earlier package), Commercient
  Products object, Commercient HubSpot new item object, Commercient HubSpot new quote line item
  object, Commercient Syspro product details object, Company, line items, order, 3 more and 8 custom
  objects
- Object display names: upsert customer, upsert invoice, upsert product, upsert invoice line, upsert
  contact, upsert sales order, create contact, create customer, create product, create sales order
  line, create invoice line, delete order, 51 more and 20 further templates
- Template groups: Account, Product, CRM Opportunity and Line, CRM Order and Line, Opportunity

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-hubspot/erps/syspro-7-and-above`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped SYSPRO 7 and above → HubSpot templates set up. dlake-crmpro-hubspot is the destination
skill this page sits under: its own text is the authority for the HubSpot conventions that hold
across every ERP, and its ERP table lists this page alongside every sibling ERP page for this
destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-hubspot/erps/syspro-7-and-above`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
