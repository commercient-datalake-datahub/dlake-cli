---
name: dlake-crmpro-zohocrm/erps/sage-200-uk
kind: erp-summary
description: >-
  Use it when standing up or reading a Sage 200 UK → Zoho CRM template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-zohocrm, the destination skill this page is a child of, which carries
  the Zoho CRM conventions that hold across every ERP.
---
# CRMPro → Zoho CRM — Sage 200 UK: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-zohocrm/erps/sage-200-uk` (or `list_skills`) against the
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
| **Accounts** | The templates push Accounts to Zoho CRM. New records are created and existing ones updated; none are deleted. | Accounts | customer accounts, customer locations, customer delivery addresses |
| **Child Accounts** | The templates push Accounts to Zoho CRM. New records are created and existing ones updated; none are deleted. | Accounts | customer delivery addresses, customer locations |
| **Sage 200 UK Customers** | The templates push Commercient Sage 200 UK Customers object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Sage 200 UK Customers object | customer accounts |
| **Sage 200 UK Address** | The templates push Commercient Sage 200 UK Address object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Sage 200 UK Address object | customer locations |
| **Contacts** | The templates push Contacts to Zoho CRM. New records are created and existing ones updated; none are deleted. | Contacts | customer contacts, customer locations, customer accounts, customer contact details, customer delivery addresses, sales order delivery addresses |
| **Products** | The templates push Products to Zoho CRM. New records are created and existing ones updated; none are deleted. | Products | stock items, stock item prices, product groups |
| **Quotes** | The templates push Quotes to Zoho CRM. New records are created and existing ones updated; none are deleted. | Quotes | sales order and return lines, stock items, sales orders and returns, customer locations, sales order delivery addresses, x |
| **Price Books** | The templates push Zoho price books to Zoho CRM. New records are created and existing ones updated; none are deleted. | Zoho price books | price bands |
| **Prodcut Pricebook** | The templates push Zoho product price book relation to Zoho CRM. New records are created and existing ones updated; none are deleted. | Zoho product price book relation | stock items, stock item prices, price bands |
| **Sales orders** | The templates push Sales orders to Zoho CRM. New records are created and existing ones updated; none are deleted. | Sales orders | sales order and return lines, stock items, sales orders and returns, customer locations, sales order delivery addresses, x |
| **Sage 200 UK Item** | The templates push Commercient Sage 200 UK Item object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Sage 200 UK Item object | stock items |
| **Sage 200 UK Warehouse** | The templates push Commercient Sage 200 UK Warehouse object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Sage 200 UK Warehouse object | warehouses |
| **Sage 200 UK Warehouse Item** | The templates push Commercient Sage 200 UK Warehouse Item object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Sage 200 UK Warehouse Item object | warehouse items, warehouses, stock items |
| **Sage 200 UK Sales Order Header** | The templates push Commercient Sage 200 UK Sales Order Header object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Sage 200 UK Sales Order Header object | sales orders and returns, sales order delivery addresses |
| **Sage 200 UK Sales order detail** | The templates push Commercient Sage 200 UK Sales Order Detail object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Sage 200 UK Sales Order Detail object | sales order and return lines, stock items |
| **Sage 200 UK Invoice header** | The templates push Commercient Sage 200 UK Invoice Header object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Sage 200 UK Invoice Header object | invoices and credit notes |
| **Sage 200 UK Invoice detail** | The templates push Commercient Sage 200 UK Invoice Detail object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Sage 200 UK Invoice Detail object | invoice and credit note lines, invoices and credit notes, stock items |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Accounts | Accounts | Commercient code (Zoho field) | 1 |
| Child Accounts | Accounts | Commercient AR customer code (Zoho field) | 2 |
| Sage 200 UK Customers | Commercient Sage 200 UK Customers object | Commercient external key column | 3 |
| Sage 200 UK Address | Commercient Sage 200 UK Address object | Commercient external key column | 4 |
| Contacts | Contacts | Commercient external key column | 5 |
| Products | Products | Commercient external key column | 6 |
| Quotes | Quotes | Commercient external key column | 7 |
| Price Books | Zoho price books | Commercient external key column | 7 |
| Prodcut Pricebook | Zoho product price book relation | Commercient external key column | 8 |
| Sales orders | Sales orders | Commercient external key column | 8 |
| Sage 200 UK Item | Commercient Sage 200 UK Item object | Commercient external key column | 8 |
| Sage 200 UK Warehouse | Commercient Sage 200 UK Warehouse object | Commercient external key column | 9 |
| Sage 200 UK Warehouse Item | Commercient Sage 200 UK Warehouse Item object | Commercient external key column | 10 |
| Sage 200 UK Sales Order Header | Commercient Sage 200 UK Sales Order Header object | Commercient external key column | 12 |
| Sage 200 UK Sales order detail | Commercient Sage 200 UK Sales Order Detail object | Commercient external key column | 13 |
| Sage 200 UK Invoice header | Commercient Sage 200 UK Invoice Header object | Commercient external key column | 14 |
| Sage 200 UK Invoice detail | Commercient Sage 200 UK Invoice Detail object | Commercient external key column | 15 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| account feed | insert only | customer accounts, customer locations, customer delivery addresses |
| child account feed | insert only | customer delivery addresses, customer locations |
| customer feed | insert only | customer accounts |
| address feed | insert only | customer locations |
| contact feed | insert only | customer contacts, customer locations, customer accounts, customer contact details, customer delivery addresses |
| product feed | insert only | stock items, stock item prices, product groups |
| quote feed | insert only | sales order and return lines, stock items, sales orders and returns, customer locations, sales order delivery addresses |
| price book feed | insert + update | price bands |
| product price book feed | insert only | stock items, stock item prices, price bands |
| Zoho sales order feed | insert only | sales order and return lines, stock items, sales orders and returns, customer locations, sales order delivery addresses |
| item feed | insert only | stock items |
| warehouse feed | insert + update | warehouses |
| warehouse item feed | insert only | warehouse items, warehouses, stock items |
| sales order feed | insert only | sales orders and returns, sales order delivery addresses |
| sales order line feed | insert only | sales order and return lines, stock items |
| invoice feed | insert only | invoices and credit notes |
| invoice line feed | insert only | invoice and credit note lines, invoices and credit notes, stock items |

