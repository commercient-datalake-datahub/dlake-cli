---
name: dlake-crmpro-zohocrm/erps/sage-100-contractor-2018-sage100c2018
kind: erp-summary
description: >-
  Use it when standing up or reading a Sage 100 Contractor 2018 (Sage 100 Contractor 2018) → Zoho
  CRM template set, when deciding which templates to import and activate, or when a run completes
  without pushing records and the answer is in the view or the configuration row. It extends
  dlake-crmpro, which covers operating CRMPro generally, and dlake-crmpro-zohocrm, the destination
  skill this page is a child of, which carries the Zoho CRM conventions that hold across every ERP.
---
# CRMPro → Zoho CRM — Sage 100 Contractor 2018 (Sage 100 Contractor 2018): what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-zohocrm/erps/sage-100-contractor-2018-sage100c2018` (or
`list_skills`) against the Commercient admin plane. Existing customers who need access or help:
contact support@commercient.com. New customers: contact sales@commercient.com to become a customer
and be whitelisted.

dlake-crmpro is the parent skill and the authority for everything general: the CRMPro tools, process
configuration and field list, the sync history, how source data is selected, and what a run that
finds nothing does. Read it first; this page does not repeat it. dlake-crmpro-zohocrm is the
destination skill this page is a child of, and the authority for the Zoho CRM conventions that hold
across every ERP: read it first, then come back here for what this source's own templates set. This
page grows as the catalogue does.

## 1. What the templates deliver

| Group | Business outcome | Objects | Source tables and views |
|---|---|---|---|
| **Sage 100 Sales Person** | The templates push Commercient Sage 100 AR Salesperson object to Zoho CRM. | Commercient Sage 100 AR Salesperson object | salespeople |
| **Account** | The templates push Accounts to Zoho CRM. | Accounts | customers, shipping addresses |
| **Sage 100 Customer** | The templates push Commercient Sage 100 Customer object to Zoho CRM. | Commercient Sage 100 Customer object | customers |
| **Sage 100 Ship To Address** | The templates push Commercient Sage 100 Shipping Address object to Zoho CRM. | Commercient Sage 100 Shipping Address object | shipping addresses |
| **Products** | The templates push Products to Zoho CRM. | Products | items |
| **Contacts** | The templates push Contacts to Zoho CRM. | Contacts | — |
| **Sage 100 Item Master** | The templates push Commercient Sage 100 Item Master object to Zoho CRM. | Commercient Sage 100 Item Master object | items |
| **Quotes** | The templates push Quotes to Zoho CRM. | Quotes | sales order lines, items, sales order headers, customers, shipping addresses, x |
| **Sage 100 Sales Order Header** | The templates push Commercient Sage 100 Sales Order History Header object to Zoho CRM. | Commercient Sage 100 Sales Order History Header object | sales order headers |
| **Sage 100 Sales Order Detail** | The templates push Commercient Sage 100 Sales Order History Detail object to Zoho CRM. | Commercient Sage 100 Sales Order History Detail object | sales order lines, sales order headers |
| **Sage 100 Invoice Header** | The templates push Commercient Sage 100 AR Invoice History Header object to Zoho CRM. | Commercient Sage 100 AR Invoice History Header object | invoice history headers |
| **Sage 100 Invoice Detail** | The templates push Commercient Sage 100 AR Invoice History Detail object to Zoho CRM. | Commercient Sage 100 AR Invoice History Detail object | invoice history lines, items |
| **Sage 100 Transcation Payment History** | The templates push Commercient Sage 100 AR Transaction Payment History object to Zoho CRM. | Commercient Sage 100 AR Transaction Payment History object | payment history |
| **Sage 100 Warehouse** | The templates push Commercient Sage 100 Item Warehouse object to Zoho CRM. | Commercient Sage 100 Item Warehouse object | item warehouse quantities |
| **Price Books** | The templates push Zoho price books to Zoho CRM. | Zoho price books | items |
| **Sage 100 Product Price Book** | The templates push Zoho product price book relation to Zoho CRM. | Zoho product price book relation | items |
| **Sales Orders** | The templates push Sales orders to Zoho CRM. | Sales orders | sales order lines, items, sales order headers, customers, shipping addresses, x |
| **Invoices** | The templates push Invoices to Zoho CRM. | Invoices | invoice history lines, items, invoice history headers, customers, shipping addresses, x |
| **CRM Ownership** | The templates push users to Zoho CRM. | users | — |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Sage 100 Sales Person | Commercient Sage 100 AR Salesperson object | Commercient external key column | 1 |
| Users | users | Commercient external key column | 1 |
| Account | Accounts | Commercient AR customer code (Zoho field) | 2 |
| Sage 100 Customer | Commercient Sage 100 Customer object | Commercient external key column | 3 |
| Sage 100 Ship To Address | Commercient Sage 100 Shipping Address object | Commercient external key column | 4 |
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
| Price Books | Zoho price books | Commercient external key column | 15 |
| Sage 100 Product Price Book | Zoho product price book relation | Commercient external key column | 16 |
| Sales Orders | Sales orders | Commercient external key column | 17 |
| Invoices | Invoices | Commercient external key column | 18 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| salesperson feed | insert + update | salespeople |
| account feed | insert + update | customers, shipping addresses |
| customer feed | insert + update | customers |
| shipping address feed | insert + update | shipping addresses |
| Sage 100 product feed | insert only | items |
| item feed | insert only | items |
| quote feed | insert only | sales order lines, items, sales order headers, customers, shipping addresses |
| sales order header feed | insert only | sales order headers |
| sales order detail feed | insert only | sales order lines, sales order headers |
| invoice header feed | insert + update | invoice history headers |
| invoice detail feed | insert + update | invoice history lines, items |
| payment history feed | insert + update | payment history |
| warehouse feed | insert only | item warehouse quantities |
| standard price book feed | insert + update | items |
| product price book feed | insert only | items |
| sales order feed | insert only | sales order lines, items, sales order headers, customers, shipping addresses |
| invoice feed | insert only | invoice history lines, items, invoice history headers, customers, shipping addresses |

