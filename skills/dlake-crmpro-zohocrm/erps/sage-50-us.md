---
name: dlake-crmpro-zohocrm/erps/sage-50-us
kind: erp-summary
description: >-
  Use it when standing up or reading a Sage 50 US → Zoho CRM template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-zohocrm, the destination skill this page is a child of, which carries
  the Zoho CRM conventions that hold across every ERP.
---
# CRMPro → Zoho CRM — Sage 50 US: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-zohocrm/erps/sage-50-us` (or `list_skills`) against the
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
| **Sage 50 US AR term** | The templates push Sage 50 US AR Term object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Sage 50 US AR Term object | general AR settings |
| **Sage 50 US Salesperson** | The templates push Sage 50 US Salesperson object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Sage 50 US Salesperson object | employees |
| **Accounts** | The templates push Accounts to Zoho CRM. New records are created and existing ones updated; none are deleted. | Accounts | customers, addresses |
| **Sage 50 US Customer** | The templates push Sage 50 US Customer object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Sage 50 US Customer object | customers |
| **Products** | The templates push Products to Zoho CRM. New records are created and existing ones updated; none are deleted. | Products | items, vendor records, inventory costs, journal lines |
| **Sage 50 US Sales order header** | The templates push Sage 50 US Sales Order Header object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Sage 50 US Sales Order Header object | journal headers |
| **Sage 50 UK Item master** | The templates push Sage 50 US Item Master object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Sage 50 US Item Master object | items |
| **Sage 50 US Sales order detail** | The templates push Sage 50 US Sales Order Detail object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Sage 50 US Sales Order Detail object | journal lines, items |
| **Sage 50 US Sales Invoice Header** | The templates push Sage 50 US Sales Invoice Header object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Sage 50 US Sales Invoice Header object | journal headers |
| **Sage 50 US Sales Invoice Detail** | The templates push Sage 50 US Sales Invoice Detail object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Sage 50 US Sales Invoice Detail object | journal lines, items |
| **Sage 50 US Address** | The templates push Sage 50 US Address object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Sage 50 US Address object | addresses |
| **Contacts** | The templates push Contacts to Zoho CRM. New records are created and existing ones updated; none are deleted. | Contacts | contacts, customers, addresses |
| **Price books** | The templates push Zoho price books to Zoho CRM. New records are created and existing ones updated; none are deleted. | Zoho price books | — |
| **Product Price books** | The templates push Zoho product price book relation to Zoho CRM. New records are created and existing ones updated; none are deleted. | Zoho product price book relation | items |
| **Sales Order** | The templates push Sales orders to Zoho CRM. New records are created and existing ones updated; none are deleted. | Sales orders | journal lines, items, journal headers, customers, addresses, x |
| **Invoices** | The templates push Invoices to Zoho CRM. New records are created and existing ones updated; none are deleted. | Invoices | journal lines, items, journal headers, customers, addresses, x |
| **CRM Ownership** | The templates push users to Zoho CRM. New records are created and existing ones updated; none are deleted. | users | — |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Users | users | Commercient external key column | 0 |
| Sage 50 US AR term | Sage 50 US AR Term object | Commercient external key column | 1 |
| Sage 50 US Salesperson | Sage 50 US Salesperson object | Commercient external key column | 2 |
| Accounts | Accounts | Commercient AR customer code (Zoho field) | 3 |
| Sage 50 US Customer | Sage 50 US Customer object | Commercient external key column | 4 |
| Products | Products | Commercient external key column | 5 |
| Sage 50 US Sales order header | Sage 50 US Sales Order Header object | Commercient external key column | 6 |
| Sage 50 UK Item master | Sage 50 US Item Master object | Commercient external key column | 6 |
| Sage 50 US Sales order detail | Sage 50 US Sales Order Detail object | Commercient external key column | 7 |
| Sage 50 US Sales Invoice Header | Sage 50 US Sales Invoice Header object | Commercient external key column | 8 |
| Sage 50 US Sales Invoice Detail | Sage 50 US Sales Invoice Detail object | Commercient external key column | 9 |
| Sage 50 US Address | Sage 50 US Address object | Commercient external key column | 10 |
| Contacts | Contacts | Commercient external key column | 12 |
| Price books | Zoho price books | Commercient external key column | 13 |
| Product Price books | Zoho product price book relation | Commercient external key column | 14 |
| Sales Order | Sales orders | Commercient external key column | 15 |
| Invoices | Invoices | Commercient external key column | 16 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| AR terms feed | insert only | general AR settings |
| salesperson feed | — | employees |
| account feed | insert only | customers, addresses |
| customer feed | insert only | customers |
| product feed | insert only | items, vendor records, inventory costs, journal lines |
| sales order feed | insert only | journal headers |
| item master feed | insert only | items |
| sales order line feed | insert only | journal lines, items |
| sales invoice feed | insert only | journal headers |
| sales invoice line feed | insert only | journal lines, items |
| address feed | insert only | addresses |
| contact feed | insert + update | contacts, customers, addresses |
| product price list feed | insert only | items |
| sales order feed (standard module) | insert only | journal lines, items, journal headers, customers, addresses |
| invoice feed (standard module) | insert only | journal lines, items, journal headers, customers, addresses |

