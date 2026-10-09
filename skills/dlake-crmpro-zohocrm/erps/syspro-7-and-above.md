---
name: dlake-crmpro-zohocrm/erps/syspro-7-and-above
kind: erp-summary
description: >-
  Use it when standing up or reading a SYSPRO 7 and above → Zoho CRM template set, when deciding
  which templates to import and activate, or when a run completes without pushing records and the
  answer is in the view or the configuration row. It extends dlake-crmpro, which covers operating
  CRMPro generally, and dlake-crmpro-zohocrm, the destination skill this page is a child of, which
  carries the Zoho CRM conventions that hold across every ERP.
---
# CRMPro → Zoho CRM — SYSPRO 7 and above: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-zohocrm/erps/syspro-7-and-above` (or `list_skills`) against the
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
| **Accounts** | The templates push Accounts to Zoho CRM. New records are created and existing ones updated; none are deleted. | Accounts | customers |
| **Syspro 7 Terms** | The templates push Commercient Syspro 7 Terms object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Syspro 7 Terms object | AR terms |
| **Child Accounts** | The templates push Accounts to Zoho CRM. New records are created and existing ones updated; none are deleted. | Accounts | customers |
| **Syspro 7 Salesperson** | The templates push Commercient Syspro 7 Salesperson object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Syspro 7 Salesperson object | salespeople |
| **Syspro 7 Customer** | The templates push Commercient Syspro 7 Customer object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Syspro 7 Customer object | customers |
| **Contacts** | The templates push Contacts to Zoho CRM. New records are created and existing ones updated; none are deleted. | Contacts | — |
| **Products** | The templates push Products to Zoho CRM. New records are created and existing ones updated; none are deleted. | Products | inventory items, inventory warehouse quantities, product classes |
| **Syspro 7 AR Multiple Address** | The templates push Commercient Syspro 7 AR Multiple Address object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Syspro 7 AR Multiple Address object | customer multiple addresses, customers |
| **Price Books** | The templates push Zoho price books to Zoho CRM. New records are created and existing ones updated; none are deleted. | Zoho price books | inventory prices |
| **Prodcut Pricebook** | The templates push Zoho product price book relation to Zoho CRM. New records are created and existing ones updated; none are deleted. | Zoho product price book relation | inventory prices |
| **Syspro 7 Inventory Master** | The templates push Commercient Syspro 7 Inventory Master object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Syspro 7 Inventory Master object | inventory items |
| **Syspro 7 Inventory Warehouse** | The templates push Commercient Syspro 7 Inventory Warehouse object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Syspro 7 Inventory Warehouse object | inventory warehouse quantities |
| **Syspro 7 SO Header** | The templates push Commercient Syspro 7 Sales Order Header object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Syspro 7 Sales Order Header object | sales orders |
| **Syspro 7 SO Detail** | The templates push Commercient Syspro 7 Sales Order Detail object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Syspro 7 Sales Order Detail object | sales order lines |
| **Syspro 7 Invoice Header** | The templates push Commercient Syspro 7 Invoice Header object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Syspro 7 Invoice Header object | AR invoices |
| **Syspro 7 Invoice Detail** | The templates push Commercient Syspro 7 Invoice Detail object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Syspro 7 Invoice Detail object | AR transaction lines |
| **CRM Ownership** | The templates push users to Zoho CRM. New records are created and existing ones updated; none are deleted. | users | — |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Users | users | Commercient external key column | 0 |
| Accounts | Accounts | Commercient AR customer code (Zoho field) | 1 |
| Syspro 7 Terms | Commercient Syspro 7 Terms object | Commercient external key column | 2 |
| Child Accounts | Accounts | Commercient AR customer code (Zoho field) | 3 |
| Syspro 7 Salesperson | Commercient Syspro 7 Salesperson object | Commercient external key column | 4 |
| Syspro 7 Customer | Commercient Syspro 7 Customer object | Commercient external key column | 5 |
| Contacts | Contacts | Commercient external key column | 6 |
| Products | Products | Commercient external key column | 7 |
| Syspro 7 AR Multiple Address | Commercient Syspro 7 AR Multiple Address object | Commercient external key column | 8 |
| Price Books | Zoho price books | Commercient external key column | 8 |
| Prodcut Pricebook | Zoho product price book relation | Commercient external key column | 9 |
| Syspro 7 Inventory Master | Commercient Syspro 7 Inventory Master object | Commercient external key column | 9 |
| Syspro 7 Inventory Warehouse | Commercient Syspro 7 Inventory Warehouse object | Commercient external key column | 10 |
| Syspro 7 SO Header | Commercient Syspro 7 Sales Order Header object | Commercient external key column | 11 |
| Syspro 7 SO Detail | Commercient Syspro 7 Sales Order Detail object | Commercient external key column | 12 |
| Syspro 7 Invoice Header | Commercient Syspro 7 Invoice Header object | Commercient external key column | 13 |
| Syspro 7 Invoice Detail | Commercient Syspro 7 Invoice Detail object | Name | 14 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Emits the linked Salesforce record | Source tables |
|---|---|---|---|
| account feed | insert + update | yes | customers |
| terms feed | insert + update | no | AR terms |
| child account feed | insert + update | yes | customers |
| salesperson feed | insert + update | no | salespeople |
| customer feed | insert only | no | customers |
| product feed | insert + update | no | inventory items, inventory warehouse quantities, product classes |
| AR multiple address feed | insert + update | no | customer multiple addresses, customers |
| price book feed | insert only | no | inventory prices |
| product price book feed | insert + update | no | inventory prices |
| inventory master feed | insert + update | no | inventory items |
| inventory warehouse feed | insert + update | no | inventory warehouse quantities |
| sales order feed | insert + update | no | sales orders |
| sales order line feed | insert + update | no | sales order lines |
| invoice feed | insert + update | no | AR invoices |
| invoice line feed | insert + update | no | AR transaction lines |

