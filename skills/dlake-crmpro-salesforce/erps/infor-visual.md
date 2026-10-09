---
name: dlake-crmpro-salesforce/erps/infor-visual
kind: erp-summary
description: >-
  Use it when standing up or reading an Infor Visual → Salesforce template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-salesforce, the destination skill this page is a child of, which
  carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Infor Visual: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/infor-visual` (or `list_skills`) against the
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
| **Infor Visual Receivable** | ERP receivable data becomes Commercient Infor Visual Receivable Managed Custom Object in Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Infor Visual Receivable Managed Custom Object | receivables |
| **Infor Visual Receivable Line** | ERP receivable line data becomes Commercient Infor Visual Receivable Line Managed Custom Object in Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Infor Visual Receivable Line Managed Custom Object | receivable lines |
| **Get Users(Owner)** | The templates push User to Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | User | — |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Contact, Commercient Infor Visual Customer Managed Custom Object, Commercient Infor Visual Sales Rep Managed Custom Object | customers, customer contacts, sales reps |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Infor Visual Address Managed Custom Object | customer addresses |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Infor Visual Customer Order Managed Custom Object, Commercient Infor Visual Customer Order Line Managed Custom Object | customer orders, customer order lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Get Users(Owner) | User | Commercient salesperson code | 1 |
| Infor Visual Salesperson | Commercient Infor Visual Sales Rep Managed Custom Object | Commercient external key (Infor package) | 2 |
| Account | Account | Commercient AR customer code | 3 |
| Infor Visual Customer | Commercient Infor Visual Customer Managed Custom Object | Commercient external key (Infor package) | 4 |
| Customer Reverse Lookup Account | Account | Commercient AR customer code | 5 |
| Infor Visual Customer Address | Commercient Infor Visual Address Managed Custom Object | Commercient external key (Infor package) | 6 |
| Infor Visual Order | Commercient Infor Visual Customer Order Managed Custom Object | Commercient external key (Infor package) | 9 |
| Infor Visual Order Line | Commercient Infor Visual Customer Order Line Managed Custom Object | Commercient external key (Infor package) | 10 |
| Infor Visual Receivable | Commercient Infor Visual Receivable Managed Custom Object | Commercient external key (Infor package) | 11 |
| Infor Visual Receivable Line | Commercient Infor Visual Receivable Line Managed Custom Object | Commercient external key (Infor package) | 12 |
| Contact | Contact | External key (custom field) | 16 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| salesperson feed | insert + update | sales reps |
| account feed | insert + update | customers |
| customer feed | insert + update | customers |
| customer account lookup feed | insert + update | customers |
| customer address feed | insert + update | customer addresses |
| order feed | insert + update | customer orders |
| order line feed | insert + update | customer order lines |
| receivable feed | insert + update | receivables |
| receivable line feed | insert + update | receivable lines |
| contact feed | insert + update | customer contacts, customers |

## 4. Order of work

The templates set run sequence from 1 to 16. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 1 — Get Users(Owner)
- 2 — Infor Visual Salesperson
- 3 — Account
- 4 — Infor Visual Customer
- 5 — Customer Reverse Lookup Account
- 6 — Infor Visual Customer Address
- 9 — Infor Visual Order
- 10 — Infor Visual Order Line
- 11 — Infor Visual Receivable
- 12 — Infor Visual Receivable Line
- 16 — Contact

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- receivable feed reads account sync output, customer sync output
- receivable line feed reads receivable sync output, Infor Visual 9 product sync output, Infor
  Visual 9 part sync output, order line sync output; no template in this set writes Infor Visual 9
  product sync output, Infor Visual 9 part sync output
- account feed reads salesperson sync output, terms sync output, user sync output; no template in
  this set writes terms sync output
- customer account lookup feed reads customer sync output
- contact feed reads account sync output, customer sync output, user sync output
- customer feed reads account sync output, salesperson sync output, terms sync output; no template
  in this set writes terms sync output
- customer address feed reads account sync output, customer sync output
- order feed reads account sync output, customer sync output
- order line feed reads order sync output, product sync output, part sync output; no template in
  this set writes product sync output, part sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Infor Visual Salesperson | Commercient Infor Visual Sales Rep Managed Custom Object | 7 | Default commission percentage → Default commission percentage (custom field), Earning code identifier → Earning code identifier, Employee identifier → Commercient employee identifier, Name 1 → Name 1 (custom field), Pay method → Commercient pay method |
| Account | Account | 17 | Commercient AR customer code column → Commercient AR customer code, Phone → Phone, Billing street → Billing street, Billing city → Billing city, Billing state → Billing state |
| Infor Visual Customer | Commercient Infor Visual Customer Managed Custom Object | 134 | Account → Account, Sales rep → Commercient sales rep (related record), linked Infor Visual terms → Commercient Infor Visual terms (related record), Accept 830 → Accept 830, Accept 862 → Accept 862 |
| Customer Reverse Lookup Account | Account | 2 | Commercient AR customer code column → Commercient AR customer code, linked Infor Visual customer (reverse lookup) → Commercient Infor Visual Customer Managed Custom Object |
| Infor Visual Customer Address | Commercient Infor Visual Address Managed Custom Object | 81 | Account → Account, Customer → Commercient customer (related record), Active flag → Active flag (custom field), Accept 830 → Accept 830, Accept 862 → Accept 862 |
| Infor Visual Order | Commercient Infor Visual Customer Order Managed Custom Object | 112 | Account → Account, Customer → Commercient customer (related record), Accept early → Accept early, Create date → Commercient create date, Desired ship date → Commercient desired ship date |
| Infor Visual Order Line | Commercient Infor Visual Customer Order Line Managed Custom Object | 97 | linked Infor Visual order → Commercient Infor Visual order (related record), Part → Commercient part (related record), Product → Product (custom field), Accept early → Accept early, Acknowledgement identifier → Commercient acknowledgement identifier |
| Infor Visual Receivable | Commercient Infor Visual Receivable Managed Custom Object | 48 | Account → Account, Customer → Commercient customer (related record), Buy rate → Commercient buy rate, Commission paid amount → Commercient commission paid amount, Create date → Commercient create date |
| Infor Visual Receivable Line | Commercient Infor Visual Receivable Line Managed Custom Object | 28 | Receivable → Receivable, Amount → Commercient amount, Commission percent → Commission percent, Customer order identifier → Commercient customer order identifier, Customer order line number → Commercient customer order line number |
| Contact | Contact | 10 | account lookup → account lookup, linked Infor Visual customer → Infor Visual customer (custom field), owner lookup → owner lookup, Family name → Family name, Given name → Given name |

## 6. Community templates

The catalogue carries 201 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 201
- Default operations: insert on 201, update on 201, delete on 201
- Marked as circular sync: 0
- Licence groups they span: 15
- Destination objects: Account, Product, Commercient Infor Visual Customer Order Line Managed Custom
  Object, Commercient Infor Visual Customer Order Managed Custom Object, Commercient Infor Visual
  Sales Rep Managed Custom Object, Contact, Commercient Infor Visual Address Managed Custom Object,
  Commercient Infor Visual Customer Managed Custom Object, Commercient Infor Visual Part Managed
  Custom Object, Commercient Infor Visual Receivable Line Managed Custom Object, Commercient Infor
  Visual terms (related record), Price book entry, Commercient Infor Visual Receivable Managed
  Custom Object, Opportunity, Commercient Infor Visual Part Warehouse Managed Custom Object,
  Opportunity line item, User, Price book object, Account contact relation, 14 more and 5 custom
  objects
- Object display names: Account, Infor Visual Customer Address, Infor Visual Order, Infor Visual
  Order Line, Infor Visual Salesperson, Product, Customer Reverse Lookup Account, Infor Visual
  Customer, Infor Visual Terms, Item Warehouse, Infor Visual Part, Standard Price Book Create, 61
  more and 7 further templates
- Template groups: Account, Product, Sales order, CRM Opportunity and Line, Customer Multi Ship
  Addresses, Invoice, Opportunity, Purchase Order, CRM Quote and Line

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/infor-visual`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Infor Visual → Salesforce templates set up. dlake-crmpro-salesforce is the destination
skill this page sits under: its own text is the authority for the Salesforce conventions that hold
across every ERP, and its ERP table lists this page alongside every sibling ERP page for this
destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/infor-visual`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
