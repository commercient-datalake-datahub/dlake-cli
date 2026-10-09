---
name: dlake-crmpro-zohocrm/erps/sage-100-premium
kind: erp-summary
description: >-
  Use it when standing up or reading a Sage 100 Premium → Zoho CRM template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-zohocrm, the destination skill this page is a child of, which carries
  the Zoho CRM conventions that hold across every ERP.
---
# CRMPro → Zoho CRM — Sage 100 Premium: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-zohocrm/erps/sage-100-premium` (or `list_skills`) against the
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
| **Account** | The templates push Accounts to Zoho CRM. | Accounts | customers, shipping addresses |
| **Sage 100 Customer** | The templates push Commercient Sage 100 Customer object to Zoho CRM. | Commercient Sage 100 Customer object | customers |
| **Sage 100 Ship To Address** | The templates push Commercient Sage 100 Shipping Address object to Zoho CRM. | Commercient Sage 100 Shipping Address object | shipping addresses |
| **Sage 100 Sales Person** | The templates push Commercient Sage 100 AR Salesperson object to Zoho CRM. | Commercient Sage 100 AR Salesperson object | salespeople |
| **Products** | The templates push Products to Zoho CRM. | Products | items, extended item descriptions |
| **Contacts** | The templates push Contacts to Zoho CRM. | Contacts | customer contacts, customers |
| **Sage 100 Item Master** | The templates push Commercient Sage 100 Item Master object to Zoho CRM. | Commercient Sage 100 Item Master object | items |
| **Quotes** | The templates push Quotes to Zoho CRM. | Quotes | sales order lines, sales order headers, customers, shipping addresses, x |
| **Sage 100 Sales Order Header** | The templates push Commercient Sage 100 Sales Order History Header object to Zoho CRM. | Commercient Sage 100 Sales Order History Header object | sales order headers |
| **Sage 100 Sales Order Detail** | The templates push Commercient Sage 100 Sales Order History Detail object to Zoho CRM. | Commercient Sage 100 Sales Order History Detail object | sales order lines, sales order headers |
| **Sage 100 Invoice Header** | The templates push Commercient Sage 100 AR Invoice History Header object to Zoho CRM. | Commercient Sage 100 AR Invoice History Header object | invoice history headers |
| **Sage 100 Invoice Detail** | The templates push Commercient Sage 100 AR Invoice History Detail object to Zoho CRM. | Commercient Sage 100 AR Invoice History Detail object | invoice history lines, items |
| **Sage 100 Transcation Payment History** | The templates push Commercient Sage 100 AR Transaction Payment History object to Zoho CRM. | Commercient Sage 100 AR Transaction Payment History object | payment history |
| **Sage 100 Warehouse** | The templates push Commercient Sage 100 Item Warehouse object to Zoho CRM. | Commercient Sage 100 Item Warehouse object | item warehouse quantities |
| **Sage 100 Product Price Book** | The templates push Zoho product price book relation to Zoho CRM. | Zoho product price book relation | items |
| **Sales Orders** | The templates push Sales orders to Zoho CRM. | Sales orders | sales order lines, sales order headers, customers, shipping addresses, x |
| **Invoices** | The templates push Invoices to Zoho CRM. | Invoices | invoice history lines, invoice history headers, customers, shipping addresses, x |
| **Sage 100 Terms** | The templates push Commercient Sage 100 Terms object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Sage 100 Terms object | terms codes |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Account | Accounts | Commercient AR customer code (Zoho field) | 1 |
| Sage 100 Customer | Commercient Sage 100 Customer object | Commercient external key column | 2 |
| Sage 100 Terms | Commercient Sage 100 Terms object | Commercient external key column | 2 |
| Sage 100 Ship To Address | Commercient Sage 100 Shipping Address object | Commercient external key column | 3 |
| Sage 100 Sales Person | Commercient Sage 100 AR Salesperson object | Commercient external key column | 4 |
| Products | Products | Commercient external key column | 5 |
| Contacts | Contacts | Commercient external key column | 6 |
| Sage 100 Item Master | Commercient Sage 100 Item Master object | Commercient external key column | 7 |
| Quotes | Quotes | Commercient external key column | 8 |
| Sage 100 Sales Order Header | Commercient Sage 100 Sales Order History Header object | Commercient external key column | 9 |
| Sage 100 Sales Order Detail | Commercient Sage 100 Sales Order History Detail object | Commercient external key column | 10 |
| Sage 100 Invoice Header | Commercient Sage 100 AR Invoice History Header object | Commercient external key column | 11 |
| Sage 100 Invoice Detail | Commercient Sage 100 AR Invoice History Detail object | Commercient external key column | 12 |
| Sage 100 Transcation Payment History | Commercient Sage 100 AR Transaction Payment History object | Commercient external key column | 13 |
| Sage 100 Warehouse | Commercient Sage 100 Item Warehouse object | Commercient external key column | 14 |
| Sage 100 Product Price Book | Zoho product price book relation | Commercient external key column | 15 |
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
| account feed | insert + update | customers, shipping addresses |
| customer feed | insert + update | customers |
| terms feed | insert only | terms codes |
| shipping address feed | insert + update | shipping addresses |
| salesperson feed | insert + update | salespeople |
| Sage 100 product feed | insert only | items, extended item descriptions |
| contact feed | insert + update | customer contacts, customers |
| item feed | insert only | items |
| quote feed | insert only | sales order lines, sales order headers, customers, shipping addresses, x |
| sales order header feed | insert only | sales order headers |
| sales order detail feed | insert only | sales order lines, sales order headers |
| invoice header feed | insert + update | invoice history headers |
| invoice detail feed | insert + update | invoice history lines, items |
| payment history feed | insert + update | payment history |
| warehouse feed | insert only | item warehouse quantities |
| product price book feed | insert only | items |
| sales order feed | insert only | sales order lines, sales order headers, customers, shipping addresses, x |
| invoice feed | insert only | invoice history lines, invoice history headers, customers, shipping addresses, x |