## 4. Order of work

The templates set run sequence from 0 to 14. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 0 — Users
- 1 — Accounts
- 2 — Syspro 7 Terms
- 3 — Child Accounts
- 4 — Syspro 7 Salesperson
- 5 — Syspro 7 Customer
- 6 — Contacts
- 7 — Products
- 8 — Syspro 7 AR Multiple Address, Price Books
- 9 — Prodcut Pricebook, Syspro 7 Inventory Master
- 10 — Syspro 7 Inventory Warehouse
- 11 — Syspro 7 SO Header
- 12 — Syspro 7 SO Detail
- 13 — Syspro 7 Invoice Header
- 14 — Syspro 7 Invoice Detail

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- account feed reads account sync output, Sales rep, salesperson sync output; no template in this
  set writes account sync output, Sales rep, salesperson sync output
- terms feed reads terms sync output; no template in this set writes terms sync output
- child account feed reads account sync output, Sales rep, salesperson sync output, child account
  sync output; no template in this set writes account sync output, Sales rep, salesperson sync
  output, child account sync output
- salesperson feed reads salesperson sync output; no template in this set writes salesperson sync
  output
- customer feed reads terms sync output, account sync output, salesperson sync output, child account
  sync output, customer sync output; no template in this set writes terms sync output, account sync
  output, salesperson sync output, child account sync output
- product feed reads product sync output; no template in this set writes product sync output
- AR multiple address feed reads account sync output, child account sync output, customer sync
  output, user sync output, AR multiple address sync output; no template in this set writes account
  sync output, child account sync output, customer sync output, user sync output
- price book feed reads price book sync output; no template in this set writes price book sync
  output
- product price book feed reads product sync output, price book sync output, product price book sync
  output; no template in this set writes product sync output, price book sync output, product price
  book sync output
- inventory master feed reads product sync output, inventory master sync output; no template in this
  set writes product sync output, inventory master sync output
- inventory warehouse feed reads product sync output, inventory master sync output, inventory
  warehouse sync output; no template in this set writes product sync output, inventory master sync
  output, inventory warehouse sync output
- sales order feed reads child account sync output, account sync output, salesperson sync output,
  customer sync output, sales order sync output; no template in this set writes child account sync
  output, account sync output, salesperson sync output, customer sync output
- sales order line feed reads sales order sync output, product sync output, sales order line sync
  output; no template in this set writes sales order sync output, product sync output, sales order
  line sync output
- invoice feed reads child account sync output, account sync output, customer sync output, sales
  order sync output, salesperson sync output, invoice sync output; no template in this set writes
  child account sync output, account sync output, customer sync output, sales order sync output
- invoice line feed reads invoice sync output, product sync output, invoice line sync output; no
  template in this set writes invoice sync output, product sync output, invoice line sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Zoho CRM pairs |
