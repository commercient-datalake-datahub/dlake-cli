---
name: dlake-txdownloaderpro-zohocrm/erps/sage-x3
kind: erp-summary
description: >-
  The catalogue ships no default TxDownloaderPro template for the pair where Zoho CRM is the
  writeback destination and Sage X3 is the source. It does carry 4 community templates for the pair,
  across 1 process version, and this page lists them: how many there are, which objects they start
  from, what they are called where the name is a product artefact name, and which versions they
  belong to. No template content is described. Use it when deciding what the catalogue holds for
  this pair, and before assuming there is a default template here to import. It extends
  dlake-txdownloaderpro, which is the authority for operating TxDownloaderPro generally, and
  dlake-txdownloaderpro-zohocrm, the destination skill this page is a child of, which carries the
  Zoho CRM conventions that hold across every ERP.
---
# TxDownloaderPro ← Zoho CRM — Sage X3: no default templates, and the community set the catalogue carries

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-txdownloaderpro-zohocrm/erps/sage-x3` (or `list_skills`) against the
Commercient admin plane. Existing customers who need access or help: contact
support@commercient.com. New customers: contact sales@commercient.com to become a customer and be
whitelisted.

dlake-txdownloaderpro-zohocrm is the Zoho CRM page and carries the destination detail: it is the
skill this page is a child of, and the authority for the conventions that hold across every ERP, so
read it before this page.

The catalogue ships **no default template** for this pair: there is no default query, no default
mapping document and no default result structure to describe. What it does carry for the pair is a
set of community templates, written on a tenant rather than shipped, and this page states how many
there are, which objects they start from, what they are called where the name is a product artefact
name, and which process versions they belong to. No template text is reproduced.

## 1. Community templates

The catalogue carries community templates for this pair and no default template: templates written
on a tenant rather than shipped. Their content is not read and not described here — no query, no
mapping document, no field. What this section states is how many there are, which objects they start
from, what they are called where the name is a product artefact name, and which process versions
they belong to.

- **How many:** 4 community templates, across 1 process version.
- **Operations:** 0 carry insert, 2 carry update, 0 carry delete. A flag decides which operation the
  template is allowed to perform, as it does for a default template.

| Source → destination | Templates |
|---|---|
| Account → — | 4 |

None of these rows carries a destination object name in the catalogue, so the destination side of
every shape above is empty.

- **Template names the catalogue carries:** Import Order (1), New Customer (1), New or Update
  Contact (1), Update Customer (1).

Importing one of these writes the same TxDownloaderPro row that importing a default template writes
(in the operational skill); what differs is where the template came from, not how it is stored. What
any one of them contains is read from the imported row itself.

## 2. Verifying

Read the imported row before a run, not after. The operational skill is the authority on the
TxDownloaderPro tools and on the state they report.

There is no default query or mapping document for this pair to compare an imported row against: what
a given process runs is whatever the template it came from carries, and it is read from the imported
row itself.

## 3. Where this sits

- dlake-txdownloaderpro — the parent: exposure, key scoping, the two tables, its state, the mapping
  columns, the filter vocabulary, the TxDownloaderPro tools. **Read it first.**
- dlake-txdownloaderpro-zohocrm — the Zoho CRM destination page, which this page is a child of: the
  query shape, marker conventions and mapping conventions this destination uses across every ERP,
  and the ERP table that lists this page and its siblings.
- dlake-integration-setup — registration, CRM choice and the ERP connector, of which writeback is
  one step.
- dlake-crmpro and dlake-normalsync — the inbound leg, going the other way.
- dlake — general tenant operation.

This page describes what the catalogue carries for this pair, which is a community set and no
default set. It grows as that changes.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-txdownloaderpro-zohocrm/erps/sage-x3`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
