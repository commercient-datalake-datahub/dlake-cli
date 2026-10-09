---
name: dlake-crmpro-hubspot/erps/sap-b1
kind: erp-summary
description: >-
  Use it when standing up or reading a SAP Business One → HubSpot template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-hubspot, the destination skill this page is a child of, which carries
  the HubSpot conventions that hold across every ERP.
---
# CRMPro → HubSpot — SAP Business One: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-hubspot/erps/sap-b1` (or `list_skills`) against the Commercient
admin plane. Existing customers who need access or help: contact support@commercient.com. New
customers: contact sales@commercient.com to become a customer and be whitelisted.

dlake-crmpro is the parent skill and the authority for everything general: the CRMPro tools, process
configuration and field list, the sync history, how source data is selected, and what a run that
finds nothing does. Read it first; this page does not repeat it. dlake-crmpro-hubspot is the
destination skill this page is a child of, and the authority for the HubSpot conventions that hold
across every ERP: read it first, then come back here for what this source's own templates set. This
page grows as the catalogue does.

## 1. What the templates deliver

| Group | Business outcome | Objects | Source tables and views |
|---|---|---|---|
| **create customer** | ERP business partners, business partner addresses data becomes company in HubSpot. New records are created and existing ones updated; none are deleted. | company | business partners, business partner addresses |
| **create item** | The templates push products to HubSpot. New records are created and existing ones updated; none are deleted. | products | items |
| **create order** | The templates push deal to HubSpot. New records are created and existing ones updated; none are deleted. | deal | sales orders |
| **create order detail** | The templates push line item to HubSpot. New records are created and existing ones updated; none are deleted. | line item | sales order lines |
| **create invoice** | The templates push deal to HubSpot. New records are created and existing ones updated; none are deleted. | deal | AR invoice documents |
| **create invoice detail** | The templates push line item to HubSpot. New records are created and existing ones updated; none are deleted. | line item | invoice lines |
| **create quote** | The templates push deal to HubSpot. New records are created and existing ones updated; none are deleted. | deal | quotes |
| **create quote detail** | The templates push line item to HubSpot. New records are created and existing ones updated; none are deleted. | line item | quotation lines |
| **create contact** | ERP business partner contacts data becomes contact in HubSpot. New records are created and existing ones updated; none are deleted. | contact | business partner contacts |

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
| new customer feed | insert only | business partners, business partner addresses |
| new contact feed | insert only | business partner contacts |
| new item feed | insert only | items |
| new order deal feed | insert only | sales orders |
| new order line item feed | insert only | sales order lines |
| new invoice deal feed | insert only | AR invoice documents |
| new invoice line item feed | insert only | invoice lines |
| new quote deal feed | insert only | quotes |
| new quote line item feed | insert only | quotation lines |

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
- new quote deal feed reads new customer sync output, new quote deal sync output; no template in
  this set writes new quote deal sync output
- new quote line item feed reads new item sync output, quote sync output (generic name); no template
  in this set writes quote sync output (generic name)
- new contact feed reads new customer sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → HubSpot pairs |
|---|---|---|---|
| create customer | company | 9 | Business partner code → Commercient AR customer code, Business partner name → name, Street → address, Address 2 → address line 2 property, City → city property |
| create item | products | 4 | Item code → stock keeping unit property, Item name → name, Item name → description, Price per unit → price |
| create order | deal | 7 | Document total → amount, Document due date → close date property, Document date → create date property, document number, Business partner name → deal name property, Business partner code → company association |
| create order detail | line item | 6 | Price → price, Quantity → quantity, Item code → product lookup property, Line item description → name, returned document entry → deal association |
| create invoice | deal | 7 | Document total → amount, Document due date → close date property, Document date → create date property, document number, Business partner name → deal name property, the linked Salesforce record → company association |
| create invoice detail | line item | 6 | Price → price, Quantity → quantity, Item code → product lookup property, Line item description → name, returned document entry → deal association |
| create quote | deal | 7 | Document total → amount, Document due date → close date property, Document date → create date property, document number, Business partner name → deal name property, Business partner code → company association |
| create quote detail | line item | 6 | Price → price, Quantity → quantity, Item code → product lookup property, Line item description → name, returned document entry → deal association |

## 6. Community templates

The catalogue carries 39 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 39
- Default operations: insert on 39, update on 39, delete on 39
- Marked as circular sync: 0
- Licence groups they span: 6
- Destination objects: line item, deal, company, contact, Contacts, products, Commercient Account
  Matching object, Commercient Contact Matching object, Commercient Product Matching object,
  Companies, Deals, invoice, order, quote and 2 custom objects
- Object display names: create contact, create invoice detail, create invoice, create order detail,
  Upsert customer, upsert invoice, upsert item, upsert order, create customer, create order, create
  quote, create quote detail, 10 more and 3 further templates
- Template groups: Account, CRM Opportunity and Line, CRM Quote and Line, Product

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-hubspot/erps/sap-b1`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped SAP Business One → HubSpot templates set up. dlake-crmpro-hubspot is the destination
skill this page sits under: its own text is the authority for the HubSpot conventions that hold
across every ERP, and its ERP table lists this page alongside every sibling ERP page for this
destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-hubspot/erps/sap-b1`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
