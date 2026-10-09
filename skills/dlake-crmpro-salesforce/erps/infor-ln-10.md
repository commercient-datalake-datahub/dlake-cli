---
name: dlake-crmpro-salesforce/erps/infor-ln-10
kind: erp-summary
description: >-
  Use it when standing up or reading an Infor LN 10 → Salesforce template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-salesforce, the destination skill this page is a child of, which
  carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Infor LN 10: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/infor-ln-10` (or `list_skills`) against the
Commercient admin plane. Existing customers who need access or help: contact
support@commercient.com. New customers: contact sales@commercient.com to become a customer and be
whitelisted.

dlake-crmpro is the parent skill and the authority for everything general: the CRMPro tools, process
configuration and field list, the sync history, how source data is selected, and what a run that
finds nothing does. Read it first; this page does not repeat it. dlake-crmpro-salesforce is the
destination skill this page is a child of, and the authority for the Salesforce conventions that
hold across every ERP: read it first, then come back here for what this source's own templates set.
This page grows as the catalogue does.

## 1. What the templates deliver

| Group | Business outcome | Objects | Source tables and views |
|---|---|---|---|
| **Account** | Sync ERP Contact data to CRM's Contact Object. If you have a store of Contacts in your ERP then enable this sync to bring them across to the CRM. New records are created, existing ones updated, and records removed in the ERP are deleted. | Contact, Commercient Baan Customer Managed Custom Object, Account, Commercient Baan Customer Address Managed Custom Object | contacts, business partner records, addresses |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Sync Customer | Commercient Baan Customer Managed Custom Object | Commercient external key (Baan package) | 4 |
| Sync Standard Contact | Contact | External key (custom field) | 5 |
| Sync Standard Contact | Contact | External key (custom field) | 5 |
| Sync Standard Contact | Commercient Baan Customer Address Managed Custom Object | Commercient external key (Baan package) | 5 |
| Sync Customer to account lookup | Account | Commercient AR customer code | 6 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| customer feed | insert + update | business partner records |
| standard contact feed | insert + update | contacts |
| account feed | insert + update | business partner records, addresses |
| shipping address feed | insert + update | addresses |
| customer account lookup feed | insert + update | business partner records |

## 4. Order of work

The templates set run sequence to 4, 5, 6. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 4 — Sync Customer
- 5 — Sync Standard Contact
- 6 — Sync Customer to account lookup

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- standard contact feed reads account sync output, customer sync output; no template in this set
  writes account sync output
- account feed reads account sync output; no template in this set writes account sync output
- customer feed reads account sync output; no template in this set writes account sync output
- customer account lookup feed reads customer sync output
- shipping address feed reads account sync output, customer sync output; no template in this set
  writes account sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Sync Standard Contact | Contact | 7 | Title → Title, Given name → Given name, Family name → Family name, Phone → Phone, Email → Email |

## 6. Community templates

The catalogue carries 10 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 10
- Default operations: insert on 10, update on 10, delete on 10
- Marked as circular sync: 0
- Licence groups they span: 5
- Destination objects: Product, Commercient Baan Item Managed Custom Object, Commercient Quote
  Header Managed Custom Object, Contact and 5 custom objects
- Object display names: Sync Item, Sync Item to product lookup, Sync Product object, Sync Quote
  header, Sync Service Contract, Sync Service Contract Line, Sync Standard Contact and 3 further
  templates
- Template groups: Product, Account, Opportunity

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/infor-ln-10`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Infor LN 10 → Salesforce templates set up. dlake-crmpro-salesforce is the destination
skill this page sits under: its own text is the authority for the Salesforce conventions that hold
across every ERP, and its ERP table lists this page alongside every sibling ERP page for this
destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/infor-ln-10`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