## 4. Order of work

The templates set run sequence from 1 to 17. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 1 — Account
- 2 — Sage 100 Customer, Sage 100 Terms
- 3 — Sage 100 Ship To Address
- 4 — Sage 100 Sales Person
- 5 — Products
- 6 — Contacts
- 7 — Sage 100 Item Master
- 8 — Quotes
- 9 — Sage 100 Sales Order Header
- 10 — Sage 100 Sales Order Detail
- 11 — Sage 100 Invoice Header
- 12 — Sage 100 Invoice Detail
- 13 — Sage 100 Transcation Payment History
- 14 — Sage 100 Warehouse
- 15 — Sage 100 Product Price Book
- 16 — Sales Orders
- 17 — Invoices

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- account feed reads account sync output; no template in this set writes account sync output
- customer feed reads account sync output, salesperson sync output, customer sync output; no
  template in this set writes account sync output, salesperson sync output, customer sync output
- shipping address feed reads account sync output, customer sync output, shipping address sync
  output; no template in this set writes account sync output, customer sync output, shipping address
  sync output
- salesperson feed reads salesperson sync output; no template in this set writes salesperson sync
  output
- Sage 100 product feed reads Sage 100 product sync output; no template in this set writes Sage 100
  product sync output
- contact feed reads account sync output, customer sync output, contact sync output; no template in
  this set writes account sync output, customer sync output, contact sync output
- item feed reads Sage 100 product sync output, item sync output; no template in this set writes
  Sage 100 product sync output, item sync output
- quote feed reads Sage 100 product sync output, account sync output, salesperson sync output, quote
  sync output; no template in this set writes Sage 100 product sync output, account sync output,
  salesperson sync output, quote sync output
- sales order header feed reads account sync output, customer sync output, salesperson sync output,
  sales order header sync output; no template in this set writes account sync output, customer sync
  output, salesperson sync output, sales order header sync output
- sales order detail feed reads sales order header sync output, item sync output, Sage 100 product
  sync output, sales order detail sync output; no template in this set writes sales order header
  sync output, item sync output, Sage 100 product sync output, sales order detail sync output
- invoice header feed reads account sync output, customer sync output, invoice header sync output;
  no template in this set writes account sync output, customer sync output, invoice header sync
  output
- invoice detail feed reads invoice header sync output, item sync output, Sage 100 product sync
  output, invoice detail sync output; no template in this set writes invoice header sync output,
  item sync output, Sage 100 product sync output, invoice detail sync output
- payment history feed reads account sync output, customer sync output, invoice header sync output,
  payment history sync output; no template in this set writes account sync output, customer sync
  output, invoice header sync output, payment history sync output
- warehouse feed reads item sync output, Sage 100 product sync output, warehouse sync output; no
  template in this set writes item sync output, Sage 100 product sync output, warehouse sync output
- product price book feed reads Sage 100 product sync output, product price book sync output
  (generic name); no template in this set writes Sage 100 product sync output, product price book
  sync output (generic name)
- sales order feed reads Sage 100 product sync output, account sync output, sales order sync output;
  no template in this set writes Sage 100 product sync output, account sync output, sales order sync
  output
- invoice feed reads Sage 100 product sync output, account sync output, sales order sync output,
  invoice sync output; no template in this set writes Sage 100 product sync output, account sync
  output, sales order sync output, invoice sync output
