---
name: dlake-crmpro-zohocrm/erps/sage-50-uk
kind: erp-summary
description: >-
  Use it when standing up or reading a Sage 50 UK → Zoho CRM template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-zohocrm, the destination skill this page is a child of, which carries
  the Zoho CRM conventions that hold across every ERP.
---
# CRMPro → Zoho CRM — Sage 50 UK: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-zohocrm/erps/sage-50-uk` (or `list_skills`) against the
Commercient admin plane. Existing customers who need access or help: contact
support@commercient.com. New customers: contact sales@commercient.com to become a customer and be
whitelisted.

dlake-crmpro is the parent skill and the authority for everything general: the CRMPro tools, process
configuration and field list, the sync history, how source data is selected, and what a run that
finds nothing does. Read it first; this page does not repeat it. dlake-crmpro-zohocrm is the
destination skill this page is a child of, and the authority for the Zoho CRM conventions that hold
across every ERP: read it first, then come back here for what this source's own templates set. This
page grows as the catalogue does.

## 1. What the templates deliver

| Group | Business outcome | Objects | Source tables and views |
|---|---|---|---|
| **Accounts** | The templates push Accounts to Zoho CRM. New records are created and existing ones updated; none are deleted. | Accounts | sales ledger customers, delivery addresses |
| **Sage 50 UK Customer** | The templates push Sage 50 UK Customer object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Sage 50 UK Customer object | sales ledger customers |
| **Sage 50 UK Address** | The templates push Sage 50 UK Address object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Sage 50 UK Address object | delivery addresses, sales ledger customers |
| **Contacts** | The templates push Contacts to Zoho CRM. New records are created and existing ones updated; none are deleted. | Contacts | delivery addresses, sales ledger customers, purchase delivery addresses, supplier accounts |
| **Products** | The templates push Products to Zoho CRM. New records are created and existing ones updated; none are deleted. | Products | stock items |
| **Item Master** | The templates push Sage 50 UK Item object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Sage 50 UK Item object | stock items |
| **Sales Order Header** | The templates push Sage 50 UK Sales Order Header object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Sage 50 UK Sales Order Header object | sales order headers, sales ledger customers, currencies |
| **Sales Order Details** | The templates push Sage 50 UK Sales Order Detail object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Sage 50 UK Sales Order Detail object | sales order lines |
| **Invoice Header** | The templates push Sage 50 UK Invoice Header object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Sage 50 UK Invoice Header object | invoices, sales ledger customers, currencies |
| **Invoice Details** | The templates push Sage 50 UK Invoice Detail object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Sage 50 UK Invoice Detail object | invoice items, invoices |
| **Suppliers** | The templates push Vendors to Zoho CRM. New records are created and existing ones updated; none are deleted. | Vendors | supplier accounts |
| **Sage 50 UK Purchase Order** | The templates push Sage 50 UK Purchase Order Header object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Sage 50 UK Purchase Order Header object | purchase order headers, sales ledger customers |
| **Sage 50 UK Purchase order line** | The templates push Sage 50 UK Purchase Order Detail object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Sage 50 UK Purchase Order Detail object | purchase order lines |
| **Sales Orders** | The templates push Sales orders to Zoho CRM. New records are created and existing ones updated; none are deleted. | Sales orders | sales order lines, sales order headers, stock items, sales ledger customers, delivery addresses, x |
| **Invoices** | The templates push Invoices to Zoho CRM. New records are created and existing ones updated; none are deleted. | Invoices | invoice items, stock items, invoices, sales order headers, sales ledger customers, delivery addresses |
| **Purchase Order** | The templates push Purchase orders to Zoho CRM. New records are created and existing ones updated; none are deleted. | Purchase orders | purchase order lines, purchase order headers, stock items, delivery addresses, x |
| **Products Update** | The templates push Products to Zoho CRM. New records are created and existing ones updated; none are deleted. | Products | stock items |
| **CRM Ownership** | The templates push users to Zoho CRM. New records are created and existing ones updated; none are deleted. | users | — |
| **Vendor** | The templates push Sage 50 UK Vendor object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Sage 50 UK Vendor object | supplier accounts |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Users | users | Commercient external key column | 0 |
| Accounts | Accounts | Commercient AR customer code (Zoho field) | 1 |
| Sage 50 UK Customer | Sage 50 UK Customer object | Commercient external key column | 2 |
| Sage 50 UK Address | Sage 50 UK Address object | Commercient external key column | 3 |
| Contacts | Contacts | Commercient external key column | 4 |
| Products | Products | Commercient external key column | 5 |
| Item Master | Sage 50 UK Item object | Commercient external key column | 6 |
| Sales Order Header | Sage 50 UK Sales Order Header object | Commercient external key column | 7 |
| Sales Order Details | Sage 50 UK Sales Order Detail object | Commercient external key column | 8 |
| Invoice Header | Sage 50 UK Invoice Header object | Commercient external key column | 9 |
| Invoice Details | Sage 50 UK Invoice Detail object | Commercient external key column | 10 |
| Suppliers | Vendors | Commercient external key column | 11 |
| Sage 50 UK Vendor | Sage 50 UK Vendor object | Commercient external key column | 11 |
| Sage 50 UK Purchase Order | Sage 50 UK Purchase Order Header object | Commercient external key column | 12 |
| Sage 50 UK Purchase order line | Sage 50 UK Purchase Order Detail object | Commercient external key column | 13 |
| Sales Orders | Sales orders | Commercient external key column | 14 |
| Invoices | Invoices | Commercient external key column | 15 |
| Purchase Order | Purchase orders | Commercient external key column | 16 |
| Products Update | Products | Commercient external key column | 17 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Emits the linked Salesforce record | Source tables |
|---|---|---|---|
| account feed | insert only | no | sales ledger customers, delivery addresses |
| Sage 50 UK customer feed | insert only | no | sales ledger customers |
| address feed | insert only | no | delivery addresses, sales ledger customers |
| contact feed | insert only | no | delivery addresses, sales ledger customers, purchase delivery addresses, supplier accounts |
| product feed | insert only | no | stock items |
| item feed | insert only | no | stock items |
| sales order feed | insert only | no | sales order headers, sales ledger customers, currencies |
| sales order line feed | insert only | no | sales order lines |
| invoice feed | insert only | no | invoices, sales ledger customers, currencies |
| invoice line feed | insert only | no | invoice items, invoices |
| supplier feed | insert only | yes | supplier accounts |
| vendor feed | insert only | no | supplier accounts |
| purchase order feed | insert only | no | purchase order headers, sales ledger customers |
| purchase order line feed | insert only | no | purchase order lines |
| sales order feed (standard module) | insert only | no | sales order lines, sales order headers, stock items, sales ledger customers, delivery addresses |
| invoice feed (standard module) | insert only | no | invoice items, stock items, invoices, sales order headers, sales ledger customers |
| purchase order feed (standard module) | insert only | no | purchase order lines, purchase order headers, stock items, delivery addresses, x |
| product feed | insert only | no | stock items |

