---
name: dlake-crmpro-salesforce/erps/steelviking
kind: erp-summary
description: >-
  Use it when standing up or reading a Steel Viking → Salesforce template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-salesforce, the destination skill this page is a child of, which
  carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Steel Viking: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/steelviking` (or `list_skills`) against the
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
| **Part** | ERP Part data becomes Commercient Part Managed Custom Object in Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Part Managed Custom Object | parts |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | account, Commercient Customer Managed Custom Object | customers |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Invoice Managed Custom Object, Commercient Invoice Line Managed Custom Object | invoices, sales orders, customers, invoice lines |
| **Product** | ERP Part, Price Book data becomes Product, Commercient Steel Viking Price Book Managed Custom Object in Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | Product, Commercient Steel Viking Price Book Managed Custom Object | parts, price books |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Sales Order Managed Custom Object, Commercient Sales Order Line Managed Custom Object | sales orders, sales order lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| CRM Account | account | Commercient AR customer code | 1 |
| Customer | Commercient Customer Managed Custom Object | Commercient external key (Baan package) | 2 |
| Part | Commercient Part Managed Custom Object | Commercient external key (Baan package) | 3 |
| Product | Product | Commercient external key (earlier package) | 4 |
| Sales order | Commercient Sales Order Managed Custom Object | Commercient external key (Baan package) | 5 |
| Invoice | Commercient Invoice Managed Custom Object | Commercient external key (Baan package) | 6 |
| Sales order line | Commercient Sales Order Line Managed Custom Object | Commercient external key (Baan package) | 7 |
| invoice line | Commercient Invoice Line Managed Custom Object | Commercient external key (Baan package) | 8 |
| Price Book | Commercient Steel Viking Price Book Managed Custom Object | Commercient external key (Baan package) | 9 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| CRM account feed | insert only | customers |
| customer feed | insert + update | customers |
| part feed | insert + update | parts |
| product feed | insert + update | parts |
| sales order feed | insert + update | sales orders |
| invoice feed | insert + update | invoices, sales orders, customers |
| sales order line feed | insert + update | sales order lines |
| invoice line feed | insert + update | invoice lines |
| price book feed | insert + update | price books |

## 4. Order of work

The templates set run sequence from 1 to 9. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 1 — CRM Account
- 2 — Customer
- 3 — Part
- 4 — Product
- 5 — Sales order
- 6 — Invoice
- 7 — Sales order line
- 8 — invoice line
- 9 — Price Book

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- CRM account feed reads CRM account sync output (alternative generic name); no template in this set
  writes CRM account sync output (alternative generic name)
- invoice line feed reads Invoice: Part: Product: invoice line; no template in this set writes
  Invoice: invoice line
- price book feed reads CRM account sync output (alternative generic name), Product: Price Book; no
  template in this set writes CRM account sync output (alternative generic name), Price Book
- sales order feed reads CRM account sync output (alternative generic name), Customer: Sales order;
  no template in this set writes CRM account sync output (alternative generic name), Customer: Sales
  order
- sales order line feed reads Sales order, Part: Product: Sales order line; no template in this set
  writes Sales order, Sales order line

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| CRM Account | account | 9 | Commercient AR customer code column → Commercient AR customer code, Billing street → Billing street, Billing city → Billing city, Billing state → Billing state, Billing country → Billing country |
| Customer | Commercient Customer Managed Custom Object | 25 | Account → Account, Customer identifier → Commercient customer identifier, Customer number → Commercient customer number, Customer name → Commercient customer name, Sales rep identifier → Commercient sales rep identifier |
| Part | Commercient Part Managed Custom Object | 2 | Part identifier → Commercient part identifier, Part number (Steel Viking) → Commercient part number |
| Product | Product | 4 | Commercient external key (earlier package) → Commercient external key (earlier package), Product code → Product code, Description → Description, Active → Active |
| Sales order | Commercient Sales Order Managed Custom Object | 29 | Account → Account, Sales order identifier → Commercient sales order identifier, Sales order number (Steel Viking) → Commercient sales order number, Customer identifier → Commercient customer identifier, Order date → Commercient order date |
| Invoice | Commercient Invoice Managed Custom Object | 32 | Account → Account, returned invoice identifier → returned invoice identifier, Invoice number (Steel Viking) → Invoice number (Steel Viking), Customer identifier → Commercient customer identifier, Invoice date → Invoice date |
| Sales order line | Commercient Sales Order Line Managed Custom Object | 13 | Sales order line identifier → Commercient sales order line identifier, Sales order identifier → Commercient sales order identifier, Part identifier → Commercient part identifier, Line number → Commercient line number, Order quantity → Commercient order quantity |
| invoice line | Commercient Invoice Line Managed Custom Object | 13 | Invoice line identifier → Invoice line identifier, returned invoice identifier → returned invoice identifier, Invoice line sequence number → Invoice line sequence number, Invoice line type → Invoice line type, Invoice line description → Invoice line description |
| Price Book | Commercient Steel Viking Price Book Managed Custom Object | 14 | Account → Account, Product → Product, Part customer price identifier → Part customer price identifier, Price type → Price type, Customer identifier → Commercient customer identifier |

## 6. Community templates

The catalogue carries 9 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 9
- Default operations: insert on 9, update on 9, delete on 9
- Marked as circular sync: 0
- Licence groups they span: 6
- Destination objects: account, Commercient Customer Managed Custom Object, Commercient Invoice
  Managed Custom Object, Commercient Invoice Line Managed Custom Object, Commercient Part Managed
  Custom Object, Commercient Sales Order Managed Custom Object, Commercient Sales Order Line Managed
  Custom Object, Commercient Steel Viking Price Book Managed Custom Object, Product
- Object display names: CRM Account, Customer, Invoice, invoice line, Part, Price Book, Product,
  Sales order, Sales order line
- Template groups: Account, Invoice, Product, Sales order

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/steelviking`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Steel Viking → Salesforce templates set up. dlake-crmpro-salesforce is the destination
skill this page sits under: its own text is the authority for the Salesforce conventions that hold
across every ERP, and its ERP table lists this page alongside every sibling ERP page for this
destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/steelviking`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
