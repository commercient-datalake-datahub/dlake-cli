---
name: dlake-crmpro-salesforce/erps/sage-500
kind: erp-summary
description: >-
  Use it when standing up or reading a Sage 500 → Salesforce template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-salesforce, the destination skill this page is a child of, which
  carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Sage 500: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/sage-500` (or `list_skills`) against the
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
| **Sage 500 Ship Methods** | The templates push Commercient Sage 500 Shipping Method Managed Custom Object to Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Sage 500 Shipping Method Managed Custom Object | list validation values, localized text strings, shipping methods |
| **Sage 500 Customer Sales Hist** | The templates push Commercient Sage 500 Customer Sales History Managed Custom Object to Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Sage 500 Customer Sales History Managed Custom Object | customer sales history |
| **Sage 500 Unit of Measure** | The templates push Commercient Sage 500 Unit of Measure Managed Custom Object to Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Sage 500 Unit of Measure Managed Custom Object | list validation values, localized text strings, units of measure |
| **GET USER** | The templates push users to Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | users | — |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Contact, Commercient Sage 500 Customer Managed Custom Object, Commercient Sage 500 Salesperson Managed Custom Object, Commercient Sage 500 Sales Team Managed Custom Object, Commercient Sage 500 Sales Team Member Managed Custom Object | customers, customer addresses, addresses, contact records, list validation values, localized text strings |
| **Customer Class Details** | Your ERP system divides your Customers into groupings, or 'classes'. New records are created and existing ones updated; none are deleted. | Commercient Sage 500 Customer Class Managed Custom Object | list validation values, localized text strings, customer classes, staged customer classes, free on board terms, payment terms |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created and existing ones updated; none are deleted. | Commercient Sage 500 Address Managed Custom Object, Commercient Sage 500 Customer Address Managed Custom Object | addresses, customers, list validation values, localized text strings, customer addresses, commission plans |
| **Product** | The templates push Commercient Sage 500 Warehouse Managed Custom Object, Commercient Sage 500 Item Managed Custom Object, Commercient Sage 500 Inventory Managed Custom Object to Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Sage 500 Warehouse Managed Custom Object, Commercient Sage 500 Item Managed Custom Object, Commercient Sage 500 Inventory Managed Custom Object, Product | list validation values, localized text strings, warehouses, items |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created and existing ones updated; none are deleted. | Commercient Sage 500 Invoice Managed Custom Object, Commercient Sage 500 Invoice Detail Managed Custom Object | list validation values, localized text strings, invoices, customers, batch logs, addresses |
| **Pricebook** | The templates push Price book entry, Price book entry to Salesforce. New records are created and existing ones updated; none are deleted. | Price book entry | items |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created and existing ones updated; none are deleted. | Commercient Sage 500 Sales Order Managed Custom Object, Commercient Sage 500 Sales Order Line Managed Custom Object | list validation values, localized text strings, sales orders, customers, sales teams |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| GET USER | users | — | 0 |
| Sage 500 Address | Commercient Sage 500 Address Managed Custom Object | Commercient external key | 1 |
| Sage 500 Salesperson | Commercient Sage 500 Salesperson Managed Custom Object | Commercient external key | 2 |
| Account | Account | Commercient AR customer code | 3 |
| Sage 500 Customer | Commercient Sage 500 Customer Managed Custom Object | Commercient external key | 4 |
| Customer Reverse Lookup Account | Account | Commercient AR customer code | 5 |
| Sage 500 Customer Address | Commercient Sage 500 Customer Address Managed Custom Object | Commercient external key | 6 |
| Sage 500 Ship Methods | Commercient Sage 500 Shipping Method Managed Custom Object | Commercient external key | 7 |
| Contact | Contact | Commercient external key | 7 |
| Sage 500 Customer Class | Commercient Sage 500 Customer Class Managed Custom Object | Commercient external key | 8 |
| Sage 500 Customer Sales Hist | Commercient Sage 500 Customer Sales History Managed Custom Object | Commercient external key | 9 |
| Sage 500 Sales Team | Commercient Sage 500 Sales Team Managed Custom Object | Commercient external key | 10 |
| Sage 500 Sales Team Member | Commercient Sage 500 Sales Team Member Managed Custom Object | Commercient external key | 11 |
| Sage 500 Warehouse | Commercient Sage 500 Warehouse Managed Custom Object | Commercient external key | 12 |
| Product | Product | Commercient external key (earlier package) | 13 |
| Create Standard Price Book | Price book entry | External key (custom field) | 14 |
| Update Standard Pricebook | Price book entry | External key (custom field) | 15 |
| Sage 500 Item | Commercient Sage 500 Item Managed Custom Object | Commercient external key | 16 |
| Item Reverse Lookup Product | Product | Commercient external key (earlier package) | 17 |
| Sage 500 Unit of Measure | Commercient Sage 500 Unit of Measure Managed Custom Object | Commercient external key | 18 |
| Sage 500 Inventory | Commercient Sage 500 Inventory Managed Custom Object | Commercient external key | 19 |
| Sage 500 Sales order | Commercient Sage 500 Sales Order Managed Custom Object | Commercient external key | 20 |
| Sage 500 Sales order line | Commercient Sage 500 Sales Order Line Managed Custom Object | Commercient external key | 21 |
| Sage 500 Invoice | Commercient Sage 500 Invoice Managed Custom Object | Commercient external key | 22 |
| Sage 500 Invoice Detail | Commercient Sage 500 Invoice Detail Managed Custom Object | Commercient external key | 23 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| address feed | — | addresses, customers |
| salesperson feed | insert + update | list validation values, localized text strings, salespeople |
| account feed | — | customers, customer addresses, addresses |
| customer feed | insert + update | list validation values, localized text strings, customers, customer addresses, addresses |
| customer account lookup feed | insert + update | customers |
| customer address feed | insert + update | list validation values, localized text strings, customer addresses, customers, addresses |
| shipping method feed | insert + update | list validation values, localized text strings, shipping methods |
| contact feed | insert + update | contact records, customers |
| customer class feed | insert + update | list validation values, localized text strings, customer classes, staged customer classes, free on board terms |
| customer sales history feed | insert + update | customer sales history |
| sales team feed | insert + update | sales teams |
| sales team member feed | insert + update | list validation values, localized text strings, sales team members |
| warehouse feed | insert + update | list validation values, localized text strings, warehouses |
| product feed | insert + update | items, warehouses, item descriptions |
| standard price book feed | insert + update | items |
| standard price book feed (changes) | insert + update | items |
| item feed | insert + update | list validation values, localized text strings, items, warehouses |
| item product lookup feed | insert + update | items |
| unit of measure feed | insert + update | list validation values, localized text strings, units of measure |
| inventory feed | insert + update | list validation values, localized text strings, inventory records, warehouses, inventory bin lists |
| sales order feed | insert + update | list validation values, localized text strings, sales orders, customers |
| sales order line feed | insert + update | list validation values, localized text strings, sales orders, customers |
| invoice feed | insert + update | list validation values, localized text strings, invoices, customers, batch logs |
| invoice line feed | insert + update | list validation values, localized text strings, invoice lines, invoices, items |

## 4. Order of work

The templates set run sequence from 0 to 23. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 0 — GET USER
- 1 — Sage 500 Address
- 2 — Sage 500 Salesperson
- 3 — Account
- 4 — Sage 500 Customer
- 5 — Customer Reverse Lookup Account
- 6 — Sage 500 Customer Address
- 7 — Sage 500 Ship Methods, Contact
- 8 — Sage 500 Customer Class
- 9 — Sage 500 Customer Sales Hist
- 10 — Sage 500 Sales Team
- 11 — Sage 500 Sales Team Member
- 12 — Sage 500 Warehouse
- 13 — Product
- 14 — Create Standard Price Book
- 15 — Update Standard Pricebook
- 16 — Sage 500 Item
- 17 — Item Reverse Lookup Product
- 18 — Sage 500 Unit of Measure
- 19 — Sage 500 Inventory
- 20 — Sage 500 Sales order
- 21 — Sage 500 Sales order line
- 22 — Sage 500 Invoice
- 23 — Sage 500 Invoice Detail

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- customer sales history feed reads customer sync output, customer address sync output, customer
  class sync output
- customer account lookup feed reads customer sync output
- contact feed reads account sync output
- customer feed reads account sync output, address sync output, salesperson sync output
- customer class feed reads salesperson sync output
- customer address feed reads customer sync output, account sync output, address sync output,
  salesperson sync output
- item feed reads product sync output, unit of measure sync output, warehouse sync output
- inventory feed reads warehouse sync output, item sync output
- invoice feed reads account sync output, customer sync output, address sync output, shipping method
  sync output
- invoice line feed reads invoice sync output, address sync output, item sync output
- standard price book feed reads product sync output
- standard price book feed (changes) reads product sync output
- item product lookup feed reads item sync output
- sales order feed reads account sync output, customer sync output, address sync output, shipping
  method sync output, salesperson sync output
- sales order line feed reads sales order sync output, address sync output, item sync output
- salesperson feed reads address sync output
- sales team member feed reads sales team sync output, salesperson sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Sage 500 Address | Commercient Sage 500 Address Managed Custom Object | 20 | returned address key → Commercient external key, returned address key → Commercient name, Address line 1 → Commercient address line 1, Address line 2 → Commercient address line 2, Address line 3 → Commercient address line 3 |
| Sage 500 Salesperson | Commercient Sage 500 Salesperson Managed Custom Object | 18 | Salesperson key → Commercient external key, returned address key → Commercient address (related record), Company identifier → Company identifier, CRM user identifier → CRM user identifier, Email address → Email address |
| Account | Account | 13 | Customer identifier → Commercient AR customer code, Customer name → Name, Address line 1, Address line 2, Address line 3 → Billing street, City → Billing city, State code → Billing state |
| Sage 500 Customer | Commercient Sage 500 Customer Managed Custom Object | 81 | Customer identifier → Commercient external key, the linked Salesforce record → Commercient account, the linked Salesforce record → Commercient address (related record), the linked Salesforce record → Commercient salesperson (related record), Active status → Commercient active status |
| Customer Reverse Lookup Account | Account | 2 | Customer identifier → Commercient AR customer code, the linked Salesforce record → Commercient Sage 500 customer record (related record) |
| Sage 500 Customer Address | Commercient Sage 500 Customer Address Managed Custom Object | 52 | Back order price → Commercient back order price value, Carrier billing method → Commercient carrier billing method value, Create type → Commercient create type value, Freight method → Commercient freight method value, Price base → Commercient price base value |
| Sage 500 Ship Methods | Commercient Sage 500 Shipping Method Managed Custom Object | 23 | Calculation method → Commercient calculation method value, Shipping method key → Commercient external key, Calculation unit → Commercient calculation unit, Company identifier → Commercient company identifier, Shipping method description → Commercient shipping method description |
| Contact | Contact | 7 | Commercient external key → Commercient external key, Family name → Family name, Email → Email, account lookup → account lookup, Title → Title |
| Sage 500 Customer Class | Commercient Sage 500 Customer Class Managed Custom Object | 63 | Billing type → Commercient billing type value, Create type → Commercient create type value, Credit limit aging category → Commercient credit limit aging category value, Free on board code → Commercient free on board identifier, Payment terms identifier → Commercient payment terms identifier |
| Sage 500 Customer Sales Hist | Commercient Sage 500 Customer Sales History Managed Custom Object | 19 | Customer class key, Customer key, Posting date, Shipping customer address key → Commercient external key, the linked Salesforce record → customer record (related record), the linked Salesforce record → customer address (related record), the linked Salesforce record → Customer class, Fiscal year → Fiscal year |
| Sage 500 Sales Team | Commercient Sage 500 Sales Team Managed Custom Object | 7 | Sales team key → Commercient external key, Company identifier → Commercient company identifier, Sales team identifier → Commercient sales team identifier, Description → Name, Description → Description |
| Sage 500 Sales Team Member | Commercient Sage 500 Sales Team Member Managed Custom Object | 11 | Sales team member key → Commercient external key, Sales team member key → Commercient name, Sales team key → Commercient sales team, Salesperson key → Commercient salesperson (related record), Can process team transactions → Commercient can process team transactions |
| Sage 500 Warehouse | Commercient Sage 500 Warehouse Managed Custom Object | 68 | Warehouse key → Commercient external key, Company identifier → Commercient company identifier, Description → Commercient description, Immediate pick list printer destination → Commercient immediate pick list printer destination, Immediate invoice printer destination → Commercient immediate invoice printer destination |
| Product | Product | 6 | Item identifier → Commercient external key (earlier package), Item identifier → Product code, Description → Commercient default warehouse, Short description → Name, Long description → Description |
| Create Standard Price Book | Price book entry | 3 | Item identifier → External key (custom field), Item identifier → product lookup, Standard price → Unit price |
| Update Standard Pricebook | Price book entry | 3 | Item identifier → External key (custom field), Active → Active, Standard price → Unit price |
| Sage 500 Item | Commercient Sage 500 Item Managed Custom Object | 64 | Commodity code identifier → Commercient commodity code, Adjusted quantity rounding method → Commercient adjusted quantity rounding method, Create type → Commercient create type, Item type → Commercient item type, Status → Commercient status |
| Item Reverse Lookup Product | Product | 2 | Item identifier → Commercient external key (earlier package), the linked Salesforce record → Commercient Sage 500 Item Managed Custom Object |
| Sage 500 Unit of Measure | Commercient Sage 500 Unit of Measure Managed Custom Object | 8 | Measure type → Commercient measure type value, Unit of measure key → Commercient external key, Company identifier → Commercient company identifier, Unit of measure identifier → Commercient unit of measure identifier, Measure type → Commercient measure type |
| Sage 500 Inventory | Commercient Sage 500 Inventory Managed Custom Object | 69 | Create type → Commercient create type value, Pick method → Commercient pick method value, Reorder method → Commercient reorder method value, Status → Commercient status value, Item key, Warehouse key → Commercient external key |
| Sage 500 Sales order | Commercient Sage 500 Sales Order Managed Custom Object | 92 | Create type → Commercient create type value, Default delivery method → Commercient default delivery method value, Freight method → Commercient freight method value, Status → Commercient status value, Customer identifier → Commercient customer identifier |
| Sage 500 Sales order line | Commercient Sage 500 Sales Order Line Managed Custom Object | 72 | Sales order line key → Commercient external key, Name → Commercient name, Sales order record key → Commercient sales order (related record), Billing address key → Commercient billing address (related record), Default shipping address key → Commercient default shipping address |
| Sage 500 Invoice | Commercient Sage 500 Invoice Managed Custom Object | 69 | Create type → Commercient create type value, Source module → Commercient source module value, Status → Commercient status value, Invoice key → Commercient external key, Invoice key → Commercient name |
| Sage 500 Invoice Detail | Commercient Sage 500 Invoice Detail Managed Custom Object | 58 | Commission base → Commercient commission base value, Return type → Commercient return type value, Customer identifier → Commercient customer identifier, Customer class identifier → Commercient customer class identifier, Customer class name → Commercient customer class name |

## 6. Community templates

The catalogue carries 135 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 135
- Default operations: insert on 135, update on 135, delete on 135
- Marked as circular sync: 3
- Licence groups they span: 14
- Destination objects: Account, Product, Commercient Sage 500 Sales Order Managed Custom Object,
  Commercient Sage 500 Customer Managed Custom Object, Commercient Sage 500 Address Managed Custom
  Object, Commercient Sage 500 Invoice Managed Custom Object, Commercient Sage 500 Invoice Detail
  Managed Custom Object, Commercient Sage 500 Item Managed Custom Object, Commercient Sage 500 Sales
  Order Line Managed Custom Object, Contact, Price book entry, Commercient Sage 500 Customer Address
  Managed Custom Object, Commercient Sage 500 Salesperson Managed Custom Object, Commercient Sage
  500 Warehouse Managed Custom Object, Commercient Sage 500 Inventory Managed Custom Object,
  Commercient Sage 500 Shipment Managed Custom Object, Commercient Sage 500 Shipping Method Managed
  Custom Object, Commercient Sage 500 Customer Class Managed Custom Object, Commercient Sage 500
  Customer Sales History Managed Custom Object, Commercient Sage 500 Shipment Line Managed Custom
  Object, 13 more and 17 custom objects
- Object display names: Account, Customer Reverse Lookup Account, Sage 500 Address, Sage 500
  Customer, Sage 500 Sales order, Item Reverse Lookup Product, Product, Sage 500 Invoice, Sage 500
  Invoice Detail, Sage 500 Item, Sage 500 Sales order line, Sage 500 Customer Address, 66 more and
  19 further templates
- Template groups: Account, Product, Customer Multi Ship Addresses, Sales order, Invoice, CRM
  Opportunity and Line, Opportunity, Pricebook

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/sage-500`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Sage 500 → Salesforce templates set up. dlake-crmpro-salesforce is the destination skill
this page sits under: its own text is the authority for the Salesforce conventions that hold across
every ERP, and its ERP table lists this page alongside every sibling ERP page for this destination.
For the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/sage-500`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
