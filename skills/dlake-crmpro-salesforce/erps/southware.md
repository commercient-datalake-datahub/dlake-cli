---
name: dlake-crmpro-salesforce/erps/southware
kind: erp-summary
description: >-
  Use it when standing up or reading a SouthWare → Salesforce template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-salesforce, the destination skill this page is a child of, which
  carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — SouthWare: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/southware` (or `list_skills`) against the
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
| **Get Salesforce Users** | The templates push User to Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | User | — |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Commercient Customer Managed Custom Object, Commercient Salesperson Managed Custom Object | customers, shipping addresses, salespeople |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Shipping Address Managed Custom Object | shipping addresses |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Invoice Header Managed Custom Object, Commercient Invoice Line Managed Custom Object, Commercient Invoice Payment Managed Custom Object | base sales history headers, base sales history lines, sales history payments, sales history headers |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Sales Order Header Managed Custom Object, Commercient Sales Order Line Managed Custom Object | sales order headers, sales order lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Get Salesforce Users | User | Commercient salesperson code | 0 |
| SouthWare Salesperson Sync | Commercient Salesperson Managed Custom Object | Commercient external key (SouthWare package) | 1 |
| SouthWare Customer Sync | Commercient Customer Managed Custom Object | Commercient external key (SouthWare package) | 3 |
| Account Reverse Lookup Sync | Account | Commercient AR customer code | 4 |
| SouthWare Ship To Address Sync | Commercient Shipping Address Managed Custom Object | Commercient external key (SouthWare package) | 4 |
| SouthWare Sales order Header Sync | Commercient Sales Order Header Managed Custom Object | Commercient external key (SouthWare package) | 5 |
| SouthWare Sales order Line Sync | Commercient Sales Order Line Managed Custom Object | Commercient external key (SouthWare package) | 6 |
| SouthWare Invoice Header Sync | Commercient Invoice Header Managed Custom Object | Commercient external key (SouthWare package) | 7 |
| SouthWare Invoice Line Sync | Commercient Invoice Line Managed Custom Object | Commercient external key (SouthWare package) | 8 |
| SouthWare Invoice Payment Sync | Commercient Invoice Payment Managed Custom Object | Commercient external key (SouthWare package) | 9 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| account feed | insert + update | customers, shipping addresses |
| salesperson feed | insert + update | salespeople |
| customer feed | insert + update | customers |
| account reverse lookup feed | insert + update | customers |
| shipping address feed | insert + update | shipping addresses |
| sales order feed | insert + update | sales order headers |
| sales order line feed | insert + update | sales order lines |
| invoice feed | insert + update | base sales history headers |
| invoice line feed | insert + update | base sales history lines |
| invoice payment feed | insert + update | sales history payments, sales history headers |

## 4. Order of work

The templates set run sequence from 0 to 9. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 0 — Get Salesforce Users
- 1 — SouthWare Salesperson Sync
- 3 — SouthWare Customer Sync
- 4 — Account Reverse Lookup Sync, SouthWare Ship To Address Sync
- 5 — SouthWare Sales order Header Sync
- 6 — SouthWare Sales order Line Sync
- 7 — SouthWare Invoice Header Sync
- 8 — SouthWare Invoice Line Sync
- 9 — SouthWare Invoice Payment Sync

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- account feed reads salesperson sync output, retrieved user sync output, account sync output; no
  template in this set writes account sync output
- account reverse lookup feed reads customer sync output, account sync output; no template in this
  set writes account sync output
- customer feed reads salesperson sync output, account sync output; no template in this set writes
  account sync output
- shipping address feed reads salesperson sync output, account sync output, customer sync output; no
  template in this set writes account sync output
- invoice feed reads account sync output, customer sync output; no template in this set writes
  account sync output
- invoice line feed reads invoice sync output
- invoice payment feed reads account sync output, customer sync output, invoice sync output; no
  template in this set writes account sync output
- sales order feed reads account sync output, customer sync output; no template in this set writes
  account sync output
- sales order line feed reads sales order sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Account Sync | Account | 15 | Commercient AR customer code column → Commercient AR customer code, Phone → Phone, Type → Type, Billing street → Billing street, Billing city → Billing city |
| SouthWare Salesperson Sync | Commercient Salesperson Managed Custom Object | 41 | Salesperson number → Commercient salesperson number, Salesperson name → Commercient salesperson name, Salesperson initials → Commercient salesperson initials, territory → Commercient territory, Commission sales period to date → Commercient commission sales period to date |
| SouthWare Customer Sync | Commercient Customer Managed Custom Object | 76 | Group number → Commercient group number, customer number → Commercient customer number, Salesperson number → Commercient salesperson number, Customer name → Commercient customer name, Address line 1 → Commercient address line 1 |
| Account Reverse Lookup Sync | Account | 2 | Commercient AR customer code column → Commercient AR customer code, Commercient SouthWare customer (related record) → Commercient SouthWare customer (related record) |
| SouthWare Ship To Address Sync | Commercient Shipping Address Managed Custom Object | 28 | customer number → Commercient customer number, Shipping address number → Commercient shipping address number, Shipping name → Commercient shipping name, Ship to address line 1 → Commercient shipping address line 1, Ship to address line 2 → Commercient shipping address line 2 |
| SouthWare Sales order Header Sync | Commercient Sales Order Header Managed Custom Object | 93 | Account → Commercient account (related record), SouthWare customer record (custom field) → Commercient SouthWare customer record (related record), Amount 1 → Commercient amount 1, Applied deposit override → Commercient applied deposit override, AR account → Commercient AR account |
| SouthWare Sales order Line Sync | Commercient Sales Order Line Managed Custom Object | 86 | Order number field → Commercient order number, Line number → Commercient line number, Item type code → Commercient item type code, Stock or service item identifier → Commercient item identifier (stock or service), Location number → Commercient location number |
| SouthWare Invoice Header Sync | Commercient Invoice Header Managed Custom Object | 106 | Account → Commercient account (related record), Customer record → Commercient Customer Managed Custom Object, Accounts receivable account number → Commercient accounts receivable account number, Apply to number → Commercient apply to number, Bill to address line 3 → Commercient bill to address line 3 |
| SouthWare Invoice Line Sync | Commercient Invoice Line Managed Custom Object | 72 | Invoice number → Commercient invoice number, Line number → Commercient line number, Invoice date → Commercient invoice date, customer number → Commercient customer number, Line item code → Commercient line item code |
| SouthWare Invoice Payment Sync | Commercient Invoice Payment Managed Custom Object | 40 | returned invoice number → returned invoice number, Sequence → Sequence, Received date → Received date, Operator → Commercient operator, Location → Location |

## 6. Community templates

The catalogue carries 31 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 31
- Default operations: insert on 31, update on 31, delete on 31
- Marked as circular sync: 0
- Licence groups they span: 11
- Destination objects: Account, Commercient Customer Managed Custom Object, Commercient Shipping
  Address Managed Custom Object, Commercient Salesperson Managed Custom Object, Commercient Sales
  Order Header Managed Custom Object, Commercient Sales Order Line Managed Custom Object,
  Commercient Invoice Header Managed Custom Object, Commercient Invoice Line Managed Custom Object,
  Opportunity, Price book entry, User, Commercient Invoice Payment Managed Custom Object, Contact,
  Content document, Product and 2 custom objects
- Object display names: Account Reverse Lookup Sync, Account Sync, Get Salesforce Users, SouthWare
  Customer Sync, SouthWare Invoice Header Sync, SouthWare Invoice Line Sync, SouthWare Sales order
  Header Sync, SouthWare Sales order Line Sync, SouthWare Salesperson Sync, SouthWare Ship To
  Address Sync, Account Sync Update, Opportunity Sync and 9 more
- Template groups: Account, Invoice, Product, Sales order, CRM Opportunity and Line, Customer Multi
  Ship Addresses

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/southware`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped SouthWare → Salesforce templates set up. dlake-crmpro-salesforce is the destination
skill this page sits under: its own text is the authority for the Salesforce conventions that hold
across every ERP, and its ERP table lists this page alongside every sibling ERP page for this
destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/southware`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
