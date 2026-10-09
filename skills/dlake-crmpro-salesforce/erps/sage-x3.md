---
name: dlake-crmpro-salesforce/erps/sage-x3
kind: erp-summary
description: >-
  Use it when standing up or reading a Sage X3 → Salesforce template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-salesforce, the destination skill this page is a child of, which
  carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Sage X3: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/sage-x3` (or `list_skills`) against the
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
| **Get Users** | The templates push users to Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | users | — |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Commercient Sage X3 Payment Term Managed Custom Object, Contact, Commercient Sage X3 Customer Managed Custom Object, Commercient Sage X3 Sales Rep Managed Custom Object | customers, business partner addresses, payment terms, contacts, contact master records |
| **AR Invoice Payments** | Commercient Syncs the payment details that are held in the receivables file and held against an invoice in the ERP. The amount received from a customer, the date of payment, the method of payment, whether partial or full payment, etc New records are created and existing ones updated; none are deleted. | Commercient Sage X3 Payment Header Managed Custom Object, Commercient Sage X3 Payment Detail Managed Custom Object | payment headers, payment lines |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created and existing ones updated; none are deleted. | Commercient Sage X3 Address Managed Custom Object | business partner addresses |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created and existing ones updated; none are deleted. | Commercient Sage X3 Invoice Header Managed Custom Object, Commercient Sage X3 Invoice Detail Managed Custom Object | sales invoices, sales invoice lines, item master records |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created and existing ones updated; none are deleted. | Commercient Sage X3 Sales Order Header Managed Custom Object, Commercient Sage X3 Sales Order Detail Managed Custom Object | sales order headers, sales order line prices, sales order line quantities |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| GET USER | users | — | 0 |
| Salesperson | Commercient Sage X3 Sales Rep Managed Custom Object | Commercient external key | 1 |
| Account | Account | Commercient AR customer code | 2 |
| Customer | Commercient Sage X3 Customer Managed Custom Object | Commercient external key | 3 |
| Shipping address | Commercient Sage X3 Address Managed Custom Object | Commercient external key | 4 |
| Sales order header | Commercient Sage X3 Sales Order Header Managed Custom Object | Commercient external key | 5 |
| Contact | Contact | Commercient external key | 6 |
| Sales order detail | Commercient Sage X3 Sales Order Detail Managed Custom Object | Commercient external key | 6 |
| Invoice header | Commercient Sage X3 Invoice Header Managed Custom Object | Commercient external key | 7 |
| Invoice detail | Commercient Sage X3 Invoice Detail Managed Custom Object | Commercient external key | 8 |
| Customer Reverse Lookup | Account | Commercient AR customer code | 9 |
| Sage X3 Payment Term | Commercient Sage X3 Payment Term Managed Custom Object | Commercient external key | 10 |
| Sage X3 Payment Header | Commercient Sage X3 Payment Header Managed Custom Object | Commercient external key | 11 |
| Sage X3 Payment Detail | Commercient Sage X3 Payment Detail Managed Custom Object | Commercient external key | 12 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| sales rep feed | insert + update | sales reps |
| account feed | insert + update | customers, business partner addresses |
| customer feed | insert + update | customers, business partner addresses |
| shipping address feed | insert + update | business partner addresses |
| sales order feed | insert + update | sales order headers |
| contact feed | insert + update | contacts, contact master records |
| sales order line feed | insert + update | sales order line prices, sales order line quantities |
| invoice feed | insert + update | sales invoices |
| invoice line feed | insert + update | sales invoice lines, item master records |
| customer account lookup feed | insert + update | customers |
| payment term feed | insert + update | payment terms |
| payment feed | insert + update | payment headers |
| payment line feed | insert + update | payment lines |

## 4. Order of work

The templates set run sequence from 0 to 12. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 0 — GET USER
- 1 — Salesperson
- 2 — Account
- 3 — Customer
- 4 — Shipping address
- 5 — Sales order header
- 6 — Contact, Sales order detail
- 7 — Invoice header
- 8 — Invoice detail
- 9 — Customer Reverse Lookup
- 10 — Sage X3 Payment Term
- 11 — Sage X3 Payment Header
- 12 — Sage X3 Payment Detail

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- customer account lookup feed reads customer sync output (generic short name)
- payment line feed reads payment header sync output (generic name), invoice header sync output
  (generic name)
- contact feed reads account sync output (generic name)
- customer feed reads account sync output (generic name)
- shipping address feed reads account sync output (generic name), customer sync output (generic
  short name)
- invoice feed reads account sync output (generic name), customer sync output (generic short name)
- invoice line feed reads invoice header sync output (generic name)
- sales order feed reads account sync output (generic name), customer sync output (generic short
  name)
- sales order line feed reads sales order header sync output (generic name)

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Salesperson | Commercient Sage X3 Sales Rep Managed Custom Object | 2 | Sales rep code → Commercient external key, Sales rep code → Name |
| Account | Account | 12 | Business partner customer number → Commercient AR customer code, Business partner customer name → Name, Business partner address line 1, Business partner address line 2 → Billing street, Business partner city → Billing city, State → Billing state |
| Customer | Commercient Sage X3 Customer Managed Custom Object | 29 | Business partner customer number → Commercient external key, Business partner customer name → Commercient name, Business partner customer number → Commercient number, Customer category → Commercient category, Address code → Commercient default address |
| Shipping address | Commercient Sage X3 Address Managed Custom Object | 16 | Address entity type,Address business partner number,Address code → Commercient external key, Address entity type,Address business partner number,Address code → Commercient name, Address code → Commercient address (related record), Address entity type → Commercient type, Address description → Commercient title |
| Sales order header | Commercient Sage X3 Sales Order Header Managed Custom Object | 72 | Sales order header number → Commercient external key, Sales order header number → Commercient name, the linked account → Account, the linked customer → Customer, Sales order header number → Commercient order number |
| Contact | Contact | 9 | Commercient external key → Commercient external key, account lookup → account lookup, Family name → Family name, Given name → Given name, Phone → Phone |
| Sales order detail | Commercient Sage X3 Sales Order Detail Managed Custom Object | 66 | Sales order header number,Sales order line number,Sales order line sequence number → Commercient external key, Sales order header number,Sales order line number,Sales order line sequence number → Commercient name, the linked Salesforce record → Commercient sales order header (related record), Sold to customer number → Commercient customer (related record), Sales order header number → Commercient sales order number |
| Invoice header | Commercient Sage X3 Invoice Header Managed Custom Object | 40 | Document number → Commercient external key, Document number → Commercient name, the linked account → Commercient account, the linked customer → Commercient customer (related record), Business partner code → Commercient customer code |
| Invoice detail | Commercient Sage X3 Invoice Detail Managed Custom Object | 54 | Document number, Invoice line number → Commercient external key, Document number, Invoice line number → Commercient name, the linked Salesforce record → Commercient invoice header (related record), Document number → Commercient document number, Invoice line number → Commercient invoice line number |
| Customer Reverse Lookup | Account | 2 | Business partner customer number → Commercient AR customer code, the linked Salesforce record → Commercient Sage X3 customer record (related record) |
| Sage X3 Payment Term | Commercient Sage X3 Payment Term Managed Custom Object | 18 | Payment term code,Payment term line → Commercient external key, Payment term code,Payment term line → Commercient name, Minimum due date amount → Commercient minimum due date amount, Due date percentage → Commercient due date percentage, Month end indicator → Commercient month end |
| Sage X3 Payment Header | Commercient Sage X3 Payment Header Managed Custom Object | 42 | Document number → Commercient external key, Document number → Commercient name, Document number → Commercient payment number, Order number → Commercient order number, Posting status → Commercient posted |
| Sage X3 Payment Detail | Commercient Sage X3 Payment Detail Managed Custom Object | 28 | Document number, Line number → Commercient external key, Document number, Line number → Commercient name, Account type → Commercient account structure, Bank amount → Commercient bank amount, Bank amount 1 → Commercient bank amount 2 |

## 6. Community templates

The catalogue carries 232 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 232
- Default operations: insert on 232, update on 232, delete on 232
- Marked as circular sync: 0
- Licence groups they span: 12
- Destination objects: Account, Commercient Sage X3 Invoice Header Managed Custom Object,
  Commercient Sage X3 Invoice Detail Managed Custom Object, Product, Commercient Sage X3 Sales Order
  Detail Managed Custom Object, Commercient Sage X3 Sales Order Header Managed Custom Object,
  Commercient Sage X3 Address Managed Custom Object, Commercient Sage X3 Customer Managed Custom
  Object, Commercient Sage X3 Sales Rep Managed Custom Object, Price book entry, Contact,
  Commercient Sage X3 Payment Detail Managed Custom Object, Commercient Sage X3 Payment Header
  Managed Custom Object, Commercient Sage X3 Payment Term Managed Custom Object, Price book object,
  Project (custom object), User, Commercient Account Matching Managed Custom Object, 13 more and 13
  custom objects
- Object display names: Invoice detail, Invoice header, Sales order detail, Sales order header,
  Account, Customer Reverse Lookup, Customer, Shipping address, Product, Salesperson, Contact, Item
  master, 55 more and 12 further templates
- Template groups: Account, Product, Invoice, Sales order, Customer Multi Ship Addresses,
  Opportunity

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/sage-x3`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Sage X3 → Salesforce templates set up. dlake-crmpro-salesforce is the destination skill
this page sits under: its own text is the authority for the Salesforce conventions that hold across
every ERP, and its ERP table lists this page alongside every sibling ERP page for this destination.
For the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/sage-x3`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
