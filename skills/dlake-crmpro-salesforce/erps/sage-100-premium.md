---
name: dlake-crmpro-salesforce/erps/sage-100-premium
kind: erp-summary
description: >-
  Use it when standing up or reading a Sage 100 Premium → Salesforce template set, when deciding
  which templates to import and activate, or when a run completes without pushing records and the
  answer is in the view or the configuration row. It extends dlake-crmpro, which covers operating
  CRMPro generally, and dlake-crmpro-salesforce, the destination skill this page is a child of,
  which carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Sage 100 Premium: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/sage-100-premium` (or `list_skills`) against the
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
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Contact, Commercient AR Customer Managed Custom Object, Commercient Salesperson Managed Custom Object | customers, customer contacts, salespeople |
| **CRM Opportunity and Line** | ERP sales order header, sales order detail data becomes Opportunity, Opportunity line item in Salesforce. New records are created and existing ones updated; none are deleted. | Opportunity, Opportunity line item | sales order headers, sales order lines |
| **CRM Quote and Line** | The templates push Quote, Opportunity to Salesforce. New records are created and existing ones updated; none are deleted. | Quote, Opportunity | sales order headers, sales order lines |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created and existing ones updated; none are deleted. | Commercient Ship-To Address Managed Custom Object | shipping addresses |
| **Product** | ERP Common information item data becomes Commercient Inventory Item Managed Custom Object, Product in Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Inventory Item Managed Custom Object, Product | items |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created and existing ones updated; none are deleted. | Commercient Invoice History Header Managed Custom Object, Commercient Payment History Managed Custom Object, Commercient Invoice History Lot Serial Managed Custom Object, Commercient Invoice History Detail Managed Custom Object | invoice history headers, customers, open invoices, salespeople, payment history |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created and existing ones updated; none are deleted. | Commercient Sales Order Header Managed Custom Object, Commercient Sales Order Line Managed Custom Object, Commercient Sales Order History Header Managed Custom Object, Commercient Sales Order History Line Managed Custom Object | sales order headers, customers, sales order lines, sales order history headers, sales order history lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| GET USER | users | — | 0 |
| Sales Person | Commercient Salesperson Managed Custom Object | Commercient external key | 1 |
| Account | Account | Commercient AR customer code | 2 |
| Customer Master | Commercient AR Customer Managed Custom Object | Commercient external key | 3 |
| Customer to Account Reverse Lookup | Account | Commercient AR customer code | 4 |
| Item Master | Commercient Inventory Item Managed Custom Object | Commercient external keys | 5 |
| Product | Product | Commercient external key (earlier package) | 6 |
| Product to Item Master Reverse Lookup | Commercient Inventory Item Managed Custom Object | Commercient external keys | 7 |
| Sales Order Header | Commercient Sales Order Header Managed Custom Object | Commercient sales order number | 11 |
| Sales Order Line | Commercient Sales Order Line Managed Custom Object | Commercient external key | 12 |
| Sales Order History Header | Commercient Sales Order History Header Managed Custom Object | Commercient external key | 13 |
| Sales Order History Line | Commercient Sales Order History Line Managed Custom Object | Commercient external key | 14 |
| Invoice Header | Commercient Invoice History Header Managed Custom Object | Commercient external key | 15 |
| Invoice Line | Commercient Invoice History Detail Managed Custom Object | Commercient external key | 16 |
| Contact | Contact | Commercient external key | 17 |
| Invoice Payment | Commercient Payment History Managed Custom Object | Commercient external key | 17 |
| Invoice Lot serial | Commercient Invoice History Lot Serial Managed Custom Object | Commercient external key | 18 |
| Shipping address | Commercient Ship-To Address Managed Custom Object | Commercient external key | 19 |
| Opportunity Header | Opportunity | External key (custom field) | 23 |
| Opportunity Line | Opportunity line item | External key (custom field) | 24 |
| Quote header | Quote | External key (custom field) | 25 |
| Opportunity Header | Opportunity | External key (custom field) | 30 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| salesperson feed | insert + update | salespeople |
| account feed | insert + update | customers |
| customer feed | insert + update | customers |
| customer account lookup feed | insert + update | customers |
| item master feed | insert + update | items |
| product feed | insert + update | items |
| product item reverse lookup feed | insert + update | items |
| sales order feed | insert + update | sales order headers, customers |
| sales order line feed | insert + update | sales order lines, sales order headers, customers |
| sales order history feed | insert + update | sales order history headers, customers |
| sales order history line feed | insert + update | sales order history lines, sales order history headers, customers |
| invoice feed | insert + update | invoice history headers, customers, open invoices, salespeople |
| invoice line feed | insert + update | invoice history lines |
| contact feed | insert + update | customer contacts |
| payment history feed | insert + update | payment history, invoice history headers, A |
| invoice lot serial feed | insert + update | invoice history lot serials |
| shipping address feed | insert + update | shipping addresses |
| opportunity feed | insert + update | sales order headers |
| quote line feed | insert only | sales order lines, sales order headers |
| quote feed | insert + update | sales order headers |
| quote line feed | insert only | sales order lines, sales order headers |

## 4. Order of work

The templates set run sequence from 0 to 30. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 0 — GET USER
- 1 — Sales Person
- 2 — Account
- 3 — Customer Master
- 4 — Customer to Account Reverse Lookup
- 5 — Item Master
- 6 — Product
- 7 — Product to Item Master Reverse Lookup
- 11 — Sales Order Header
- 12 — Sales Order Line
- 13 — Sales Order History Header
- 14 — Sales Order History Line
- 15 — Invoice Header
- 16 — Invoice Line
- 17 — Contact, Invoice Payment
- 18 — Invoice Lot serial
- 19 — Shipping address
- 23 — Opportunity Header
- 24 — Opportunity Line
- 25 — Quote header
- 30 — Opportunity Header

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- account feed reads salesperson sync output (generic name), user sync output
- customer account lookup feed reads customer sync output (generic name)
- contact feed reads account sync output (generic name)
- opportunity feed reads customer sync output (generic name), account sync output (generic name),
  salesperson sync output (generic name)
- quote line feed reads quote sync output (generic name), product record sync output (generic name),
  quote line sync output (generic name); no template in this set writes quote line sync output
  (generic name)
- quote feed reads customer sync output (generic name), account sync output (generic name),
  opportunity sync output (generic name), salesperson sync output (generic name)
- quote line feed reads quote sync output (generic name), product record sync output (generic name),
  quote line sync output (generic name); no template in this set writes quote line sync output
  (generic name)
- customer feed reads account sync output (generic name), salesperson sync output (generic name)
- shipping address feed reads salesperson sync output (generic name), account sync output (generic
  name), customer sync output (generic name)
- product item reverse lookup feed reads product record sync output (generic name)
- invoice feed reads customer sync output (generic name), account sync output (generic name),
  salesperson sync output (generic name)
- payment history feed reads customer sync output (generic name), account sync output (generic
  name), invoice sync output (generic name)
- invoice lot serial feed reads invoice sync output (generic name), invoice line sync output
  (generic name)
- invoice line feed reads invoice sync output (generic name), item sync output (generic name)
- product feed reads item sync output (generic name)
- sales order feed reads customer sync output (generic name), account sync output (generic name),
  salesperson sync output (generic name)
- sales order line feed reads sales order header sync output (generic name)
- sales order history feed reads customer sync output (generic name), account sync output (generic
  name)
- sales order history line feed reads sales order history sync output (generic name)

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Sales Person | Commercient Salesperson Managed Custom Object | 25 | Salesperson division, Salesperson number → Commercient external key, Salesperson name, Salesperson division, Salesperson number → name, Salesperson division → Salesperson division, Salesperson number → Salesperson number, Salesperson name → Salesperson name |
| Account | Account | 15 | AR division number, Customer number → Commercient AR customer code, Customer name, AR division number, Customer number → Name, Telephone number → Phone, Salesperson division, Salesperson number → Commercient Salesperson Managed Custom Object, Address line 1, Address line 2, Address line 3 → Billing street |
| Customer to Account Reverse Lookup | Account | 2 | AR division number,Customer number → Commercient AR customer code, the linked Salesforce record → Commercient Sage customer record |
| Item Master | Commercient Inventory Item Managed Custom Object | 82 | Item code → Commercient external keys, Item code → Commercient item code, Item code → Commercient name, Item type → Commercient item type, Item description → Commercient item description |
| Product | Product | 6 | the linked Salesforce record → Commercient Sage 100 inventory item (related record), Item code → Commercient external key (earlier package), Inactive item → Active, Item description → Name, Item code → Product code |
| Product to Item Master Reverse Lookup | Commercient Inventory Item Managed Custom Object | 2 | Item code → Commercient external keys, the linked Salesforce record → Commercient product |
| Sales Order Line | Commercient Sales Order Line Managed Custom Object | 64 | returned sales order number, Line key → Commercient external key, the linked Salesforce record → Commercient Sage 100 open sales order (related record), returned sales order number, Line key → Name, returned sales order number → Commercient sales order number, Line key → Commercient line key |
| Sales Order History Header | Commercient Sales Order History Header Managed Custom Object | 88 | returned sales order number → Commercient external key, returned sales order number → Commercient name, the linked Salesforce record → Commercient account, the linked Salesforce record → Commercient customer (related record), Order status → Commercient order status |
| Sales Order History Line | Commercient Sales Order History Line Managed Custom Object | 63 | returned sales order number, Sequence number → Commercient external key, returned sales order number, Sequence number → Commercient name, the linked Salesforce record → Commercient sales order history header, Sequence number → Commercient sequence number, Line key → Commercient line key |
| Invoice Header | Commercient Invoice History Header Managed Custom Object | 87 | Invoice number, Header sequence number → Commercient external key, Invoice number, Header sequence number → Name, AR division number, Customer number → Account, AR division number, Customer number → Sage 100 customer record (custom field), Salesperson division, Salesperson number → Salesperson |
| Invoice Line | Commercient Invoice History Detail Managed Custom Object | 59 | Invoice number,Header sequence number,Detail sequence number → Commercient external key, Invoice number,Header sequence number → Commercient Sage 100 invoice (related record), Item code → Commercient Sage 100 item (related record), Invoice number → Commercient invoice number, Header sequence number → Commercient header sequence number |
| Contact | Contact | 14 | Commercient external key → Commercient external key, account lookup → account lookup, Family name → Family name, Given name → Given name, Email → Email |
| Invoice Payment | Commercient Payment History Managed Custom Object | 32 | the linked Salesforce record → Account, the linked Salesforce record → Sage 100 customer record (custom field), the linked Salesforce record → Invoice, AR division number → Commercient AR division number, Customer number → Commercient customer number |
| Invoice Lot serial | Commercient Invoice History Lot Serial Managed Custom Object | 9 | Invoice number,Header sequence number,Detail sequence number,Lot serial number → Commercient external key, Invoice number → Sage 100 invoices (custom field), Invoice number,Header sequence number,Detail sequence number → Sage 100 invoice line items (custom field), Detail sequence number → Detail sequence number, Header sequence number → Header sequence number |
| Opportunity Header | Opportunity | 7 | returned sales order number → External key (custom field), returned sales order number → Name, the linked Salesforce record → account lookup, Taxable amount → Amount, the linked Salesforce record → price book lookup |
| Opportunity Line | Opportunity line item | 8 | returned sales order number, Line key → External key (custom field), the linked Salesforce record → Quote, the linked Salesforce record → product lookup, the linked Salesforce record → price book entry lookup, Promise date → Service date |
| Quote header | Quote | 22 | returned sales order number → External key (custom field), returned sales order number → Name, the linked opportunity → Opportunity, the linked salesperson → Sage 100 salesperson object (custom), the linked terms → Terms |
| Opportunity Header | Opportunity | 8 | returned sales order number, Line key → External key (custom field), the linked Salesforce record → Quote, the linked Salesforce record → product lookup, the linked Salesforce record → price book entry lookup, Promise date → Service date |

## 6. Community templates

The catalogue carries 104 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 104
- Default operations: insert on 104, update on 104, delete on 104
- Marked as circular sync: 1
- Licence groups they span: 13
- Destination objects: Account, Commercient Inventory Item Managed Custom Object, Commercient
  Invoice History Header Managed Custom Object, Product, Commercient AR Customer Managed Custom
  Object, Commercient Invoice History Detail Managed Custom Object, Price book entry, Commercient
  Invoice History Lot Serial Managed Custom Object, Commercient Sales Order History Line Managed
  Custom Object, Commercient Sales Order History Header Managed Custom Object, Commercient
  Salesperson Managed Custom Object, Commercient Ship-To Address Managed Custom Object, Contact,
  Commercient Payment History Managed Custom Object, Commercient Sales Order Line Managed Custom
  Object, Commercient Sales Order Header Managed Custom Object, Commercient Item Warehouse Managed
  Custom Object, Commercient Sales Order Lot Serial History Managed Custom Object, Opportunity line
  item, Price book entry and 12 more
- Object display names: Product to Item Master Reverse Lookup, Account, Customer to Account Reverse
  Lookup, Customer Master, Invoice Header, Invoice Line, Product, Contact, Invoice Lot serial, Item
  Master, Sales Order History Header, Sales Order History Line and 28 more
- Template groups: Account, Product, Invoice, Sales order, Customer Multi Ship Addresses, CRM
  Opportunity and Line, CRM Quote and Line, Invoice History Headers

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/sage-100-premium`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Sage 100 Premium → Salesforce templates set up. dlake-crmpro-salesforce is the
destination skill this page sits under: its own text is the authority for the Salesforce conventions
that hold across every ERP, and its ERP table lists this page alongside every sibling ERP page for
this destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/sage-100-premium`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
