---
name: dlake-crmpro-salesforce/erps/workday
kind: erp-summary
description: >-
  Use it when standing up or reading a Workday → Salesforce template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-salesforce, the destination skill this page is a child of, which
  carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Workday: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/workday` (or `list_skills`) against the
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
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Commercient Workday Customer Master Managed Custom Object | customers, customer addresses, customer extra data |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Workday Customer Address Managed Custom Object | customer addresses |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Workday Sales Invoice Managed Custom Object, Commercient Workday Sales Invoice Lines Managed Custom Object | sales invoices, sales invoice extra fields, sales invoice lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Account Cache | Account | Commercient AR customer code | 1 |
| Account Create | Account | Commercient AR customer code | 1 |
| Customer | Commercient Workday Customer Master Managed Custom Object | Commercient external key | 2 |
| Customer Reverse Lookup | Account | Commercient AR customer code | 3 |
| Invoice | Commercient Workday Sales Invoice Managed Custom Object | Commercient external key | 4 |
| Invoice Line | Commercient Workday Sales Invoice Lines Managed Custom Object | Commercient external key | 5 |
| Address | Commercient Workday Customer Address Managed Custom Object | Commercient external key | 6 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| account create feed | insert + update | customers, customer addresses, customer extra data |
| customer feed | insert + update | customers, customer extra data |
| customer reverse lookup feed | insert + update | customers |
| invoice feed | insert + update | sales invoices, sales invoice extra fields |
| invoice line feed | insert + update | sales invoice lines, sales invoices |
| address feed | insert + update | customer addresses |

## 4. Order of work

The templates set run sequence to 1, 2, 3, 4, 5, 6. A run processes active rows in ascending run
sequence, which is the order the templates put them in:

- 1 — Account Cache, Account Create
- 2 — Customer
- 3 — Customer Reverse Lookup
- 4 — Invoice
- 5 — Invoice Line
- 6 — Address

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- account create feed reads account sync output
- customer reverse lookup feed reads customer sync output
- customer feed reads account sync output
- address feed reads account sync output, customer sync output
- invoice feed reads account sync output, customer sync output
- invoice line feed reads invoice sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Account Cache | Account | 11 | Commercient AR customer code column → Commercient AR customer code, Billing street → Billing street, Billing city → Billing city, Billing postal code → Billing postal code, Billing country → Billing country |
| Account Create | Account | 11 | Commercient AR customer code column → Commercient AR customer code, Billing street → Billing street, Billing city → Billing city, Billing postal code → Billing postal code, Billing country → Billing country |
| Customer | Commercient Workday Customer Master Managed Custom Object | 45 | returned customer identifier → Commercient customer identifier, Customer reference identifier → Customer reference identifier, Customer name → Commercient customer name, Worktag only → Commercient worktag only, Submit → Commercient submit |
| Customer Reverse Lookup | Account | 2 | Commercient AR customer code column → Commercient AR customer code, Workday customer master lookup column → Workday customer master (custom field) |
| Invoice | Commercient Workday Sales Invoice Managed Custom Object | 44 | Customer invoice identifier → Customer invoice identifier, Submit → Commercient submit, Locked in Workday → Locked in Workday, Populate with default bill to contacts → Populate with default bill to contacts, Award billing sequence number → Award billing sequence number |
| Invoice Line | Commercient Workday Sales Invoice Lines Managed Custom Object | 27 | returned invoice number → returned invoice number, Customer invoice identifier → Customer invoice identifier, Customer invoice line reference → Customer invoice line reference, Customer invoice line reference identifier → Customer invoice line reference identifier, Line order → Commercient line order |
| Address | Commercient Workday Customer Address Managed Custom Object | 14 | returned customer identifier → Commercient customer identifier, Address identifier → Commercient address identifier, Address format type → Commercient address format type, Address line 1 → Commercient address line 1, Address line 2 → Commercient address line 2 |

## 6. Community templates

The catalogue carries 16 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 16
- Default operations: insert on 16, update on 16, delete on 16
- Marked as circular sync: 0
- Licence groups they span: 5
- Destination objects: Account, Commercient Workday Customer Address Managed Custom Object,
  Commercient Workday Customer Master Managed Custom Object, Commercient Workday Sales Invoice
  Managed Custom Object, Commercient Workday Sales Invoice Lines Managed Custom Object, Content
  document
- Object display names: Account Cache, Account Create, Address, Customer, Customer Reverse Lookup,
  Invoice, Invoice Line and a further template
- Template groups: Account, Invoice, Customer Multi Ship Addresses

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/workday`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Workday → Salesforce templates set up. dlake-crmpro-salesforce is the destination skill
this page sits under: its own text is the authority for the Salesforce conventions that hold across
every ERP, and its ERP table lists this page alongside every sibling ERP page for this destination.
For the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/workday`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
