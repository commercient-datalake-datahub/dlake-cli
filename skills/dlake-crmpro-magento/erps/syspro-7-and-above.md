---
name: dlake-crmpro-magento/erps/syspro-7-and-above
kind: erp-summary
description: >-
  Use it when standing up or reading a SYSPRO 7 and above → Magento template set, when deciding
  which templates to import and activate, or when a run completes without pushing records and the
  answer is in the view or the configuration row. It extends dlake-crmpro, which covers operating
  CRMPro generally, and dlake-crmpro-magento, the destination skill this page is a child of, which
  carries the Magento conventions that hold across every ERP.
---
# CRMPro → Magento — SYSPRO 7 and above: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-magento/erps/syspro-7-and-above` (or `list_skills`) against the
Commercient admin plane. Existing customers who need access or help: contact
support@commercient.com. New customers: contact sales@commercient.com to become a customer and be
whitelisted.

dlake-crmpro is the parent skill and the authority for everything general: the CRMPro tools, process
configuration and field list, the sync history, how source data is selected, and what a run that
finds nothing does. Read it first; this page does not repeat it. dlake-crmpro-magento is the
destination skill this page is a child of, and the authority for the Magento conventions that hold
across every ERP: read it first, then come back here for what this source's own templates set. This
page grows as the catalogue does.

## 1. What the templates deliver

| Group | Business outcome | Objects | Source tables and views |
|---|---|---|---|
| **Magento get product** | The templates push products to Magento. New records are created, existing ones updated, and records removed in the ERP are deleted. | products | — |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Magento get product | products | external key column | 0 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. Community templates

The catalogue carries 8 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 8
- Default operations: insert on 8, update on 8, delete on 8
- Marked as circular sync: 0
- Licence groups they span: 2
- Destination objects: products, customers, orders, Update Price
- Object display names: Magento customer, Magento get product, Magento order tracking, Magento
  product, Magento update price and 2 further templates
- Template groups: CRM Order and Line

## 4. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-magento/erps/syspro-7-and-above`.

## 5. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped SYSPRO 7 and above → Magento templates set up. dlake-crmpro-magento is the destination
skill this page sits under: its own text is the authority for the Magento conventions that hold
across every ERP, and its ERP table lists this page alongside every sibling ERP page for this
destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-magento/erps/syspro-7-and-above`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
