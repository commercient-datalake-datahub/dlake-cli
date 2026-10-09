---
name: dlake-crmpro-hubspot/erps/dynamics-business-central
kind: erp-summary
description: >-
  Use it when standing up or reading a Dynamics Business Central → HubSpot template set, when
  deciding which templates to import and activate, or when a run completes without pushing records
  and the answer is in the view or the configuration row. It extends dlake-crmpro, which covers
  operating CRMPro generally, and dlake-crmpro-hubspot, the destination skill this page is a child
  of, which carries the HubSpot conventions that hold across every ERP.
---
# CRMPro → HubSpot — Dynamics Business Central: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-hubspot/erps/dynamics-business-central` (or `list_skills`)
against the Commercient admin plane. Existing customers who need access or help: contact
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
| **create product** | ERP items data becomes product in HubSpot. New records are created and existing ones updated; none are deleted. | product | items |
| **create company** | ERP customers data becomes company in HubSpot. New records are created and existing ones updated; none are deleted. | company | customers |
| **create contact** | ERP contacts data becomes contact in HubSpot. New records are created and existing ones updated; none are deleted. | contact | contacts |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Run sequence |
|---|---|---|
| create company | company | 5 |
| create contact | contact | 5 |
| create product | product | 10 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| new customer feed | insert only | customers |
| new contact feed | insert only | contacts |
| new item feed | insert only | items |

## 4. Order of work

The templates set run sequence to 5, 10. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 5 — create company, create contact
- 10 — create product

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- new contact feed reads new customer sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → HubSpot pairs |
|---|---|---|---|
| create company | company | 8 | No → Commercient AR customer code, Name → name, Address → address, City → city property, County → state |
| create contact | contact | 13 | Email → email, Company name → company, Name → last name property, Phone number → phone, Mobile phone number → mobile phone property |
| create product | product | 8 | No → record key, No → external key property, No → stock keeping unit property, Description → name, Search description → description |

## 6. Community templates

The catalogue carries 62 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 62
- Default operations: insert on 62, update on 62, delete on 62
- Marked as circular sync: 0
- Licence groups they span: 7
- Destination objects: deal, company, line item, product, contact, notes, Bill To Matching (custom
  object), Commercient Dynamics Business Central customer object, Data Matching (custom object),
  HubSpot customer (custom object), HubSpot new contact (custom object), HubSpot new item (custom
  object) and 5 custom objects
- Object display names: upsert company, upsert contact, upsert product, upsert ship to, upsert
  invoice deal, create company, create contact, create product, delete order deal, get company, get
  products, insert company notes US, 29 more and 11 further templates
- Template groups: Account, Product, CRM Opportunity and Line

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-hubspot/erps/dynamics-business-central`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Dynamics Business Central → HubSpot templates set up. dlake-crmpro-hubspot is the
destination skill this page sits under: its own text is the authority for the HubSpot conventions
that hold across every ERP, and its ERP table lists this page alongside every sibling ERP page for
this destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-hubspot/erps/dynamics-business-central`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
