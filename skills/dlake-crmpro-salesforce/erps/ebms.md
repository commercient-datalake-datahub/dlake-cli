---
name: dlake-crmpro-salesforce/erps/ebms
kind: erp-summary
description: >-
  What the CRMPro template catalogue carries for an EBMS source pushing into Salesforce: no Standard
  template ships for this pair, and the 18 community templates it does carry are stated as counts,
  destination objects and template groups only — a community template is authored in a tenant, so
  its names, notes, field mapping and SQL are not published. The destination objects they write are
  Account, Price book entry, Product, Contact, Shipping Address (custom object) and more. Use it
  when deciding whether a shipped template set exists for an EBMS → Salesforce before standing one
  up, and what the community set covers. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-salesforce, the destination skill this page is a child of, which
  carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — EBMS: what the template catalogue carries

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/ebms` (or `list_skills`) against the Commercient
admin plane. Existing customers who need access or help: contact support@commercient.com. New
customers: contact sales@commercient.com to become a customer and be whitelisted.

dlake-crmpro is the parent skill and the authority for everything general: the CRMPro tools, process
configuration and field list, the sync history, how source data is selected, and what a run that
finds nothing does. Read it first; this page does not repeat it. dlake-crmpro-salesforce is the
destination skill this page is a child of, and the authority for the Salesforce conventions that
hold across every ERP: read it first. The catalogue ships no Standard template for this pair. Its
templates are community templates, authored in a tenant and imported the same way as any other, so
what follows is what that set amounts to — how many templates, which operations they default to,
which destination objects they write and which groups they fall in. This page grows as the catalogue
does.

## 1. Community templates

The catalogue carries 18 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in.

- Templates: 18
- Default operations: insert on 18, update on 18, delete on 18
- Marked as circular sync: 0
- Licence groups they span: 10
- Destination objects: Account, Price book entry, Product, Contact, Shipping Address (custom
  object), User and 9 custom objects
- Object display names: Contact, EBMS Customer, EBMS Customer to account lookup, EBMS Invoice
  Detail, EBMS Invoice Header, EBMS Item, EBMS Item Warehouse, EBMS Item to product lookup, EBMS
  Sales Order Detail, EBMS Sales Order Header, EBMS Salesperson, EBMS Shipping address, 4 more and 2
  further templates
- Template groups: Product, Account, Invoice, Sales order, Customer Multi Ship Addresses

## 2. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/ebms`.

## 3. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the EBMS → Salesforce template catalogue carries. dlake-crmpro-salesforce is the destination skill
this page sits under: its own text is the authority for the Salesforce conventions that hold across
every ERP, and its ERP table lists this page alongside every sibling ERP page for this destination.
For the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/ebms`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
