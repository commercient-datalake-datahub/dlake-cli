---
name: dlake-crmpro-hubspot/erps/infor-cloudsuite
kind: erp-summary
description: >-
  Use it when standing up or reading an Infor CloudSuite → HubSpot template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-hubspot, the destination skill this page is a child of, which carries
  the HubSpot conventions that hold across every ERP.
---
# CRMPro → HubSpot — Infor CloudSuite: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-hubspot/erps/infor-cloudsuite` (or `list_skills`) against the
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
| **create contact** | ERP contact master records, customer contacts, customer addresses data becomes contact in HubSpot. New records are created and existing ones updated; none are deleted. | contact | contact master records, customer contacts, customer addresses |
| **create company** | ERP customer address, customer master data becomes company in HubSpot. New records are created and existing ones updated; none are deleted. | company | customers, customer addresses |
| **create opporrtunity deal** | ERP customer address, customer master, opportunity master data becomes deal in HubSpot. New records are created and existing ones updated; none are deleted. | deal | opportunities, customer addresses, customers, salespeople |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Run sequence |
|---|---|---|
| create company | company | 1 |
| create opportunity | deal | 3 |
| create contact | contact | 5 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Emits the linked Salesforce record | Source tables |
|---|---|---|---|
| new customer feed | insert only | no | customers, customer addresses |
| new opportunity deal feed | insert only | yes | opportunities, customer addresses, customers, salespeople |
| new contact feed | insert only | no | contact master records, customer contacts, customer addresses |

## 4. Order of work

The templates set run sequence to 1, 3, 5. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 1 — create company
- 3 — create opportunity
- 5 — create contact

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- new contact feed reads new customer sync output
- new opportunity deal feed reads new customer sync output, new contact sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → HubSpot pairs |
|---|---|---|---|
| create company | company | 10 | Site reference, ERP customer number, Customer sequence number → Commercient AR customer code, name → name, city property → city property, state → state, postal code property → postal code property |
| create opportunity | deal | 9 | Opportunity identifier, Site reference → external key property, description, Opportunity identifier → deal name property, Estimated value → amount, Close date, Projected close date → close date property, create date property → create date property |
| create contact | contact | 19 | Contact identifier → external key property, ERP first name → first name property, ERP last name → last name property, email → email, Office phone → phone |

## 6. Community templates

The catalogue carries 52 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 52
- Default operations: insert on 52, update on 52, delete on 52
- Marked as circular sync: 0
- Licence groups they span: 6
- Destination objects: deal, company, line item, contact, product, quote, Account Matching (custom
  object), Companies, Deals, Shipping Address Matching (custom object) and a custom object
- Object display names: upsert company, upsert opportunity, upsert contact, upsert batch product,
  upsert estimate line, upsert product, create company, create contact, create opportunity, create
  quote, create quote line, delete batch line item, 20 more and 8 further templates
- Template groups: Account, CRM Quote and Line, CRM Opportunity and Line, Customer Multi Ship
  Addresses

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-hubspot/erps/infor-cloudsuite`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Infor CloudSuite → HubSpot templates set up. dlake-crmpro-hubspot is the destination
skill this page sits under: its own text is the authority for the HubSpot conventions that hold
across every ERP, and its ERP table lists this page alongside every sibling ERP page for this
destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-hubspot/erps/infor-cloudsuite`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
