---
name: dlake-crmpro-salesforce/erps/epicor-9-and-9-5
kind: erp-summary
description: >-
  Use it when standing up or reading an Epicor 9 and 9.5 → Salesforce template set, when deciding
  which templates to import and activate, or when a run completes without pushing records and the
  answer is in the view or the configuration row. It extends dlake-crmpro, which covers operating
  CRMPro generally, and dlake-crmpro-salesforce, the destination skill this page is a child of,
  which carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Epicor 9 and 9.5: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/epicor-9-and-9-5` (or `list_skills`) against the
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
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | account, Contact, Commercient Epicor Customer Managed Custom Object | customers, customer contacts |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. Existing records are updated only — nothing is created and nothing is deleted. | Commercient Ship To Managed Custom Object | shipping addresses, customers |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). Existing records are updated only — nothing is created and nothing is deleted. | Commercient Invoice Header Managed Custom Object, Commercient Invoice Detail Managed Custom Object | invoice headers, customers, invoice lines |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. Existing records are updated only — nothing is created and nothing is deleted. | Commercient Order Header Managed Custom Object, Commercient Order Detail Managed Custom Object | sales order headers, A, customers, sales order lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Epicor 9 account | account | Commercient AR customer code | 1 |
| Epicor 9 customer | Commercient Epicor Customer Managed Custom Object | Commercient external key (Epicor package) | 2 |
| Epicor 9 customer Reverse Lookup Account | account | Commercient AR customer code | 3 |
| Epicor 9 customer ship to | Commercient Ship To Managed Custom Object | Commercient external key (Epicor package) | 4 |
| Epicor 9 order header | Commercient Order Header Managed Custom Object | Commercient external key (Epicor package) | 5 |
| Epicor 9 order detail | Commercient Order Detail Managed Custom Object | Commercient external key (Epicor package) | 6 |
| Sync Contact | Contact | External key (custom field) | 7 |
| Epicor 9 invoice header | Commercient Invoice Header Managed Custom Object | Commercient external key (Epicor package) | 7 |
| Epicor 9 invoice detail | Commercient Invoice Detail Managed Custom Object | Commercient external key (Epicor package) | 8 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| account feed | insert + update | customers |
| customer feed | insert + update | customers |
| customer account lookup feed | insert + update | customers |
| shipping address feed | insert + update | shipping addresses, customers |
| sales order feed | insert + update | sales order headers, A, customers |
| sales order line feed | insert + update | sales order lines |
| contact feed | insert + update | customer contacts, customers |
| invoice feed | insert + update | invoice headers, customers |
| invoice line feed | insert + update | invoice lines |

## 4. Order of work

The templates set run sequence to 1, 2, 3, 4, 5, 6, 7, 8. A run processes active rows in ascending
run sequence, which is the order the templates put them in:

- 1 — Epicor 9 account
- 2 — Epicor 9 customer
- 3 — Epicor 9 customer Reverse Lookup Account
- 4 — Epicor 9 customer ship to
- 5 — Epicor 9 order header
- 6 — Epicor 9 order detail
- 7 — Sync Contact, Epicor 9 invoice header
- 8 — Epicor 9 invoice detail

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- customer account lookup feed reads customer sync output
- contact feed reads account sync output, customer sync output
- customer feed reads account sync output
- shipping address feed reads account sync output, customer sync output
- invoice feed reads account sync output, customer sync output
- invoice line feed reads invoice sync output
- sales order feed reads account sync output, customer sync output
- sales order line feed reads sales order sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Epicor 9 account | account | 11 | Customer identifier → Commercient AR customer code, Name → Name, Phone number → Phone, Epicor bill to address line 1, Epicor bill to address line 2, Epicor bill to address line 3 → Billing street, Epicor bill to city → Billing city |
| Epicor 9 customer | Commercient Epicor Customer Managed Custom Object | 78 | Company, Customer identifier → Commercient external key (Epicor package), Name → Name, Account reference number → Account reference number, Address 1 → Address 1, Address 2 → Address 2 |
| Epicor 9 customer Reverse Lookup Account | account | 2 | Customer identifier → Commercient AR customer code, the linked Salesforce record → Commercient customer master (related record) |
| Epicor 9 customer ship to | Commercient Ship To Managed Custom Object | 12 | Company, Customer number, Shipping address number → Commercient external key (Epicor package), Name → Commercient name (Epicor package), Address 1 → Commercient address line 1, Address 2 → Commercient address line 2, Address 3 → Commercient address line 3 |
| Epicor 9 order header | Commercient Order Header Managed Custom Object | 78 | Company, Order number → Commercient external key (Epicor package), Company, Order number → Commercient name (Epicor package), Apply charges → Commercient apply charges, AR letter of credit identifier → Commercient letter of credit identifier, Bill to contact number → Commercient bill to contact number |
| Epicor 9 order detail | Commercient Order Detail Managed Custom Object | 81 | Company,Order line number,Order number → Commercient external key (Epicor package), Company,Order line number,Order number → Commercient name (Epicor package), Advance billing balance → Commercient advance billing balance, Base part number → Commercient base part number, Base revision number → Commercient base revision number |
| Sync Contact | Contact | 20 | Family name → Family name, Given name → Given name, Mailing street → Mailing street, Mailing city → Mailing city, Mailing state → Mailing state |
| Epicor 9 invoice header | Commercient Invoice Header Managed Custom Object | 72 | Company,Invoice number → Commercient external key (Epicor package), Company,Invoice number → Commercient name (Epicor package), Apply date → Commercient apply date, Billing contact number → Commercient billing contact number, Billing date → Commercient billing date |
| Epicor 9 invoice detail | Commercient Invoice Detail Managed Custom Object | 75 | Company,invoice line,Invoice number → Commercient external key (Epicor package), Advance billing credit → Advance billing credit, Advance billing gain or loss → Advance billing gain or loss, Asset number → Asset number, Bill to customer number → Commercient invoice bill to customer number |

## 6. Community templates

The catalogue carries 39 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 39
- Default operations: insert on 39, update on 39, delete on 39
- Marked as circular sync: 0
- Licence groups they span: 11
- Destination objects: account, Commercient Epicor Customer Managed Custom Object, Commercient
  Invoice Detail Managed Custom Object, Commercient Invoice Header Managed Custom Object,
  Commercient Order Detail Managed Custom Object, Commercient Order Header Managed Custom Object,
  Commercient Ship To Managed Custom Object, Product, Account, Commercient Sales Rep Managed Custom
  Object, Commercient Epicor 9 Item Managed Custom Object, Commercient Epicor 9 Item Warehouse
  Managed Custom Object, Commercient Serial Number Managed Custom Object, Commercient Terms Managed
  Custom Object, Contact, Location, Price book entry, Product item
- Object display names: Epicor 9 Product, Epicor 9 customer ship to, Epicor 9 customer, Epicor 9
  customer Reverse Lookup Account, Epicor 9 invoice detail, Epicor 9 invoice header, Epicor 9 order
  detail, Epicor 9 order header, Epicor 9 Create Standard Price Book, Epicor 9 Item Master, Epicor 9
  Item Warehouse, Epicor 9 Product Reverse Lookup and 19 more
- Template groups: Account, Product, Invoice, Sales order, Customer Multi Ship Addresses

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/epicor-9-and-9-5`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Epicor 9 and 9.5 → Salesforce templates set up. dlake-crmpro-salesforce is the
destination skill this page sits under: its own text is the authority for the Salesforce conventions
that hold across every ERP, and its ERP table lists this page alongside every sibling ERP page for
this destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/epicor-9-and-9-5`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