## 4. Order of work

The templates set run sequence from 1 to 15. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 1 — Accounts
- 2 — Child Accounts
- 3 — Sage 200 UK Customers
- 4 — Sage 200 UK Address
- 5 — Contacts
- 6 — Products
- 7 — Quotes, Price Books
- 8 — Prodcut Pricebook, Sales orders, Sage 200 UK Item
- 9 — Sage 200 UK Warehouse
- 10 — Sage 200 UK Warehouse Item
- 12 — Sage 200 UK Sales Order Header
- 13 — Sage 200 UK Sales order detail
- 14 — Sage 200 UK Invoice header
- 15 — Sage 200 UK Invoice detail

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- account feed reads account sync output (generic name); no template in this set writes account sync
  output (generic name)
- child account feed reads account sync output (generic name); no template in this set writes
  account sync output (generic name)
- customer feed reads account sync output (generic name), customer sync output (generic short name);
  no template in this set writes account sync output (generic name), customer sync output (generic
  short name)
- address feed reads account sync output (generic name), customer sync output (generic short name),
  address sync output (generic name); no template in this set writes account sync output (generic
  name), customer sync output (generic short name), address sync output (generic name)
- contact feed reads account sync output (generic name), customer sync output (generic short name),
  contact sync output; no template in this set writes account sync output (generic name), customer
  sync output (generic short name), contact sync output
- product feed reads product sync output; no template in this set writes product sync output
- quote feed reads product sync output, account sync output (generic name), quote sync output; no
  template in this set writes product sync output, account sync output (generic name), quote sync
  output
- price book feed reads price book sync output; no template in this set writes price book sync
  output
- product price book feed reads price book sync output, product sync output, product price book sync
  output; no template in this set writes price book sync output, product sync output, product price
  book sync output
- Zoho sales order feed reads product sync output, account sync output (generic name), Zoho sales
  order sync output; no template in this set writes product sync output, account sync output
  (generic name), Zoho sales order sync output
- item feed reads product sync output, item sync output; no template in this set writes product sync
  output, item sync output
- warehouse feed reads warehouse sync output; no template in this set writes warehouse sync output
- warehouse item feed reads product sync output, item sync output, warehouse sync output, warehouse
  item sync output; no template in this set writes product sync output, item sync output, warehouse
  sync output, warehouse item sync output
- sales order feed reads account sync output (generic name), customer sync output (generic short
  name), contact sync output, sales order header sync output (generic name); no template in this set
  writes account sync output (generic name), customer sync output (generic short name), contact sync
  output, sales order header sync output (generic name)
- sales order line feed reads item sync output, sales order header sync output (generic name), sales
  order line sync output; no template in this set writes item sync output, sales order header sync
  output (generic name), sales order line sync output
- invoice feed reads account sync output (generic name), customer sync output (generic short name),
  invoice sync output; no template in this set writes account sync output (generic name), customer
  sync output (generic short name), invoice sync output
- invoice line feed reads invoice sync output, invoice line sync output; no template in this set
  writes invoice sync output, invoice line sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Zoho CRM pairs |
