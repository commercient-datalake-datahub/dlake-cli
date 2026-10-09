---
name: dlake-crmpro-salesforce/erps/sage-50-uk
kind: erp-summary
description: >-
  Use it when standing up or reading a Sage 50 UK → Salesforce template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-salesforce, the destination skill this page is a child of, which
  carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Sage 50 UK: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/sage-50-uk` (or `list_skills`) against the
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
| **Sage 50 UK Audit Header** | ERP audit header data becomes Commercient Sage 50 UK Audit Header Managed Custom Object in Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Sage 50 UK Audit Header Managed Custom Object | audit header transactions |
| **Sage 50 UK Audit Split** | ERP audit header, audit split data becomes Commercient Sage 50 UK Audit Split Managed Custom Object in Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Sage 50 UK Audit Split Managed Custom Object | audit split lines, audit header transactions |
| **GET USER** | The templates push users to Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | users | — |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created and existing ones updated; none are deleted. | Account, Contact, Commercient Sage 50 UK customer | sales ledger customers, delivery addresses |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created and existing ones updated; none are deleted. | Commercient Sage 50 UK Address Managed Custom Object | delivery addresses |
| **Product** | The templates push Commercient Sage 50 UK Item Managed Custom Object, Product to Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Sage 50 UK Item Managed Custom Object, Product | stock items |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created and existing ones updated; none are deleted. | Commercient Sage 50 UK Invoice Header Managed Custom Object, Commercient Sage 50 UK Invoice Detail Managed Custom Object | invoices, invoice items |
| **Pricebook** | The templates push Price book entry to Salesforce. New records are created and existing ones updated; none are deleted. | Price book entry | stock items |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created and existing ones updated; none are deleted. | Commercient Sage 50 UK Sales Order Header Managed Custom Object, Commercient Sage 50 UK Sales Order Detail Managed Custom Object | sales order headers, sales order lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| GET USER | users | — | 0 |
| Account | Account | Commercient AR customer code | 1 |
| Customer Master | Commercient Sage 50 UK customer | Commercient external key | 2 |
| Customer Reverse Lookup Account | Account | Commercient AR customer code | 3 |
| Sage 50 UK Address | Commercient Sage 50 UK Address Managed Custom Object | Commercient external key | 4 |
| Sales Order Header | Commercient Sage 50 UK Sales Order Header Managed Custom Object | Commercient external key | 5 |
| Sales Order Details | Commercient Sage 50 UK Sales Order Detail Managed Custom Object | Commercient external key | 6 |
| Invoice Header | Commercient Sage 50 UK Invoice Header Managed Custom Object | Commercient external key | 7 |
| Invoice Details | Commercient Sage 50 UK Invoice Detail Managed Custom Object | Commercient external key | 8 |
| Product | Product | Commercient external key (earlier package) | 9 |
| Item Master | Commercient Sage 50 UK Item Managed Custom Object | Commercient external key | 10 |
| Item To Product Reverse Lookup | Product | Commercient external key (earlier package) | 11 |
| Create Standard Price Book | Price book entry | External key (custom field) | 12 |
| Standard Pricebook Update | Price book entry | External key (custom field) | 13 |
| Contacts | Contact | Commercient external key | 18 |
| Sage 50 UK Audit Header | Commercient Sage 50 UK Audit Header Managed Custom Object | Commercient external key | 19 |
| Sage 50 UK Audit Split | Commercient Sage 50 UK Audit Split Managed Custom Object | Commercient external key | 20 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| account feed | insert + update | sales ledger customers, delivery addresses |
| Sage 50 UK customer feed | insert + update | sales ledger customers, delivery addresses |
| customer account lookup feed | insert + update | sales ledger customers |
| address feed | insert + update | delivery addresses |
| sales order feed | insert + update | sales order headers |
| sales order line feed | insert + update | sales order lines |
| invoice feed | insert + update | invoices |
| invoice line feed | insert + update | invoice items |
| product feed | insert + update | stock items |
| item feed | insert + update | stock items |
| item product lookup feed | insert + update | stock items |
| standard price book feed | insert only | stock items |
| standard price book feed (changes) | insert + update | stock items |
| contact feed | insert + update | sales ledger customers |
| audit header feed | insert + update | audit header transactions |
| audit split feed | insert + update | audit split lines, audit header transactions |

## 4. Order of work

The templates set run sequence from 0 to 20. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 0 — GET USER
- 1 — Account
- 2 — Customer Master
- 3 — Customer Reverse Lookup Account
- 4 — Sage 50 UK Address
- 5 — Sales Order Header
- 6 — Sales Order Details
- 7 — Invoice Header
- 8 — Invoice Details
- 9 — Product
- 10 — Item Master
- 11 — Item To Product Reverse Lookup
- 12 — Create Standard Price Book
- 13 — Standard Pricebook Update
- 18 — Contacts
- 19 — Sage 50 UK Audit Header
- 20 — Sage 50 UK Audit Split

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- audit header feed reads account sync output, Sage 50 UK customer sync output, sales order sync
  output, invoice sync output
- audit split feed reads audit header sync output, account sync output, Sage 50 UK customer sync
  output
- customer account lookup feed reads Sage 50 UK customer sync output
- contact feed reads account sync output
- Sage 50 UK customer feed reads account sync output, address sync output
- address feed reads account sync output, Sage 50 UK customer sync output
- item feed reads product sync output
- invoice feed reads account sync output, Sage 50 UK customer sync output
- invoice line feed reads invoice sync output, item sync output
- standard price book feed reads product sync output
- standard price book feed (changes) reads standard price book sync output, product sync output
- item product lookup feed reads item sync output
- sales order feed reads account sync output, Sage 50 UK customer sync output
- sales order line feed reads sales order sync output, item sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Account | Account | 13 | Account reference → Commercient AR customer code, Name → Name, Address 1, Address 2 → Billing street, Address 3 → Billing city, Address line 4 → Billing state |
| Customer Master | Commercient Sage 50 UK customer | 76 | Account reference → Commercient external key, Name → Name, Status number → Commercient status number, Department number (ERP) → Commercient department number, Currency → Commercient currency |
| Customer Reverse Lookup Account | Account | 2 | Account reference → Commercient AR customer code, the linked Salesforce record → Commercient Sage 50 UK customer |
| Sage 50 UK Address | Commercient Sage 50 UK Address Managed Custom Object | 26 | Reference → external key column, Account reference → Account, Account reference → Customer, Tax code → Tax code (second field), Address type field → Address type field |
| Sales Order Header | Commercient Sage 50 UK Sales Order Header Managed Custom Object | 93 | Order number → Commercient external key, Account reference → Commercient account, Account reference → Commercient customer (related record), Delivery status code (ERP) → Commercient delivery status code, Order type code → Commercient order type code |
| Sales Order Details | Commercient Sage 50 UK Sales Order Detail Managed Custom Object | 50 | Order number,Item number,Job number → Commercient external key, Order number,Item number,Job number → Commercient name, Order number → Commercient sales order header, Service flag → Commercient service flag (second field), Tax code identifier → Commercient tax code identifier (second field) |
| Invoice Header | Commercient Sage 50 UK Invoice Header Managed Custom Object | 93 | Invoice number → Commercient external key, Account reference → Account, Account reference → Customer, Invoice type code → Commercient invoice type code, Global department number → Commercient global department number |
| Invoice Details | Commercient Sage 50 UK Invoice Detail Managed Custom Object | 44 | Invoice number, Item number → Commercient external key, the linked Salesforce record → Commercient invoice header (related record), Service flag → Commercient service flag, Tax code identifier → Commercient tax code identifier, Item number → Commercient item number |
| Product | Product | 5 | Stock code → Commercient external key (earlier package), Stock code → Product code, Description → Name, Description → Description, Inactive flag → Active |
| Item Master | Commercient Sage 50 UK Item Managed Custom Object | 92 | Stock code → Commercient external key, Web description → Name, the linked Salesforce record → Product, Web publish → Web publish, Web special → Web special |
| Item To Product Reverse Lookup | Product | 2 | Stock code → Commercient external key (earlier package), the linked Salesforce record → Commercient Sage 50 UK Item Managed Custom Object |
| Create Standard Price Book | Price book entry | 5 | Stock code → External key (custom field), the linked Salesforce record → price book lookup, the linked Salesforce record → product lookup, Inactive flag → Active, Sales price → Unit price |
| Standard Pricebook Update | Price book entry | 4 | Stock code → External key (custom field), Inactive flag → Active, Sales price → Unit price, Supplier part number → Litre volume (custom field) |
| Contacts | Contact | 7 | Account reference → Commercient external key, the linked Salesforce record → account lookup, Contact name field → Family name, Contact name field → Given name, Email → Email |
| Sage 50 UK Audit Header | Commercient Sage 50 UK Audit Header Managed Custom Object | 90 | Transaction number, Account reference, Sales or purchase reference, Invoice reference → Commercient external key, Transaction number, Account reference, Sales or purchase reference, Invoice reference → Commercient name, Account reference → Commercient account, Account reference → Commercient customer (related record), Sales or purchase reference → Commercient sales order header |

## 6. Community templates

The catalogue carries 266 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 266
- Default operations: insert on 266, update on 266, delete on 266
- Marked as circular sync: 2
- Licence groups they span: 12
- Destination objects: Account, Product, Price book entry, Commercient Sage 50 UK Invoice Detail
  Managed Custom Object, Commercient Sage 50 UK customer, Commercient Sage 50 UK Invoice Header
  Managed Custom Object, Commercient Sage 50 UK Sales Order Detail Managed Custom Object,
  Commercient Sage 50 UK Sales Order Header Managed Custom Object, Commercient Sage 50 UK Address
  Managed Custom Object, Commercient Sage 50 UK Item Managed Custom Object, Contact, Commercient
  Sage 50 UK Audit Header Managed Custom Object, Commercient Sage 50 UK Audit Split Managed Custom
  Object, Invoice (custom object), Commercient Sage 50 UK Sales Receipt Managed Custom Object,
  Commercient Account Managed Custom Object, Commercient Account Matching Managed Custom Object,
  Commercient Sage 50 UK Purchase Order Detail Managed Custom Object, Commercient Sage 50 UK
  Purchase Order Header Managed Custom Object and 8 more
- Object display names: Account, Invoice Details, Invoice Header, Customer Reverse Lookup Account,
  Customer Master, Sage 50 UK Address, Sales Order Details, Sales Order Header, Contacts, Item To
  Product Reverse Lookup, Product, Create Standard Price Book, 36 more and 5 further templates
- Template groups: Account, Product, Invoice, Sales order, Customer Multi Ship Addresses, Pricebook,
  CRM Quote and Line, CRM Opportunity and Line

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/sage-50-uk`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Sage 50 UK → Salesforce templates set up. dlake-crmpro-salesforce is the destination
skill this page sits under: its own text is the authority for the Salesforce conventions that hold
across every ERP, and its ERP table lists this page alongside every sibling ERP page for this
destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/sage-50-uk`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
