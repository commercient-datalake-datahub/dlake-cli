---
name: dlake-crmpro-salesforce/erps/traverse-process-pro-global
kind: erp-summary
description: >-
  Use it when standing up or reading a Traverse Process PRO Global → Salesforce template set, when
  deciding which templates to import and activate, or when a run completes without pushing records
  and the answer is in the view or the configuration row. It extends dlake-crmpro, which covers
  operating CRMPro generally, and dlake-crmpro-salesforce, the destination skill this page is a
  child of, which carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Traverse Process PRO Global: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/traverse-process-pro-global` (or `list_skills`)
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
| **Get Salesforce User** | The templates push User to Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | User | — |
| **Traverse Process Pro Global Sales order transaction header** | ERP sales order transaction header table data becomes Commercient Sales Order Transaction Header Managed Custom Object in Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Sales Order Transaction Header Managed Custom Object | sales order transaction headers |
| **Traverse Process Pro Global Sales order transaction detail** | ERP sales order transaction detail table data becomes Commercient Sales Order Transaction Detail Managed Custom Object in Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Sales Order Transaction Detail Managed Custom Object | sales order transaction lines |
| **Traverse Process Pro Global AR history header** | ERP AR history header table data becomes Commercient AR History Header Managed Custom Object in Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient AR History Header Managed Custom Object | AR history headers |
| **Traverse Process Pro Global AR history detail** | ERP AR history detail table data becomes Commercient AR History Detail Managed Custom Object in Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient AR History Detail Managed Custom Object | AR history lines |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Commercient AR Customer Managed Custom Object | customers, customer sales, customer shipping addresses |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient AR Shipping Address Managed Custom Object | customer shipping addresses |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient AR Open Invoice Managed Custom Object, Invoice (custom object), Invoice Detail (custom object) | open AR invoices, AR history headers (source view), AR history headers, ERP, AR history lines (source view) |
| **Product** | The templates push Product to Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | Product | — |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created, existing ones updated, and records removed in the ERP are deleted. | Order Header (custom object), Order Detail (custom object) | sales order transaction headers (source view), sales order transaction headers, sales order transaction lines (source view), sales order transaction lines, ERP |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Get Salesforce User | User | Commercient salesperson code | 0 |
| Get Salesforce Product | Product | — | 0 |
| Account | Account | Commercient AR customer code | 1 |
| Child Account | Account | Commercient AR customer code | 2 |
| Traverse Process Pro Global Customer | Commercient AR Customer Managed Custom Object | Commercient customer identifier | 3 |
| Traverse Process Pro Global Customer to account lookup | Account | Commercient AR customer code | 4 |
| Traverse Process Pro Global Ship to | Commercient AR Shipping Address Managed Custom Object | Commercient external key (package 7) | 5 |
| Traverse Process Pro Global Sales order transaction header | Commercient Sales Order Transaction Header Managed Custom Object | Commercient transaction identifier | 6 |
| Traverse Process Pro Global Sales order transaction detail | Commercient Sales Order Transaction Detail Managed Custom Object | Commercient external key (package 7) | 7 |
| Traverse Process Pro Global AR history header | Commercient AR History Header Managed Custom Object | Commercient external key (package 7) | 8 |
| Traverse Process Pro Global AR history detail | Commercient AR History Detail Managed Custom Object | Commercient external key (package 7) | 9 |
| Traverse Process Pro Global AR open invoice | Commercient AR Open Invoice Managed Custom Object | Commercient counter | 10 |
| Order Header | Order Header (custom object) | Sales order header identifier (custom field) | 11 |
| Order Detail | Order Detail (custom object) | Sales order line identifier (custom field) | 12 |
| Invoice Header | Invoice (custom object) | Invoice header identifier (custom field) | 13 |
| Invoice Detail | Invoice Detail (custom object) | Invoice line identifier (custom field) | 14 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| account feed | insert + update | customers, customer sales |
| child account feed | insert + update | customer shipping addresses, customers |
| customer feed | insert + update | customers |
| customer account lookup feed | insert + update | customers |
| shipping address feed | insert + update | customer shipping addresses |
| sales order transaction header feed | insert + update | sales order transaction headers |
| sales order transaction detail feed | insert + update | sales order transaction lines |
| AR history header feed | insert + update | AR history headers |
| AR history detail feed | insert + update | AR history lines |
| AR open invoice feed | insert + update | open AR invoices |
| order header feed | insert + update | sales order transaction headers (source view), sales order transaction headers |
| order detail feed | insert + update | sales order transaction lines (source view), sales order transaction lines, sales order transaction headers, sales order transaction headers (source view), ERP |
| invoice header feed | insert + update | AR history headers (source view), AR history headers, ERP |
| invoice detail feed | insert + update | AR history lines (source view), AR history lines, AR history headers (source view), ERP |

## 4. Order of work

The templates set run sequence from 0 to 14. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 0 — Get Salesforce User, Get Salesforce Product
- 1 — Account
- 2 — Child Account
- 3 — Traverse Process Pro Global Customer
- 4 — Traverse Process Pro Global Customer to account lookup
- 5 — Traverse Process Pro Global Ship to
- 6 — Traverse Process Pro Global Sales order transaction header
- 7 — Traverse Process Pro Global Sales order transaction detail
- 8 — Traverse Process Pro Global AR history header
- 9 — Traverse Process Pro Global AR history detail
- 10 — Traverse Process Pro Global AR open invoice
- 11 — Order Header
- 12 — Order Detail
- 13 — Invoice Header
- 14 — Invoice Detail

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- sales order transaction header feed reads account sync output, customer sync output
- sales order transaction detail feed reads sales order transaction header sync output
- AR history header feed reads account sync output, customer sync output, sales order transaction
  header sync output
- AR history detail feed reads AR history header sync output
- account feed reads user sync output
- child account feed reads user sync output
- customer account lookup feed reads customer sync output, account sync output
- customer feed reads account sync output
- shipping address feed reads account sync output, customer sync output
- AR open invoice feed reads account sync output
- invoice header feed reads account sync output, order header sync output
- invoice detail feed reads invoice header sync output, product sync output
- order header feed reads account sync output
- order detail feed reads order header sync output, product sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Account | Account | 20 | Commercient AR customer code column → Commercient AR customer code, Billing street → Billing street, Billing city → Billing city, Billing state → Billing state, Billing country → Billing country |
| Child Account | Account | 13 | Commercient AR customer code column → Commercient AR customer code, parent account lookup → parent account lookup, Billing street → Billing street, Billing city → Billing city, Billing state → Billing state |
| Traverse Process Pro Global Customer | Commercient AR Customer Managed Custom Object | 65 | Customer identifier → Commercient customer identifier, Account → Account, Account type → Account type, Address 1 → Commercient address line 1, Address 2 → Commercient address line 2 |
| Traverse Process Pro Global Customer to account lookup | Account | 2 | Commercient AR customer code column → Commercient AR customer code, Commercient Traverse customer record column → Commercient Traverse customer record (related record) |
| Traverse Process Pro Global Ship to | Commercient AR Shipping Address Managed Custom Object | 24 | Address 1 → Commercient address line 1, Address 2 → Commercient address line 2, Address type → Commercient address type, Attention → Commercient attention, City → Commercient city |
| Traverse Process Pro Global Sales order transaction header | Commercient Sales Order Transaction Header Managed Custom Object | 75 | Transaction identifier → Commercient transaction identifier, Account → Account, Traverse customer record column → Traverse customer record (custom field), Transaction type → Commercient transaction type, Batch identifier → Batch identifier |
| Traverse Process Pro Global Sales order transaction detail | Commercient Sales Order Transaction Detail Managed Custom Object | 71 | sales order transaction header table → Commercient Sales Order Transaction Header Managed Custom Object, Transaction identifier → Commercient transaction identifier, Entry number → Commercient entry number, Item job → Commercient item job, Location identifier → Location identifier |
| Traverse Process Pro Global AR history header | Commercient AR History Header Managed Custom Object | 95 | Account → Account, Traverse customer record column → Commercient Traverse customer record (related record), sales order transaction header table → Commercient Sales Order Transaction Header Managed Custom Object, Posting run → Commercient posting run, Transaction identifier → Commercient transaction identifier |
| Traverse Process Pro Global AR history detail | Commercient AR History Detail Managed Custom Object | 80 | AR history header table → Commercient AR History Header Managed Custom Object, Transaction identifier → Commercient transaction identifier, Entry number → Commercient entry number, Item job → Commercient item job, Warehouse identifier → Commercient warehouse identifier |
| Traverse Process Pro Global AR open invoice | Commercient AR Open Invoice Managed Custom Object | 30 | Counter → Commercient counter, Account → Account, Customer identifier → Commercient customer identifier, Invoice number → Invoice number, Record type → Record type |
| Order Header | Order Header (custom object) | 16 | Sales order header identifier column → Sales order header identifier (custom field), Account → Account (custom field), Required date → Required date (custom field), Order total → Order total (custom field), Customer ship to number → Customer ship to number (custom field) |
| Order Detail | Order Detail (custom object) | 10 | Sales order line identifier column → Sales order line identifier (custom field), Order header column → Order Header (custom object), Product → Product (custom field), Description → Description (custom field), Quantity → Quantity (custom field) |
| Invoice Header | Invoice (custom object) | 17 | Invoice header identifier column → Invoice header identifier (custom field), Account → Account (custom field), Order → Order (custom field), returned purchase order number → PO number (custom field), Invoice date → Invoice date (custom field) |
| Invoice Detail | Invoice Detail (custom object) | 11 | Invoice line identifier column → Invoice line identifier (custom field), Invoice → Invoice (custom object), Product → Product (custom field), Description → Description (custom field), Quantity → Quantity (custom field) |

## 6. Community templates

The catalogue carries 19 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 19
- Default operations: insert on 19, update on 19, delete on 19
- Marked as circular sync: 0
- Licence groups they span: 8
- Destination objects: Account, Product, Commercient AR Customer Managed Custom Object, Commercient
  AR History Detail Managed Custom Object, Commercient AR History Header Managed Custom Object,
  Commercient AR Open Invoice Managed Custom Object, Commercient AR Shipping Address Managed Custom
  Object, Commercient Inventory Item Managed Custom Object, Commercient Sales Order Transaction
  Detail Managed Custom Object, Commercient Sales Order Transaction Header Managed Custom Object,
  Invoice (custom object), Invoice Detail (custom object), Order Detail (custom object), Order
  Header (custom object), User
- Object display names: Account, Child Account, Get Salesforce Product, Get Salesforce User, Invoice
  Detail, Invoice Header, Order Detail, Order Header, Product, Traverse Process Pro Global AR
  history detail, Traverse Process Pro Global AR history header, Traverse Process Pro Global AR open
  invoice and 7 more
- Template groups: Account, Product, Invoice, Sales order, Customer Multi Ship Addresses

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/traverse-process-pro-global`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Traverse Process PRO Global → Salesforce templates set up. dlake-crmpro-salesforce is
the destination skill this page sits under: its own text is the authority for the Salesforce
conventions that hold across every ERP, and its ERP table lists this page alongside every sibling
ERP page for this destination. For the extract leg that fills the source data, see dlake-normalsync;
for the on-premises agent that runs it, dlake-syncagent; for the writeback leg,
dlake-txdownloaderpro; for standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/traverse-process-pro-global`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
