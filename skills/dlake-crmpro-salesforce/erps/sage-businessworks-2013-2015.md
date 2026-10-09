---
name: dlake-crmpro-salesforce/erps/sage-businessworks-2013-2015
kind: erp-summary
description: >-
  Use it when standing up or reading a Sage BusinessWorks 2013/2015 → Salesforce template set, when
  deciding which templates to import and activate, or when a run completes without pushing records
  and the answer is in the view or the configuration row. It extends dlake-crmpro, which covers
  operating CRMPro generally, and dlake-crmpro-salesforce, the destination skill this page is a
  child of, which carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Sage BusinessWorks 2013/2015: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/sage-businessworks-2013-2015` (or `list_skills`)
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
| **Sync Part** | ERP inventory part data becomes Commercient Inventory Part Managed Custom Object in Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Inventory Part Managed Custom Object | inventory parts |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created and existing ones updated; none are deleted. | Account, Commercient Sage Business Works AR Terms Managed Custom Object, Commercient Sage Business Works AR Customer Managed Custom Object | customers, AR terms codes |
| **Product** | ERP order entry line item, order entry invoice, AR invoice data becomes Commercient Sage Business Works Order Entry Line Item Managed Custom Object, Product in Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Sage Business Works Order Entry Line Item Managed Custom Object, Product | order entry line items, order entry invoices, AR invoices, inventory parts, product lines |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created and existing ones updated; none are deleted. | Commercient Sage Business Works Order Entry Invoice Managed Custom Object | order entry invoices, customers, AR invoices |
| **Pricebook** | ERP inventory part, inventory part price data becomes Price book entry, Price book entry, price book entry in Salesforce. New records are created and existing ones updated; none are deleted. | Price book entry | inventory parts, inventory part prices |
| **Opportunity** | ERP order entry quote, AR customer data becomes Commercient Order Entry Quote Managed Custom Object in Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Order Entry Quote Managed Custom Object | order entry quotes, customers |
| **Quote line number** | ERP order entry line item, order entry sales order data becomes Commercient Sage Business Works Order Entry Line Item Managed Custom Object in Salesforce. New records are created and existing ones updated; none are deleted. | Commercient Sage Business Works Order Entry Line Item Managed Custom Object | order entry line items, order entry sales orders |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created and existing ones updated; none are deleted. | Commercient Order Entry Sales Order Managed Custom Object, Commercient Sage Business Works Order Entry Line Item Managed Custom Object | order entry sales orders, customers, order entry line items |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Sync Account | Account | Commercient AR customer code | 1 |
| Sync AR customer | Commercient Sage Business Works AR Customer Managed Custom Object | Commercient external key | 2 |
| Sync AR terms | Commercient Sage Business Works AR Terms Managed Custom Object | Commercient external key | 5 |
| Sync order entry invoice | Commercient Sage Business Works Order Entry Invoice Managed Custom Object | Commercient external key | 6 |
| Sync order entry line item | Commercient Sage Business Works Order Entry Line Item Managed Custom Object | Commercient external key | 7 |
| Sync Product object | Product | Commercient external key (earlier package) | 8 |
| Sync Part | Commercient Inventory Part Managed Custom Object | Commercient external key | 9 |
| Product object Reverse Lookup | Product | Commercient external key (earlier package) | 10 |
| Account Reverse Lookup | Account | Commercient AR customer code | 11 |
| Price Book Entry Create | Price book entry | External key (custom field) | 12 |
| Price Book Entry update | Price book entry | External key (custom field) | 13 |
| Custom Price Book Entry Create | Price book entry | External key (custom field) | 14 |
| Custom Price Book Entry update | price book entry | External key (custom field) | 15 |
| Sync OE Sales Order | Commercient Order Entry Sales Order Managed Custom Object | Commercient external key | 16 |
| Sync OE Sales Order Line | Commercient Sage Business Works Order Entry Line Item Managed Custom Object | Commercient external key | 17 |
| Sync quote sync output (generic name) | Commercient Order Entry Quote Managed Custom Object | Commercient external key | 18 |
| Sync quote sync output (generic name) Line | Commercient Sage Business Works Order Entry Line Item Managed Custom Object | Commercient external key | 19 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| account feed | insert + update | customers |
| AR customer feed | insert + update | customers |
| AR terms feed | insert + update | AR terms codes |
| order entry invoice feed | insert + update | order entry invoices, customers, AR invoices |
| order entry line item feed | insert + update | order entry line items, order entry invoices, AR invoices |
| product feed | insert + update | inventory parts, product lines |
| inventory part feed | insert + update | inventory parts |
| product part lookup feed | insert + update | inventory parts |
| customer account lookup feed | insert + update | customers |
| price book feed (new entries) | insert only | inventory parts, inventory part prices |
| price book feed (changes) | insert + update | inventory parts, inventory part prices |
| custom price book feed (new entries) | — | inventory parts, inventory part prices |
| custom price book feed (changes) | — | inventory parts, inventory part prices |
| order entry sales order feed | insert + update | order entry sales orders, customers |
| sales order line feed | insert + update | order entry line items, order entry sales orders |
| order entry quote feed | insert + update | order entry quotes, customers |
| sales order line feed | insert + update | order entry line items, order entry sales orders |

## 4. Order of work

The templates set run sequence from 1 to 19. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 1 — Sync Account
- 2 — Sync AR customer
- 5 — Sync AR terms
- 6 — Sync order entry invoice
- 7 — Sync order entry line item
- 8 — Sync Product object
- 9 — Sync Part
- 10 — Product object Reverse Lookup
- 11 — Account Reverse Lookup
- 12 — Price Book Entry Create
- 13 — Price Book Entry update
- 14 — Custom Price Book Entry Create
- 15 — Custom Price Book Entry update
- 16 — Sync OE Sales Order
- 17 — Sync OE Sales Order Line
- 18 — Sync quote sync output (generic name)
- 19 — Sync quote sync output (generic name) Line

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- inventory part feed reads product sync output
- customer account lookup feed reads AR customer sync output
- AR customer feed reads account sync output
- order entry line item feed reads order entry invoice sync output, AR invoice sync output; no
  template in this set writes AR invoice sync output
- order entry invoice feed reads account sync output, AR customer sync output, AR invoice sync
  output; no template in this set writes AR invoice sync output
- price book feed (new entries) reads product sync output
- price book feed (changes) reads product sync output
- custom price book feed (new entries) reads product sync output
- custom price book feed (changes) reads product sync output
- product part lookup feed reads inventory part sync output
- order entry quote feed reads account sync output, AR customer sync output
- sales order line feed reads order entry sales order sync output, sales order line sync output
- order entry sales order feed reads account sync output, AR customer sync output
- sales order line feed reads order entry sales order sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Sync Account | Account | 10 | record identifier → Commercient AR customer code, Name → Name, Address 1, Address 2 → Billing street, City → Billing city, State → Billing state |
| Sync AR customer | Commercient Sage Business Works AR Customer Managed Custom Object | 127 | record identifier → Commercient external key, record identifier → Commercient name, AR customer surrogate key → Commercient AR customer surrogate key, record identifier → Commercient identifier, Address 1 → Commercient address line 1 |
| Sync AR terms | Commercient Sage Business Works AR Terms Managed Custom Object | 9 | AR terms surrogate key → Commercient external key, AR terms surrogate key → Name, Prepaid flag → Commercient prepaid flag, Discount percent → Commercient discount percent, Discount mode → Commercient discount mode |
| Sync order entry invoice | Commercient Sage Business Works Order Entry Invoice Managed Custom Object | 36 | the linked Salesforce record → Commercient account, the linked Salesforce record → Commercient Sage Business Works customer (related record), the linked Salesforce record → Commercient Sage Business Works invoice header (related record), Invoice number → Invoice number (custom field), Invoice date → Invoice date (custom field) |
| Sync order entry line item | Commercient Sage Business Works Order Entry Line Item Managed Custom Object | 36 | Entity foreign key,Order entry line item surrogate key → Commercient external key, Invoice number,Order entry line item surrogate key → Commercient name, Order entry line item surrogate key → Commercient order entry line item surrogate key, Next record → Commercient next record, Type → Commercient type |
| Sync Product object | Product | 5 | record identifier → Product code, record identifier → Name, record identifier → Commercient external key (earlier package), Description 1 → Description, Active → Active |
| Sync Part | Commercient Inventory Part Managed Custom Object | 113 | record identifier → Commercient external key, record identifier → Commercient identifier, the linked Salesforce record → Commercient product, record identifier → Name, record identifier → Product code |
| Product object Reverse Lookup | Product | 2 | record identifier → Commercient external key (earlier package), the linked Salesforce record → Sage Business Works part (custom field) |
| Account Reverse Lookup | Account | 2 | record identifier → Commercient AR customer code, the linked Salesforce record → Commercient Sage Business Works customer (related record) |
| Price Book Entry Create | Price book entry | 5 | record identifier → External key (custom field), the linked Salesforce record → product lookup, Price 1 amount 2 → Unit price, the linked Salesforce record → price book lookup, Active → Active |
| Price Book Entry update | Price book entry | 3 | record identifier → External key (custom field), Price 1 amount 2 → Unit price, Active → Active |
| Custom Price Book Entry Create | Price book entry | 6 | record identifier → External key (custom field), the linked Salesforce record → product lookup, Price 1 amount 1 → Unit price, Row timestamp → Row timestamp |
| Custom Price Book Entry update | price book entry | 3 | record identifier → External key (custom field), Price 1 amount 1 → Unit price, Active → Active |
| Sync OE Sales Order | Commercient Order Entry Sales Order Managed Custom Object | 64 | Order entry sales order surrogate key → Commercient external key, Order number → Name, AR customer foreign key → Commercient account, AR customer foreign key → Commercient Sage Business Works customer (related record), Type → Type |
| Sync OE Sales Order Line | Commercient Sage Business Works Order Entry Line Item Managed Custom Object | 24 | record identifier → Commercient identifier, Order number → Name, Description → Description, Comment 1 → Comment 1, Comment 2 → Comment 2 |
| Sync quote sync output (generic name) | Commercient Order Entry Quote Managed Custom Object | 66 | the linked Salesforce account record → Commercient account, the linked price book → Commercient Sage Business Works customer (related record), external key column → Commercient external key, Name → Name, Next record → Next record |
| Sync quote sync output (generic name) Line | Commercient Sage Business Works Order Entry Line Item Managed Custom Object | 25 | Entity foreign key, Order entry line item surrogate key → Commercient external key, Order number, Order entry line item surrogate key → Name, record identifier → Commercient identifier, Description → Description, Comment 1 → Comment 1 |

## 6. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/sage-businessworks-2013-2015`.

## 7. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Sage BusinessWorks 2013/2015 → Salesforce templates set up. dlake-crmpro-salesforce is
the destination skill this page sits under: its own text is the authority for the Salesforce
conventions that hold across every ERP, and its ERP table lists this page alongside every sibling
ERP page for this destination. For the extract leg that fills the source data, see dlake-normalsync;
for the on-premises agent that runs it, dlake-syncagent; for the writeback leg,
dlake-txdownloaderpro; for standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/sage-businessworks-2013-2015`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
