---
name: dlake-crmpro-salesforce/erps/eci-e-automate
kind: erp-summary
description: >-
  Use it when standing up or reading an ECi e-Automate → Salesforce template set, when deciding
  which templates to import and activate, or when a run completes without pushing records and the
  answer is in the view or the configuration row. It extends dlake-crmpro, which covers operating
  CRMPro generally, and dlake-crmpro-salesforce, the destination skill this page is a child of,
  which carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — ECi e-Automate: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/eci-e-automate` (or `list_skills`) against the
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
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, ECi E-Automate Customer (custom object) | customer details |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created, existing ones updated, and records removed in the ERP are deleted. | ECi E-Automate Invoice Header (custom object), ECi E-Automate Invoice Detail (custom object) | invoices, invoice lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Child Account | Account | Commercient AR customer code | 2 |
| ECI E-AUTOMATE Customer | ECi E-Automate Customer (custom object) | External key (custom field) | 3 |
| ECI E-AUTOMATE Customer to account lookup | Account | Commercient AR customer code | 4 |
| ECI E-AUTOMATE Invoice header | ECi E-Automate Invoice Header (custom object) | External key (custom field) | 5 |
| ECI E-AUTOMATE Invoice detail | ECi E-Automate Invoice Detail (custom object) | External key (custom field) | 6 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| account feed | insert + update | customer details |
| customer feed | insert + update | customer details |
| customer account lookup feed | insert + update | customer details |
| invoice feed | insert + update | invoices |
| invoice line feed | insert + update | invoice lines |

## 4. Order of work

The templates set run sequence to 2, 3, 4, 5, 6. A run processes active rows in ascending run
sequence, which is the order the templates put them in:

- 2 — Child Account
- 3 — ECI E-AUTOMATE Customer
- 4 — ECI E-AUTOMATE Customer to account lookup
- 5 — ECI E-AUTOMATE Invoice header
- 6 — ECI E-AUTOMATE Invoice detail

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- customer account lookup feed reads customer sync output, account sync output
- customer feed reads account sync output
- invoice feed reads account sync output, customer sync output
- invoice line feed reads account sync output, customer sync output, invoice sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Child Account | Account | 8 | Commercient AR customer code column → Commercient AR customer code, Phone → Phone, Shipping street → Shipping street, Shipping city → Shipping city, Shipping state → Shipping state |
| ECI E-AUTOMATE Customer | ECi E-Automate Customer (custom object) | 20 | account lookup value → Account (custom field), Extension data → Extension data (custom field), Address 1 → Address 1 (custom field), Address 2 → Address 2 (custom field), Bill to customer number → Bill to customer number (custom field) |
| ECI E-AUTOMATE Customer to account lookup | Account | 2 | Commercient AR customer code column → Commercient AR customer code, ECi E-Automate Customer (custom object) → ECi E-Automate Customer (custom object) |
| ECI E-AUTOMATE Invoice header | ECi E-Automate Invoice Header (custom object) | 7 | Account → Account (custom field), ECi E-Automate Customer (custom object) → ECi E-Automate Customer (custom object), Extension data → Extension data (custom field), Customer number → Customer number (custom field), Internal invoice number → Internal invoice number (custom field) |
| ECI E-AUTOMATE Invoice detail | ECi E-Automate Invoice Detail (custom object) | 30 | Account → Account (custom field), ECi E-Automate Customer (custom object) → ECi E-Automate Customer (custom object), ECi E-Automate Invoice Header (custom object) → ECi E-Automate Invoice Header (custom object), Extension data → Extension data (custom field), Invoice detail identifier 2 → Invoice detail identifier 2 (custom field) |

## 6. Community templates

The catalogue carries 7 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 7
- Default operations: insert on 7, update on 7, delete on 7
- Marked as circular sync: 0
- Licence groups they span: 3
- Destination objects: Account and 4 custom objects
- Object display names: Account, Child Account and 5 further templates
- Template groups: Account, Invoice

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/eci-e-automate`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped ECi e-Automate → Salesforce templates set up. dlake-crmpro-salesforce is the destination
skill this page sits under: its own text is the authority for the Salesforce conventions that hold
across every ERP, and its ERP table lists this page alongside every sibling ERP page for this
destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/eci-e-automate`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
