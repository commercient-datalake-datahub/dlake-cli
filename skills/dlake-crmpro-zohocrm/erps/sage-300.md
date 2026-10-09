---
name: dlake-crmpro-zohocrm/erps/sage-300
kind: erp-summary
description: >-
  Use it when standing up or reading a Sage 300 → Zoho CRM template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-zohocrm, the destination skill this page is a child of, which carries
  the Zoho CRM conventions that hold across every ERP.
---
# CRMPro → Zoho CRM — Sage 300: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-zohocrm/erps/sage-300` (or `list_skills`) against the
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
| **Sage 300 Salesperson** | The templates push Commercient Sage 300 Salesperson object to Zoho CRM. | Commercient Sage 300 Salesperson object | salespeople |
| **Parent Account** | The templates push Accounts to Zoho CRM. | Accounts | customers, customer shipping locations |
| **Child Account** | The templates push Accounts to Zoho CRM. | Accounts | customer shipping locations, customers |
| **Sage 300 Customer** | The templates push Commercient Sage 300 Customer object to Zoho CRM. | Commercient Sage 300 Customer object | customers |
| **Contacts** | The templates push Contacts to Zoho CRM. | Contacts | customers |
| **Sage 300 Ship to** | The templates push Commercient Sage 300 Ship To Address object to Zoho CRM. | Commercient Sage 300 Ship To Address object | customer shipping locations |
| **Products** | The templates push Products to Zoho CRM. | Products | inventory items |
| **Sage 300 Item** | The templates push Commercient Sage 300 Item object to Zoho CRM. | Commercient Sage 300 Item object | inventory items |
| **Sage 300 Sales order header** | The templates push Commercient Sage 300 Sales Order Header object to Zoho CRM. | Commercient Sage 300 Sales Order Header object | order entry headers |
| **Sage 300 Sales order detail** | The templates push Commercient Sage 300 Sales Order Detail object to Zoho CRM. | Commercient Sage 300 Sales Order Detail object | order entry lines |
| **Sage 300 Invoice Header** | The templates push Commercient Sage 300 Invoice Header object to Zoho CRM. | Commercient Sage 300 Invoice Header object | invoice headers |
| **Sage 300 Invoice Detail** | The templates push Commercient Sage 300 Invoice Detail object to Zoho CRM. | Commercient Sage 300 Invoice Detail object | invoice lines |
| **Pricebook** | The templates push Zoho price books to Zoho CRM. | Zoho price books | item prices |
| **Product Price Book** | The templates push Zoho product price book relation to Zoho CRM. | Zoho product price book relation | item prices |
| **Sales Orders** | The templates push Sales orders to Zoho CRM. | Sales orders | order entry lines, inventory items, order entry headers, customers, customer shipping locations, x |
| **Invoices** | The templates push Invoices to Zoho CRM. | Invoices | invoice lines, inventory items, invoice headers, customers, order entry headers, customer shipping locations |
| **CRM Ownership** | The templates push users to Zoho CRM. | users | — |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Users | users | Commercient external key column | 1 |
| Sage 300 Salesperson | Commercient Sage 300 Salesperson object | Commercient external key column | 2 |
| Parent Account | Accounts | Commercient AR customer code (Zoho field) | 3 |
| Child Account | Accounts | Commercient AR customer code (Zoho field) | 4 |
| Sage 300 Customer | Commercient Sage 300 Customer object | Commercient external key column | 5 |
| Contacts | Contacts | Commercient external key column | 6 |
| Sage 300 Ship to | Commercient Sage 300 Ship To Address object | Commercient external key column | 7 |
| Products | Products | Commercient external key column | 8 |
| Sage 300 Item | Commercient Sage 300 Item object | Commercient external key column | 9 |
| Sage 300 Sales order header | Commercient Sage 300 Sales Order Header object | Commercient external key column | 10 |
| Sage 300 Sales order detail | Commercient Sage 300 Sales Order Detail object | Commercient external key column | 11 |
| Sage 300 Invoice Header | Commercient Sage 300 Invoice Header object | Commercient external key column | 12 |
| Sage 300 Invoice Detail | Commercient Sage 300 Invoice Detail object | Commercient external key column | 13 |
| Pricebook | Zoho price books | Commercient external key column | 14 |
| Product Price Book | Zoho product price book relation | Commercient external key column | 15 |
| Sales Orders | Sales orders | Commercient external key column | 16 |
| Invoices | Invoices | Commercient external key column | 17 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| salesperson feed | insert only | salespeople |
| account feed | insert + update | customers, customer shipping locations |
| child account feed | insert + update | customer shipping locations, customers |
| customer feed | insert only | customers |
| contact feed | insert + update | customers |
| shipping address feed | insert only | customer shipping locations |
| product feed | insert only | inventory items |
| item feed | insert only | inventory items |
| sales order feed | insert only | order entry headers |
| sales order line feed | insert only | order entry lines |
| invoice feed | insert only | invoice headers |
| invoice line feed | insert only | invoice lines |
| price book feed | insert + update | item prices |
| product price book feed | insert only | item prices |
| sales order feed (standard module) | insert only | order entry lines, inventory items, order entry headers, customers, customer shipping locations |
| invoice feed (standard module) | insert only | invoice lines, inventory items, invoice headers, customers, order entry headers |