|---|---|---|---|
| Accounts | Accounts | 24 | Customer account identifier → Commercient code (custom field), Customer account identifier → Commercient AR customer code (Zoho field), Customer account name → Account name, Address line 1 → Address 1, Address line 2 → Address 2 |
| Child Accounts | Accounts | 23 | Customer identifier, Customer delivery address identifier → Commercient AR customer code (Zoho field), Postal name, Customer identifier, Customer delivery address identifier, Description → Account name, Address line 1 → Address 1, Address line 2 → Address 2, Address line 3 → Address 3 |
| Sage 200 UK Customers | Commercient Sage 200 UK Customers object | 83 | Customer account identifier → Commercient external key (custom field), Customer account number → Customer account number (Zoho field), the linked Salesforce record → Account, Customer account name → Customer account name (Zoho field), Customer account name → Name |
| Sage 200 UK Address | Commercient Sage 200 UK Address object | 15 | Customer location identifier → Commercient external key (custom field), Address line 1 → Commercient address line 1, Address line 2 → Commercient address line 2, Address line 3 → Address line 3 (Zoho field), Address line 4 → Address line 4 (Zoho field) |
| Contacts | Contacts | 8 | Customer contact identifier → Commercient external key column, Customer account identifier → Account name, Customer account identifier → Sage 200 UK customer (custom field), Family name → Last name, Given name → First name |
| Products | Products | 13 | Code → Commercient external key column, Name → Name, Name → Product name (Zoho field), Code → ERP product code, Description → Description |
| Price Books | Zoho price books | 4 | Price band identifier → Commercient external key (custom field), Name → Price book name (Zoho field), Description → Description, Active → Active |
| Prodcut Pricebook | Zoho product price book relation | 6 | Code → Commercient external key column, Price → List price, Price band identifier → Price band identifier, Record version stamp → Active, the linked Salesforce record → Price book lookup (Zoho field) |
| Sage 200 UK Item | Commercient Sage 200 UK Item object | 93 | Item identifier → Commercient external key (custom field), Code → Code, Name → Name, Analysis code 1 → Analysis code 1 (Zoho field), Analysis code 2 → Analysis code 2 (Zoho field) |
| Sage 200 UK Warehouse | Commercient Sage 200 UK Warehouse object | 33 | Warehouse identifier → Commercient external key (custom field), Name → Name, Description → Description, Use for sales trading → Use for sales trading, Postal name → Postal name |
| Sage 200 UK Warehouse Item | Commercient Sage 200 UK Warehouse Item object | 26 | Warehouse item identifier → Commercient external key (custom field), Warehouse item identifier → Name, Warehouse item identifier → external key column, Code → Item code, Name → Warehouse name |
| Sage 200 UK Sales Order Header | Commercient Sage 200 UK Sales Order Header object | 74 | Customer identifier → Account, Sales order delivery address identifier → Contact (custom field), Customer identifier → Sage 200 UK customer (related record), Order or return identifier → Commercient external key (custom field), Order or return identifier → Name |
| Sage 200 UK Sales order detail | Commercient Sage 200 UK Sales Order Detail object | 103 | Item code → Commercient Sage 200 UK Item Managed Custom Object, Order or return identifier → Sales order header (related record), Print sequence number, Sales order line identifier → Name, Print sequence number, Sales order line identifier → external key column, Print sequence number, Sales order line identifier → Commercient external key (custom field) |
| Sage 200 UK Invoice header | Commercient Sage 200 UK Invoice Header object | 52 | Customer identifier → Sage 200 UK customer (related record), Customer identifier → Account, Document status identifier → Document status (custom field), Invoice or credit type identifier → Invoice or credit type name (custom field), Invoice or credit identifier → Name |
| Sage 200 UK Invoice detail | Commercient Sage 200 UK Invoice Detail object | 25 | Print sequence number, Invoice or credit line identifier → Name, Invoice or credit identifier → invoice header (related record), Print sequence number, Invoice or credit line identifier → Commercient external key (custom field), Despatch receipt numbers → Despatch receipt numbers (Zoho field), User name → User name (Zoho field) |

## 6. Community templates

The catalogue carries 11 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 11
- Default operations: insert on 11, update on 11, delete on 11
- Marked as circular sync: 0
- Licence groups they span: 4
- Destination objects: Accounts, Commercient Sage 200 UK Address object, Commercient Sage 200 UK
  Customers object, Commercient Sage 200 UK Invoice Detail object, Commercient Sage 200 UK Invoice
  Header object, Commercient Sage 200 UK Item object, Commercient Sage 200 UK Sales Order Header
  object, Commercient Sage 200 UK Sales Order Detail object, Commercient Sage 200 UK Warehouse
  object, Commercient Sage 200 UK Warehouse Item object, Contacts
- Object display names: Accounts, Contacts, Sage 200 UK Address, Sage 200 UK Customers, Sage 200 UK
  Invoice detail, Sage 200 UK Invoice header, Sage 200 UK Item, Sage 200 UK Sales Order Header, Sage
  200 UK Sales order detail, Sage 200 UK Warehouse, Sage 200 UK Warehouse Item
- Template groups: Account, CRM Order and Line

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-zohocrm/erps/sage-200-uk`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Sage 200 UK → Zoho CRM templates set up. dlake-crmpro-zohocrm is the destination skill
this page sits under: its own text is the authority for the Zoho CRM conventions that hold across
every ERP, and its ERP table lists this page alongside every sibling ERP page for this destination.
For the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-zohocrm/erps/sage-200-uk`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
