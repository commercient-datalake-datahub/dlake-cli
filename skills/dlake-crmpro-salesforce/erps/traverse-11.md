---
name: dlake-crmpro-salesforce/erps/traverse-11
kind: erp-summary
description: >-
  Use it when standing up or reading a Traverse 11 → Salesforce template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-salesforce, the destination skill this page is a child of, which
  carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Traverse 11: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/traverse-11` (or `list_skills`) against the
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
| **Get Users** | The templates push users to Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | users | — |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created and existing ones updated; none are deleted. | Account, Commercient AR Customer Managed Custom Object, Commercient AR Salesperson Managed Custom Object | customers, customer shipping addresses, sales reps |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created and existing ones updated; none are deleted. | Commercient AR Shipping Address Managed Custom Object | customer shipping addresses, customers |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created and existing ones updated; none are deleted. | Commercient AR Open Invoice Managed Custom Object | open AR invoices |
| **Invoice History Headers** | Invoice line item details are synced in their entirety to the Commercient Invoice Details MCO or the Invoice History Details MCO based on your ERP system functionality. New records are created and existing ones updated; none are deleted. | Commercient AR History Detail Managed Custom Object, Commercient AR History Header Managed Custom Object | AR history lines, AR history headers, customers |
| **Opportunity** | The templates push Commercient Quote Header Managed Custom Object to Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Quote Header Managed Custom Object | process configuration, sales order transaction headers, customers, sales order transaction lines (Traverse 11 table), sales order transaction headers (Traverse 11 table) |
| **Quote line number** | The templates push Commercient Quote Detail Managed Custom Object to Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Quote Detail Managed Custom Object | sales order transaction lines, sales order transaction headers |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created and existing ones updated; none are deleted. | Commercient Sales Order Transaction Header Managed Custom Object, Commercient Sales Order Transaction Detail Managed Custom Object | sales order transaction headers (Traverse 11 table), customers, sales order transaction lines (Traverse 11 table) |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| GET USER | users | — | 0 |
| Traverse Account | Account | Commercient AR customer code | 1 |
| Traverse Salesperson | Commercient AR Salesperson Managed Custom Object | External key (custom field) | 1 |
| Traverse Customer | Commercient AR Customer Managed Custom Object | Commercient customer identifier | 2 |
| Traverse Customer Reverse Lookup | Account | Commercient AR customer code | 3 |
| Traverse Shipping address | Commercient AR Shipping Address Managed Custom Object | Commercient external key (package 7) | 4 |
| Traverse Sales order transaction Header | Commercient Sales Order Transaction Header Managed Custom Object | Commercient transaction identifier | 5 |
| Traverse Sales order transaction Details | Commercient Sales Order Transaction Detail Managed Custom Object | Commercient external key (package 7) | 6 |
| Traverse AR open invoice | Commercient AR Open Invoice Managed Custom Object | Commercient counter | 7 |
| Traverse AR history Header | Commercient AR History Header Managed Custom Object | Commercient external key (package 7) | 8 |
| Traverse AR history Details | Commercient AR History Detail Managed Custom Object | Commercient external key (package 7) | 9 |
| Traverse Quote Header | Commercient Quote Header Managed Custom Object | External key (custom field) | 11 |
| Traverse Quote Details | Commercient Quote Detail Managed Custom Object | External key (custom field) | 12 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| account feed | insert + update | customers, customer shipping addresses |
| salesperson feed | insert + update | sales reps |
| customer feed | insert + update | customers |
| customer account lookup feed | insert + update | customers |
| shipping address feed | insert + update | customer shipping addresses, customers |
| sales order transaction header feed | insert + update | sales order transaction headers (Traverse 11 table), customers |
| sales order transaction detail feed | insert + update | sales order transaction lines (Traverse 11 table), sales order transaction headers (Traverse 11 table) |
| AR open invoice feed | insert + update | open AR invoices |
| AR history header feed | insert + update | AR history headers, customers |
| AR history detail feed | insert + update | AR history lines, AR history headers |
| quote header feed | insert + update | process configuration, sales order transaction headers, customers, sales order transaction lines (Traverse 11 table) |
| quote line feed | insert + update | sales order transaction lines, sales order transaction headers |

## 4. Order of work

The templates set run sequence from 0 to 12. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 0 — GET USER
- 1 — Traverse Account, Traverse Salesperson
- 2 — Traverse Customer
- 3 — Traverse Customer Reverse Lookup
- 4 — Traverse Shipping address
- 5 — Traverse Sales order transaction Header
- 6 — Traverse Sales order transaction Details
- 7 — Traverse AR open invoice
- 8 — Traverse AR history Header
- 9 — Traverse AR history Details
- 11 — Traverse Quote Header
- 12 — Traverse Quote Details

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- account feed reads user sync output, salesperson sync output
- customer account lookup feed reads customer sync output
- customer feed reads account sync output
- shipping address feed reads account sync output, customer sync output
- AR open invoice feed reads account sync output; no template in this set writes
- AR history detail feed reads AR history header sync output, AR history header sync output (generic
  name); no template in this set writes AR history header sync output (generic name)
- AR history header feed reads account sync output, customer sync output, sales order transaction
  header sync output
- quote header feed reads account sync output, customer sync output, sales order transaction header
  sync output, sales order transaction detail sync output; no template in this set writes
- quote line feed reads quote header sync output
- sales order transaction header feed reads account sync output, customer sync output
- sales order transaction detail feed reads sales order transaction header sync output; no template
  in this set writes

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Traverse Account | Account | 14 | Customer identifier → Commercient AR customer code, Customer name → Name, Address 1,Address 2 → Billing street, City → Billing city, Region → Billing state |
| Traverse Salesperson | Commercient AR Salesperson Managed Custom Object | 29 | Sales rep identifier → External key (custom field), Name → Name, Address 1 → Address 1, Address 2 → Address 2, City → City |
| Traverse Customer | Commercient AR Customer Managed Custom Object | 66 | Customer identifier → Commercient customer identifier, Customer name → Name, Account type → Account type, Address 1 → Address 1, Address 2 → Address 2 |
| Traverse Customer Reverse Lookup | Account | 2 | Customer identifier → Commercient AR customer code, the linked Salesforce record → Traverse customer record (custom field) |
| Traverse Shipping address | Commercient AR Shipping Address Managed Custom Object | 26 | Customer identifier,Ship to identifier → Commercient external key (package 7), Customer identifier,Ship to identifier → Name, Customer identifier → Customer identifier, Ship to identifier → Ship to identifier, Shipping name → Shipping name |
| Traverse Sales order transaction Header | Commercient Sales Order Transaction Header Managed Custom Object | 77 | Transaction identifier → Commercient transaction identifier, Name → Commercient name, Transaction type → Commercient transaction type, Batch identifier → Commercient batch identifier, Location identifier → Commercient location identifier |
| Traverse Sales order transaction Details | Commercient Sales Order Transaction Detail Managed Custom Object | 74 | Transaction identifier,Entry number → Commercient external key (package 7), Transaction identifier,Entry number → Commercient name, Transaction identifier → Commercient transaction identifier, Entry number → Commercient entry number, Item job → Commercient item job |
| Traverse AR open invoice | Commercient AR Open Invoice Managed Custom Object | 31 | Counter → Commercient counter, Name → Name, Customer identifier → Customer identifier, Invoice number → Invoice number, Record type → Record type |
| Traverse AR history Header | Commercient AR History Header Managed Custom Object | 98 | Transaction identifier, Posting run → Commercient external key (package 7), Transaction identifier, Posting run → Commercient name, Posting run → Commercient posting run, Transaction identifier → Commercient transaction identifier, Transaction type → Commercient transaction type |
| Traverse AR history Details | Commercient AR History Detail Managed Custom Object | 80 | Transaction identifier → Commercient transaction identifier, Entry number → Commercient entry number, Posting run → Commercient posting run, Item job → Commercient item job, Warehouse identifier → Commercient warehouse identifier |
| Traverse Quote Header | Commercient Quote Header Managed Custom Object | 77 | Transaction identifier → External key (custom field), Transaction identifier → Name, Transaction type → Transaction type, Transaction type → Transaction type name, Batch identifier → Batch identifier |
| Traverse Quote Details | Commercient Quote Detail Managed Custom Object | 74 | Transaction identifier, Entry number → External key (custom field), Transaction identifier, Entry number → Name, Transaction identifier → Transaction identifier, Entry number → Entry number, Item job → Item job |

## 6. Community templates

The catalogue carries 65 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 65
- Default operations: insert on 65, update on 65, delete on 65
- Marked as circular sync: 2
- Licence groups they span: 14
- Destination objects: Account, Commercient AR Customer Managed Custom Object, Commercient AR
  History Detail Managed Custom Object, Commercient AR History Header Managed Custom Object,
  Commercient AR Open Invoice Managed Custom Object, Commercient AR Shipping Address Managed Custom
  Object, Commercient Sales Order Transaction Detail Managed Custom Object, Commercient Sales Order
  Transaction Header Managed Custom Object, Commercient AR Salesperson Managed Custom Object,
  Contact, Order, Price book entry, Product, Commercient Account Matching Managed Custom Object,
  Commercient Contact Matching Managed Custom Object, Commercient Traverse 11 Account Matching
  Managed Custom Object, Commercient Quote Detail Managed Custom Object, Commercient Quote Header
  Managed Custom Object, Commercient Traverse Inventory Item Managed Custom Object, Commercient
  Traverse Item Warehouse Managed Custom Object, 3 more and 7 custom objects
- Object display names: Traverse Account, Traverse AR history Details, Traverse AR history Header,
  Traverse AR open invoice, Traverse Customer, Traverse Customer Reverse Lookup, Traverse Shipping
  address, Traverse Sales order transaction Details, Traverse Sales order transaction Header,
  Traverse Salesperson, Traverse Contacts, Traverse Service Repair Orders Lines, 20 more and a
  further template
- Template groups: Account, Product, Invoice, Customer Multi Ship Addresses, Opportunity, CRM Order
  and Line, Sales order, Invoice History Headers, Quote line number

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/traverse-11`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Traverse 11 → Salesforce templates set up. dlake-crmpro-salesforce is the destination
skill this page sits under: its own text is the authority for the Salesforce conventions that hold
across every ERP, and its ERP table lists this page alongside every sibling ERP page for this
destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/traverse-11`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
