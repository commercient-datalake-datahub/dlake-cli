---
name: dlake-crmpro-shopify/erps/dynamics-business-central
kind: erp-summary
description: >-
  Use it when standing up or reading a Dynamics Business Central → Shopify template set, when
  deciding which templates to import and activate, or when a run completes without pushing records
  and the answer is in the view or the configuration row. It extends dlake-crmpro, which covers
  operating CRMPro generally, and dlake-crmpro-shopify, the destination skill this page is a child
  of, which carries the Shopify conventions that hold across every ERP.
---
# CRMPro → Shopify — Dynamics Business Central: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-shopify/erps/dynamics-business-central` (or `list_skills`)
against the Commercient admin plane. Existing customers who need access or help: contact
support@commercient.com. New customers: contact sales@commercient.com to become a customer and be
whitelisted.

dlake-crmpro is the parent skill and the authority for everything general: the CRMPro tools, process
configuration and field list, the sync history, how source data is selected, and what a run that
finds nothing does. Read it first; this page does not repeat it. dlake-crmpro-shopify is the
destination skill this page is a child of, and the authority for the Shopify conventions that hold
across every ERP: read it first, then come back here for what this source's own templates set. This
page grows as the catalogue does.

## 1. What the templates deliver

| Group | Business outcome | Objects | Source tables and views |
|---|---|---|---|
| **Create Customer** | The templates push Customer to Shopify. Records are created once; they are not updated and none are deleted. | Customer | customers |
| **Customer Address** | The templates push Customer address to Shopify. Records are created once; they are not updated and none are deleted. | Customer address | customers |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Run sequence |
|---|---|---|
| Create Customer | Customer | 1 |
| Create Customer Address | Customer address | 2 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| new customer feed | insert only | customers |
| new customer address feed | insert only | customers |

## 4. Order of work

The templates set run sequence to 1, 2. A run processes active rows in ascending run sequence, which
is the order the templates put them in:

- 1 — Create Customer
- 2 — Create Customer Address

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- new customer address feed reads new customer sync output

## 5. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-shopify/erps/dynamics-business-central`.

## 6. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Dynamics Business Central → Shopify templates set up. dlake-crmpro-shopify is the
destination skill this page sits under: its own text is the authority for the Shopify conventions
that hold across every ERP, and its ERP table lists this page alongside every sibling ERP page for
this destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-shopify/erps/dynamics-business-central`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