- terms feed reads terms sync output; no template in this set writes terms sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Zoho CRM pairs |
|---|---|---|---|
| Account | Accounts | 26 | AR division number, Customer number → Commercient AR customer code (Zoho field), Customer name → Account name, Email address → Email address 1, Telephone → Phone, Ship to address line 1, Ship to address line 2, Ship to address line 3 → Shipping street |
| Sage 100 Customer | Commercient Sage 100 Customer object | 76 | AR division number, Customer number → Commercient external key (custom field), AR division number → AR division number (custom field), Address line 1 → Address line 1 (custom field), Address line 2 → Address line 2 (custom field), Address line 3 → Address line 3 (custom field) |
| Sage 100 Terms | Commercient Sage 100 Terms object | 16 | Date created → Commercient ERP date created, Days before discount due → Commercient days before discount due, Days before due → Commercient days before due, Discount calculation method → Commercient discount calculation method, Discount date, day of the month → Commercient discount date, day of the month |
| Sage 100 Ship To Address | Commercient Sage 100 Shipping Address object | 20 | AR division number, Customer number, Ship-to code → Commercient external key (custom field), Shipping name, AR division number, Customer number, Ship-to code → Name, AR division number → Area division number (custom field), Contact code → Contact code (custom field), Customer number → Customer number (custom field) |
| Sage 100 Sales Person | Commercient Sage 100 AR Salesperson object | 27 | Salesperson division,Salesperson number → Commercient external key (custom field), Salesperson name,Salesperson number → Name, Address line 1 → Commercient address line 1, Address line 2 → Commercient address line 2, Address line 3 → Address line 3 (Zoho field) |
| Products | Products | 9 | the linked Salesforce record → Id, Item code → Commercient external key column, Item code → Product name (Zoho field), Item code → ERP product code, Item description → Description |
| Contacts | Contacts | 19 | AR division number, Customer number, Contact code → Commercient external key column, Email address → Email, Telephone 2 → Home phone, Telephone number 1 → Phone, Fax number → Fax |
| Sage 100 Item Master | Commercient Sage 100 Item Master object | 91 | the linked Salesforce record → Id, Item code → external key column, Item code → Commercient external key (custom field), Item code → Name, Row timestamp → Row timestamp |
| Quotes | Quotes | 20 | AR division number,Sales order number → Commercient external key (custom field), the linked Salesforce record → Account name, Sales order number → Name, Sales order number → returned quote number, Date created → ERP created date (custom field) |
| Sage 100 Sales Order Header | Commercient Sage 100 Sales Order History Header object | 83 | Sales order number → external key column, Sales order number → Commercient external key (custom field), Sales order number → Name, AR division number → Area division number key (Zoho field), Batch email → Batch email (Zoho field) |
| Sage 100 Sales Order Detail | Commercient Sage 100 Sales Order History Detail object | 67 | Sales order number, Line key → Commercient external key (custom field), Sales order number, Line key → Name, AP division number → AP division number (custom field), Alias item number → Alias item number (custom field), Alternate tax identifier → Alternate tax identifier (custom field) |
| Sage 100 Invoice Header | Commercient Sage 100 AR Invoice History Header object | 91 | Invoice number, Header sequence number → Commercient external key (custom field), Invoice number, Header sequence number → Name, AR division number → AR division number (custom field), Amount subject to discount → Amount subject to discount (custom field), Apply to invoice number → Apply to invoice number (custom field) |
| Sage 100 Invoice Detail | Commercient Sage 100 AR Invoice History Detail object | 60 | Invoice number, Header sequence number, Detail sequence number → Commercient external key (custom field), Invoice number, Header sequence number, Detail sequence number → Name, AP division number → AP division number (custom field), Alias item number → Alias item number (custom field), Alternate tax identifier → Alternate tax identifier (custom field) |
| Sage 100 Transcation Payment History | Commercient Sage 100 AR Transaction Payment History object | 22 | AR division number → Area division key (custom field), Customer number → Customer number (custom field), Invoice number → Invoice number key (custom field), Invoice type → Invoice type (custom field), Invoice header sequence number → Invoice history header sequence number (custom field) |
| Sage 100 Warehouse | Commercient Sage 100 Item Warehouse object | 28 | Item code, Warehouse code → Commercient external key (custom field), Item code, Warehouse code → Name, Average cost → Average cost (custom field), Bin location → Bin location (custom field), Cost calculation cost committed → Cost calculation cost committed (custom field) |
| Sage 100 Product Price Book | Zoho product price book relation | 5 | the linked Salesforce record → Product lookup (Zoho field), the linked Salesforce record → Price book lookup (Zoho field), Standard unit price → List price, Item code → Commercient external key column, Row timestamp → Row timestamp |
| Sales Orders | Sales orders | 19 | AR division number,Sales order number → external key column, AR division number,Sales order number → Commercient external key (custom field), the linked Salesforce record → Account name, Sales order number → Name, Sales order number → Sales order number (Zoho field) |
| Invoices | Invoices | 19 | AR division number,Invoice number → Commercient external key (custom field), the linked Salesforce record → Account name, Sales order number → Sales order (single record), Invoice number → Name, Invoice number → Invoice number (Zoho field) |

## 6. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-zohocrm/erps/sage-100-premium`.

## 7. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Sage 100 Premium → Zoho CRM templates set up. dlake-crmpro-zohocrm is the destination
skill this page sits under: its own text is the authority for the Zoho CRM conventions that hold
across every ERP, and its ERP table lists this page alongside every sibling ERP page for this
destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-zohocrm/erps/sage-100-premium`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
