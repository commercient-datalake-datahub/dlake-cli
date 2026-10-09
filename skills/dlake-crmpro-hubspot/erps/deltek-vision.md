---
name: dlake-crmpro-hubspot/erps/deltek-vision
kind: erp-summary
description: >-
  Use it when standing up or reading a Deltek Vision → HubSpot template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-hubspot, the destination skill this page is a child of, which carries
  the HubSpot conventions that hold across every ERP.
---
# CRMPro → HubSpot — Deltek Vision: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-hubspot/erps/deltek-vision` (or `list_skills`) against the
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
| **create customer** | ERP clients, client addresses data becomes company in HubSpot. New records are created and existing ones updated; none are deleted. | company | clients, client addresses |
| **create opportunity** | The templates push deal to HubSpot. New records are created and existing ones updated; none are deleted. | deal | opportunities |
| **create project** | The templates push deal to HubSpot. New records are created and existing ones updated; none are deleted. | deal | projects |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Run sequence |
|---|---|---|
| create customer | company | 5 |
| create opportunity | deal | 11 |
| create project | deal | 12 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| new customer feed | insert only | clients, client addresses |
| new opportunity deal feed | insert only | opportunities |
| new project deal feed | insert only | projects |

## 4. Order of work

The templates set run sequence to 5, 11, 12. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 5 — create customer
- 11 — create opportunity
- 12 — create project

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- new opportunity deal feed reads new customer sync output, new contact sync output, opportunity
  sync output (generic name); no template in this set writes new contact sync output, opportunity
  sync output (generic name)
- new project deal feed reads new customer sync output, new contact sync output, new project deal
  sync output; no template in this set writes new contact sync output, new project deal sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → HubSpot pairs |
|---|---|---|---|
| create customer | company | 8 | Client identifier → Commercient AR customer code, Name → name, Address 1 → address, Address 2 → address line 2 property, City → city property |
| create opportunity | deal | 9 | Opportunity revenue → amount, close date property → close date property, Open date → create date property, Name → deal name property, Client identifier → company association |
| create project | deal | 9 | Work breakdown structure level 1, Work breakdown structure level 2, Work breakdown structure level 3 → external key property, Project fee → amount, End date → close date property, create date property → create date property, Name, Work breakdown structure level 1, Work breakdown structure level 2, Work breakdown structure level 3 → deal name property |

## 6. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-hubspot/erps/deltek-vision`.

## 7. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Deltek Vision → HubSpot templates set up. dlake-crmpro-hubspot is the destination skill
this page sits under: its own text is the authority for the HubSpot conventions that hold across
every ERP, and its ERP table lists this page alongside every sibling ERP page for this destination.
For the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-hubspot/erps/deltek-vision`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
