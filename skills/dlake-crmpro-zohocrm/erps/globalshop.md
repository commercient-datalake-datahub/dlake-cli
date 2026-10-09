---
name: dlake-crmpro-zohocrm/erps/globalshop
kind: erp-summary
description: >-
  Use it when standing up or reading a GlobalShop → Zoho CRM template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-zohocrm, the destination skill this page is a child of, which carries
  the Zoho CRM conventions that hold across every ERP.
---
# CRMPro → Zoho CRM — GlobalShop: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-zohocrm/erps/globalshop` (or `list_skills`) against the
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
| **Users** | The templates push users to Zoho CRM. New records are created and existing ones updated; none are deleted. | users | — |
| **Global Shop Term** | The templates push Commercient Global Shop terms object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Global Shop terms object | AR terms |
| **Global Shop Salesperson** | The templates push Commercient Global Shop salesperson object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Global Shop salesperson object | salespeople |
| **Account** | The templates push Accounts to Zoho CRM. New records are created and existing ones updated; none are deleted. | Accounts | customer master records, multiple shipping addresses |
| **Global Shop Customer** | The templates push Commercient Global Shop customer object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Global Shop customer object | customer master records |
| **Contacts** | The templates push Contacts to Zoho CRM. New records are created and existing ones updated; none are deleted. | Contacts | contacts |
| **Global Shop Ship to** | The templates push Commercient Global Shop shipping address object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Global Shop shipping address object | multiple shipping addresses |
| **Products** | The templates push Products to Zoho CRM. New records are created and existing ones updated; none are deleted. | Products | inventory master items |
| **Global Shop Item** | The templates push Commercient Global Shop item master object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Global Shop item master object | inventory master items |
| **Global Shop Sales order** | The templates push Commercient Global Shop sales order header object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Global Shop sales order header object | order headers |
| **Global Shop Item list** | The templates push Commercient Global Shop item list object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Global Shop item list object | item master records |
| **Global Shop Sales order line** | The templates push Commercient Global Shop sales order line object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Global Shop sales order line object | order lines |
| **Global Shop Invoice** | The templates push Commercient Global Shop invoice header object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Global Shop invoice header object | order history headers |
| **Global Shop invoice line** | The templates push Commercient Global Shop invoice line object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Global Shop invoice line object | order history lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Users | users | Commercient external key column | 0 |
| Global Shop Term | Commercient Global Shop terms object | Commercient Global Shop external key | 1 |
| Global Shop Salesperson | Commercient Global Shop salesperson object | Commercient Global Shop external key | 2 |
| Account | Accounts | Commercient external key column | 3 |
| Global Shop Customer | Commercient Global Shop customer object | Commercient Global Shop external key | 4 |
| Contacts | Contacts | Commercient external key column | 4 |
| Global Shop Ship to | Commercient Global Shop shipping address object | Commercient Global Shop external key | 5 |
| Products | Products | Commercient external key column | 6 |
| Global Shop Item | Commercient Global Shop item master object | Commercient Global Shop external key | 7 |
| Global Shop Sales order | Commercient Global Shop sales order header object | Commercient Global Shop external key | 8 |
| Global Shop Item list | Commercient Global Shop item list object | Commercient Global Shop external key | 8 |
| Global Shop Sales order line | Commercient Global Shop sales order line object | Commercient Global Shop external key | 9 |
| Global Shop Invoice | Commercient Global Shop invoice header object | Commercient Global Shop external key | 10 |
| Global Shop invoice line | Commercient Global Shop invoice line object | Commercient Global Shop external key | 11 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| terms feed | insert only | AR terms |
| salesperson feed | insert only | salespeople |
| account feed | insert only | customer master records, multiple shipping addresses |
| customer feed | insert + update | customer master records |
| contact feed | insert only | contacts |
| shipping address feed | insert + update | multiple shipping addresses |
| product feed | insert + update | inventory master items |
| item feed | insert + update | inventory master items |
| sales order feed | insert + update | order headers |
| item list feed | insert + update | item master records |
| sales order line feed | insert + update | order lines |
| invoice feed | insert + update | order history headers |
| invoice line feed | insert + update | order history lines |

## 4. Order of work

The templates set run sequence from 0 to 11. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 0 — Users
- 1 — Global Shop Term
- 2 — Global Shop Salesperson
- 3 — Account
- 4 — Global Shop Customer, Contacts
- 5 — Global Shop Ship to
- 6 — Products
- 7 — Global Shop Item
- 8 — Global Shop Sales order, Global Shop Item list
- 9 — Global Shop Sales order line
- 10 — Global Shop Invoice
- 11 — Global Shop invoice line

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- terms feed reads terms sync output; no template in this set writes terms sync output
- salesperson feed reads salesperson sync output; no template in this set writes salesperson sync
  output
- account feed reads account sync output; no template in this set writes account sync output
- customer feed reads account sync output, customer sync output; no template in this set writes
  account sync output, customer sync output
