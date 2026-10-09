---
name: dlake-crmpro-salesforce/erps/deltek-vision
kind: erp-summary
description: >-
  Use it when standing up or reading a Deltek Vision → Salesforce template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-salesforce, the destination skill this page is a child of, which
  carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Deltek Vision: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/deltek-vision` (or `list_skills`) against the
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
| **(ungrouped)** | The templates push Price book object to Salesforce. New records are created and existing ones updated; none are deleted. | Price book object | — |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Contact, Commercient Client Managed Custom Object | clients, client addresses, contacts |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Client Address Managed Custom Object | client addresses |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Account | Account | Commercient AR customer code | 1 |
| Contact Object | Contact | External key (custom field) | 2 |
| Customer Address Sync | Commercient Client Address Managed Custom Object | Commercient external key (Deltek package) | 3 |
| Customer Sync | Commercient Client Managed Custom Object | Commercient external key (Deltek package) | 4 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| account feed | insert + update | clients, client addresses |
| contact feed | insert + update | contacts, clients |
| customer address feed | insert + update | client addresses |
| customer feed | insert + update | clients |

## 4. Order of work

The templates set run sequence to 1, 2, 3, 4. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 1 — Account
- 2 — Contact Object
- 3 — Customer Address Sync
- 4 — Customer Sync

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- contact feed reads account sync output
- customer feed reads account sync output
- customer address feed reads account sync output, customer sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Price book object | Price book object | 10 | Customer identifier, Ship to identifier → Commercient AR customer code, Customer name, Customer identifier, Ship to identifier → Name, Bill to address line 1, Bill to address line 2, Bill to address line 3 → Billing street, Bill to address city → Billing city, Bill to address state → Billing state |
| Account | Account | 8 | Commercient AR customer code column → Commercient AR customer code, Billing street → Billing street, Billing city → Billing city, Billing state → Billing state, Billing country → Billing country |
| Contact Object | Contact | 12 | account lookup → account lookup, Given name → Given name, Family name → Family name, Email → Email, Phone → Phone |
| Customer Address Sync | Commercient Client Address Managed Custom Object | 28 | Account → Account, Deltek client (related record) → Deltek client (related record), Accounting → Accounting, Address → Commercient address, Address 1 → Commercient address line 1 |
| Customer Sync | Commercient Client Managed Custom Object | 41 | Client identifier → Commercient client identifier, Client → Commercient client, Type → Commercient type, Status → Commercient status, Export indicator → Commercient export indicator |

## 6. Community templates

The catalogue carries 40 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 40
- Default operations: insert on 40, update on 40, delete on 40
- Marked as circular sync: 4
- Licence groups they span: 8
- Destination objects: Account, Contact, Commercient Client Managed Custom Object, Commercient
  Client Address Managed Custom Object, Deltek Opportunity (custom object), Deltek Opportunity
  Custom Field (custom object), Commercient Account Managed Custom Object, Commercient Contacts
  Managed Custom Object, Deltek Invoice Line (custom object), Deltek Invoice Master (custom object),
  Deltek Project Code (custom object), Deltek Project Description (custom object), Deltek Project
  Summary Sub (custom object), Opportunity, Price book object and 5 custom objects
- Object display names: Customer Sync, Customer Address Sync, Account, Account Sync, Contact Object,
  Contact Sync, Deltek Opportunity Custom Field, Opportunity, Project Sync, Update ERP AR customer
  code, Customer to Account Reverse Lookup, Deltek Invoice Line, 9 more and 6 further templates
- Template groups: Account, Opportunity, Customer Multi Ship Addresses, Invoice, CRM Opportunity and
  Line

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/deltek-vision`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Deltek Vision → Salesforce templates set up. dlake-crmpro-salesforce is the destination
skill this page sits under: its own text is the authority for the Salesforce conventions that hold
across every ERP, and its ERP table lists this page alongside every sibling ERP page for this
destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/deltek-vision`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