## 4. Order of work

The templates set run sequence from 0 to 17. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 0 — Users
- 1 — Accounts
- 2 — Sage 50 UK Customer
- 3 — Sage 50 UK Address
- 4 — Contacts
- 5 — Products
- 6 — Item Master
- 7 — Sales Order Header
- 8 — Sales Order Details
- 9 — Invoice Header
- 10 — Invoice Details
- 11 — Suppliers, Sage 50 UK Vendor
- 12 — Sage 50 UK Purchase Order
- 13 — Sage 50 UK Purchase order line
- 14 — Sales Orders
- 15 — Invoices
- 16 — Purchase Order
- 17 — Products Update

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- account feed reads Sales rep, account sync output (generic name); no template in this set writes
  Sales rep, account sync output (generic name)
- Sage 50 UK customer feed reads account sync output (generic name), customer sync output (generic
  short name); no template in this set writes account sync output (generic name), customer sync
  output (generic short name)
- address feed reads account sync output (generic name), customer sync output (generic short name),
  address sync output (generic name); no template in this set writes account sync output (generic
  name), customer sync output (generic short name), address sync output (generic name)
- contact feed reads Sales rep, account sync output (generic name), contact sync output, supplier
  sync output, Pur:; no template in this set writes Sales rep, account sync output (generic name),
  contact sync output, supplier sync output
- product feed reads product sync output; no template in this set writes product sync output
- item feed reads product sync output, item sync output (generic name); no template in this set
  writes product sync output, item sync output (generic name)
- sales order feed reads account sync output (generic name), customer sync output (generic short
  name), sales order header sync output (generic name); no template in this set writes account sync
  output (generic name), customer sync output (generic short name), sales order header sync output
  (generic name)
- sales order line feed reads sales order header sync output (generic name), item sync output
  (generic name), sales order line sync output (generic name); no template in this set writes sales
  order header sync output (generic name), item sync output (generic name), sales order line sync
  output (generic name)
- invoice feed reads account sync output (generic name), customer sync output (generic short name),
  invoice sync output (generic name); no template in this set writes account sync output (generic
  name), customer sync output (generic short name), invoice sync output (generic name)
- invoice line feed reads invoice sync output (generic name), item sync output (generic name),
  invoice line sync output (generic name); no template in this set writes invoice sync output
  (generic name), item sync output (generic name), invoice line sync output (generic name)
- supplier feed reads Sales rep, supplier sync output; no template in this set writes Sales rep,
  supplier sync output
- purchase order feed reads supplier sync output, vendor sync output, purchase order sync output; no
  template in this set writes supplier sync output, vendor sync output, purchase order sync output
- purchase order line feed reads purchase order sync output, product sync output, purchase order
  line sync output; no template in this set writes purchase order sync output, product sync output,
  purchase order line sync output
