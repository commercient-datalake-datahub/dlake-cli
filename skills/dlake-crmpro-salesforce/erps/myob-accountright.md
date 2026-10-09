---
name: dlake-crmpro-salesforce/erps/myob-accountright
kind: erp-summary
description: >-
  Use it when standing up or reading a MYOB AccountRight → Salesforce template set, when deciding
  which templates to import and activate, or when a run completes without pushing records and the
  answer is in the view or the configuration row. It extends dlake-crmpro, which covers operating
  CRMPro generally, and dlake-crmpro-salesforce, the destination skill this page is a child of,
  which carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — MYOB AccountRight: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/myob-accountright` (or `list_skills`) against
the Commercient admin plane. Existing customers who need access or help: contact
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
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Contact, Commercient MYOB Customers Managed Custom Object, Commercient MYOB Salesperson Managed Custom Object | customers, customer addresses, contact cards, contact card addresses, employees, employee addresses |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. Existing records are updated only — nothing is created and nothing is deleted. | Commercient MYOB Customer Addresses Managed Custom Object | customer addresses |
| **Product** | The templates push Commercient MYOB Items Managed Custom Object, Product to Salesforce. Existing records are updated only — nothing is created and nothing is deleted. | Commercient MYOB Items Managed Custom Object, Product | items |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). Existing records are updated only — nothing is created and nothing is deleted. | Commercient MYOB Item Sales Managed Custom Object, Commercient MYOB Item Sale Lines Managed Custom Object | item sales, item sale lines |
| **Pricebook** | The templates push Price book entry to Salesforce. New records are created and existing ones updated; none are deleted. | Price book entry | items, item price level details |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. Existing records are updated only — nothing is created and nothing is deleted. | Commercient MYOB Item Sales Orders Managed Custom Object, Commercient MYOB Item Sales Order Lines Managed Custom Object | item sales orders, item sales order lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| GET USER | users | — | 0 |
| MYOB Salesperson | Commercient MYOB Salesperson Managed Custom Object | External key (custom field) | 1 |
| Account | Account | Commercient AR customer code | 3 |
| Customer | Commercient MYOB Customers Managed Custom Object | Commercient external key (Acumatica and MYOB package) | 4 |
| Customer to account Reverse Lookup | Account | Commercient AR customer code | 5 |
| Contact | Contact | External key (custom field) | 6 |
| Customer Address | Commercient MYOB Customer Addresses Managed Custom Object | Commercient external key (Acumatica and MYOB package) | 6 |
| Product | Product | Commercient external key (earlier package) | 7 |
| Item | Commercient MYOB Items Managed Custom Object | Commercient external key (Acumatica and MYOB package) | 8 |
| Item to product Reverse Lookup | Product | Commercient external key (earlier package) | 9 |
| Sales Order | Commercient MYOB Item Sales Orders Managed Custom Object | Commercient external key (Acumatica and MYOB package) | 10 |
| Sales Order Lines | Commercient MYOB Item Sales Order Lines Managed Custom Object | Commercient external key (Acumatica and MYOB package) | 11 |
| Sales Invoice | Commercient MYOB Item Sales Managed Custom Object | Commercient external key (Acumatica and MYOB package) | 12 |
| Sales Invoice Lines | Commercient MYOB Item Sale Lines Managed Custom Object | Commercient external key (Acumatica and MYOB package) | 13 |
| Standard Pricebook Create | Price book entry | External key (custom field) | 14 |
| Standard Pricebook Update | Price book entry | External key (custom field) | 15 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| salesperson feed | insert + update | employees, employee addresses |
| account feed | insert + update | customers, customer addresses |
| customer feed | insert + update | customers |
| customer account lookup feed | insert + update | customers |
| contact feed | insert + update | contact cards, contact card addresses, customers |
| customer address feed | insert + update | customer addresses |
| product feed | insert + update | items |
| item feed | insert + update | items |
| product item lookup feed | insert + update | items |
| sales order feed | insert + update | item sales orders |
| sales order line feed | insert + update | item sales order lines |
| sales invoice feed | insert + update | item sales |
| sales invoice line feed | insert + update | item sale lines |
| standard price book feed (new entries) | insert + update | items, item price level details |
| standard price book feed (changes) | insert + update | items, item price level details |

## 4. Order of work

The templates set run sequence from 0 to 15. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 0 — GET USER
- 1 — MYOB Salesperson
- 3 — Account
- 4 — Customer
- 5 — Customer to account Reverse Lookup
- 6 — Contact, Customer Address
- 7 — Product
- 8 — Item
- 9 — Item to product Reverse Lookup
- 10 — Sales Order
- 11 — Sales Order Lines
- 12 — Sales Invoice
- 13 — Sales Invoice Lines
- 14 — Standard Pricebook Create
- 15 — Standard Pricebook Update

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- account feed reads user sync output
- customer account lookup feed reads customer sync output
- customer feed reads account sync output
- customer address feed reads account sync output, customer sync output
- item feed reads product sync output
- sales invoice feed reads account sync output, customer sync output
- sales invoice line feed reads sales invoice sync output, product sync output, item sync output
- standard price book feed (new entries) reads product sync output
- standard price book feed (changes) reads product sync output
- product item lookup feed reads item sync output
- sales order feed reads account sync output, customer sync output
- sales order line feed reads sales order sync output, product sync output, item sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| MYOB Salesperson | Commercient MYOB Salesperson Managed Custom Object | 38 | Company record identifier → Company record identifier, Employee unique identifier → Employee unique identifier (custom field), Entity tag → Entity tag (custom field), Company name → Company name (custom field), Current balance → Current balance |
| Account | Account | 13 | Company record identifier, Customer unique identifier → Commercient AR customer code, Company name, Family name → Name, Phone 1 → Phone, Street line 1, Street line 2, Street line 3, Street line 4 → Billing street, City → Billing city |
| Customer | Commercient MYOB Customers Managed Custom Object | 64 | Company record identifier,Customer unique identifier → Commercient external key (Acumatica and MYOB package), Company name,Family name → Name, the linked Salesforce record → Account, Company record identifier → Commercient company record identifier, Customer unique identifier → Commercient customer unique identifier |
| Customer to account Reverse Lookup | Account | 2 | Company record identifier, Customer unique identifier → Commercient AR customer code, the linked Salesforce record → Commercient Acumatica Cloud customer (related record) |
| Contact | Contact | 11 | Fax → Fax, Email → Email, Phone → Phone, Mailing street → Mailing street, Mailing city → Mailing city |
| Customer Address | Commercient MYOB Customer Addresses Managed Custom Object | 28 | Company record identifier, Customer unique identifier, Location → Commercient external key (Acumatica and MYOB package), Customer company name, Customer last name, Location → Name, the linked Salesforce record → Account, the linked Salesforce record → Commercient Acumatica Cloud customer (related record), Company record identifier → Company record identifier |
| Product | Product | 5 | Company record identifier,Item unique identifier → Commercient external key (earlier package), Name → Name, Number → Product code, Active → Active, Description → Description |
| Item | Commercient MYOB Items Managed Custom Object | 59 | Company record identifier,Item unique identifier → Commercient external key (Acumatica and MYOB package), Name → Name, the linked Salesforce record → Product, Company record identifier → Commercient company record identifier, Item unique identifier → Commercient item unique identifier |
| Item to product Reverse Lookup | Product | 2 | Company record identifier, Item unique identifier → Commercient external key (earlier package), the linked Salesforce record → Commercient Acumatica Cloud item (related record) |
| Sales Order | Commercient MYOB Item Sales Orders Managed Custom Object | 53 | Company record identifier, Sales order unique identifier → Commercient external key (Acumatica and MYOB package), Sales order number → Name, Company record identifier, Customer unique identifier → Account, Company record identifier, Customer unique identifier → Commercient Acumatica Cloud customer (related record), Company record identifier → Commercient company record identifier |
| Sales Order Lines | Commercient MYOB Item Sales Order Lines Managed Custom Object | 25 | Company record identifier, Sales order unique identifier, Row identifier → Commercient external key (Acumatica and MYOB package), Sales order number, Index → Name, the linked Salesforce record → Commercient Acumatica Cloud sales order (related record), the linked Salesforce record → Product, the linked Salesforce record → Commercient Acumatica Cloud item (related record) |
| Sales Invoice | Commercient MYOB Item Sales Managed Custom Object | 58 | Company record identifier,Sale unique identifier → Commercient external key (Acumatica and MYOB package), Number → Name, Company record identifier,Customer unique identifier → Account, Company record identifier,Customer unique identifier → Commercient Acumatica Cloud customer (related record), Invoice type → Invoice type |
| Sales Invoice Lines | Commercient MYOB Item Sale Lines Managed Custom Object | 28 | Company record identifier, Sale unique identifier, Row identifier → Commercient external key (Acumatica and MYOB package), Number, Index → Name, the linked Salesforce record → Commercient Acumatica Cloud sales invoice (related record), the linked Salesforce record → Product, the linked Salesforce record → Commercient Acumatica Cloud item (related record) |
| Standard Pricebook Create | Price book entry | 5 | Company record identifier, Number → External key (custom field), the linked Salesforce record → product lookup, Active → Active, Price level A → Unit price, Row timestamp → Row timestamp |
| Standard Pricebook Update | Price book entry | 4 | Company record identifier, Number → External key (custom field), Active → Active, Price level A → Unit price, Row timestamp → Row timestamp |

## 6. Community templates

The catalogue carries 123 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 123
- Default operations: insert on 123, update on 123, delete on 123
- Marked as circular sync: 4
- Licence groups they span: 11
- Destination objects: Account, Product, Price book entry, Commercient MYOB Customers Managed Custom
  Object, Contact, Commercient MYOB Customer Addresses Managed Custom Object, Commercient MYOB Items
  Managed Custom Object, Commercient MYOB Item Sales Managed Custom Object, Commercient MYOB Item
  Sale Lines Managed Custom Object, Commercient MYOB Item Sales Order Lines Managed Custom Object,
  Commercient MYOB Item Sales Orders Managed Custom Object, Commercient MYOB Salesperson Managed
  Custom Object, Content document, Order, Order product, Commercient MYOB Contact Address Managed
  Custom Object, Commercient MYOB Sales Invoice Service Managed Custom Object, Commercient MYOB
  Sales Service Detail Managed Custom Object, MYOB Customer Payment (custom object), MYOB Customer
  Payment Line (custom object), 1 more and a custom object
- Object display names: Account, Contact, Customer, Customer to account Reverse Lookup, Customer
  Address, Item, Item to product Reverse Lookup, Product, Sales Invoice, Sales Order Lines, Sales
  Invoice Lines, Sales Orders and 29 more
- Template groups: Account, Product, Invoice, Sales order, Customer Multi Ship Addresses, CRM Order
  and Line

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/myob-accountright`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped MYOB AccountRight → Salesforce templates set up. dlake-crmpro-salesforce is the
destination skill this page sits under: its own text is the authority for the Salesforce conventions
that hold across every ERP, and its ERP table lists this page alongside every sibling ERP page for
this destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/myob-accountright`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
