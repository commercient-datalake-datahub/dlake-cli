---
name: dlake-crmpro-salesforce/erps/sage-100-us-sage100us
kind: erp-summary
description: >-
  Use it when standing up or reading a Sage 100 US (Sage 100 US) → Salesforce template set, when
  deciding which templates to import and activate, or when a run completes without pushing records
  and the answer is in the view or the configuration row. It extends dlake-crmpro, which covers
  operating CRMPro generally, and dlake-crmpro-salesforce, the destination skill this page is a
  child of, which carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Sage 100 US (Sage 100 US): what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/sage-100-us-sage100us` (or `list_skills`)
against the Commercient admin plane. Existing customers who need access or help: contact
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
| **GET USER** | The templates push users to Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | users | — |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created and existing ones updated; none are deleted. | Account, Commercient Terms Code Managed Custom Object, Contact, Commercient AR Customer Managed Custom Object, Commercient Salesperson Managed Custom Object | customers, shipping addresses, terms codes, customer contacts, salespeople |
| **AR Invoice Payments** | Commercient Syncs the payment details that are held in the receivables file and held against an invoice in the ERP. The amount received from a customer, the date of payment, the method of payment, whether partial or full payment, etc New records are created and existing ones updated; none are deleted. | Commercient Payment History Managed Custom Object | payment history |
| **CRM Opportunity and Line** | ERP sales order header, sales order detail data becomes Opportunity, Opportunity line item in Salesforce. New records are created and existing ones updated; none are deleted. | Opportunity, Opportunity line item | sales order headers, sales order lines |
| **CRM Ownership** | The templates push User to Salesforce. | User | — |
| **CRM Quote and Line** | The templates push Quote, Quote line item to Salesforce. New records are created and existing ones updated; none are deleted. | Quote, Quote line item | sales order headers, sales order lines |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created and existing ones updated; none are deleted. | Commercient Ship-To Address Managed Custom Object | shipping addresses |
| **Product** | ERP common information extended description, Common information item, inventory management item warehouse data becomes Commercient Inventory Item Managed Custom Object, Commercient Item Warehouse Managed Custom Object, Commercient Item Transaction History Managed Custom Object in Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Inventory Item Managed Custom Object, Commercient Item Warehouse Managed Custom Object, Commercient Item Transaction History Managed Custom Object, Product | items, extended item descriptions, process configuration, item warehouse quantities, item transaction history |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created and existing ones updated; none are deleted. | Commercient Open Invoice Managed Custom Object, Commercient Invoice History Header Managed Custom Object, Commercient Invoice History Detail Managed Custom Object | invoice headers, invoice history headers, invoice history lines |
| **Pricebook** | ERP Common information item data becomes Price book entry in Salesforce. New records are created and existing ones updated; none are deleted. | Price book entry | items |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created and existing ones updated; none are deleted. | Commercient Sales Order Header Managed Custom Object, Commercient Sales Order Line Managed Custom Object, Commercient Sales Order History Header Managed Custom Object, Commercient Sales Order History Line Managed Custom Object | sales order headers, sales order lines, process configuration, sales order history headers, sales order history lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| GET USER | users | — | 0 |
| Get Users | User | Commercient salesperson code | 0 |
| AR terms | Commercient Terms Code Managed Custom Object | Commercient external key | 1 |
| Sales Person | Commercient Salesperson Managed Custom Object | Commercient external key | 2 |
| Account | Account | Commercient AR customer code | 3 |
| Customer | Commercient AR Customer Managed Custom Object | Commercient external key | 4 |
| Ship to address | Commercient Ship-To Address Managed Custom Object | Commercient external key | 5 |
| Account customer lookup | Account | Commercient AR customer code | 6 |
| Inventory item | Commercient Inventory Item Managed Custom Object | Commercient external keys | 7 |
| Product | Product | Commercient external key (earlier package) | 8 |
| Item product Reverse lookup | Commercient Inventory Item Managed Custom Object | Commercient external keys | 9 |
| Sales order | Commercient Sales Order Header Managed Custom Object | Commercient sales order number | 10 |
| Sales order line | Commercient Sales Order Line Managed Custom Object | Commercient external key | 11 |
| Sales order History Header | Commercient Sales Order History Header Managed Custom Object | Commercient external key | 12 |
| Sales order History Line | Commercient Sales Order History Line Managed Custom Object | Commercient external key | 13 |
| Open Invoice | Commercient Open Invoice Managed Custom Object | Commercient external key | 14 |
| Invoice Header | Commercient Invoice History Header Managed Custom Object | Commercient external key | 15 |
| Invoice Details | Commercient Invoice History Detail Managed Custom Object | Commercient external key | 16 |
| Transcation Payment History | Commercient Payment History Managed Custom Object | Commercient external key | 17 |
| Item Warehouse | Commercient Item Warehouse Managed Custom Object | Commercient external key | 18 |
| Create Standard Price Book | Price book entry | External key (custom field) | 19 |
| Update Standard Price Book | Price book entry | External key (custom field) | 20 |
| Contact | Contact | Commercient external key | 21 |
| Item Transaction History | Commercient Item Transaction History Managed Custom Object | Commercient external key | 22 |
| Opportunity Header | Opportunity | External key (custom field) | 23 |
| Opportunity Line | Opportunity line item | External key (custom field) | 31 |
| Quote header | Quote | External key (custom field) | 32 |
| Quote line | Quote line item | External key (custom field) | 33 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| terms code feed | insert + update | terms codes |
| salesperson feed | insert + update | salespeople |
| account feed | insert + update | customers, shipping addresses |
| customer feed | insert only | customers |
| shipping address feed | insert + update | shipping addresses |
| customer account lookup feed | insert + update | customers |
| inventory item feed | insert + update | items, extended item descriptions |
| product feed | insert + update | items |
| product item reverse lookup feed | insert + update | items, process configuration |
| sales order feed | insert + update | sales order headers |
| sales order line feed | insert + update | sales order lines, sales order headers, process configuration |
| sales order history feed | insert + update | sales order history headers |
| sales order history line feed | insert + update | sales order history lines, sales order history headers |
| open invoice feed | insert + update | invoice headers |
| invoice history feed | insert + update | invoice history headers |
| invoice history line feed | insert + update | invoice history lines, invoice history headers |
| payment history feed | insert + update | payment history |
| item warehouse feed | insert + update | item warehouse quantities |
| price book feed (new entries) | insert only | items |
| price book feed (changes) | insert only | items |
| contact feed | insert + update | customer contacts |
| item transaction history feed | insert + update | item transaction history |
| Sage 100 US opportunity feed | insert + update | sales order headers |
| quote line feed | insert only | sales order lines, sales order headers |
| quote feed | insert + update | sales order headers |
| quote line feed | insert only | sales order lines, sales order headers |

## 4. Order of work

The templates set run sequence from 0 to 33. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 0 — GET USER, Get Users
- 1 — AR terms
- 2 — Sales Person
- 3 — Account
- 4 — Customer
- 5 — Ship to address
- 6 — Account customer lookup
- 7 — Inventory item
- 8 — Product
- 9 — Item product Reverse lookup
- 10 — Sales order
- 11 — Sales order line
- 12 — Sales order History Header
- 13 — Sales order History Line
- 14 — Open Invoice
- 15 — Invoice Header
- 16 — Invoice Details
- 17 — Transcation Payment History
- 18 — Item Warehouse
- 19 — Create Standard Price Book
- 20 — Update Standard Price Book
- 21 — Contact
- 22 — Item Transaction History
- 23 — Opportunity Header
- 31 — Opportunity Line
- 32 — Quote header
- 33 — Quote line

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- account feed reads salesperson sync output, user sync output
- customer account lookup feed reads customer sync output
- payment history feed reads account sync output, customer sync output, invoice history sync output
- contact feed reads account sync output, customer sync output
- Sage 100 US opportunity feed reads customer sync output, account sync output, salesperson sync
  output
- quote line feed reads quote sync output (generic name), product sync output, quote line sync
  output (generic name)
- quote feed reads customer sync output, account sync output, opportunity sync output (generic
  name), salesperson sync output
- quote line feed reads quote sync output (generic name), product sync output
- customer feed reads account sync output, salesperson sync output
- shipping address feed reads account sync output, customer sync output, salesperson sync output
- product item reverse lookup feed reads product sync output
- item warehouse feed reads product sync output, inventory item sync output
- item transaction history feed reads account sync output, customer sync output, shipping address
  sync output, product sync output, inventory item sync output
- open invoice feed reads account sync output, customer sync output, salesperson sync output
- invoice history feed reads account sync output, customer sync output, salesperson sync output
- invoice history line feed reads invoice history sync output, account sync output, customer sync
  output, salesperson sync output
- price book feed (new entries) reads product sync output
- price book feed (changes) reads product sync output
- product feed reads inventory item sync output
- sales order feed reads account sync output, customer sync output, salesperson sync output
- sales order line feed reads sales order sync output, account sync output, customer sync output,
  salesperson sync output
- sales order history feed reads account sync output, customer sync output, salesperson sync output,
  Sage 100 UK account sync output; no template in this set writes Sage 100 UK account sync output
- sales order history line feed reads sales order history sync output, account sync output, customer
  sync output, salesperson sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| AR terms | Commercient Terms Code Managed Custom Object | 2 | Terms code → Commercient external key, Terms code description → Name |
| Sales Person | Commercient Salesperson Managed Custom Object | 2 | Salesperson division, Salesperson number → Commercient external key, Salesperson name → Name |
| Account | Account | 15 | AR division number, Customer number → Commercient AR customer code, Customer name → Name, Salesperson division, Salesperson number → Commercient Salesperson Managed Custom Object, Telephone number → Phone, Address line 1, Address line 2, Address line 3 → Billing street |
| Customer | Commercient AR Customer Managed Custom Object | 96 | AR division number,Customer number → Commercient external key, Customer name → Name, the linked Salesforce record → Account, the linked Salesforce record → Salesperson, AR division number → AR division number |
| Ship to address | Commercient Ship-To Address Managed Custom Object | 5 | AR division number, Customer number, returned shipping address code → Commercient external key, Shipping name → Name, AR division number, Customer number → Account, AR division number, Customer number → Sage 100 customer records (custom field), Salesperson number → Salesperson |
| Account customer lookup | Account | 2 | AR division number, Customer number → Commercient AR customer code, the linked Salesforce record → Commercient Sage customer record |
| Inventory item | Commercient Inventory Item Managed Custom Object | 3 | Item code → Commercient external keys, Item code → Name, Extended description text → Extended description text (custom field) |
| Product | Product | 6 | Item code → Commercient external key (earlier package), Item code → Name, Item code → Product code, Item description → Description, Inactive item → Active |
| Item product Reverse lookup | Commercient Inventory Item Managed Custom Object | 2 | Item code → Commercient external keys, the linked Salesforce record → Commercient product |
| Sales order | Commercient Sales Order Header Managed Custom Object | 112 | AR division number, returned sales order number → Commercient sales order number, returned sales order number → Name, AR division number, Customer number → Account, AR division number, Customer number → Sage 100 customer record (custom field), Salesperson division, Salesperson number → Salesperson |
| Sales order line | Commercient Sales Order Line Managed Custom Object | 5 | returned sales order number,Line key → Commercient external key, the linked Salesforce record → Commercient Sage 100 open sales order (related record), the linked Salesforce record → Account, the linked Salesforce record → Commercient Sage 100 customer records, the linked Salesforce record → Commercient Salesperson Managed Custom Object |
| Sales order History Header | Commercient Sales Order History Header Managed Custom Object | 97 | AR division number,returned sales order number → Commercient external key, returned sales order number → Name, AR division number,Customer number → Account, AR division number,Customer number → Customer, Salesperson division,Salesperson number → Sage 100 salesperson (custom field) |
| Sales order History Line | Commercient Sales Order History Line Managed Custom Object | 6 | returned sales order number, Sequence number → Commercient external key, returned sales order number, Sequence number → Name, the linked Salesforce record → Sales order history header (custom field), Customer number → Account (custom field), Customer number → Sage 100 customer records (custom field) |
| Open Invoice | Commercient Open Invoice Managed Custom Object | 5 | Invoice number,Invoice type → Commercient external key, Invoice number,Invoice type → Name, AR division number,Customer number → Account, AR division number,Customer number → Sage 100 customer records (custom field), Salesperson division,Salesperson number → Sage 100 salesperson (custom field) |
| Invoice Header | Commercient Invoice History Header Managed Custom Object | 5 | Invoice number, Header sequence number → Commercient external key, Invoice number, Header sequence number → Name, AR division number, Customer number → Account, AR division number, Customer number → Sage 100 customer records (custom field), Salesperson division, Salesperson number → Sage 100 salesperson (custom field) |
| Invoice Details | Commercient Invoice History Detail Managed Custom Object | 6 | Invoice number, Header sequence number, Detail sequence number → Commercient external key, Invoice number, Header sequence number, Detail sequence number → Name, AR division number, Customer number → Account, AR division number, Customer number → Sage 100 customer records (custom field), Salesperson division, Salesperson number → Sage 100 salesperson object (custom) |
| Transcation Payment History | Commercient Payment History Managed Custom Object | 5 | AR division number, Customer number → Account, AR division number, Customer number → Sage 100 customer record (custom field), Invoice number, Invoice history header sequence number → Invoice |
| Item Warehouse | Commercient Item Warehouse Managed Custom Object | 4 | Item code,Warehouse code → Commercient external key, Item code,Warehouse code → Name, the linked Salesforce record → Commercient product, the linked Salesforce record → Commercient inventory item |
| Create Standard Price Book | Price book entry | 5 | Item code → External key (custom field), Active → Active, Standard unit price → Unit price, the linked Salesforce record → price book lookup, the linked Salesforce record → product lookup |
| Update Standard Price Book | Price book entry | 3 | Item code → External key (custom field), Active → Active, Standard unit price → Unit price |
| Contact | Contact | 14 | AR division number, Customer number, Contact code → Commercient external key, AR division number, Customer number → account lookup, AR division number, Customer number → Sage 100 customer records (custom field), Contact name → Family name, Email address → Email |
| Item Transaction History | Commercient Item Transaction History Managed Custom Object | 7 | AR division number,Customer number → Account (custom field), AR division number,Customer number → Sage 100 customer records (custom field), AR division number,Customer number,returned shipping address code → Sage 100 shipping address (custom field), Item code → Product, Item code → Sage 100 inventory item (custom field) |
| Opportunity Header | Opportunity | 7 | returned sales order number → External key (custom field), returned sales order number → Name, the linked Salesforce record → account lookup, Taxable amount → Amount, the linked Salesforce record → price book lookup |
| Opportunity Line | Opportunity line item | 8 | returned sales order number, Line key → External key (custom field), the linked Salesforce record → Quote, the linked Salesforce record → product lookup, the linked Salesforce record → price book entry lookup, Promise date → Service date |
| Quote header | Quote | 22 | returned sales order number → External key (custom field), returned sales order number → Name, returned sales order number → Opportunity, Salesperson division, Salesperson number → Sage 100 salesperson object (custom), Bill to name → Billing name |
| Quote line | Quote line item | 8 | returned sales order number, Line key → External key (custom field), the linked Salesforce record → Quote, the linked Salesforce record → product lookup, the linked Salesforce record → price book entry lookup, Promise date → Service date |

## 6. Community templates

The catalogue carries 77 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 77
- Default operations: insert on 77, update on 77, delete on 77
- Marked as circular sync: 0
- Licence groups they span: 12
- Destination objects: Account, Commercient AR Customer Managed Custom Object, Commercient Ship-To
  Address Managed Custom Object, Commercient Inventory Item Managed Custom Object, Price book entry,
  Commercient Invoice History Detail Managed Custom Object, Commercient Invoice History Header
  Managed Custom Object, Commercient Payment History Managed Custom Object, Commercient Open Invoice
  Managed Custom Object, Commercient Sales Order History Line Managed Custom Object, Commercient
  Sales Order History Header Managed Custom Object, Commercient Salesperson Managed Custom Object,
  Commercient Terms Code Managed Custom Object, Commercient Sales Order Line Managed Custom Object,
  Commercient Sales Order Header Managed Custom Object, Contact, Product, Commercient Item Warehouse
  Managed Custom Object, User, Account Matching (custom object) and 6 more
- Object display names: Account, Customer, Ship to address, Account customer lookup, AR terms, Open
  Invoice, Sales Person, Sales order, Sales order History Header, Sales order History Line, Sales
  order line, Transcation Payment History and 23 more
- Template groups: Account, Product, Sales order, Invoice, Customer Multi Ship Addresses, Invoice
  History Headers

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/sage-100-us-sage100us`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Sage 100 US (Sage 100 US) → Salesforce templates set up. dlake-crmpro-salesforce is the
destination skill this page sits under: its own text is the authority for the Salesforce conventions
that hold across every ERP, and its ERP table lists this page alongside every sibling ERP page for
this destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/sage-100-us-sage100us`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
