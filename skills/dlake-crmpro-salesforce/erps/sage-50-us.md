---
name: dlake-crmpro-salesforce/erps/sage-50-us
kind: erp-summary
description: >-
  Use it when standing up or reading a Sage 50 US → Salesforce template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-salesforce, the destination skill this page is a child of, which
  carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Sage 50 US: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/sage-50-us` (or `list_skills`) against the
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
| **GET USER** | The templates push users to Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | users | — |
| **Get Pricebook** | The templates push Price book entry to Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | Price book entry | — |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created and existing ones updated; none are deleted. | Account, Commercient Sage 50 US AR Terms Managed Custom Object, Contact, Commercient Sage 50 US Customer Managed Custom Object, Commercient Sage 50 US Salesperson Managed Custom Object | customers, addresses, contacts, general AR settings, employees |
| **CRM Opportunity and Line** | ERP journal header, Employee, Customer master data becomes Oppertunity, Opportunity line item in Salesforce. New records are created and existing ones updated; none are deleted. | Oppertunity, Opportunity line item | journal headers, employees, customers, journal lines, items |
| **CRM Quote and Line** | ERP journal header, Customer master, Address data becomes Quote, Quote line item in Salesforce. New records are created and existing ones updated; none are deleted. | Quote, Quote line item | journal headers, customers, employees, addresses, journal lines, items |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created and existing ones updated; none are deleted. | Commercient Sage 50 US Address Managed Custom Object | addresses, customers |
| **Product** | The templates push Commercient Line Item Managed Custom Object, Product, Product Reverse Lookup to Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Line Item Managed Custom Object, Product, Product Reverse Lookup | items |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created and existing ones updated; none are deleted. | Commercient Sage 50 US Sales Invoice Header Managed Custom Object, Commercient Sage 50 US Sales Invoice Detail Managed Custom Object | journal headers, customers, employees, journal lines, items |
| **Pricebook** | The templates push Price book entry to Salesforce. New records are created and existing ones updated; none are deleted. | Price book entry | items |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created and existing ones updated; none are deleted. | Commercient Sage 50 US Sales Order Header Managed Custom Object, Commercient Sage 50 US Sales Order Detail Managed Custom Object | journal headers, customers, journal lines, items |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| GET USER | users | — | 0 |
| Get Price book entry | Price book entry | External key (custom field) | 0 |
| Get Product | Product | External key (custom field) | 0 |
| Sage 50 US Salesperson | Commercient Sage 50 US Salesperson Managed Custom Object | Commercient external key | 1 |
| Account | Account | Commercient AR customer code | 2 |
| Ship to account | Account | Commercient AR customer code | 3 |
| Contact | Contact | Commercient external key | 4 |
| Sage 50 US Customer | Commercient Sage 50 US Customer Managed Custom Object | Commercient external key | 5 |
| Customer Reverse Lookup Account | Account | Commercient AR customer code | 6 |
| Sage 50 US AR term | Commercient Sage 50 US AR Terms Managed Custom Object | Commercient external key | 7 |
| Sage 50 US Address | Commercient Sage 50 US Address Managed Custom Object | Commercient external key | 8 |
| Product | Product | Commercient external key (earlier package) | 9 |
| Sage 50 US Sales Order Header | Commercient Sage 50 US Sales Order Header Managed Custom Object | Commercient external key | 14 |
| Sage 50 US Sales Order Detail | Commercient Sage 50 US Sales Order Detail Managed Custom Object | Commercient external key | 15 |
| Sage 50 US Sales Invoice Header | Commercient Sage 50 US Sales Invoice Header Managed Custom Object | Commercient external key | 16 |
| Sage 50 US Sales Invoice Detail | Commercient Sage 50 US Sales Invoice Detail Managed Custom Object | Commercient external key | 17 |
| Sage 50 US Item Master | Commercient Line Item Managed Custom Object | Commercient external key | 18 |
| Oppertunity | Oppertunity | Commercient AR customer code | 20 |
| Opportunity line item | Opportunity line item | Commercient AR customer code | 21 |
| Quote | Quote | Commercient AR customer code | 22 |
| Quote line item | Quote line item | Commercient AR customer code | 23 |
| Standard Pricebook Create | Price book entry | External key (custom field) | 24 |
| Standard Pricebook Update | Price book entry | External key (custom field) | 25 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| item product lookup feed | insert + update | items |
| salesperson feed | insert + update | employees |
| account feed | insert + update | customers, addresses |
| shipping account feed | insert + update | contacts, customers, addresses |
| contact feed | insert + update | contacts, customers, addresses |
| customer feed | insert + update | customers |
| customer account lookup feed | insert + update | customers |
| AR terms feed | insert + update | general AR settings |
| address feed | insert + update | addresses, customers |
| product feed | insert + update | items |
| sales order feed | insert + update | journal headers, customers |
| sales order line feed | insert + update | journal lines, items, customers |
| sales invoice feed | insert + update | journal headers, customers, employees |
| sales invoice line feed | insert + update | journal lines, items, customers |
| line item feed | insert + update | items |
| opportunity feed | insert + update | journal headers, employees, customers |
| opportunity line feed | insert + update | journal lines, items |
| quote feed | insert + update | journal headers, customers, employees, addresses |
| quote line feed | insert + update | journal lines, items |
| standard price book feed (new entries) | insert only | items |
| standard price book feed (changes) | insert + update | items |

## 4. Order of work

The templates set run sequence from 0 to 25. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 0 — GET USER, Get Price book entry, Get Product
- 1 — Sage 50 US Salesperson
- 2 — Account
- 3 — Ship to account
- 4 — Contact
- 5 — Sage 50 US Customer
- 6 — Customer Reverse Lookup Account
- 7 — Sage 50 US AR term
- 8 — Sage 50 US Address
- 9 — Product
- 14 — Sage 50 US Sales Order Header
- 15 — Sage 50 US Sales Order Detail
- 16 — Sage 50 US Sales Invoice Header
- 17 — Sage 50 US Sales Invoice Detail
- 18 — Sage 50 US Item Master
- 20 — Oppertunity
- 21 — Opportunity line item
- 22 — Quote
- 23 — Quote line item
- 24 — Standard Pricebook Create
- 25 — Standard Pricebook Update

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- account feed reads salesperson sync output, user sync output
- shipping account feed reads account sync output, user sync output, salesperson sync output
- customer account lookup feed reads customer sync output
- contact feed reads account sync output
- opportunity feed reads account sync output
- opportunity line feed reads product sync output, opportunity sync output
- quote feed reads account sync output, opportunity sync output
- quote line feed reads product sync output, quote sync output
- customer feed reads salesperson sync output, account sync output
- address feed reads customer sync output, account sync output
- line item feed reads product sync output
- sales invoice feed reads customer sync output, account sync output, salesperson sync output
- sales invoice line feed reads sales invoice sync output, customer sync output, account sync output
- standard price book feed (new entries) reads product sync output
- standard price book feed (changes) reads product sync output
- item product lookup feed reads item sync output, item product lookup sync output; no template in
  this set writes item product lookup sync output
- sales order feed reads customer sync output, account sync output, salesperson sync output
- sales order line feed reads customer sync output, account sync output, sales order sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Product Reverse Lookup | Product Reverse Lookup | 2 | Item record number → External key (custom field), the linked Salesforce record → Sage 50 US item master (custom field) |
| Sage 50 US Salesperson | Commercient Sage 50 US Salesperson Managed Custom Object | 97 | Employee record number → Commercient external key, Employee global identifier (ERP) → Commercient employee global identifier, Deposit prenote global identifier 4 → Commercient deposit prenote global identifier 4, Supervisor global identifier → Commercient supervisor global identifier, Photo global identifier → Commercient photo global identifier |
| Account | Account | 18 | Customer identifier → Commercient AR customer code, Customer billing name,Customer identifier → Name, Global identifier → Commercient customer unique identifier, Customer type → Industry, Address line 1,Address line 2 → Billing street |
| Ship to account | Account | 10 | Customer identifier,Record number → Commercient AR customer code, Customer billing name,Customer identifier,Given name,Family name → Name, Address line 1,Address line 2 → Shipping street, City → Shipping city, State → Shipping state |
| Contact | Contact | 10 | Record number → Commercient external key, Family name → Family name, Given name → Given name, Email → Email, Address line 1, Address line 2 → Mailing street |
| Sage 50 US Customer | Commercient Sage 50 US Customer Managed Custom Object | 91 | Employee record number → Commercient salesperson, Customer identifier → Commercient account, Global identifier → Commercient global identifier, Row timestamp → Commercient time stamp, Customer identifier → Commercient external key |
| Customer Reverse Lookup Account | Account | 2 | Customer identifier → Commercient AR customer code, the linked Salesforce record → Commercient Sage 50 US customer record (related record) |
| Sage 50 US AR term | Commercient Sage 50 US AR Terms Managed Custom Object | 84 | Global identifier → Commercient external key, Global identifier → Commercient global identifier, Merchant key length (ERP) → Commercient merchant key length, Age by due date (ERP) → Commercient age by due date, Statement plus → Commercient statement plus |
| Sage 50 US Address | Commercient Sage 50 US Address Managed Custom Object | 16 | Address record number → Commercient external key, Address type description → Commercient address type description, Name → Commercient name, Address line 1 → Commercient address line 1, Address line 2 → Commercient address line 2 |
| Product | Product | 6 | Item record number → Commercient external key (earlier package), Item identifier → Name, Item identifier → Product code, Sales description → Description, Location → Default warehouse (product field) |
| Sage 50 US Sales Order Header | Commercient Sage 50 US Sales Order Header Managed Custom Object | 82 | Customer identifier → Commercient account, Customer identifier → Commercient customer (related record), Employee record number → Commercient salesperson, Row timestamp → Row timestamp, Posting order number → Commercient external key |
| Sage 50 US Sales Order Detail | Commercient Sage 50 US Sales Order Detail Managed Custom Object | 47 | Customer identifier → Commercient account, Customer record number (ERP) → Commercient customer (related record), Posting order number → Commercient sales order header (related record), Line global identifier → external key column, Used for reimbursable expense → Used for reimbursable expense |
| Sage 50 US Sales Invoice Header | Commercient Sage 50 US Sales Invoice Header Managed Custom Object | 83 | Customer identifier → Commercient account, Customer identifier → Commercient customer (related record), Employee record number → Commercient salesperson, Posting order number → Commercient external key, Transaction posted flag → Commercient transaction posted flag |
| Sage 50 US Sales Invoice Detail | Commercient Sage 50 US Sales Invoice Detail Managed Custom Object | 47 | Customer identifier → Commercient account, Customer record number (ERP) → Commercient customer (related record), Posting order number → Commercient sales invoice header, Line global identifier → external key column, Purchase or sales order closed (ERP) → Purchase or sales order closed (ERP) |
| Sage 50 US Item Master | Commercient Line Item Managed Custom Object | 98 | Item record number → Commercient external key, the linked Salesforce record → Commercient product, Item identifier → Name, Item description text → Item description text, Item inactive flag → Item inactive flag |
| Oppertunity | Oppertunity | 9 | Posting order number → External key (custom field), the linked Salesforce account record → account lookup, the linked Salesforce price book record → price book lookup |
| Opportunity line item | Opportunity line item | 7 | Item identifier → product lookup, Posting order number → Opportunity, Posting order number, Line row number → external key column, Cost record number → Unit price, Quantity → Quantity |
| Quote | Quote | 18 | Posting order number → External key (custom field), Posting order number → Name, the linked Salesforce record → Opportunity identifier, the linked Salesforce record → account lookup, Status → Status |
| Quote line item | Quote line item | 8 | Posting order number, Line row number → External key (custom field), Item identifier → product lookup, Posting order number → Quote, Quantity → Quantity, Line date → Service date |
| Standard Pricebook Create | Price book entry | 5 | the linked Salesforce record → product lookup, the linked Salesforce record → price book lookup, Active → Active, Price level 1 amount → Unit price, Item record number → External key (custom field) |
| Standard Pricebook Update | Price book entry | 3 | Active → Active, Price level 1 amount → Unit price, Item record number → External key (custom field) |

## 6. Community templates

The catalogue carries 438 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 438
- Default operations: insert on 438, update on 438, delete on 438
- Marked as circular sync: 11
- Licence groups they span: 18
- Destination objects: Account, Product, Commercient Sage 50 US Sales Invoice Header Managed Custom
  Object, Commercient Sage 50 US Customer Managed Custom Object, Commercient Sage 50 US Address
  Managed Custom Object, Commercient Sage 50 US Sales Order Detail Managed Custom Object,
  Commercient Sage 50 US Sales Order Header Managed Custom Object, Commercient Sage 50 US Sales
  Invoice Detail Managed Custom Object, Contact, Commercient Sage 50 US Salesperson Managed Custom
  Object, Commercient Sage 50 US AR Terms Managed Custom Object, Commercient Line Item Managed
  Custom Object, Price book entry, Opportunity, Commercient Sage 50 US Purchase Order Detail Managed
  Custom Object, Commercient Sage 50 US Purchase Order Header Managed Custom Object, Commercient
  Sage 50 US Vendors Managed Custom Object, Oppertunity, User, 22 more and 7 custom objects
- Object display names: Account, Customer Reverse Lookup Account, Sage 50 US Sales Invoice Header,
  Sage 50 US Address, Sage 50 US Customer, Sage 50 US Sales Invoice Detail, Sage 50 US Sales Order
  Detail, Sage 50 US Sales Order Header, Contact, Sage 50 US Salesperson, Sage 50 US AR term,
  Product, 68 more and 10 further templates
- Template groups: Account, Product, Invoice, Sales order, Customer Multi Ship Addresses, CRM
  Opportunity and Line, Purchase Order, CRM Quote and Line, CRM Order and Line, Invoice History
  Headers, Opportunity, Quote line number

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/sage-50-us`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Sage 50 US → Salesforce templates set up. dlake-crmpro-salesforce is the destination
skill this page sits under: its own text is the authority for the Salesforce conventions that hold
across every ERP, and its ERP table lists this page alongside every sibling ERP page for this
destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/sage-50-us`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
