---
name: dlake-crmpro-shopify/erps/syspro-7-and-above
kind: erp-summary
description: >-
  Use it alongside dlake-crmpro-shopify when standing up or debugging a SYSPRO → Shopify Phase 1
  sync, or when a product's quantity or price is not moving. It extends dlake-crmpro, which covers
  operating CRMPro generally, and dlake-crmpro-shopify, the destination skill this page is a child
  of, which carries the Shopify conventions that hold across every ERP.
---
# CRMPro → Shopify — SYSPRO 7 and above: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-shopify/erps/syspro-7-and-above` (or `list_skills`) against the
Commercient admin plane. Existing customers who need access or help: contact
support@commercient.com. New customers: contact sales@commercient.com to become a customer and be
whitelisted.

dlake-crmpro is the product parent and the authority for everything general: the CRMPro tools,
process configuration and field list, the sync history, how source data is selected, and what a run
that finds nothing does. Read those first; this page does not repeat them.

Where the catalogue and a live install disagree, trust the install and say so.

## 1. What the default catalogue delivers

Four template groups ship for SYSPRO 7 → Shopify.

| Group | Business outcome | Object | Source view |
|---|---|---|---|
| **Customer** | ERP customers become Shopify customers: the name is split into first, middle and last, the email and phone come across, and the ERP customer code travels with the record. New customers are created; existing ones are not updated and none are deleted. | Customer | new customer feed |
| **Customer Address** | Each ERP customer's shipping address becomes the default address on the matching Shopify customer. Created once per customer; not updated, not deleted. | Customer address | new customer address feed |
| **Product object** | ERP stock codes become Shopify products, with the ERP description as the title and body, the stock code as the Stock keeping unit, the price from the ERP price table and the on hand quantity summed across warehouses. Created once; not updated, not deleted. | Product object | new product feed |
| **Product object update** | Keeps an existing Shopify variant in step with the ERP: one leg pushes the on hand quantity when it differs from Shopify's, the other pushes the selling price when it differs. Update-only — neither leg creates or deletes anything. | Product quantity, Product price | product quantity update feed, product price update feed |

Note the split: **creation and maintenance are different processes on different objects.** Product
creation happens once through Product object; everything afterwards is the two Product object update
legs. A price change in the ERP does not flow through the create leg, because that leg never
revisits a product it has already created.

## 2. The source data and the sync output

| View |
|---|
| new customer feed |
| new customer address feed |
| new product feed |
| product quantity update feed |
| product price update feed |

Run sequence in this set is 1 and 2 in the customer group, 1 in the product group, and 1 on both
update legs — values that collide across groups, so set a coherent order yourself after import.

## 3. A worked create view

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-shopify/erps/syspro-7-and-above`.

## 4. A worked update view

The mirror join, the value carrying record key and the integer cast are the three things
dlake-crmpro-shopify warns about, and this is the view they are read from.

## 5. Where this sits

dlake-crmpro is the general operating surface, and dlake-crmpro-shopify is the destination skill
this page sits under: its own text is the authority for the Shopify conventions that hold across
every ERP, and its ERP table lists this page alongside every sibling ERP page for this destination.
For the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-shopify/erps/syspro-7-and-above`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