- contact feed reads account sync output, contact sync output; no template in this set writes
  account sync output, contact sync output
- shipping address feed reads account sync output, customer sync output, shipping address sync
  output; no template in this set writes account sync output, customer sync output, shipping address
  sync output
- product feed reads item sync output, product sync output; no template in this set writes item sync
  output, product sync output
- item feed reads product sync output, item sync output; no template in this set writes product sync
  output, item sync output
- sales order feed reads account sync output, customer sync output, sales order sync output; no
  template in this set writes account sync output, customer sync output, sales order sync output
- item list feed reads product sync output, item sync output, item list sync output; no template in
  this set writes product sync output, item sync output, item list sync output
- sales order line feed reads sales order sync output, product sync output, item sync output, sales
  order line sync output; no template in this set writes sales order sync output, product sync
  output, item sync output, sales order line sync output
- invoice feed reads account sync output, customer sync output, invoice sync output; no template in
  this set writes account sync output, customer sync output, invoice sync output
- invoice line feed reads invoice sync output, product sync output, item sync output, invoice line
  sync output; no template in this set writes invoice sync output, product sync output, item sync
  output, invoice line sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Zoho CRM pairs |
|---|---|---|---|
| Global Shop Term | Commercient Global Shop terms object | 14 | Commercient terms code, → Commercient external key column, Commercient terms code, → Name, Commercient terms code → Commercient terms code, Terms discount days → Terms discount days, Terms discount percent → Terms discount percent |
| Global Shop Salesperson | Commercient Global Shop salesperson object | 9 | Key 1,Key 2,Salesperson code,Filler → Commercient external key column, Buyer shipping → Buyer shipping, Filler → Filler, Filler 3 → Filler 3, Salesperson → Name |
| Account | Accounts | 13 | Customer → Commercient external key column, Name of customer → Account name, Telephone number → Phone, Address 1, Address 2 → Billing street (Zoho field), City → Billing city (Zoho field) |
| Global Shop Customer | Commercient Global Shop customer object | 30 | Customer → Commercient external key column, Name of customer → Name, Address 1 → Address 1, Address 2 → Address 2, Assigned user group → Commercient assign user group |
| Contacts | Contacts | 11 | Customer code,Contact type,record identifier → Commercient external key column, Customer code → Account name, Job title → Title, Contact last name,Name,Customer code,Contact type,record identifier → Last name, Contact first name → First name |
| Global Shop Ship to | Commercient Global Shop shipping address object | 74 | Customer,Shipping address sequence → Commercient external key column, Customer name,Shipping address sequence → Name, →, Customer → Customer, Customer name → Customer name |
| Products | Products | 10 | Part, location → Commercient external key (custom field), Part → Product name (Zoho field), Part → ERP product code, the linked Salesforce record → Global Shop inventory (related record), Quantity on hand → Quantity in stock (Zoho field) |
| Global Shop Item | Commercient Global Shop item master object | 78 | Part, location → Commercient external key column, Part → Name, ABC classification code → Commercient ABC classification code, Alternate cost amount → Commercient alternate cost amount, Back order counter → Commercient back order counter |
| Global Shop Sales order | Commercient Global Shop sales order header object | 97 | Order number → Commercient external key column, Order number → Name, Customer → Account, Always discount flag → Commercient always use discount indicator, Area → Area |
| Global Shop Item list | Commercient Global Shop item list object | 50 | Allocation type → Allocation type, Allocated → Allocated, Bin → Bin, Certification code → Commercient cart code, Certification date → Commercient certification date |
| Global Shop Sales order line | Commercient Global Shop sales order line object | 100 | Order number,Record number → Commercient external key column, Order number,Record number → Name, Order number → Commercient Global Shop sales order header (related record), Part,location → Product, Part,location → Commercient Global Shop item master (related record) |
| Global Shop Invoice | Commercient Global Shop invoice header object | 99 | invoice record sync output (generic name), Order number, Order suffix → Commercient external key column, invoice record sync output (generic name), Order number, Order suffix → Name, Customer → Account, Customer → Commercient Global Shop customer (related record), Address 1 → Commercient invoice address line 1 |
| Global Shop invoice line | Commercient Global Shop invoice line object | 70 | invoice record sync output (generic name), Order number, Order suffix, Order line number → Commercient external key column, invoice record sync output (generic name) → the linked GlobalShop invoice header, Part, location → Product, Part, location → Commercient Global Shop item master (related record), Shipping country → Commercient shipping country |

## 6. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-zohocrm/erps/globalshop`.

## 7. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped GlobalShop → Zoho CRM templates set up. dlake-crmpro-zohocrm is the destination skill
this page sits under: its own text is the authority for the Zoho CRM conventions that hold across
every ERP, and its ERP table lists this page alongside every sibling ERP page for this destination.
For the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-zohocrm/erps/globalshop`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
