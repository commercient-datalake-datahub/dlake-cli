---
name: dlake-crmpro-salesforce/erps/infor-ln-6
kind: erp-summary
description: >-
  Use it when standing up or reading an Infor LN 6 → Salesforce template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-salesforce, the destination skill this page is a child of, which
  carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Infor LN 6: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/infor-ln-6` (or `list_skills`) against the
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
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Commercient Baan Customer Managed Custom Object, Commercient Baan Salesperson Managed Custom Object | business partner records, billing business partner records, addresses, countries |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Baan Customer Address Managed Custom Object | addresses, business partner records |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Baan Invoice Header Managed Custom Object, Commercient Baan Invoice Detail Managed Custom Object, Commercient Baan Invoice Payment Managed Custom Object | sales invoices, sales invoice lines |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Baan Sales Order Header Managed Custom Object, Commercient Baan Sales Order Detail Managed Custom Object | sales orders, sales order lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Sales Person Sync | Commercient Baan Salesperson Managed Custom Object | External key (custom field) | 2 |
| Account Sync | Account | Commercient AR customer code | 4 |
| Customer Sync | Commercient Baan Customer Managed Custom Object | Commercient external key (Baan package) | 6 |
| Sales order Header Sync | Commercient Baan Sales Order Header Managed Custom Object | Commercient external key (Baan package) | 7 |
| Sales order Line Sync | Commercient Baan Sales Order Detail Managed Custom Object | Commercient external key (Baan package) | 9 |
| Invoice Header Sync | Commercient Baan Invoice Header Managed Custom Object | Commercient external key (Baan package) | 10 |
| Invoice Line Sync | Commercient Baan Invoice Detail Managed Custom Object | Commercient external key (Baan package) | 11 |
| Invoice Payment Sync | Commercient Baan Invoice Payment Managed Custom Object | Commercient external key 1 (Baan package) | 12 |
| Address Sync | Commercient Baan Customer Address Managed Custom Object | Commercient external key (Baan package) | 15 |
| Account To Customer Reverse Lookup | Account | Commercient AR customer code | 21 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| salesperson feed | insert + update | employee records |
| account feed | — | business partner records, billing business partner records, addresses |
| customer feed | insert + update | business partner records |
| sales order feed | insert + update | sales orders |
| sales order line feed | insert + update | sales order lines |
| invoice feed | insert + update | sales invoices |
| invoice line feed | insert + update | sales invoice lines |
| invoice payment feed | insert + update | sales invoices, sales invoice lines |
| address feed | insert + update | addresses, business partner records |
| account reverse lookup feed | insert + update | business partner records |

## 4. Order of work

The templates set run sequence from 2 to 21. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 2 — Sales Person Sync
- 4 — Account Sync
- 6 — Customer Sync
- 7 — Sales order Header Sync
- 9 — Sales order Line Sync
- 10 — Invoice Header Sync
- 11 — Invoice Line Sync
- 12 — Invoice Payment Sync
- 15 — Address Sync
- 21 — Account To Customer Reverse Lookup

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- account reverse lookup feed reads account sync output, customer sync output
- customer feed reads account sync output
- address feed reads account sync output, customer sync output
- invoice feed reads account sync output, customer sync output
- invoice line feed reads retrieved opportunity sync output, invoice sync output; no template in
  this set writes retrieved opportunity sync output
- invoice payment feed reads account sync output, customer sync output, invoice sync output
- sales order feed reads account sync output, customer sync output
- sales order line feed reads sales order sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Sales Person Sync | Commercient Baan Salesperson Managed Custom Object | 12 | →, →, Department code → Department code, Baan employee number → Commercient employee number, Baan name 2 → |
| Account Sync | Account | 20 | Commercient AR customer code column → Commercient AR customer code, Shipping street → Shipping street, Shipping city → Shipping city, Shipping state → Shipping state, Shipping country → Shipping country |
| Customer Sync | Commercient Baan Customer Managed Custom Object | 34 | Address code → Address code, →, →, Business partner text → Commercient business partner text, Business partner status → Commercient business partner status |
| Sales order Header Sync | Commercient Baan Sales Order Header Managed Custom Object | 59 | Account → Account, Customer → Commercient Customer Managed Custom Object, →, Area code → Area code, Business partner text → Commercient business partner text |
| Sales order Line Sync | Commercient Baan Sales Order Detail Managed Custom Object | 57 | Sales order header (related record) → Commercient sales order header (related record), Order amount → Commercient order amount, Discount amount → Commercient discount amount, →, → |
| Invoice Header Sync | Commercient Baan Invoice Header Managed Custom Object | 58 | →, Amount in home currency 1 → Commercient amount in home currency 1, Amount in home currency 2 → Commercient amount in home currency 2, Amount in home currency 3 → Commercient amount in home currency 3, Amount in invoice currency → Commercient amount in invoice currency |
| Invoice Line Sync | Commercient Baan Invoice Detail Managed Custom Object | 44 | Amount in home currency 1 → Commercient amount in home currency 1, Amount in home currency 2 → Commercient amount in home currency 2, Amount in home currency 3 → Commercient amount in home currency 3, Amount in invoice currency → Commercient amount in invoice currency, → |
| Invoice Payment Sync | Commercient Baan Invoice Payment Managed Custom Object | 41 | external key 1 column → Commercient external key 1 (Baan package), Amount in home currency 1 → Commercient amount in home currency 1, Amount in invoice currency → Commercient amount in invoice currency, Amount in home currency 2 → Commercient amount in home currency 2, Amount in home currency 3 → Commercient amount in home currency 3 |
| Address Sync | Commercient Baan Customer Address Managed Custom Object | 45 | Account → Account, Customer → Commercient Customer Managed Custom Object, Address code → Address code, Baan address line 1 → Baan address line 1, Baan address line 2 → |
| Account To Customer Reverse Lookup | Account | 2 | Commercient AR customer code column → Commercient AR customer code, Commercient Customer Managed Custom Object → Commercient Customer Managed Custom Object |

## 6. Community templates

The catalogue carries 21 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 21
- Default operations: insert on 21, update on 21, delete on 21
- Marked as circular sync: 0
- Licence groups they span: 11
- Destination objects: Account, Baan Division Code (custom object), Baan Opportunity Header (custom
  object), Baan Opportunity Line (custom object), Baan Order Shipping (custom object), Commercient
  Baan Customer Managed Custom Object, Commercient Baan Customer Address Managed Custom Object,
  Commercient Baan Invoice Detail Managed Custom Object, Commercient Baan Invoice Header Managed
  Custom Object, Commercient Baan Invoice Payment Managed Custom Object, Commercient Baan Payment
  Term Managed Custom Object, Commercient Baan Sales Order Detail Managed Custom Object, Commercient
  Baan Sales Order Header Managed Custom Object, Commercient Baan Salesperson Managed Custom Object,
  Content document, Opportunity, Quote History (custom object) and a custom object
- Object display names: Account Sync, Account To Customer Reverse Lookup, Address Sync, Baan
  Opportunity header Sync, Baan Opportunity line Sync, Baan Division Code (custom object), Baan
  Order Shipping (custom object), Customer Sync, Get Account, Get Opportunity, Invoice Header Sync,
  Invoice Line Sync, 8 more and a further template
- Template groups: Account, Customer Multi Ship Addresses, Invoice, Opportunity, Sales order, CRM
  Opportunity and Line

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/infor-ln-6`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Infor LN 6 → Salesforce templates set up. dlake-crmpro-salesforce is the destination
skill this page sits under: its own text is the authority for the Salesforce conventions that hold
across every ERP, and its ERP table lists this page alongside every sibling ERP page for this
destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/infor-ln-6`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
