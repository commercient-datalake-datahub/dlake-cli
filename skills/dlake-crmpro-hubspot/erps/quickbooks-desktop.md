---
name: dlake-crmpro-hubspot/erps/quickbooks-desktop
kind: erp-summary
description: >-
  Use it when standing up or reading a QuickBooks Desktop → HubSpot template set, when deciding
  which templates to import and activate, or when a run completes without pushing records and the
  answer is in the view or the configuration row. It extends dlake-crmpro, which covers operating
  CRMPro generally, and dlake-crmpro-hubspot, the destination skill this page is a child of, which
  carries the HubSpot conventions that hold across every ERP.
---
# CRMPro → HubSpot — QuickBooks Desktop: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-hubspot/erps/quickbooks-desktop` (or `list_skills`) against the
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
| **create item** | The templates push products to HubSpot. New records are created and existing ones updated; none are deleted. | products | inventory items |
| **create order** | The templates push deal to HubSpot. New records are created and existing ones updated; none are deleted. | deal | sales orders |
| **create order detail** | The templates push line item to HubSpot. New records are created and existing ones updated; none are deleted. | line item | sales order lines |
| **create invoice** | The templates push deal to HubSpot. New records are created and existing ones updated; none are deleted. | deal | invoices |
| **create invoice detail** | The templates push line item to HubSpot. New records are created and existing ones updated; none are deleted. | line item | invoice lines |
| **create estimate** | The templates push deal to HubSpot. New records are created and existing ones updated; none are deleted. | deal | estimates |
| **create estimate detail** | The templates push line item to HubSpot. New records are created and existing ones updated; none are deleted. | line item | estimate lines |
| **create contact** | ERP customers, sales reps data becomes contact in HubSpot. New records are created and existing ones updated; none are deleted. | contact | contacts, additional contact information, customers, employees, contact list |

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
| create estimate | deal | 15 |
| create estimate detail | line item | 16 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Emits the linked Salesforce record | Source tables |
|---|---|---|---|
| new customer feed | insert only | no | customers |
| new contact feed | insert only | yes | contacts, additional contact information, customers, employees, contact list |
| new item feed | insert only | no | inventory items |
| new order deal feed | insert only | no | sales orders |
| new order line item feed | insert only | no | sales order lines |
| new invoice deal feed | insert only | no | invoices |
| new invoice line item feed | insert only | no | invoice lines |
| new estimate deal feed | insert only | no | estimates |
| new estimate line item feed | insert only | no | estimate lines |

## 4. Order of work

The templates set run sequence to 5, 10, 11, 12, 13, 14, 15, 16. A run processes active rows in
ascending run sequence, which is the order the templates put them in:

- 5 — create customer, create contact
- 10 — create item
- 11 — create order
- 12 — create order detail
- 13 — create invoice
- 14 — create invoice detail
- 15 — create estimate
- 16 — create estimate detail

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
- new estimate deal feed reads new customer sync output, estimate; no template in this set writes
  estimate
- new estimate line item feed reads new item sync output, estimate; no template in this set writes
  estimate
- new contact feed reads new customer sync output, new owner sync output; no template in this set
  writes new owner sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → HubSpot pairs |
|---|---|---|---|
| create customer | company | 9 | returned list identifier → Commercient AR customer code, Name → name, Billing address block line 2 → address, Billing address block line 3 → address line 2 property, Bill address city → city property |
| create contact | contact | 13 | Given name → first name property, Family name → last name property, Email → email, Copy email address → Copy email address, Phone → phone |
| create item | products | 5 | Name → stock keeping unit property, Full name → name, Sales description → description, Average cost → cost of goods sold property, Sales price → price |
| create order | deal | 7 | Total amount → amount, Due date → close date property, Transaction date → create date property, Reference number, Customer full name → deal name property, Customer list identifier → company association |
| create order detail | line item | 6 | Sales order line rate → price, Sales order line quantity → quantity, the linked Salesforce record → product lookup property, Sales order line item full name → name, the linked Salesforce record → deal association |
| create invoice | deal | 8 | Subtotal → amount, Due date → close date property, Transaction date → create date property, Reference number, Customer full name → deal name property, Customer list identifier → company association |
| create invoice detail | line item | 6 | Line rate → price, Line quantity → quantity, Line item list identifier → product lookup property, Line item full name → name, returned transaction identifier → deal association |
| create estimate | deal | 8 | Subtotal → amount, Due date → close date property, Transaction date → create date property, Reference number, Customer full name → deal name property, Customer list identifier → company association |
| create estimate detail | line item | 6 | Line rate → price, Line quantity → quantity, the linked Salesforce record → product lookup property, Line item full name → name, the linked Salesforce record → deal association |

## 6. Community templates

The catalogue carries 646 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 646
- Default operations: insert on 646, update on 646, delete on 646
- Marked as circular sync: 0
- Licence groups they span: 9
- Destination objects: deal, line item, company, contact, products, product, Commercient Account
  Matching Managed Custom Object, Commercient Contact Matching object, order, Commercient Product
  Matching Managed Custom Object, companies, contacts, deals, invoice, Commercient QuickBooks
  customer object, Commercient QuickBooks invoice object, Commercient QuickBooks sales order object,
  Commercient Accounts object, Commercient Contact Data 1 object, 29 more and 19 custom objects
- Object display names: upsert customer, create invoice detail, upsert item, create contact, create
  customer, upsert invoice, create item, upsert contact, create invoice, upsert invoice detail,
  upsert estimate, upsert order, 156 more and 37 further templates
- Template groups: Account, CRM Opportunity and Line, Product, CRM Order and Line, Invoice

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-hubspot/erps/quickbooks-desktop`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped QuickBooks Desktop → HubSpot templates set up. dlake-crmpro-hubspot is the destination
skill this page sits under: its own text is the authority for the HubSpot conventions that hold
across every ERP, and its ERP table lists this page alongside every sibling ERP page for this
destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-hubspot/erps/quickbooks-desktop`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
