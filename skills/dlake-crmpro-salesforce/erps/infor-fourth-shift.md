---
name: dlake-crmpro-salesforce/erps/infor-fourth-shift
kind: erp-summary
description: >-
  Use it when standing up or reading an Infor Fourth Shift → Salesforce template set, when deciding
  which templates to import and activate, or when a run completes without pushing records and the
  answer is in the view or the configuration row. It extends dlake-crmpro, which covers operating
  CRMPro generally, and dlake-crmpro-salesforce, the destination skill this page is a child of,
  which carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Infor Fourth Shift: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/infor-fourth-shift` (or `list_skills`) against
the Commercient admin plane. Existing customers who need access or help: contact
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
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created and existing ones updated; none are deleted. | Account, Commercient Customer Master Managed Custom Object | customers, shipping locations |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created and existing ones updated; none are deleted. | Commercient Shipping Location Managed Custom Object | shipping locations, customers |
| **Product** | The templates push Commercient Item Master Managed Custom Object, Commercient Inventory Change Managed Custom Object, Product to Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Item Master Managed Custom Object, Commercient Inventory Change Managed Custom Object, Product | items, inventory changes |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created and existing ones updated; none are deleted. | Commercient Invoice Header Managed Custom Object, Commercient Invoice Line Managed Custom Object | customer order headers, customers, customer order lines, invoice headers, invoice lines |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created and existing ones updated; none are deleted. | Commercient Sales Order Header Managed Custom Object, Commercient Sales Order Line Managed Custom Object | customer order headers, customers, customer order lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Account | Account | Commercient AR customer code | 1 |
| Customer Master | Commercient Customer Master Managed Custom Object | Commercient external key (Infor package) | 2 |
| Customer to Account Reverse Lookup | Account | Commercient AR customer code | 3 |
| Item Master | Commercient Item Master Managed Custom Object | Commercient external key (Infor package) | 4 |
| Product | Product | Commercient external key (earlier package) | 5 |
| Product to Item Master Reverse Lookup | Commercient Item Master Managed Custom Object | Commercient external key (Infor package) | 6 |
| Sales Order Header | Commercient Sales Order Header Managed Custom Object | Commercient external key (Infor package) | 7 |
| Sales Order Line | Commercient Sales Order Line Managed Custom Object | Commercient external key (Infor package) | 8 |
| Invoice Header | Commercient Invoice Header Managed Custom Object | Commercient external key (Infor package) | 9 |
| Invoice Line | Commercient Invoice Line Managed Custom Object | Commercient external key (Infor package) | 10 |
| Warehouse | Commercient Inventory Change Managed Custom Object | Commercient external key (Infor package) | 11 |
| Shipping location | Commercient Shipping Location Managed Custom Object | Commercient external key (Infor package) | 12 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| account feed | insert + update | customers, shipping locations |
| customer feed | insert + update | customers |
| customer account lookup feed | insert + update | customers |
| item feed | insert + update | items |
| product feed | insert + update | items |
| product reverse lookup feed | insert + update | items |
| sales order feed | insert + update | customer order headers, customers |
| sales order line feed | insert + update | customer order lines |
| sales order feed | insert + update | customer order headers, customers, customer order lines, invoice headers |
| invoice line feed | insert + update | invoice lines |
| warehouse feed | insert + update | inventory changes |
| shipping location feed | insert + update | shipping locations, customers |

## 4. Order of work

The templates set run sequence from 1 to 12. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 1 — Account
- 2 — Customer Master
- 3 — Customer to Account Reverse Lookup
- 4 — Item Master
- 5 — Product
- 6 — Product to Item Master Reverse Lookup
- 7 — Sales Order Header
- 8 — Sales Order Line
- 9 — Invoice Header
- 10 — Invoice Line
- 11 — Warehouse
- 12 — Shipping location

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- customer account lookup feed reads customer sync output
- customer feed reads account sync output
- shipping location feed reads account sync output, customer sync output
- product reverse lookup feed reads product record sync output (generic name)
- sales order feed reads account sync output, customer sync output, sales order sync output, item
  sync output (generic name), sales order line sync output
- invoice line feed reads invoice sync output, item sync output (generic name)
- product feed reads item sync output (generic name)
- sales order feed reads account sync output, customer sync output
- sales order line feed reads sales order sync output, item sync output (generic name)

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Account | Account | 10 | Customer identifier → Commercient AR customer code, Customer name → Name, Bill to name → Billing street, Bill to address state → Billing state, Bill to postal code → Billing postal code |
| Customer Master | Commercient Customer Master Managed Custom Object | 28 | Customer key → Commercient external key (Infor package), Customer name → Name, Customer identifier → Customer identifier, Customer name → Customer name, Customer status → Customer status |
| Customer to Account Reverse Lookup | Account | 2 | Customer identifier → Commercient AR customer code, the linked Salesforce record → Commercient customer master (related record) |
| Item Master | Commercient Item Master Managed Custom Object | 34 | Item key → Commercient name, Item key → Commercient external key (Infor package), Item class 1 key → Commercient item class 1 key, Item class 2 key → Commercient item class 2 key, Item class 3 key → Commercient item class 3 key |
| Product | Product | 7 | Item key → Commercient external key (earlier package), Item key, Item number → Name, Item number → Product code, Item description text → Description, Item status → Active |
| Product to Item Master Reverse Lookup | Commercient Item Master Managed Custom Object | 2 | Item key → Commercient external key (Infor package), the linked Salesforce record → Commercient product (related record) |
| Sales Order Header | Commercient Sales Order Header Managed Custom Object | 4 | Customer order number → Name, Customer identifier → Account, Customer key → Commercient customer master (related record), Customer order header key → external key column |
| Sales Order Line | Commercient Sales Order Line Managed Custom Object | 4 | Customer order header key, Customer order line number → Name, Customer order line key → Commercient external key (Infor package), Customer order header key → Commercient sales order header (related record), Item key → Commercient item master (related record) |
| Invoice Header | Commercient Invoice Header Managed Custom Object | 98 | AR invoice header key → Commercient external key (Infor package), Customer identifier → Commercient account (related record), Customer key → Commercient customer master (related record), returned invoice number → Commercient name, Customer order number → Commercient customer order number |
| Invoice Line | Commercient Invoice Line Managed Custom Object | 3 | AR invoice header key,Invoice line number → Commercient external key (Infor package), the linked Salesforce record → Commercient item master (related record), the linked Salesforce record → Commercient invoice header (related record) |
| Warehouse | Commercient Inventory Change Managed Custom Object | 37 | Applied → Commercient applied, Applied date and time → Commercient applied date and time, User identifier → Commercient user identifier, Transaction date and time → Commercient transaction date and time, Document number → Commercient document number |
| Shipping location | Commercient Shipping Location Managed Custom Object | 11 | Shipping location key → Commercient external key (Infor package), Shipping location name → Name, the linked Salesforce record → Account, the linked Salesforce record → Commercient customer master (related record), Shipping location identifier → Shipping location identifier |

## 6. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/infor-fourth-shift`.

## 7. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Infor Fourth Shift → Salesforce templates set up. dlake-crmpro-salesforce is the
destination skill this page sits under: its own text is the authority for the Salesforce conventions
that hold across every ERP, and its ERP table lists this page alongside every sibling ERP page for
this destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/infor-fourth-shift`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
