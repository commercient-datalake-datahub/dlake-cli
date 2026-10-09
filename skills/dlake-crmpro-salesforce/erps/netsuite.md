---
name: dlake-crmpro-salesforce/erps/netsuite
kind: erp-summary
description: >-
  Use it when standing up or reading a NetSuite → Salesforce template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-salesforce, the destination skill this page is a child of, which
  carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — NetSuite: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/netsuite` (or `list_skills`) against the
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
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Contact, Commercient NetSuite Customers Managed Custom Object, Commercient NetSuite Salesperson Managed Custom Object | customers, customer address book entries, contacts, employees |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created and existing ones updated; none are deleted. | Commercient NetSuite Customer Address Book Managed Custom Object | customer address book entries, customers |
| **Product** | ERP Item, Inventory item, Assembly item data becomes Commercient NetSuite Item Managed Custom Object, Commercient NetSuite Warehouse Managed Custom Object, Product in Salesforce. New records are created and existing ones updated; none are deleted. | Commercient NetSuite Item Managed Custom Object, Commercient NetSuite Warehouse Managed Custom Object, Product | items, inventory items |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created and existing ones updated; none are deleted. | Commercient NetSuite Invoice Managed Custom Object, Commercient NetSuite Invoice Item Managed Custom Object | invoices, invoice items |
| **Pricebook** | ERP Price list, Assembly item, Non inventory resale item data becomes Price book object, Price book entry in Salesforce. New records are created and existing ones updated; none are deleted. | Price book object, Price book entry | price lists |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created and existing ones updated; none are deleted. | Commercient NetSuite Sales Order Managed Custom Object, Commercient NetSuite Sales Order Item Managed Custom Object | sales orders, sales order items |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| GET USER | users | — | 0 |
| Account | Account | Commercient AR customer code | 1 |
| NetSuite Salesperson | Commercient NetSuite Salesperson Managed Custom Object | Commercient external key (NetSuite package) | 1 |
| Account-Child | Account | Commercient AR customer code | 2 |
| Customer | Commercient NetSuite Customers Managed Custom Object | Commercient external key (NetSuite package) | 3 |
| Customer To Account Reverse Lookup | Account | Commercient AR customer code | 4 |
| Customer Address Book | Commercient NetSuite Customer Address Book Managed Custom Object | Commercient external key (NetSuite package) | 5 |
| Sales order | Commercient NetSuite Sales Order Managed Custom Object | Commercient external key (NetSuite package) | 6 |
| Sales order Line | Commercient NetSuite Sales Order Item Managed Custom Object | Commercient external key (NetSuite package) | 7 |
| Invoice | Commercient NetSuite Invoice Managed Custom Object | Commercient external key (NetSuite package) | 8 |
| Invoice Line | Commercient NetSuite Invoice Item Managed Custom Object | Commercient external key (NetSuite package) | 9 |
| Contact | Contact | External key (custom field) | 10 |
| Item | Commercient NetSuite Item Managed Custom Object | Commercient external key (NetSuite package) | 11 |
| Product | Product | Commercient external key (earlier package) | 12 |
| Product to item reverse lookup | Commercient NetSuite Item Managed Custom Object | Commercient external key (NetSuite package) | 13 |
| Item Warehouse | Commercient NetSuite Warehouse Managed Custom Object | Commercient external key (NetSuite package) | 14 |
| Price book object | Price book object | External key (custom field) | 14 |
| Create Standard Price Book | Price book entry | External key (custom field) | 15 |
| Update Standard Price Book | Price book entry | External key (custom field) | 16 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| account feed | insert + update | customers, customer address book entries |
| salesperson feed | insert + update | employees |
| child account feed | insert + update | customers, customer address book entries |
| customer feed | insert + update | customers |
| customer account lookup feed | insert + update | customers |
| customer address book feed | insert + update | customer address book entries, customers |
| sales order feed | insert + update | sales orders |
| sales order line feed | insert + update | sales order items, sales orders |
| invoice feed | insert + update | invoices |
| invoice line feed | insert + update | invoice items, invoices |
| contact feed | insert + update | contacts |
| item feed | insert + update | items |
| product feed | insert + update | items |
| product item reverse lookup feed | insert + update | items |
| item warehouse feed | insert + update | inventory items |
| standard price book feed (new entries) | insert only | price lists |
| standard price book feed (changes) | insert + update | price lists |

## 4. Order of work

The templates set run sequence from 0 to 16. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 0 — GET USER
- 1 — Account, NetSuite Salesperson
- 2 — Account-Child
- 3 — Customer
- 4 — Customer To Account Reverse Lookup
- 5 — Customer Address Book
- 6 — Sales order
- 7 — Sales order Line
- 8 — Invoice
- 9 — Invoice Line
- 10 — Contact
- 11 — Item
- 12 — Product
- 13 — Product to item reverse lookup
- 14 — Item Warehouse, Price book object
- 15 — Create Standard Price Book
- 16 — Update Standard Price Book

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- account feed reads salesperson sync output, user sync output
- child account feed reads salesperson sync output, user sync output
- customer account lookup feed reads customer sync output
- contact feed reads account sync output, customer sync output
- customer feed reads account sync output
- customer address book feed reads account sync output, customer sync output
- product item reverse lookup feed reads product sync output
- item warehouse feed reads product sync output, item sync output
- invoice feed reads account sync output, customer sync output
- invoice line feed reads invoice sync output
- standard price book feed (new entries) reads product sync output
- product feed reads item sync output
- sales order feed reads account sync output, customer sync output
- sales order line feed reads sales order sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Account | Account | 15 | Internal identifier → Commercient AR customer code, Entity identifier → Name, phone → Phone, Address 1, Address 2, Address line 3 → Billing street, city property → Billing city |
| NetSuite Salesperson | Commercient NetSuite Salesperson Managed Custom Object | 74 | Salesperson account number → Salesperson account number, Default address → Commercient default address, Payroll provider identifier → Commercient payroll provider identifier, Alternate name → Commercient alternate name, → |
| Customer | Commercient NetSuite Customers Managed Custom Object | 145 | Internal identifier → Commercient external key (NetSuite package), Entity identifier → Name, the linked Salesforce record → Account, Custom form internal identifier → Commercient custom form internal identifier, Custom form external identifier → Commercient custom form external identifier |
| Customer To Account Reverse Lookup | Account | 2 | Internal identifier → Commercient AR customer code, the linked Salesforce record → Commercient customer (related record) |
| Customer Address Book | Commercient NetSuite Customer Address Book Managed Custom Object | 22 | parent account lookup,Internal identifier → Commercient external key (NetSuite package), addressee,Entity identifier → Commercient name, the linked Salesforce record → Commercient account (related record), the linked Salesforce record → Commercient customer (related record), parent account lookup → Commercient parent identifier |
| Sales order | Commercient NetSuite Sales Order Managed Custom Object | 197 | Internal identifier → Commercient external key (NetSuite package), Transaction number → Name, Entity internal identifier → Account, Entity internal identifier → Customer, Created date → Commercient created date |
| Sales order Line | Commercient NetSuite Sales Order Item Managed Custom Object | 93 | Internal identifier, line → Commercient external key (NetSuite package), Internal identifier, line, Transaction number → Name, the linked Salesforce record → Sales order, Internal identifier → Internal identifier, Job internal identifier → Job internal identifier |
| Invoice | Commercient NetSuite Invoice Managed Custom Object | 160 | Internal identifier → Commercient external key (NetSuite package), Transaction number → Name, Entity internal identifier → Account, Entity internal identifier → Customer, Created date → Commercient created date |
| Invoice Line | Commercient NetSuite Invoice Item Managed Custom Object | 73 | Internal identifier, line → Commercient external key (NetSuite package), Internal identifier, line, Transaction number → Name, the linked Salesforce record → Commercient invoice (related record), Internal identifier → Commercient internal identifier, Job internal identifier → Commercient job internal identifier |
| Product | Product | 6 | Internal identifier → Commercient external key (earlier package), Item identifier → Name, Item identifier → Product code, Stock description → Description, Inactive status → Active |
| Product to item reverse lookup | Commercient NetSuite Item Managed Custom Object | 2 | Internal identifier → Commercient external key (NetSuite package), the linked Salesforce record → Commercient product (related record) |
| Price book object | Price book object | 2 | External key (custom field) → External key (custom field), Name → Name |
| Create Standard Price Book | Price book entry | 5 | Id → External key (custom field), the linked Salesforce record → price book lookup, the linked Salesforce record → product lookup, value → Unit price, Active → Active |
| Update Standard Price Book | Price book entry | 3 | Id → External key (custom field), value → Unit price |

## 6. Community templates

The catalogue carries 253 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 253
- Default operations: insert on 253, update on 253, delete on 253
- Marked as circular sync: 2
- Licence groups they span: 12
- Destination objects: Account, Commercient NetSuite Item Managed Custom Object, Price book entry,
  Product, Commercient NetSuite Invoice Managed Custom Object, Commercient NetSuite Customers
  Managed Custom Object, Commercient NetSuite Invoice Item Managed Custom Object, Commercient
  NetSuite Sales Order Managed Custom Object, Commercient NetSuite Sales Order Item Managed Custom
  Object, Commercient NetSuite Customer Address Book Managed Custom Object, Commercient NetSuite
  Warehouse Managed Custom Object, Contact, Commercient NetSuite Salesperson Managed Custom Object,
  NetSuite Return Merchandise Authorization Order Line (custom object), Commercient Account Matching
  Managed Custom Object, Commercient NetSuite Term Managed Custom Object, NetSuite Return
  Merchandise Authorization Order (custom object), Price book object, Credit Memo (custom object),
  11 more and 2 custom objects
- Object display names: Account, Customer To Account Reverse Lookup, Account-Child, Contact,
  Customer Address Book, Invoice, Product, Customer, Invoice Line, Item, Item Warehouse, Sales
  order, 54 more and 3 further templates
- Template groups: Account, Product, Sales order, Invoice, Customer Multi Ship Addresses, CRM Order
  and Line

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/netsuite`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped NetSuite → Salesforce templates set up. dlake-crmpro-salesforce is the destination skill
this page sits under: its own text is the authority for the Salesforce conventions that hold across
every ERP, and its ERP table lists this page alongside every sibling ERP page for this destination.
For the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/netsuite`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
