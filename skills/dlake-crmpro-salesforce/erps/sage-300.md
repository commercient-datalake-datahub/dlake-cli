---
name: dlake-crmpro-salesforce/erps/sage-300
kind: erp-summary
description: >-
  Use it when standing up or reading a Sage 300 → Salesforce template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-salesforce, the destination skill this page is a child of, which
  carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Sage 300: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/sage-300` (or `list_skills`) against the
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
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Contacts, Commercient Sage 300 Customer Managed Custom Object, Commercient Sage 300 Sales Rep Managed Custom Object | customers, customer shipping locations |
| **AR Invoice Payments** | Commercient Syncs the payment details that are held in the receivables file and held against an invoice in the ERP. The amount received from a customer, the date of payment, the method of payment, whether partial or full payment, etc New records are created and existing ones updated; none are deleted. | Commercient Sage 300 Invoice Payment Managed Custom Object | applied receipt details, AR invoice batch headers, receipts |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created and existing ones updated; none are deleted. | Commercient Sage 300 Version 1 Invoice Header Managed Custom Object, Commercient Sage 300 Version 1 Invoice Line Managed Custom Object, Commercient Invoice Document Payment Managed Custom Object | AR invoice batch headers, AR invoice batch lines, invoice line optional fields, open document payments |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created and existing ones updated; none are deleted. | Commercient Sage 300 Sales Order Header Managed Custom Object, Commercient Sage 300 Sales Order Line Managed Custom Object | order entry headers, order entry lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| GET USER | users | — | 0 |
| Sage 300 Salesperson | Commercient Sage 300 Sales Rep Managed Custom Object | Commercient external key | 2 |
| Parent Account | Account | Commercient AR customer code | 3 |
| Child Account | Account | Commercient AR customer code | 4 |
| Sage 300 Customer | Commercient Sage 300 Customer Managed Custom Object | Commercient external key | 5 |
| Contacts | Contacts | Commercient external key column | 6 |
| Sage 300 Sales order header | Commercient Sage 300 Sales Order Header Managed Custom Object | Commercient external key | 8 |
| Sage 300 Sales order detail | Commercient Sage 300 Sales Order Line Managed Custom Object | Commercient external key | 9 |
| Invoice Header | Commercient Sage 300 Version 1 Invoice Header Managed Custom Object | Commercient external key | 10 |
| Invoice Line | Commercient Sage 300 Version 1 Invoice Line Managed Custom Object | Commercient external key | 11 |
| Invoice Payment | Commercient Sage 300 Invoice Payment Managed Custom Object | Commercient external key | 12 |
| Invoice Document Payment | Commercient Invoice Document Payment Managed Custom Object | Commercient external key | 13 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| account feed | insert + update | customers, customer shipping locations |
| account feed | insert + update | customers, customer shipping locations |
| child account feed | insert + update | customer shipping locations, customers |
| customer feed | insert only | customers |
| contact feed | insert + update | customers |
| sales order feed | insert only | order entry headers |
| sales order line feed | insert only | order entry lines |
| invoice feed | insert + update | AR invoice batch headers |
| invoice line feed | insert + update | AR invoice batch lines, invoice line optional fields |
| invoice payment feed | insert + update | applied receipt details, AR invoice batch headers, receipts |
| invoice document payment feed | insert + update | open document payments, AR invoice batch headers |

## 4. Order of work

The templates set run sequence from 0 to 13. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 0 — GET USER
- 2 — Sage 300 Salesperson
- 3 — Parent Account
- 4 — Child Account
- 5 — Sage 300 Customer
- 6 — Contacts
- 8 — Sage 300 Sales order header
- 9 — Sage 300 Sales order detail
- 10 — Invoice Header
- 11 — Invoice Line
- 12 — Invoice Payment
- 13 — Invoice Document Payment

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- account feed reads user sync output, salesperson sync output, account sync output; no template in
  this set writes salesperson sync output, account sync output
- child account feed reads user sync output, account sync output; no template in this set writes
  account sync output
- invoice payment feed reads account sync output, customer sync output, invoice sync output; no
  template in this set writes account sync output, customer sync output
- contact feed reads account sync output, customer sync output, contact sync output; no template in
  this set writes account sync output, customer sync output, contact sync output
- customer feed reads account sync output, customer sync output; no template in this set writes
  account sync output, customer sync output
- invoice feed reads account sync output, customer sync output; no template in this set writes
  account sync output, customer sync output
- invoice line feed reads invoice sync output; no template in this set writes
- invoice document payment feed reads account sync output, customer sync output, invoice sync
  output; no template in this set writes account sync output, customer sync output
- sales order feed reads account sync output, customer sync output, sales order sync output; no
  template in this set writes account sync output, customer sync output, sales order sync output
- sales order line feed reads sales order sync output, item sync output, product sync output, sales
  order line sync output; no template in this set writes sales order sync output, item sync output,
  product sync output, sales order line sync output
- account feed reads user sync output, salesperson sync output, account sync output; no template in
  this set writes salesperson sync output, account sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Sage 300 Salesperson | Commercient Sage 300 Sales Rep Managed Custom Object | 12 | ERP customer number → Commercient AR customer code, Customer name → Name, Street address line 1, Street address line 2, Street address line 3 → Billing street, Customer city → Billing city, State code → Billing state |
| Parent Account | Account | 13 | ERP customer number → Commercient AR customer code, Customer name → Name, Street address line 1, Street address line 2, Street address line 3 → Billing street, Customer city → Billing city, State code → Billing state |
| Child Account | Account | 7 | ERP customer number, Customer shipping location code → Commercient AR customer code, Customer name, Customer shipping location code → Name, Street address line 1, Street address line 2, Street address line 3, Street address line 4 → Shipping street, Customer city → Shipping city, State code → Shipping state |
| Sage 300 Customer | Commercient Sage 300 Customer Managed Custom Object | 2 | ERP customer number → Commercient external key, Customer name → Name |
| Contacts | Contacts | 9 | Account name → Account name, Sage 300 customer (custom field) → Sage 300 customer (custom field), Commercient external key column → Commercient external key column, Last name → Last name, First name → First name |
| Sage 300 Sales order header | Commercient Sage 300 Sales Order Header Managed Custom Object | 175 | Order unique key → Commercient external key, Order unique key → Name, Customer → Commercient account, Customer → Commercient customer (related record), Audit date → Commercient audit date |
| Sage 300 Sales order detail | Commercient Sage 300 Sales Order Line Managed Custom Object | 3 | Order unique key, ERP line number → Commercient external key, Order unique key, ERP line number → Name, Order unique key → Sales order header (related record) |
| Invoice Header | Commercient Sage 300 Version 1 Invoice Header Managed Custom Object | 100 | Batch number,Entry number → Commercient external key, Invoice number → Name, ERP customer number → Account, ERP customer number → Customer, Audit user → Audit user |
| Invoice Line | Commercient Sage 300 Version 1 Invoice Line Managed Custom Object | 86 | Batch number,Entry number,Detail line number → Commercient external key, Batch number,Entry number,Detail line number → Name, the linked Salesforce record → Commercient invoice header (related record), Audit user → Audit user, Audit organisation → Audit organisation |
| Invoice Payment | Commercient Sage 300 Invoice Payment Managed Custom Object | 82 | Invoice number → Commercient external key, Invoice number → Commercient name, AR version → Commercient AR version, Account set → Commercient account set, Bank code → Commercient bank code |
| Invoice Document Payment | Commercient Invoice Document Payment Managed Custom Object | 43 | ERP customer number,Payment number,Remittance number,Business date,Transaction type,Sequence number → Commercient external key, ERP customer number,Invoice number,Payment number,Remittance number,Business date,Transaction type,Sequence number → Commercient name, ERP customer number → Commercient customer number, Invoice number → Commercient invoice number, Payment number → Commercient payment number |

## 6. Community templates

The catalogue carries 254 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 254
- Default operations: insert on 254, update on 254, delete on 254
- Marked as circular sync: 2
- Licence groups they span: 13
- Destination objects: Account, Commercient Sage 300 Customer Managed Custom Object, Product,
  Commercient Sage 300 Version 1 Invoice Line Managed Custom Object, Commercient Sage 300 Version 1
  Invoice Header Managed Custom Object, Price book entry, Commercient Sage 300 Sales Rep Managed
  Custom Object, Commercient Sage 300 Sales Order Header Managed Custom Object, Commercient Sage 300
  Sales Order Line Managed Custom Object, Commercient Sage 300 Invoice Payment Managed Custom
  Object, Commercient Sage 300 Shipping Address Managed Custom Object, Commercient Invoice Document
  Payment Managed Custom Object, User, Commercient Sage 300 Invoice Header Managed Custom Object,
  Commercient Sage 300 Invoice Line Managed Custom Object, Commercient Sage 300 Terms Managed Custom
  Object, Contacts, Commercient Sage 300 Sales History Detail Managed Custom Object, Contact,
  Inventory (custom object), 17 more and 12 custom objects
- Object display names: Invoice Header, Invoice Line, Sage 300 Customer, Account, Child Account,
  Contacts, Invoice Document Payment, Parent Account, Sage 300 Sales order detail, Sage 300 Sales
  order header, Sage 300 Salesperson, Sync Item TO Product object Lookup, 87 more and 32 further
  templates
- Template groups: Account, Product, Invoice, Sales order, Customer Multi Ship Addresses, CRM
  Opportunity and Line, CRM Ownership

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/sage-300`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Sage 300 → Salesforce templates set up. dlake-crmpro-salesforce is the destination skill
this page sits under: its own text is the authority for the Salesforce conventions that hold across
every ERP, and its ERP table lists this page alongside every sibling ERP page for this destination.
For the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/sage-300`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
