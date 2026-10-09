---
name: dlake-crmpro-salesforce/erps/microsoft-dynamics-nav
kind: erp-summary
description: >-
  Use it when standing up or reading a Microsoft Dynamics NAV → Salesforce template set, when
  deciding which templates to import and activate, or when a run completes without pushing records
  and the answer is in the view or the configuration row. It extends dlake-crmpro, which covers
  operating CRMPro generally, and dlake-crmpro-salesforce, the destination skill this page is a
  child of, which carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Microsoft Dynamics NAV: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/microsoft-dynamics-nav` (or `list_skills`)
against the Commercient admin plane. Existing customers who need access or help: contact
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
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Contact, Commercient Dynamics NAV Customer Managed Custom Object | — |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Dynamics NAV Shipping Address (custom object), Commercient Dynamics NAV Shipment Headers Managed Custom Object, Commercient Dynamics NAV Shipment Lines Managed Custom Object | — |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Dynamics NAV Invoice Header Managed Custom Object, Commercient Dynamics NAV Invoice Lines Managed Custom Object | — |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Dynamics NAV Order Header Managed Custom Object, Commercient Dynamics NAV Order Lines Managed Custom Object | — |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Sync Account | Account | Commercient AR customer code | 1 |
| Sync Customer | Commercient Dynamics NAV Customer Managed Custom Object | Commercient external key (Dynamics NAV package) | 2 |
| Sync Customer TO Account Lookup | Account | Commercient AR customer code | 3 |
| Sync Ship TO Address | Commercient Dynamics NAV Shipping Address (custom object) | External key (custom field) | 4 |
| Sync Sales Order Header | Commercient Dynamics NAV Order Header Managed Custom Object | Commercient external key (Dynamics NAV package) | 6 |
| Sync Sales Order Detail | Commercient Dynamics NAV Order Lines Managed Custom Object | Commercient external key (Dynamics NAV package) | 7 |
| Sync invoice record sync output (generic name) Header | Commercient Dynamics NAV Invoice Header Managed Custom Object | Commercient external key (Dynamics NAV package) | 8 |
| Sync invoice record sync output (generic name) Detail | Commercient Dynamics NAV Invoice Lines Managed Custom Object | Commercient external key (Dynamics NAV package) | 9 |
| Sync Shipment Header | Commercient Dynamics NAV Shipment Headers Managed Custom Object | Commercient external key (Dynamics NAV package) | 10 |
| Sync Shipment Detail | Commercient Dynamics NAV Shipment Lines Managed Custom Object | Commercient external key (Dynamics NAV package) | 11 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| contact feed | insert + update | — |
| account feed | insert + update | — |
| customer feed | insert + update | — |
| customer account lookup feed | insert + update | — |
| shipping address feed | insert + update | — |
| sales order header feed | insert + update | — |
| sales order line feed | insert + update | — |
| invoice feed | insert + update | — |
| invoice line feed | insert + update | — |
| shipment header feed | insert + update | — |
| shipment line feed | insert + update | — |

## 4. Order of work

The templates set run sequence from 1 to 11. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 1 — Sync Account
- 2 — Sync Customer
- 3 — Sync Customer TO Account Lookup
- 4 — Sync Ship TO Address
- 6 — Sync Sales Order Header
- 7 — Sync Sales Order Detail
- 8 — Sync invoice record sync output (generic name) Header
- 9 — Sync invoice record sync output (generic name) Detail
- 10 — Sync Shipment Header
- 11 — Sync Shipment Detail

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- customer account lookup feed reads customer sync output
- contact feed reads account sync output, contact sync output; no template in this set writes
  contact sync output
- customer feed reads account sync output
- shipping address feed reads account sync output, customer sync output
- shipment header feed reads account sync output, customer sync output
- shipment line feed reads shipment header sync output
- invoice feed reads account sync output, customer sync output
- invoice line feed reads invoice sync output
- sales order header feed reads account sync output, customer sync output
- sales order line feed reads sales order header sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Sync Contact | Contact | 11 | account lookup → account lookup, Family name → Family name, Given name → Given name, Email → Email, Mailing street → Mailing street |
| Sync Account | Account | 12 | Commercient AR customer code column → Commercient AR customer code, Billing street → Billing street, Billing city → Billing city, Billing state → Billing state, Billing country → Billing country |
| Sync Customer | Commercient Dynamics NAV Customer Managed Custom Object | 54 | No → Commercient number, Name line 2 → Commercient name 2, Address → Commercient address, Address 2 → Commercient address line 2, City → Commercient city |
| Sync Customer TO Account Lookup | Account | 2 | Commercient AR customer code column → Commercient AR customer code, Dynamics NAV customers → Dynamics NAV customers (custom field) |
| Sync Ship TO Address | Commercient Dynamics NAV Shipping Address (custom object) | 27 | Customer number → Customer number (custom field), Code → Code (custom field), Name line 2 → Name 2 (custom field), Address → Address (custom field), Address 2 → Address 2 (custom field) |
| Sync Sales Order Header | Commercient Dynamics NAV Order Header Managed Custom Object | 59 | Document type → Document type, No → Commercient number, Sell to customer number → Sell to customer number, Bill to customer number → Bill to customer number, Bill to name → Commercient bill to name |
| Sync Sales Order Detail | Commercient Dynamics NAV Order Lines Managed Custom Object | 79 | Document type → Document type, Document number → Document number, Source line number → Commercient line number, Sell to customer number → Sell to customer number, Type → Commercient type |
| Sync invoice record sync output (generic name) Header | Commercient Dynamics NAV Invoice Header Managed Custom Object | 55 | No → Commercient number, Sell to customer number → Sell to customer number, Bill to customer number → Bill to customer number, Bill to name → Commercient bill to name, Bill to address → Commercient bill to address |
| Sync invoice record sync output (generic name) Detail | Commercient Dynamics NAV Invoice Lines Managed Custom Object | 52 | Document number → Document number, Source line number → Commercient line number, Sell to customer number → Sell to customer number, Type → Commercient type, No → Commercient number |
| Sync Shipment Header | Commercient Dynamics NAV Shipment Headers Managed Custom Object | 61 | No → Commercient number, Sell to customer number → Sell to customer number, Bill to customer number → Bill to customer number, Bill to name → Commercient bill to name, Bill to address → Commercient bill to address |
| Sync Shipment Detail | Commercient Dynamics NAV Shipment Lines Managed Custom Object | 58 | Document number → Document number, Source line number → Commercient line number, Sell to customer number → Sell to customer number, Type → Commercient type, No → Commercient number |

## 6. Community templates

The catalogue carries 134 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 134
- Default operations: insert on 134, update on 134, delete on 134
- Marked as circular sync: 5
- Licence groups they span: 14
- Destination objects: Account, Commercient Dynamics NAV Customer Managed Custom Object, Commercient
  Dynamics NAV Invoice Header Managed Custom Object, Commercient Dynamics NAV Order Header Managed
  Custom Object, Commercient Dynamics NAV Invoice Lines Managed Custom Object, Commercient Dynamics
  NAV Order Lines Managed Custom Object, Price book entry, Product, Contact, Commercient Dynamics
  NAV Address Managed Custom Object, Commercient Dynamics NAV Shipment Headers Managed Custom
  Object, Commercient Dynamics NAV Shipment Lines Managed Custom Object, Asset, Commercient Dynamics
  NAV Item Managed Custom Object, Price book object, Commercient Dynamics NAV Shipping Address
  (custom object), Dimension Value (custom object), Invoice Payment (custom object), Opportunity,
  Payment Terms (custom object), 8 more and 9 custom objects
- Object display names: Sync Account, Sync Customer, Sync Customer TO Account Lookup, Sync invoice
  record sync output (generic name) Detail, Sync invoice record sync output (generic name) Header,
  Sync Sales Order Detail, Sync Sales Order Header, Sync Ship TO Address, Sync Contact, Sync
  Shipment Detail, Sync Shipment Header, Product, 36 more and a further template
- Template groups: Account, Product, Customer Multi Ship Addresses, Invoice, Sales order, CRM Quote
  and Line, CRM Opportunity and Line, Opportunity, Quote line number

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/microsoft-dynamics-nav`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Microsoft Dynamics NAV → Salesforce templates set up. dlake-crmpro-salesforce is the
destination skill this page sits under: its own text is the authority for the Salesforce conventions
that hold across every ERP, and its ERP table lists this page alongside every sibling ERP page for
this destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/microsoft-dynamics-nav`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
