---
name: dlake-crmpro-salesforce/erps/sage-mas-90
kind: erp-summary
description: >-
  Use it when standing up or reading a Sage MAS 90 → Salesforce template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-salesforce, the destination skill this page is a child of, which
  carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Sage MAS 90: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/sage-mas-90` (or `list_skills`) against the
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
| **GET USER** | The templates push users to Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | users | — |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | account, Commercient AR Customer Managed Custom Object, Commercient Salesperson Managed Custom Object | customers, salespeople |
| **AR Invoice Payments** | Commercient Syncs the payment details that are held in the receivables file and held against an invoice in the ERP. The amount received from a customer, the date of payment, the method of payment, whether partial or full payment, etc New records are created and existing ones updated; none are deleted. | Commercient Payment History Managed Custom Object | payment history, invoice history headers |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created and existing ones updated; none are deleted. | Commercient Ship-To Address Managed Custom Object | shipping addresses |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created and existing ones updated; none are deleted. | Commercient Invoice History Header Managed Custom Object, Commercient Invoice History Detail Managed Custom Object | invoice history headers, open invoices, salespeople, invoice history lines |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created and existing ones updated; none are deleted. | Commercient Sales Order Header Managed Custom Object, Commercient Sales Order Line Managed Custom Object | sales order headers, sales order lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| GET USER | users | — | 0 |
| Sync Salesperson | Commercient Salesperson Managed Custom Object | Commercient external key | 1 |
| CRM Account | account | Commercient AR customer code | 2 |
| Sage Customer | Commercient AR Customer Managed Custom Object | Commercient external key | 3 |
| Sage Ship To Address | Commercient Ship-To Address Managed Custom Object | Commercient external key | 5 |
| Sage Sales order Header | Commercient Sales Order Header Managed Custom Object | Commercient sales order number | 12 |
| Sage Sales order Detail | Commercient Sales Order Line Managed Custom Object | Commercient external key | 13 |
| Sage Invoice History Header | Commercient Invoice History Header Managed Custom Object | Commercient external key | 14 |
| Sage Invoice History Detail | Commercient Invoice History Detail Managed Custom Object | Commercient external key | 15 |
| Sage Transaction Payment History | Commercient Payment History Managed Custom Object | Commercient external key | 16 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| salesperson feed | insert + update | salespeople |
| CRM account feed | insert + update | customers |
| customer feed | insert + update | customers |
| shipping address feed | insert + update | shipping addresses |
| sales order feed | insert + update | sales order headers |
| sales order line feed | insert + update | sales order lines |
| invoice history feed | insert + update | invoice history headers, open invoices, salespeople |
| invoice history line feed | insert + update | invoice history lines |
| payment history feed | insert + update | payment history, invoice history headers |

## 4. Order of work

The templates set run sequence from 0 to 16. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 0 — GET USER
- 1 — Sync Salesperson
- 2 — CRM Account
- 3 — Sage Customer
- 5 — Sage Ship To Address
- 12 — Sage Sales order Header
- 13 — Sage Sales order Detail
- 14 — Sage Invoice History Header
- 15 — Sage Invoice History Detail
- 16 — Sage Transaction Payment History

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- payment history feed reads CRM account sync output, customer sync output, invoice history sync
  output
- customer feed reads CRM account sync output
- shipping address feed reads CRM account sync output, customer sync output
- invoice history feed reads CRM account sync output, customer sync output
- invoice history line feed reads invoice history sync output
- sales order feed reads CRM account sync output, customer sync output
- sales order line feed reads sales order sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Sync Salesperson | Commercient Salesperson Managed Custom Object | 26 | Sales manager (custom field) → Commercient sales manager, Salesperson division → Commercient salesperson division, Salesperson number → Commercient salesperson number, Salesperson name → Commercient salesperson name, Address line 1 → Commercient address line 1 |
| CRM Account | account | 12 | AR division number, Customer number → Commercient AR customer code, Customer name, Customer number → Name, Address line 1, Address line 2, Address line 3 → Billing street, City → Billing city, State → Billing state |
| Sage Customer | Commercient AR Customer Managed Custom Object | 75 | AR division number,Customer number → Commercient external key, Customer name → Commercient name, AR division number → Commercient AR division number, Customer number → Commercient customer number, Address line 1 → Commercient address line 1 |
| Sage Ship To Address | Commercient Ship-To Address Managed Custom Object | 33 | AR division number, Customer number, Ship-to code → Commercient external key, AR division number → Commercient AR division number, Customer number → Commercient customer number, Ship-to code → Commercient ship to code, Shipping name → Commercient shipping name |
| Sage Sales order Header | Commercient Sales Order Header Managed Custom Object | 132 | Sales order number → Commercient sales order number, Order date → Commercient order date, Order type → Commercient order type, Order status → Commercient order status, Master repeating order number → Commercient master repeating order number |
| Sage Sales order Detail | Commercient Sales Order Line Managed Custom Object | 64 | Sales order number, Line key → Commercient external key, Sales order number → Commercient sales order number, Line key → Commercient line key, Line sequence number → Commercient line sequence number, Item code → Commercient item code |
| Sage Invoice History Header | Commercient Invoice History Header Managed Custom Object | 119 | Invoice number, Header sequence number → Commercient external key, Invoice number → Commercient invoice number, Header sequence number → Commercient header sequence number, Module code → Commercient module code, Invoice type → Commercient invoice type |
| Sage Invoice History Detail | Commercient Invoice History Detail Managed Custom Object | 57 | Invoice number, Header sequence number, Detail sequence number → Commercient external key, Invoice number → Commercient invoice number, Header sequence number → Commercient header sequence number, Detail sequence number → Commercient detail sequence number, Item code → Commercient item code |
| Sage Transaction Payment History | Commercient Payment History Managed Custom Object | 30 | AR division number → Commercient AR division number, Customer number → Commercient customer number, Invoice number → Commercient invoice number, Invoice type → Commercient invoice type, Invoice header sequence number → Commercient invoice header sequence number |

## 6. Community templates

The catalogue carries 50 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 50
- Default operations: insert on 50, update on 50, delete on 50
- Marked as circular sync: 0
- Licence groups they span: 12
- Object display names: AR customer, CI Item to Vendor lookup, Get Account, Get Case, Get sales
  order header, Invoice Details, Invoice Header, Invoice Tracking History, Price book entry Create,
  Price book entry Update, Product Reverse Lookup, Purchase Order, 29 more and 9 further templates
- Template groups: Product, Sales order, Account, Purchase Order, Customer Multi Ship Addresses,
  Invoice, Invoice History Headers

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/sage-mas-90`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Sage MAS 90 → Salesforce templates set up. dlake-crmpro-salesforce is the destination
skill this page sits under: its own text is the authority for the Salesforce conventions that hold
across every ERP, and its ERP table lists this page alongside every sibling ERP page for this
destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/sage-mas-90`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