## 4. Order of work

The templates set run sequence from 1 to 17. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 1 — Users
- 2 — Sage 300 Salesperson
- 3 — Parent Account
- 4 — Child Account
- 5 — Sage 300 Customer
- 6 — Contacts
- 7 — Sage 300 Ship to
- 8 — Products
- 9 — Sage 300 Item
- 10 — Sage 300 Sales order header
- 11 — Sage 300 Sales order detail
- 12 — Sage 300 Invoice Header
- 13 — Sage 300 Invoice Detail
- 14 — Pricebook
- 15 — Product Price Book
- 16 — Sales Orders
- 17 — Invoices

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- salesperson feed reads salesperson sync output; no template in this set writes salesperson sync
  output
- account feed reads user sync output (generic name), salesperson sync output, account sync output;
  no template in this set writes user sync output (generic name), salesperson sync output, account
  sync output
- child account feed reads account sync output; no template in this set writes account sync output
- customer feed reads account sync output, customer sync output; no template in this set writes
  account sync output, customer sync output
- contact feed reads account sync output, customer sync output, contact sync output; no template in
  this set writes account sync output, customer sync output, contact sync output
- shipping address feed reads account sync output, customer sync output, salesperson sync output,
  shipping address sync output; no template in this set writes account sync output, customer sync
  output, salesperson sync output, shipping address sync output
- product feed reads product sync output; no template in this set writes product sync output
- item feed reads product sync output, item sync output; no template in this set writes product sync
  output, item sync output
- sales order feed reads account sync output, customer sync output, sales order sync output; no
  template in this set writes account sync output, customer sync output, sales order sync output
- sales order line feed reads sales order sync output, item sync output, product sync output, sales
  order line sync output; no template in this set writes sales order sync output, item sync output,
  product sync output, sales order line sync output
- invoice feed reads account sync output, customer sync output, invoice sync output; no template in
  this set writes account sync output, customer sync output, invoice sync output
- invoice line feed reads invoice sync output, item sync output, invoice line sync output; no
  template in this set writes invoice sync output, item sync output, invoice line sync output
- price book feed reads price book sync output; no template in this set writes price book sync
  output
- product price book feed reads product sync output, price book sync output, product price book sync
  output; no template in this set writes product sync output, price book sync output, product price
  book sync output
- sales order feed (standard module) reads product sync output, account sync output, sales order
  sync output (standard module); no template in this set writes product sync output, account sync
  output, sales order sync output (standard module)