## 4. Order of work

The templates set run sequence from 1 to 18. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 1 — Sage 100 Sales Person, Users
- 2 — Account
- 3 — Sage 100 Customer
- 4 — Sage 100 Ship To Address
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
- 15 — Price Books
- 16 — Sage 100 Product Price Book
- 17 — Sales Orders
- 18 — Invoices

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- salesperson feed reads salesperson sync output; no template in this set writes salesperson sync
  output
- account feed reads salesperson sync output, user sync output (generic name), account sync output;
  no template in this set writes salesperson sync output, user sync output (generic name), account
  sync output
- customer feed reads account sync output, salesperson sync output, customer sync output; no
  template in this set writes account sync output, salesperson sync output, customer sync output
- shipping address feed reads account sync output, customer sync output, shipping address sync
  output; no template in this set writes account sync output, customer sync output, shipping address
  sync output
- Sage 100 product feed reads Sage 100 product sync output; no template in this set writes Sage 100
  product sync output
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
- standard price book feed reads price book sync output (generic name); no template in this set
  writes price book sync output (generic name)
- product price book feed reads Sage 100 product sync output, product price book sync output
  (generic name); no template in this set writes Sage 100 product sync output, product price book
  sync output (generic name)
- sales order feed reads Sage 100 product sync output, account sync output, sales order sync output;
  no template in this set writes Sage 100 product sync output, account sync output, sales order sync
  output
- invoice feed reads Sage 100 product sync output, account sync output, sales order sync output,
  invoice sync output; no template in this set writes Sage 100 product sync output, account sync
  output, sales order sync output, invoice sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Zoho CRM pairs |