## 4. Order of work

The templates set run sequence from 0 to 16. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 0 — Users
- 1 — Sage 50 US AR term
- 2 — Sage 50 US Salesperson
- 3 — Accounts
- 4 — Sage 50 US Customer
- 5 — Products
- 6 — Sage 50 US Sales order header, Sage 50 UK Item master
- 7 — Sage 50 US Sales order detail
- 8 — Sage 50 US Sales Invoice Header
- 9 — Sage 50 US Sales Invoice Detail
- 10 — Sage 50 US Address
- 12 — Contacts
- 13 — Price books
- 14 — Product Price books
- 15 — Sales Order
- 16 — Invoices

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- AR terms feed reads AR terms sync output (generic name); no template in this set writes AR terms
  sync output (generic name)
- salesperson feed reads salesperson sync output (generic name); no template in this set writes
  salesperson sync output (generic name)
- account feed reads account sync output; no template in this set writes account sync output
- customer feed reads account sync output, salesperson sync output (generic name), customer sync
  output (generic short name); no template in this set writes account sync output, salesperson sync
  output (generic name), customer sync output (generic short name)
- product feed reads product sync output (generic name); no template in this set writes product sync
  output (generic name)
- sales order feed reads account sync output, customer sync output (generic short name), salesperson
  sync output (generic name), sales order header sync output (generic name); no template in this set
  writes account sync output, customer sync output (generic short name), salesperson sync output
  (generic name), sales order header sync output (generic name)
- item master feed reads product sync output (generic name), item master sync output; no template in
  this set writes product sync output (generic name), item master sync output
- sales order line feed reads customer sync output (generic short name), sales order header sync
  output (generic name), product sync output (generic name), item master sync output, sales order
  line sync output (generic name); no template in this set writes customer sync output (generic
  short name), sales order header sync output (generic name), product sync output (generic name),
  item master sync output
- sales invoice feed reads account sync output, customer sync output (generic short name), sales
  order header sync output (generic name), salesperson sync output (generic name), sales invoice
  sync output (generic name); no template in this set writes account sync output, customer sync
  output (generic short name), sales order header sync output (generic name), salesperson sync
  output (generic name)
- sales invoice line feed reads customer sync output (generic short name), sales invoice sync output
  (generic name), product sync output (generic name), item master sync output, sales invoice line
  sync output (generic name); no template in this set writes customer sync output (generic short
  name), sales invoice sync output (generic name), product sync output (generic name), item master
  sync output
- address feed reads account sync output, customer sync output (generic short name), address sync
  output (generic name); no template in this set writes account sync output, customer sync output
  (generic short name), address sync output (generic name)
- contact feed reads account sync output, contact sync output; no template in this set writes
  account sync output, contact sync output
- product price list feed reads product sync output (generic name), price book sync output (generic
  name), product price book sync output (generic name); no template in this set writes product sync
  output (generic name), price book sync output (generic name), product price book sync output
  (generic name)
- sales order feed (standard module) reads product sync output (generic name), account sync output,
  sales order sync output (standard module); no template in this set writes product sync output
  (generic name), account sync output, sales order sync output (standard module)