|---|---|---|---|
| Accounts | Accounts | 23 | Customer → Commercient AR customer code (Zoho field), Salesperson → Syspro 7 salesperson (related record), Name → Account name, Short name → Account short name (custom field), Special instructions → Description |
| Syspro 7 Terms | Commercient Syspro 7 Terms object | 16 | Terms code → Commercient external key (custom field), Terms code → Name, Ageing code → Ageing code (custom field), Description → Description, Discount date → Discount date (custom field) |
| Child Accounts | Accounts | 26 | Customer → Commercient AR customer code (Zoho field), Name → Account name, Short name → Account short name (custom field), Special instructions → Description, Branch → Branch |
| Syspro 7 Salesperson | Commercient Syspro 7 Salesperson object | 32 | Salesperson → Commercient external key (custom field), Branch → Branch, Commission percentage → Commission percent, Name → Name, Actual sales 1 → Sales actual 1 (custom field) |
| Syspro 7 Customer | Commercient Syspro 7 Customer object | 141 | Customer → Commercient external key (custom field), Additional telephone → Additional telephone (custom field), Alternate method flag → Alternate method flag (custom field), Apply line discount → Apply line discount (custom field), Apply order discount → Apply order discount (custom field) |
| Contacts | Contacts | 5 | Customer identifier → Commercient external key column, Given name → Given name, Family name → Family name, Email → Email, Phone → Phone |
| Products | Products | 13 | Description → Product name (Zoho field), Stock code → ERP product code, Stock code → Commercient external key (custom field), Long description → Description, Row timestamp → Row timestamp |
| Syspro 7 AR Multiple Address | Commercient Syspro 7 AR Multiple Address object | 27 | Customer,Address code → Commercient external key (custom field), Customer,Address code → Name, Address code → Address code (custom field), Area → Area, City → City |
| Price Books | Zoho price books | 2 | Price code → Price book name (Zoho field), Price code → Commercient external key column |
| Prodcut Pricebook | Zoho product price book relation | 5 | the linked Salesforce record → Price book lookup (Zoho field), the linked Salesforce record → Product lookup (Zoho field), Stock code,Price code → Commercient external key column, Selling price → List price, Row timestamp → Row timestamp |
| Syspro 7 Inventory Master | Commercient Syspro 7 Inventory Master object | 134 | Description → Name, Stock code → Commercient external key (custom field), Row timestamp → Row timestamp, Abc analysis required → Abc analysis required (custom field), Abc class → Abc class (custom field) |
| Syspro 7 Inventory Warehouse | Commercient Syspro 7 Inventory Warehouse object | 126 | Stock code,Warehouse → Commercient external key (custom field), Stock code,Warehouse → Name, Abc class → Abc class (custom field), Aged quantity 1 → Aged quantity 1 (custom field), Aged quantity 2 → Aged quantity 2 (custom field) |
| Syspro 7 SO Header | Commercient Syspro 7 Sales Order Header object | 121 | Sales order → Commercient external key (custom field), Sales order → Name, Active flag → Active flag (custom field), Alternate shipping address flag → Alternate shipping address flag (custom field), Alternate key → Alternate key (custom field) |
| Syspro 7 SO Detail | Commercient Syspro 7 Sales Order Detail object | 135 | Sales order,Sales order line → Commercient external key (custom field), Sales order,Sales order line → Name, Credit reason → Credit reason (custom field), Fixed quantity per → Fixed quantity per (custom field), Fixed quantity per flag → Fixed quantity per flag (custom field) |
| Syspro 7 Invoice Header | Commercient Syspro 7 Invoice Header object | 50 | Customer,Invoice,Document type → Commercient external key (custom field), Customer,Invoice,Document type → Name, Account conversion rate (invoice) → Account conversion rate (invoice custom field), Account multiply or divide (invoice) → Account multiply or divide (invoice custom field), Account currency → Account currency (invoice custom field) |
| Syspro 7 Invoice Detail | Commercient Syspro 7 Invoice Detail object | 84 | Abc update → Abc update (custom field), Account conversion rate (invoice line) → Account conversion rate (invoice line custom field), Account currency → Account currency (invoice line custom field), Account multiply or divide (invoice line) → Account multiply or divide (invoice line custom field), Area → Area |

## 6. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-zohocrm/erps/syspro-7-and-above`.

## 7. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped SYSPRO 7 and above → Zoho CRM templates set up. dlake-crmpro-zohocrm is the destination
skill this page sits under: its own text is the authority for the Zoho CRM conventions that hold
across every ERP, and its ERP table lists this page alongside every sibling ERP page for this
destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-zohocrm/erps/syspro-7-and-above`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
