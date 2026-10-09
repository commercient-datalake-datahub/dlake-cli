---
name: dlake-crmpro-salesforce/erps/sage-200-us
kind: erp-summary
description: >-
  Use it when standing up or reading a Sage 200 US → Salesforce template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-salesforce, the destination skill this page is a child of, which
  carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Sage 200 US: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/sage-200-us` (or `list_skills`) against the
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
| **Sage 200 US Vendor Master** | ERP AP vendor data becomes AP vendor (custom object) in Salesforce. New records are created and existing ones updated; none are deleted. | AP vendor (custom object) | vendors |
| **Location** | ERP location data becomes Commercient Location Managed Custom Object in Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Location Managed Custom Object | locations |
| **Sage 200 US Work order Header** | ERP AR customer, sales order master, salesperson master data becomes Work order master (custom object) in Salesforce. New records are created and existing ones updated; none are deleted. | Work order master (custom object) | work orders, sales order headers, customers, salespeople |
| **Sage 200 US Work order Line** | ERP Inventory item, work order master, work order transaction data becomes Work order line (custom object) in Salesforce. New records are created and existing ones updated; none are deleted. | Work order line (custom object) | work order lines, work orders, inventory items |
| **GET USER** | The templates push users to Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | users | — |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Contact, Commercient Salesperson Managed Custom Object | customers, customer shipping addresses, salespeople |
| **AR Invoice Payments** | Commercient Syncs the payment details that are held in the receivables file and held against an invoice in the ERP. The amount received from a customer, the date of payment, the method of payment, whether partial or full payment, etc New records are created and existing ones updated; none are deleted. | Sage 200 US invoice payments (custom object) | cash receipts, AR invoice headers, customers |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created and existing ones updated; none are deleted. | Commercient Customer Shipping Address Managed Custom Object, Commercient AR Address Managed Custom Object | customer shipping addresses, customers, salespeople, AR addresses, AR invoice headers |
| **Product** | ERP item location, Inventory item, location data becomes Commercient Item Location Managed Custom Object, Commercient Inventory Item Managed Custom Object, Bill of materials header (custom object) in Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Item Location Managed Custom Object, Commercient Inventory Item Managed Custom Object, Bill of materials header (custom object), Bill of materials line (custom object), Product | item locations, inventory items, from, to, locations, bills of materials |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created and existing ones updated; none are deleted. | Commercient Invoice Header Managed Custom Object, Commercient Invoice Detail Managed Custom Object | AR invoice headers, sales order headers, customers, AR invoice lines, inventory items |
| **Pricebook** | ERP Inventory item data becomes Price book entry in Salesforce. New records are created and existing ones updated; none are deleted. | Price book entry | inventory items |
| **Purchase Order** | ERP purchase order master, AP vendor, location data becomes Purchase order master (custom object) in Salesforce. New records are created and existing ones updated; none are deleted. | Purchase order master (custom object) | purchase order headers, vendors, locations, salespeople |
| **Purchase Order Line** | ERP purchase order transaction, purchase order master, Inventory item data becomes Purchase order line (custom object) in Salesforce. New records are created and existing ones updated; none are deleted. | Purchase order line (custom object) | purchase order lines, purchase order headers, inventory items |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created and existing ones updated; none are deleted. | Commercient Customer Master Managed Custom Object, Commercient Sales Order Header Managed Custom Object, Commercient Sales Order Detail Managed Custom Object | customers, salespeople, sales order headers, locations, sales order lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| GET USER | users | — | 0 |
| Sage 200 US Salesperson | Commercient Salesperson Managed Custom Object | Commercient external key (package 7) | 1 |
| Sage 200 US Salesperson | Commercient Salesperson Managed Custom Object | Commercient external key (package 7) | 1 |
| Account | Account | Commercient AR customer code | 2 |
| Sage 200 US Customer Master | Commercient Customer Master Managed Custom Object | Commercient external key (package 7) | 3 |
| Customer To Account Reverse Lookup | Account | Commercient AR customer code | 4 |
| Sage 200 US Vendor Master | AP vendor (custom object) | External key (custom field) | 5 |
| Contact sync | Contact | Commercient external key | 6 |
| Sage 200 US Customer Ship To Address | Commercient Customer Shipping Address Managed Custom Object | Commercient external key (package 7) | 7 |
| Product | Product | Commercient external key (earlier package) | 8 |
| Location | Commercient Location Managed Custom Object | Commercient external key (package 7) | 9 |
| Item Location | Commercient Item Location Managed Custom Object | Commercient external key (package 7) | 10 |
| Item Master | Commercient Inventory Item Managed Custom Object | Commercient external key (package 7) | 11 |
| Create Standard Price Book | Price book entry | External key (custom field) | 12 |
| Update Standard Price Book | Price book entry | External key (custom field) | 13 |
| Item Master Reverse Lookup With Product | Product | Commercient external key (earlier package) | 14 |
| Sage 200 US Sales Order Header | Commercient Sales Order Header Managed Custom Object | Commercient external key (package 7) | 15 |
| Sage 200 US Sales Order Detail | Commercient Sales Order Detail Managed Custom Object | Commercient external key (package 7) | 16 |
| Sage 200 US Invoice Header | Commercient Invoice Header Managed Custom Object | Commercient external key (package 7) | 17 |
| Sage 200 US Invoice Detail | Commercient Invoice Detail Managed Custom Object | Commercient external key (package 7) | 18 |
| Sage 200 US BOM Header | Bill of materials header (custom object) | External key (custom field) | 19 |
| Sage 200 US bill of materials line | Bill of materials line (custom object) | External key (custom field) | 20 |
| Sage 200US Invoice Payments | Sage 200 US invoice payments (custom object) | External key (custom field) | 21 |
| Sage 200 US Purchase order Header | Purchase order master (custom object) | External key (custom field) | 22 |
| Sage 200 US Purchase order Line | Purchase order line (custom object) | External key (custom field) | 23 |
| Sage 200 US Work order Header | Work order master (custom object) | External key (custom field) | 24 |
| Sage 200 US Work order Line | Work order line (custom object) | External key (custom field) | 25 |
| Sage 200 Shipping address AR address | Commercient AR Address Managed Custom Object | Commercient external key (package 7) | 26 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| salesperson feed | insert + update | salespeople |
| salesperson feed | insert + update | salespeople |
| account feed | insert + update | customers, customer shipping addresses, salespeople |
| customer feed | insert + update | customers, salespeople |
| customer account lookup feed | insert + update | customers |
| vendor feed | insert + update | vendors |
| contact feed | insert + update | customers |
| shipping address feed | insert + update | customer shipping addresses, customers, salespeople |
| product feed | insert + update | inventory items |
| location feed | insert + update | locations |
| item location feed | insert + update | item locations, inventory items, from, to, locations |
| item master feed | insert + update | inventory items |
| price book feed (new entries) | insert + update | inventory items |
| price book feed (changes) | insert + update | inventory items |
| item product lookup feed | insert + update | inventory items |
| sales order feed | insert + update | sales order headers, customers, salespeople, locations |
| sales order line feed | insert + update | sales order lines, sales order headers, inventory items |
| invoice feed | insert + update | AR invoice headers, sales order headers, customers |
| invoice line feed | insert + update | AR invoice lines, AR invoice headers, inventory items, locations |
| bill of materials feed | insert + update | bills of materials, inventory items, item locations, locations |
| bill of materials line feed | insert + update | bill of materials lines, bills of materials, inventory items, locations |
| payment history feed | insert + update | cash receipts, AR invoice headers, customers |
| purchase order feed | insert + update | purchase order headers, vendors, locations, salespeople |
| purchase order line feed | insert + update | purchase order lines, purchase order headers, inventory items |
| work order feed | insert + update | work orders, sales order headers, customers, salespeople |
| work order line feed | insert + update | work order lines, work orders, inventory items |
| AR address shipping address feed | insert + update | AR addresses, AR invoice headers, customer shipping addresses, customers |

## 4. Order of work

The templates set run sequence from 0 to 26. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 0 — GET USER
- 1 — Sage 200 US Salesperson
- 2 — Account
- 3 — Sage 200 US Customer Master
- 4 — Customer To Account Reverse Lookup
- 5 — Sage 200 US Vendor Master
- 6 — Contact sync
- 7 — Sage 200 US Customer Ship To Address
- 8 — Product
- 9 — Location
- 10 — Item Location
- 11 — Item Master
- 12 — Create Standard Price Book
- 13 — Update Standard Price Book
- 14 — Item Master Reverse Lookup With Product
- 15 — Sage 200 US Sales Order Header
- 16 — Sage 200 US Sales Order Detail
- 17 — Sage 200 US Invoice Header
- 18 — Sage 200 US Invoice Detail
- 19 — Sage 200 US BOM Header
- 20 — Sage 200 US bill of materials line
- 21 — Sage 200US Invoice Payments
- 22 — Sage 200 US Purchase order Header
- 23 — Sage 200 US Purchase order Line
- 24 — Sage 200 US Work order Header
- 25 — Sage 200 US Work order Line
- 26 — Sage 200 Shipping address AR address

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- vendor feed reads vendor account sync output; no template in this set writes vendor account sync
  output
- work order feed reads account sync output, customer sync output, salesperson sync output, sales
  order sync output
- work order line feed reads work order sync output, product sync output, item master sync output
- account feed reads salesperson sync output, user sync output
- customer account lookup feed reads customer sync output
- payment history feed reads invoice sync output, account sync output, customer sync output
- contact feed reads account sync output
- shipping address feed reads account sync output, customer sync output, salesperson sync output
- AR address shipping address feed reads customer sync output, account sync output, shipping address
  sync output, invoice sync output
- item location feed reads product sync output, item master sync output, location sync output
- item master feed reads product sync output
- bill of materials feed reads item master sync output, product sync output, location sync output
- bill of materials line feed reads bill of materials sync output, item master sync output, location
  sync output
- invoice feed reads account sync output, customer sync output, sales order sync output
- invoice line feed reads invoice sync output, product sync output, item master sync output,
  location sync output
- price book feed (new entries) reads product sync output
- price book feed (changes) reads product sync output
- item product lookup feed reads item master sync output
- purchase order feed reads account sync output, customer sync output, vendor sync output, location
  sync output, salesperson sync output
- purchase order line feed reads purchase order sync output, product sync output, item master sync
  output
- customer feed reads salesperson sync output, account sync output
- sales order feed reads account sync output, customer sync output, salesperson sync output,
  location sync output
- sales order line feed reads sales order sync output, product sync output, item master sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Sage 200 US Salesperson | Commercient Salesperson Managed Custom Object | 33 | Row identity number → Commercient external key (package 7), Salesperson code → Name, Salesperson code → Commercient salesperson code, Sales type → Commercient sales type, Sales group → Commercient sales group |
| Sage 200 US Salesperson | Commercient Salesperson Managed Custom Object | 32 | Salesperson code → Commercient salesperson code, Sales type → Commercient sales type, Sales group → Commercient sales group, ERP last name → Commercient family name, ERP first name → Commercient given name |
| Account | Account | 13 | Row identity number → Commercient AR customer code, company → Name, phone → Phone, Address 1, address line 2 property → Billing street, city property → Billing city |
| Sage 200 US Customer Master | Commercient Customer Master Managed Custom Object | 91 | Row identity number → Commercient external key (package 7), company → Name, the linked Salesforce record → Account, the linked Salesforce record → Process Pro salesperson (custom field), Customer number → Customer number |
| Customer To Account Reverse Lookup | Account | 2 | Row identity number → Commercient AR customer code, the linked Salesforce record → Commercient Process Pro customer master (related record) |
| Sage 200 US Vendor Master | AP vendor (custom object) | 73 | Row identity number → External key (custom field), company → Name, Vendor number → Vendor number, company → company, contact → contact |
| Contact sync | Contact | 12 | Row identity number → Commercient external key, contact → Family name, contact → Given name, the linked Salesforce record → account lookup, email → Email |
| Sage 200 US Customer Ship To Address | Commercient Customer Shipping Address Managed Custom Object | 47 | Row identity number → Commercient external key (package 7), company → Name, Customer number → Account, Customer number → Commercient Process Pro customer master (related record), Salesperson code → Process Pro salesperson (custom field) |
| Product | Product | 5 | Row identity number → Commercient external key (earlier package), item → Name, item → Product code, Item description → Description |
| Location | Commercient Location Managed Custom Object | 32 | Row identity number → Commercient external key (package 7), Location code → Name, Location code → Location code, Location description → Location description, Street address line 1 → Street address line 1 |
| Item Location | Commercient Item Location Managed Custom Object | 72 | Row identity number → Commercient external key (package 7), item, Location code → Name, the linked Salesforce record → Commercient product (related record), the linked Salesforce record → Commercient Process Pro location (related record), the linked Salesforce record → Commercient Process Pro item master (related record) |
| Item Master | Commercient Inventory Item Managed Custom Object | 82 | Row identity number → Commercient external key (package 7), item → Name, the linked Salesforce record → Product, item → Commercient item, Item description → Commercient item description |
| Create Standard Price Book | Price book entry | 5 | Row identity number → External key (custom field), Standard cost → Unit price, the linked Salesforce record → price book lookup, the linked Salesforce record → product lookup |
| Update Standard Price Book | Price book entry | 3 | Row identity number → External key (custom field), type → Active, Standard cost → Unit price |
| Item Master Reverse Lookup With Product | Product | 2 | Row identity number → Commercient external key (earlier package), the linked Salesforce record → Commercient Process Pro item master (related record) |
| Sage 200 US Sales Order Header | Commercient Sales Order Header Managed Custom Object | 78 | Row identity number → Commercient external key (package 7), Sales order number → Name, Customer number → Account, Customer number → Commercient Process Pro customer master (related record), Salesperson code → Process Pro salesperson (custom field) |
| Sage 200 US Sales Order Detail | Commercient Sales Order Detail Managed Custom Object | 86 | Row identity number → Commercient external key (package 7), Sales order number, transaction line number → Name, item → Product, item → Process Pro item master (custom field), Sales order number → Commercient Process Pro sales order header (related record) |
| Sage 200 US Invoice Header | Commercient Invoice Header Managed Custom Object | 73 | Row identity number → Commercient external key (package 7), Invoice number → Name, the linked Salesforce record → Account, the linked Salesforce record → Commercient Process Pro customer master (related record), Invoice number → Invoice number |
| Sage 200 US Invoice Detail | Commercient Invoice Detail Managed Custom Object | 69 | Row identity number → Commercient external key (package 7), Invoice number, transaction line number → Name, Row identity number → Commercient Process Pro invoice header (related record), item → Product, item → Process Pro item master (custom field) |
| Sage 200 US BOM Header | Bill of materials header (custom object) | 34 | Row identity number → External key (custom field), Bill of materials number → Name, the linked Salesforce record → Product (custom field), the linked Salesforce record → Sage 200 US item master (custom field, bill of materials header), Bill of materials number → Bill of materials number |
| Sage 200 US bill of materials line | Bill of materials line (custom object) | 43 | Bill of materials number → Name, Row identity number → External key (custom field), the linked Salesforce record → Sage 200 US bill of materials header (custom field), the linked Salesforce record → Sage 200 US item master (custom field, bill of materials line), the linked Salesforce record → Process Pro location (custom field) |
| Sage 200US Invoice Payments | Sage 200 US invoice payments (custom object) | 46 | Row identity number → External key (custom field), Invoice number → Name, Invoice number → Invoice number, Invoice date → Invoice date, Customer number → Customer number |
| Sage 200 US Purchase order Header | Purchase order master (custom object) | 57 | Row identity number → External key (custom field), Purchase order number → Name, Vendor number → Sage 200 US vendor master (custom field), Location code → Process Pro location (custom field), Purchase order number → Purchase order number |
| Sage 200 US Purchase order Line | Purchase order line (custom object) | 68 | Row identity number → External key (custom field), Purchase order number, transaction line number → Name, item → Product (custom field), item → Sage 200 US item master (custom field, bill of materials header), Purchase order number → Sage 200 US purchase order header (custom field) |
| Sage 200 US Work order Header | Work order master (custom object) | 43 | Row identity number → External key (custom field), Work order number → Name, the linked Salesforce record → Account (custom field), the linked Salesforce record → Sage 200 US customer master (custom field), the linked Salesforce record → Sage 200 US salesperson (custom field) |
| Sage 200 US Work order Line | Work order line (custom object) | 66 | Row identity number → External key (custom field), Work order number, transaction line number → Name, item → Product (custom field), item → Sage 200 US item master (custom field, work order line), Work order number → Sage 200 US work order header (custom field) |
| Sage 200 Shipping address AR address | Commercient AR Address Managed Custom Object | 28 | Row identity number → Commercient external key (package 7), company → Commercient name, Customer number → Commercient customer number (second field), Invoice number → Process Pro invoice header (custom field), Customer number → Process Pro customer master (custom field) |

## 6. Community templates

The catalogue carries 28 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 28
- Default operations: insert on 28, update on 28, delete on 28
- Marked as circular sync: 0
- Licence groups they span: 11
- Destination objects: Account, Contact, Price book entry, Product, AP vendor (custom object),
  Commercient AR Address Managed Custom Object, Commercient Customer Shipping Address Managed Custom
  Object, Commercient Customer Master Managed Custom Object, Commercient Invoice Header Managed
  Custom Object, Commercient Invoice Detail Managed Custom Object, Commercient Item Location Managed
  Custom Object, Commercient Inventory Item Managed Custom Object, Commercient Location Managed
  Custom Object, Commercient Sales Order Header Managed Custom Object, Commercient Salesperson
  Managed Custom Object, Commercient Sales Order Detail Managed Custom Object, Bill of materials
  line (custom object), Bill of materials header (custom object), Purchase order master (custom
  object), Purchase order line (custom object) and 4 more
- Object display names: Account, Contact sync, Create Standard Price Book, Customer To Account
  Reverse Lookup, Get Contacts, Item Location, Item Master, Item Master Reverse Lookup With Product,
  Location, Product, Sage 200US Invoice Payments, Sage 200 Shipping address AR address and 16 more
- Template groups: Product, Account, Sales order, Invoice, Customer Multi Ship Addresses, Purchase
  Order

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/sage-200-us`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Sage 200 US → Salesforce templates set up. dlake-crmpro-salesforce is the destination
skill this page sits under: its own text is the authority for the Salesforce conventions that hold
across every ERP, and its ERP table lists this page alongside every sibling ERP page for this
destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/sage-200-us`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