- invoice feed (standard module) reads product sync output, account sync output, sales order sync
  output (standard module), invoice sync output (standard module); no template in this set writes
  product sync output, account sync output, sales order sync output (standard module), invoice sync
  output (standard module)

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Zoho CRM pairs |
|---|---|---|---|
| Users | users | 1 | Commercient external key column → Commercient external key column |
| Sage 300 Salesperson | Commercient Sage 300 Salesperson object | 26 | Salesperson code → Commercient external key (custom field), Employee name → Name, Salesperson code → Salesperson, Audit date → Audit date (Zoho field), Audit time → Audit time (Zoho field) |
| Parent Account | Accounts | 18 | ERP customer number → Commercient AR customer code (Zoho field), Customer name → Account name, Street address line 1, Street address line 2, Street address line 3 → Billing street (Zoho field), Customer city → Billing city (Zoho field), State code → Billing state |
| Child Account | Accounts | 11 | ERP customer number, Customer shipping location code → Commercient AR customer code (Zoho field), Customer name, Customer shipping location code → Account name, Street address line 1, Street address line 2, Street address line 3, Street address line 4 → Shipping street, Customer city → Shipping city, State code → Shipping state |
| Sage 300 Customer | Commercient Sage 300 Customer object | 157 | ERP customer number → Commercient external key (custom field), Customer name → Name, ERP customer number → Customer number, Audit date → Audit date (Zoho field), Audit time → Audit time (Zoho field) |
| Contacts | Contacts | 11 | the linked Salesforce record → Account name, the linked Salesforce record → Sage 300 customer (custom field), ERP customer number → external key column, ERP customer number → Commercient external key column, Contact name → Last name |
| Sage 300 Ship to | Commercient Sage 300 Ship To Address object | 56 | ERP customer number, Customer shipping location code → Commercient external key (custom field), Shipping location name, Customer shipping location code → Name, ERP customer number → Customer number, Customer shipping location code → Ship to location (custom field), Audit date → Audit date (Zoho field) |
| Products | Products | 8 | ERP item number → Product name (Zoho field), ERP item number → ERP product code, ERP item number → Commercient external key (custom field), Item description → Description, Inactive → Product active (Zoho field) |
| Sage 300 Item | Commercient Sage 300 Item object | 69 | ERP item number → Commercient external key (custom field), ERP item number → Name, ERP item number → Item number, Audit date → Audit date (Zoho field), Audit time → Audit time (Zoho field) |
| Sage 300 Sales order header | Commercient Sage 300 Sales Order Header object | 163 | Customer → Account, Order unique key → Commercient external key (custom field), Order unique key → Name, Order unique key → Order unique key (custom field), Audit date → Audit date (Zoho field) |
| Sage 300 Sales order detail | Commercient Sage 300 Sales Order Detail object | 154 | Order unique key, ERP line number → Commercient external key (custom field), Order unique key, ERP line number → Name, Order unique key → Order unique key (custom field), ERP line number → Line number, Audit date → Audit date (Zoho field) |
| Sage 300 Invoice Header | Commercient Sage 300 Invoice Header object | 164 | Invoice unique key → Commercient external key (custom field), Invoice unique key → Name, Invoice unique key → Invoice unique key (custom field), Audit date → Audit date (Zoho field), Audit time → Audit time (Zoho field) |
| Sage 300 Invoice Detail | Commercient Sage 300 Invoice Detail object | 122 | Invoice unique key, ERP line number → Commercient external key (custom field), Invoice unique key, ERP line number → Name, Invoice unique key → Invoice unique key (custom field), ERP line number → Line unique key (custom field), Audit date → Audit date (Zoho field) |
| Pricebook | Zoho price books | 4 | Price list, Price type → Commercient external key (custom field), Price list, Price type → Price book name (Zoho field), Price list, Price type → Description |
| Product Price Book | Zoho product price book relation | 4 | the linked Salesforce record → Product lookup (Zoho field), the linked Salesforce record → Price book lookup (Zoho field), Unit price → List price, Price list, Price type, ERP item number → Commercient external key column |
| Sales Orders | Sales orders | 18 | Order number → Commercient external key (custom field), the linked Salesforce record → Account name, Order number → Name, Order number → Sales order number (Zoho field), Order date → ERP created date (custom field) |
| Invoices | Invoices | 19 | Invoice unique key → Commercient external key (custom field), the linked Salesforce record → Account name, Order unique key → Sales order (single record), Invoice unique key → Name, Invoice unique key → Invoice number (Zoho field) |

## 6. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-zohocrm/erps/sage-300`.

## 7. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Sage 300 → Zoho CRM templates set up. dlake-crmpro-zohocrm is the destination skill this
page sits under: its own text is the authority for the Zoho CRM conventions that hold across every
ERP, and its ERP table lists this page alongside every sibling ERP page for this destination. For
the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-zohocrm/erps/sage-300`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