|---|---|---|---|
| Sage 100 Sales Person | Commercient Sage 100 AR Salesperson object | 27 | Salesperson division,Salesperson number → Commercient external key (custom field), Salesperson name,Salesperson number → Name, Address line 1 → Commercient address line 1, Address line 2 → Commercient address line 2, Address line 3 → Address line 3 (Zoho field) |
| Users | users | 1 | external key column → Commercient external key column |
| Account | Accounts | 26 | AR division number, Customer number → Commercient AR customer code (Zoho field), Customer name → Account name, Email address → Email address 1, Telephone → Phone, Ship to address line 1, Ship to address line 2, Ship to address line 3 → Shipping street |
| Sage 100 Customer | Commercient Sage 100 Customer object | 78 | AR division number, Customer number → external key column, AR division number, Customer number → Commercient external key (custom field), AR division number → AR division number (Zoho field), Address line 1 → Commercient address line 1, Address line 2 → Commercient address line 2 |
| Sage 100 Ship To Address | Commercient Sage 100 Shipping Address object | 20 | AR division number, Customer number, Ship-to code → Commercient external key (custom field), Shipping name → Name, AR division number → Area division number (custom field), Contact code → Contact code (custom field), Customer number → Customer number (custom field) |
| Products | Products | 7 | Item code → Commercient external key column, Item code → Product name (Zoho field), Item code → ERP product code, Item description → Description, Inactive item → Product active (Zoho field) |
| Contacts | Contacts | 5 | external key column → Commercient external key column, Given name → Given name, Family name → Family name, Email → Email, Phone → Phone |
| Sage 100 Item Master | Commercient Sage 100 Item Master object | 91 | the linked Salesforce record → Id, Item code → external key column, Item code → Commercient external key (custom field), Item code → Name, Row timestamp → Row timestamp |
| Quotes | Quotes | 18 | AR division number,Sales order number → Commercient external key (custom field), the linked Salesforce record → Account name, Sales order number → Name, Sales order number → returned quote number, Date created → ERP created date (custom field) |
| Sage 100 Sales Order Header | Commercient Sage 100 Sales Order History Header object | 82 | Sales order number → Commercient external key (custom field), Sales order number → Name, AR division number → Area division number key (Zoho field), Batch email → Batch email (Zoho field), Batch fax → Batch fax (Zoho field) |
| Sage 100 Sales Order Detail | Commercient Sage 100 Sales Order History Detail object | 67 | Sales order number, Line key → Commercient external key (custom field), Sales order number, Line key → Name, AP division number → AP division number (custom field), Alias item number → Alias item number (custom field), Alternate tax identifier → Alternate tax identifier (custom field) |
| Sage 100 Invoice Header | Commercient Sage 100 AR Invoice History Header object | 89 | Invoice number, Header sequence number → Commercient external key (custom field), Invoice number, Header sequence number → Name, AR division number → AR division number (custom field), Amount subject to discount → Amount subject to discount (custom field), Apply to invoice number → Apply to invoice number (custom field) |
| Sage 100 Invoice Detail | Commercient Sage 100 AR Invoice History Detail object | 62 | Invoice number, Header sequence number, Detail sequence number → Commercient external key (custom field), Invoice number, Header sequence number, Detail sequence number → Name, AP division number → AP division number (custom field), Alias item number → Alias item number (custom field), Alternate tax identifier → Alternate tax identifier (custom field) |
| Sage 100 Transcation Payment History | Commercient Sage 100 AR Transaction Payment History object | 22 | AR division number → Area division key (custom field), Customer number → Customer number (custom field), Invoice number → Invoice number key (custom field), Invoice type → Invoice type (custom field), Invoice header sequence number → Invoice history header sequence number (custom field) |
| Sage 100 Warehouse | Commercient Sage 100 Item Warehouse object | 30 | Item code, Warehouse code → Commercient external key (custom field), Item code, Warehouse code → Name, Average cost → Average cost (custom field), Bin location → Bin location (custom field), Cost calculation cost committed → Cost calculation cost committed (custom field) |
| Price Books | Zoho price books | 4 | Commercient external key (custom field) → Commercient external key (custom field), Price book name (Zoho field) → Price book name (Zoho field), Active → Active, Description → Description |
| Sage 100 Product Price Book | Zoho product price book relation | 5 | the linked Salesforce record → Product lookup (Zoho field), the linked Salesforce record → Price book lookup (Zoho field), Standard unit price → List price, Item code → Commercient external key column, Row timestamp → Row timestamp |
| Sales Orders | Sales orders | 18 | AR division number,Sales order number → Commercient external key (custom field), the linked Salesforce record → Account name, Sales order number → Name, Sales order number → Sales order number (Zoho field), Date created → ERP created date (custom field) |
| Invoices | Invoices | 20 | AR division number,Invoice number → Commercient external key (custom field), the linked Salesforce record → Account name, Sales order number → Sales order (single record), Invoice number → Name, Invoice number → Invoice number (Zoho field) |

## 6. Community templates

The catalogue carries 18 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 18
- Default operations: insert on 18, update on 18, delete on 18
- Marked as circular sync: 0
- Licence groups they span: 7
- Destination objects: Accounts, Contacts, Zoho price books, Products, Quotes, Commercient Sage 100
  AR Invoice History Detail object, Commercient Sage 100 AR Invoice History Header object,
  Commercient Sage 100 AR Salesperson object, Commercient Sage 100 AR Transaction Payment History
  object, Commercient Sage 100 Customer object, Commercient Sage 100 Item Master object, Commercient
  Sage 100 Open Invoice object, Commercient Sage 100 Terms object, Commercient Sage 100 Sales Order
  History Detail object, Commercient Sage 100 Sales Order History Header object, Commercient Sage
  100 Shipping Address object, users and a custom object
- Object display names: Account, Contacts, Price Books, Products, Quotes, Sage Open Invoice, Sage
  Terms, Sage 100 Customer, Sage 100 Invoice Detail, Sage 100 Invoice Header, Sage 100 Item Master,
  Sage 100 Product Price Book and 6 more
- Template groups: Account, CRM Order and Line, CRM Quote and Line, Product

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-zohocrm/erps/sage-100-contractor-2018-sage100c2018`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Sage 100 Contractor 2018 (Sage 100 Contractor 2018) → Zoho CRM templates set up.
dlake-crmpro-zohocrm is the destination skill this page sits under: its own text is the authority
for the Zoho CRM conventions that hold across every ERP, and its ERP table lists this page alongside
every sibling ERP page for this destination. For the extract leg that fills the source data, see
dlake-normalsync; for the on-premises agent that runs it, dlake-syncagent; for the writeback leg,
dlake-txdownloaderpro; for standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-zohocrm/erps/sage-100-contractor-2018-sage100c2018`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