- sales order feed (standard module) reads product sync output, account sync output (generic name),
  contact sync output, sales order sync output (standard module); no template in this set writes
  product sync output, account sync output (generic name), contact sync output, sales order sync
  output (standard module)
- invoice feed (standard module) reads product sync output, account sync output (generic name),
  sales order sync output (standard module), contact sync output, invoice sync output (standard
  module); no template in this set writes product sync output, account sync output (generic name),
  sales order sync output (standard module), contact sync output
- purchase order feed (standard module) reads product sync output, account sync output (generic
  name), contact sync output, supplier sync output, purchase order sync output (standard module),
  Pur:; no template in this set writes product sync output, account sync output (generic name),
  contact sync output, supplier sync output
- product feed reads product sync output; no template in this set writes product sync output
- vendor feed reads vendor sync output; no template in this set writes vendor sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Zoho CRM pairs |
|---|---|---|---|
| Accounts | Accounts | 31 | Account reference → Commercient AR customer code (Zoho field), Account reference → Commercient external key column, Account reference → Sage account reference (custom field), Credit position → Credit position, Status → Account status (custom field) |
| Sage 50 UK Customer | Sage 50 UK Customer object | 114 | Account reference → Commercient external key (custom field), Name → Name, Address 1 → Address 1, Address 2 → Address 2, Address 3 → Address 3 |
| Sage 50 UK Address | Sage 50 UK Address object | 29 | Reference → Commercient external key (custom field), Reference → external key column, Tax code → Tax code, Address type field → Address type field, Record deleted → Record deleted |
| Contacts | Contacts | 16 | Reference → Commercient external key column, Account reference → Account name, Contact name field → Last name, Contact name field → First name, Email → Email |
| Products | Products | 18 | Description → Product name (Zoho field), Stock code → ERP product code, Stock code → Commercient external key (custom field), Description → Description, Inactive flag → Product active (Zoho field) |
| Item Master | Sage 50 UK Item object | 114 | Description → Name, Stock code → Commercient external key (custom field), Web publish → Web publish, Web special → Web special, Department number (ERP) → Department number (custom field) |
| Sales Order Header | Sage 50 UK Sales Order Header object | 95 | Order number → Commercient external key (custom field), Delivery status code (ERP) → Shipping status code (custom field), Order type code → Order type code, Allocated status code → Allocated status code, Courier number → Courier number |
| Sales Order Details | Sage 50 UK Sales Order Detail object | 52 | Item number, Job number, Order number, Item identifier → Commercient external key (custom field), Item number, Job number, Order number, Item identifier → Name, Item number, Job number, Order number, Item identifier → Sage 50 UK sales order detail name (custom field), Order number → sales order header (related record), Stock code → item (related record) |
| Invoice Header | Sage 50 UK Invoice Header object | 98 | Invoice number → Commercient external key (custom field), Name → Name, Account reference → Customer, Account reference → Account, Currency → Currency |
| Invoice Details | Sage 50 UK Invoice Detail object | 48 | Invoice number → Sage 50 UK invoice header (related record), Item number → Item, Invoice number, Item number, Item identifier → Sage 50 UK invoice detail name (custom field), Invoice number, Item number, Item identifier → Name, Invoice number, Item number, Item identifier → external key column |
| Suppliers | Vendors | 17 | Account reference, Name → Commercient external key (custom field), Name → Vendor name, Account reference → Sage account reference (vendor custom field), Contact name field → Contact name field, Email → Email |
| Sage 50 UK Vendor | Sage 50 UK Vendor object | 126 | Account reference → Commercient external key (custom field), Name → Name, Account on hold → Account on hold, Account reference → Account reference (custom field), Address 1 → Address 1 |
| Sage 50 UK Purchase Order | Sage 50 UK Purchase Order Header object | 83 | Order number → Commercient external key (custom field), Account reference → Sage 50 UK vendor (related record), Account reference → Supplier (custom field), Order number → Name, Address 1 → Address 1 |
| Sage 50 UK Purchase order line | Sage 50 UK Purchase Order Detail object | 54 | Order number, Item identifier, Item number, Job number → Commercient external key (custom field), Stock code → product (related record), Order number → Sage 50 UK purchase order header (related record), Order number, Item identifier, Item number, Job number → Name, Additional discount rate → Additional discount rate |
| Products Update | Products | 18 | Description → Product name (Zoho field), Stock code → ERP product code, Stock code → Commercient external key (custom field), Description → Description, Inactive flag → Product active (Zoho field) |

## 6. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-zohocrm/erps/sage-50-uk`.

## 7. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Sage 50 UK → Zoho CRM templates set up. dlake-crmpro-zohocrm is the destination skill
this page sits under: its own text is the authority for the Zoho CRM conventions that hold across
every ERP, and its ERP table lists this page alongside every sibling ERP page for this destination.
For the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-zohocrm/erps/sage-50-uk`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
