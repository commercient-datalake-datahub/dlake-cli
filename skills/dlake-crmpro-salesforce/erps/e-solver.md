---
name: dlake-crmpro-salesforce/erps/e-solver
kind: erp-summary
description: >-
  Use it when standing up or reading an E-solver → Salesforce template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-salesforce, the destination skill this page is a child of, which
  carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — E-solver: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/e-solver` (or `list_skills`) against the
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
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Commercient Esolver Customer Managed Custom Object, Commercient Esolver Salesperson Managed Custom Object | customer and supplier master records, customers and suppliers, address master records, customer and supplier commercial terms, user field values, account view feed |
| **CRM Ownership** | The templates push User to Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | User | — |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Esolver Ship To Address Managed Custom Object | address master records, customers and suppliers, customer and supplier address details |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Esolver Invoice Header Managed Custom Object, Commercient Esolver Invoice Detail Managed Custom Object | general document list, document headers, document footers, sales invoice lines |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Esolver Sales Order Header Managed Custom Object, Commercient Esolver Sales Order Detail Managed Custom Object | general document list, document headers, document footers, customer order lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Get User | User | — | 0 |
| Esolver salesperson | Commercient Esolver Salesperson Managed Custom Object | External key (custom field) | 1 |
| Account | Account | Commercient AR customer code | 2 |
| CRM Account child | account | Commercient AR customer code | 3 |
| Esolver customer | Commercient Esolver Customer Managed Custom Object | External key (custom field) | 3 |
| Account customer lookup | Account | Commercient AR customer code | 4 |
| Esolver address | Commercient Esolver Ship To Address Managed Custom Object | External key (custom field) | 5 |
| Esolver sales Order | Commercient Esolver Sales Order Header Managed Custom Object | External key (custom field) | 6 |
| Esolver sales Order line number | Commercient Esolver Sales Order Detail Managed Custom Object | External key (custom field) | 7 |
| Esolver invoice Header | Commercient Esolver Invoice Header Managed Custom Object | External key (custom field) | 8 |
| Esolver invoice Details | Commercient Esolver Invoice Detail Managed Custom Object | External key (custom field) | 9 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| salesperson feed | insert + update | customers and suppliers, customer and supplier master records, address master records |
| account feed | — | customer and supplier master records, customers and suppliers, address master records, customer and supplier commercial terms, user field values |
| child account feed | insert + update | account view feed |
| customer feed | insert only | customer and supplier master records, customers and suppliers, address master records, customer and supplier commercial terms, user field values |
| customer account lookup feed | insert only | customer and supplier master records, customers and suppliers, address master records, customer and supplier commercial terms, user field values |
| master data address feed | insert only | address master records, customers and suppliers, customer and supplier address details |
| sales order feed | insert + update | general document list, document headers, document footers |
| sales order line feed | insert + update | general document list, document headers, document footers, customer order lines |
| invoice feed | insert + update | general document list, document headers, document footers |
| invoice line feed | insert + update | general document list, document headers, document footers, sales invoice lines |

## 4. Order of work

The templates set run sequence from 0 to 9. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 0 — Get User
- 1 — Esolver salesperson
- 2 — Account
- 3 — CRM Account child, Esolver customer
- 4 — Account customer lookup
- 5 — Esolver address
- 6 — Esolver sales Order
- 7 — Esolver sales Order line number
- 8 — Esolver invoice Header
- 9 — Esolver invoice Details

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- account feed reads salesperson sync output, user sync output
- child account feed reads account sync output, salesperson sync output, user sync output
- customer account lookup feed reads customer sync output
- customer feed reads account sync output
- master data address feed reads account sync output, customer sync output
- invoice feed reads account sync output, customer sync output
- invoice line feed reads invoice sync output
- sales order feed reads account sync output, customer sync output
- sales order line feed reads sales order sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Esolver salesperson | Commercient Esolver Salesperson Managed Custom Object | 16 | Database group → Database group (custom field), Master data type → Master data type (custom field), Customer or supplier code → Customer or supplier code (custom field), Agent master record → Agent master record (custom field), Progressive number → Progressive number (custom field) |
| Account | Account | 30 | Commercient AR customer code column → Commercient AR customer code, Account number → Account number (custom field), Sales agent → Sales agent (custom field), Active in Esolver → Active in Esolver (custom field), Email → Email (custom field) |
| CRM Account child | account | 19 | Commercient AR customer code column → Commercient AR customer code, parent account lookup → parent account lookup, Phone → Phone, Billing street → Billing street, Billing city → Billing city |
| Esolver customer | Commercient Esolver Customer Managed Custom Object | 18 | account lookup value → Account (custom field), Database group → Database group (custom field), Value field 16 → Value field 16 (custom field), Billing name 1 → Billing name 1 (custom field), Phone number → Phone number (custom field) |
| Account customer lookup | Account | 2 | Commercient AR customer code column → Commercient AR customer code, Esolver customer → Esolver customer (custom field) |
| Esolver address | Commercient Esolver Ship To Address Managed Custom Object | 44 | Customer master data identifier → Customer master data identifier (custom field), Progressive number → Progressive number (custom field), Transport lead time → Transport lead time (custom field), Alternative code → Alternative code (custom field), Reference group → Reference group (custom field) |
| Esolver sales Order | Commercient Esolver Sales Order Header Managed Custom Object | 28 | Document class → Document class (custom field), Document group → Document group (custom field), Registration code → Registration code (custom field), Registration date → Registration date (custom field), Registration number (order header) → Registration number (custom field, order header) |
| Esolver sales Order line number | Commercient Esolver Sales Order Detail Managed Custom Object | 42 | Document group → Document group (custom field), Registration date → Registration date (custom field), Registration number (order line) → Registration number (custom field, order line), Customer or supplier code → Customer or supplier code (custom field), Document total in accounting currency → Document total in accounting currency (custom field) |
| Esolver invoice Header | Commercient Esolver Invoice Header Managed Custom Object | 28 | Document class → Document class (custom field), Document group → Document group (custom field), Registration code → Registration code (custom field), Registration date → Registration date (custom field), Registration number (order header) → Registration number (custom field, order header) |
| Esolver invoice Details | Commercient Esolver Invoice Detail Managed Custom Object | 44 | Document class → Document class (custom field), Registration code → Registration code (custom field), Document group → Document group (custom field), Registration date → Registration date (custom field), Registration number (order line) → Registration number (custom field, order line) |

## 6. Community templates

The catalogue carries 12 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 12
- Default operations: insert on 12, update on 12, delete on 12
- Marked as circular sync: 0
- Licence groups they span: 7
- Destination objects: Account, account, Commercient Esolver Customer Managed Custom Object,
  Commercient Esolver Invoice Detail Managed Custom Object, Commercient Esolver Invoice Header
  Managed Custom Object, Commercient Esolver Sales Order Detail Managed Custom Object, Commercient
  Esolver Sales Order Header Managed Custom Object, Commercient Esolver Salesperson Managed Custom
  Object, Commercient Esolver Ship To Address Managed Custom Object, Record type and a custom object
- Object display names: Account, Account customer lookup, CRM Account child, Esolver Invoice credit
  note, Esolver address, Esolver customer, Esolver invoice Details, Esolver invoice Header, Esolver
  sales Order, Esolver sales Order line number, Esolver salesperson, Get Record type
- Template groups: Account, Invoice, Sales order, Customer Multi Ship Addresses

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/e-solver`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped E-solver → Salesforce templates set up. dlake-crmpro-salesforce is the destination skill
this page sits under: its own text is the authority for the Salesforce conventions that hold across
every ERP, and its ERP table lists this page alongside every sibling ERP page for this destination.
For the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/e-solver`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