- invoice feed (standard module) reads product sync output (generic name), account sync output,
  sales order sync output (standard module), invoice sync output (standard module); no template in
  this set writes product sync output (generic name), account sync output, sales order sync output
  (standard module), invoice sync output (standard module)

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Zoho CRM pairs |
|---|---|---|---|
| Sage 50 US AR term | Sage 50 US AR Term object | 87 | Global identifier → external key column, Global identifier → Commercient external key (custom field), Global identifier → Global identifier, Merchant key length (ERP) → Merchant key length (custom field), Age by due date (ERP) → Age by due date (custom field) |
| Sage 50 US Salesperson | Sage 50 US Salesperson object | 103 | Employee record number → external key column, Employee record number → Commercient external key (custom field), Employee identifier → Sage 50 US salesperson name (custom field), Employee identifier → Name, Employee global identifier (ERP) → Employee global identifier (custom field) |
| Accounts | Accounts | 20 | Customer record number (ERP) → Commercient AR customer code (Zoho field), Customer identifier → Account smart identifier (custom field), Customer billing name → Account name, Customer record number (ERP) → Commercient account number, Address line 1 → Billing street (Zoho field) |
| Sage 50 US Customer | Sage 50 US Customer object | 93 | Global identifier → Global identifier, Customer billing name → Name, the linked account record → Account, the linked salesperson → Salesperson (related record), Row timestamp → Row timestamp |
| Products | Products | 17 | Item identifier → Commercient external key (custom field), Item identifier → Product name (Zoho field), Item identifier → ERP product code, Sales description → Description, Item inactive flag → Product active (Zoho field) |
| Sage 50 US Sales order header | Sage 50 US Sales Order Header object | 85 | Customer or vendor identifier → Accounts, Customer or vendor identifier → Customer, Employee record number → Salesperson (related record), Posting order number → Sage 50 US sales order header name (custom field), Reference → Name |
| Sage 50 UK Item master | Sage 50 US Item Master object | 99 | Item identifier → Commercient external key (custom field), Item identifier → Name, Cost of goods sold account record number → Cost of goods sold account record number (custom field), Category → Category, Cost method → Cost method (custom field) |
| Sage 50 US Sales order detail | Sage 50 US Sales Order Detail object | 57 | Customer record number (ERP) → Customer, Posting order number → Sales order header (related record), Item record number → item master (related record), Item identifier → Products (custom field), Line row number, Row description → Sage 50 US sales order detail name (custom field) |
| Sage 50 US Sales Invoice Header | Sage 50 US Sales Invoice Header object | 90 | Customer or vendor identifier → Account, Customer or vendor identifier → Customer, Employee record number → Salesperson (related record), Invoice order number → Sales order header (related record), Reference → status |
| Sage 50 US Sales Invoice Detail | Sage 50 US Sales Invoice Detail object | 41 | Line global identifier → Commercient external key (custom field), Purchase or sales order closed (ERP) → Purchase or sales order is closed (custom field), Used for reimbursable expense → Used for reimbursable expense, PO created → PO created (custom field), Has serial numbers → Has serial numbers (custom field) |
| Sage 50 US Address | Sage 50 US Address object | 19 | Customer record number (ERP) → Account, Customer record number (ERP) → Customer, Address record number → external key column, Address record number → Commercient external key (custom field), Address type number → Address type description (custom field) |
| Contacts | Contacts | 14 | the linked Salesforce record → Account name, Record number → Record number, returned record identifier → returned record identifier, returned record identifier → Commercient external key (custom field), Family name → Last name |
| Price books | Zoho price books | 2 | external key column → Commercient external key column, Name → Name |
| Product Price books | Zoho product price book relation | 6 | Item identifier → Commercient external key column, Item identifier → Product lookup (Zoho field), Price book name → Price book lookup (Zoho field), Price amount → List price, Price book name → Price book name |

## 6. Community templates

The catalogue carries 55 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 55
- Default operations: insert on 55, update on 55, delete on 55
- Marked as circular sync: 0
- Licence groups they span: 5
- Destination objects: Accounts, Sage 50 US Address object, Sage 50 US Customer object, Sage 50 US
  Sales Invoice Detail object, Sage 50 US Sales Invoice Header object, Sage 50 US Sales Order Detail
  object, Sage 50 US Sales Order Header object, Sage 50 US Salesperson object, Contacts, Products,
  Sage 50 US AR Term object, Sage 50 US Item Master object, Zoho price books, Quotes, Invoices,
  Sales orders, Vendors and 3 custom objects
- Object display names: Sage 50 US Address, Sage 50 US Customer, Sage 50 US Sales order detail, Sage
  50 US Sales order header, Sage 50 US Salesperson, Accounts, Contacts, Products, Sage 50 US AR
  term, Sage 50 US Sales Invoice Detail, Sage 50 US Sales Invoice Header, Price books, 11 more and a
  further template
- Template groups: CRM Order and Line, Account, CRM Quote and Line

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-zohocrm/erps/sage-50-us`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Sage 50 US → Zoho CRM templates set up. dlake-crmpro-zohocrm is the destination skill
this page sits under: its own text is the authority for the Zoho CRM conventions that hold across
every ERP, and its ERP table lists this page alongside every sibling ERP page for this destination.
For the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-zohocrm/erps/sage-50-us`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
