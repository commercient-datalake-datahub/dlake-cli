---
name: dlake-crmpro-hubspot/erps/hubspot
kind: erp-summary
description: >-
  Use it when standing up or reading a HubSpot → HubSpot template set, when deciding which templates
  to import and activate, or when a run completes without pushing records and the answer is in the
  view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro generally,
  and dlake-crmpro-hubspot, the destination skill this page is a child of, which carries the HubSpot
  conventions that hold across every ERP.
---
# CRMPro → HubSpot — HubSpot: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-hubspot/erps/hubspot` (or `list_skills`) against the Commercient
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
| **create order** | The templates push deal to HubSpot. New records are created and existing ones updated; none are deleted. | deal | — |

## 2. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → HubSpot pairs |
|---|---|---|---|
| create order | deal | 10 | Customer identifier, Ship to identifier → Commercient AR customer code, Customer name, Customer identifier, Ship to identifier → Name, Bill to address line 1, Bill to address line 2, Bill to address line 3 → Billing street, Bill to address city → Billing city, Bill to address state → Billing state |

## 3. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-hubspot/erps/hubspot`.

## 4. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped HubSpot → HubSpot templates set up. dlake-crmpro-hubspot is the destination skill this
page sits under: its own text is the authority for the HubSpot conventions that hold across every
ERP, and its ERP table lists this page alongside every sibling ERP page for this destination. For
the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-hubspot/erps/hubspot`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
