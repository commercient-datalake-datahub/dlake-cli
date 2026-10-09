---
name: dlake-crmpro-shopify/erps/microsoft-dynamics-gp-2013
kind: erp-summary
description: >-
  Use it when standing up or reading a Microsoft Dynamics GP 2013 → Shopify template set, when
  deciding which templates to import and activate, or when a run completes without pushing records
  and the answer is in the view or the configuration row. It extends dlake-crmpro, which covers
  operating CRMPro generally, and dlake-crmpro-shopify, the destination skill this page is a child
  of, which carries the Shopify conventions that hold across every ERP.
---
# CRMPro → Shopify — Microsoft Dynamics GP 2013: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-shopify/erps/microsoft-dynamics-gp-2013` (or `list_skills`)
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
| **Product object update** | The templates push Product price, Product quantity to Shopify. Existing records are updated only — nothing is created and nothing is deleted. | Product price, Product quantity | item currencies, items, Shopify product variants (mirrored), item quantities, Shopify inventory levels (mirrored) |
| **Customer** | The templates push Customer to Shopify. Records are created once; they are not updated and none are deleted. | Customer | customers |
| **Customer Address** | The templates push Customer address to Shopify. Records are created once; they are not updated and none are deleted. | Customer address | customers |
| **Product object** | The templates push Product object to Shopify. Records are created once; they are not updated and none are deleted. | Product object | item currencies, item quantities, items |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Run sequence |
|---|---|---|
| Update Price | Product price | 1 |
| Create Customer | Customer | 1 |
| Create Product | Product object | 1 |
| Update Quantity | Product quantity | 1 |
| Customer address | Customer address | 2 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| product price update feed | — | item currencies, items, Shopify product variants (mirrored) |
| new customer feed | insert only | customers |
| new product feed | insert only | item currencies, item quantities, items |
| product quantity update feed | — | item quantities, items, Shopify product variants (mirrored), Shopify inventory levels (mirrored) |
| new customer address feed | insert only | customers |

## 4. Order of work

The templates set run sequence to 1, 2. A run processes active rows in ascending run sequence, which
is the order the templates put them in:

- 1 — Update Price, Create Customer, Create Product, Update Quantity
- 2 — Customer address

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- new customer address feed reads new customer sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Shopify pairs |
|---|---|---|---|
| Update Price | Product price | 3 | record key → record key, product variant identifier → product variant identifier, Price → Price |
| Create Customer | Customer | 9 | record key → record key, description → description, ERP code → ERP code, Given name → Given name, Middle name → Middle name |
| Create Product | Product object | 7 | Title → Title, Product body text → Product body text, record key → record key, →, Stock keeping unit → Stock keeping unit |
| Update Quantity | Product quantity | 6 | record key → record key, Id → Id, Inventory quantity → Inventory quantity, inventory item identifier → inventory item identifier, Previous quantity → Previous quantity |
| Customer address | Customer address | 16 | record key → record key, description → description, ERP code → ERP code, Address 1 → Address 1, Address 2 → Address 2 |

## 6. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-shopify/erps/microsoft-dynamics-gp-2013`.

## 7. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Microsoft Dynamics GP 2013 → Shopify templates set up. dlake-crmpro-shopify is the
destination skill this page sits under: its own text is the authority for the Shopify conventions
that hold across every ERP, and its ERP table lists this page alongside every sibling ERP page for
this destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-shopify/erps/microsoft-dynamics-gp-2013`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
