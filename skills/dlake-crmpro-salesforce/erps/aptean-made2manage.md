---
name: dlake-crmpro-salesforce/erps/aptean-made2manage
kind: erp-summary
description: >-
  Use it when standing up or reading an Aptean Made2Manage → Salesforce template set, when deciding
  which templates to import and activate, or when a run completes without pushing records and the
  answer is in the view or the configuration row. It extends dlake-crmpro, which covers operating
  CRMPro generally, and dlake-crmpro-salesforce, the destination skill this page is a child of,
  which carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Aptean Made2Manage: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/aptean-made2manage` (or `list_skills`) against
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
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Commercient Aptean Payment Terms Managed Custom Object, Contact, Commercient Aptean Customer Managed Custom Object, Commercient Aptean Salesperson Managed Custom Object | customers, salespeople, addresses, payment term codes, phone numbers, customer extensions |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created and existing ones updated; none are deleted. | Commercient Aptean Address Managed Custom Object | addresses, customers |
| **Product** | The templates push Commercient Aptean Inventory Item Managed Custom Object to Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Aptean Inventory Item Managed Custom Object | inventory master items |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created and existing ones updated; none are deleted. | Commercient Aptean AR Invoice Managed Custom Object, Commercient Aptean AR Invoice Line Managed Custom Object | AR invoice headers, customers, AR invoice lines, inventory master items |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created and existing ones updated; none are deleted. | Commercient Aptean Sales Order Managed Custom Object, Commercient Aptean Sales Order Line Managed Custom Object | sales order headers, customers, sales order items, inventory master items |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Payment term | Commercient Aptean Payment Terms Managed Custom Object | Commercient external key (Aptean package) | 1 |
| Salesperson | Commercient Aptean Salesperson Managed Custom Object | Commercient external key (Aptean package) | 2 |
| Account | Account | Commercient AR customer code | 3 |
| Customer | Commercient Aptean Customer Managed Custom Object | Commercient external key (Aptean package) | 4 |
| Address | Commercient Aptean Address Managed Custom Object | Commercient external key 1 | 5 |
| Item Master | Commercient Aptean Inventory Item Managed Custom Object | Commercient external key (Aptean package) | 6 |
| Sales order header | Commercient Aptean Sales Order Managed Custom Object | Commercient external key (Aptean package) | 7 |
| Sales order detail | Commercient Aptean Sales Order Line Managed Custom Object | Commercient external key (Aptean package) | 8 |
| Invoice header | Commercient Aptean AR Invoice Managed Custom Object | Commercient external key (Aptean package) | 9 |
| Invoice detail | Commercient Aptean AR Invoice Line Managed Custom Object | Commercient external key (Aptean package) | 10 |
| Contact Sync | Contact | External key (custom field) | 13 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| payment terms feed | insert + update | payment term codes |
| salesperson feed | insert + update | salespeople |
| customer feed | insert + update | customers |
| address feed | insert + update | addresses, customers |
| item feed | insert + update | inventory master items |
| sales order feed (Commercient objects) | insert + update | sales order headers, customers |
| sales order line feed (Commercient objects) | insert + update | sales order items, sales order headers, inventory master items |
| invoice feed (Commercient objects) | insert + update | AR invoice headers, customers |
| invoice line feed (Commercient objects) | insert + update | AR invoice lines, AR invoice headers, inventory master items |
| contact feed | insert + update | phone numbers, customer extensions |

## 4. Order of work

The templates set run sequence from 1 to 13. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 1 — Payment term
- 2 — Salesperson
- 3 — Account
- 4 — Customer
- 5 — Address
- 6 — Item Master
- 7 — Sales order header
- 8 — Sales order detail
- 9 — Invoice header
- 10 — Invoice detail
- 13 — Contact Sync

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- Account reads salesperson sync output, account sync output; no template in this set writes
  salesperson sync output, account sync output
- payment terms feed reads payment terms sync output; no template in this set writes payment terms
  sync output
- contact feed reads account sync output; no template in this set writes account sync output
- customer feed reads account sync output, customer sync output; no template in this set writes
  account sync output, customer sync output
- address feed reads customer sync output, address sync output; no template in this set writes
  customer sync output, address sync output
- item feed reads item sync output; no template in this set writes item sync output
- invoice feed (Commercient objects) reads account sync output, customer sync output, invoice sync
  output (Commercient objects); no template in this set writes account sync output, customer sync
  output, invoice sync output (Commercient objects)
- invoice line feed (Commercient objects) reads invoice sync output (Commercient objects), item sync
  output, invoice line sync output (Commercient objects); no template in this set writes invoice
  sync output (Commercient objects), item sync output, invoice line sync output (Commercient
  objects)
- sales order feed (Commercient objects) reads account sync output, customer sync output, sales
  order sync output (Commercient objects); no template in this set writes account sync output,
  customer sync output, sales order sync output (Commercient objects)
- sales order line feed (Commercient objects) reads sales order sync output (Commercient objects),
  item sync output, sales order line sync output (Commercient objects); no template in this set
  writes sales order sync output (Commercient objects), item sync output, sales order line sync
  output (Commercient objects)
- salesperson feed reads salesperson sync output; no template in this set writes salesperson sync
  output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Payment term | Commercient Aptean Payment Terms Managed Custom Object | 13 | → Commercient external key (Aptean package), Terms description → Name, Terms class → Terms class, Terms description → Terms description, Terms code → Terms code |
| Salesperson | Commercient Aptean Salesperson Managed Custom Object | 17 | → Commercient external key (Aptean package), Salesperson last name → Name, →, Salesperson last name → Salesperson last name, Salesperson code → Salesperson code |
| Account | Account | 13 | → Commercient AR customer code, Aptean company name → Name, ERP phone → Phone, Street address → Billing street, Address city → Billing city |
| Customer | Commercient Aptean Customer Managed Custom Object | 73 | → Commercient external key (Aptean package), Aptean company name → Commercient name, Aptean customer number → Commercient customer number, Aptean company name → Commercient company name, Aptean city → Commercient customer city |
| Address | Commercient Aptean Address Managed Custom Object | 32 | the linked Salesforce record → Aptean customer (related record), → external key 1 column, → Name, Long distance → Long distance, Address key → Address key |
| Item Master | Commercient Aptean Inventory Item Managed Custom Object | 96 | → Commercient external key (Aptean package), Aptean item description → Name, Aptean part number → Aptean part number, Item revision → Item revision, Item status code → Item status code |
| Sales order header | Commercient Aptean Sales Order Managed Custom Object | 77 | → Commercient external key (Aptean package), → Commercient name, Aptean sales order number → Commercient sales order number, Aptean customer number → Commercient customer number, Aptean company name → Commercient company name |
| Sales order detail | Commercient Aptean Sales Order Line Managed Custom Object | 69 | → Commercient external key (Aptean package), Order item number → Commercient line item number, Aptean part number → Commercient part number, Part revision → Commercient part revision, Aptean sales order number → Commercient sales order number |
| Invoice header | Commercient Aptean AR Invoice Managed Custom Object | 68 | → Commercient external key (Aptean package), → Commercient name, Aptean bill to city → Commercient billing city, Aptean bill to company → Commercient billing company, Aptean bill to country → Commercient billing country |
| Invoice detail | Commercient Aptean AR Invoice Line Managed Custom Object | 55 | → Commercient external key (Aptean package), → Commercient name, Back order quantity → Commercient back order quantity, Aptean invoice number → Commercient invoice number, Cost → Commercient cost |
| Contact Sync | Contact | 8 | Given name → Given name, Family name → Family name, Fax → Fax, mobile phone property → mobile phone property, Title → Title |

## 6. Community templates

The catalogue carries 182 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 182
- Default operations: insert on 182, update on 182, delete on 182
- Marked as circular sync: 0
- Licence groups they span: 19
- Destination objects: Account, Product, Opportunity, Contact, Quote, Price book entry, Opportunity
  line item, Quote line item, Attachment, Aptean Opportunity (custom object), Aptean Quote (custom
  object), Aptean Quote Line (custom object), Shipping Address (custom object), Sales Order Shipping
  Header (custom object), Sales Order Shipping Line (custom object), User and 29 custom objects
- Object display names: Customer, Sales order detail, Sales order header, Account, Address, Invoice
  detail, Invoice header, Item Master, Account Reverse Lookup, Contact Sync, Salesperson, Product,
  61 more and 22 further templates
- Template groups: Account, Product, Sales order, CRM Opportunity and Line, Customer Multi Ship
  Addresses, Invoice, CRM Quote and Line, Opportunity, Pricebook, Open AR Invoice Header, Quote line
  number

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/aptean-made2manage`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Aptean Made2Manage → Salesforce templates set up. dlake-crmpro-salesforce is the
destination skill this page sits under: its own text is the authority for the Salesforce conventions
that hold across every ERP, and its ERP table lists this page alongside every sibling ERP page for
this destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/aptean-made2manage`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
