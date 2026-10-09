---
name: dlake-crmpro-salesforce/erps/epicor-10-cloud
kind: erp-summary
description: >-
  Use it when standing up or reading an Epicor 10 Cloud → Salesforce template set, when deciding
  which templates to import and activate, or when a run completes without pushing records and the
  answer is in the view or the configuration row. It extends dlake-crmpro, which covers operating
  CRMPro generally, and dlake-crmpro-salesforce, the destination skill this page is a child of,
  which carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Epicor 10 Cloud: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/epicor-10-cloud` (or `list_skills`) against the
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
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Contact, Commercient Epicor 10 Customer Managed Custom Object, Commercient Epicor 10 Sales Rep Managed Custom Object | customers, shipping addresses, customer contacts, sales reps |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Epicor 10 Ship To Managed Custom Object | shipping addresses, customers |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Epicor 10 Invoice Header Managed Custom Object, Commercient Epicor 10 Invoice Detail Managed Custom Object | invoice headers, customers, invoice lines |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Epicor 10 Sales Order Header Managed Custom Object, Commercient Epicor 10 Sales Order Detail Managed Custom Object | sales order headers, customers, sales order lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Epicor 10 Cloud Salesperson | Commercient Epicor 10 Sales Rep Managed Custom Object | Commercient external key (Epicor package) | 1 |
| Account | Account | Commercient AR customer code | 2 |
| Contact | Contact | External key (custom field) | 3 |
| Epicor 10 Cloud Customer | Commercient Epicor 10 Customer Managed Custom Object | Commercient external key (Epicor package) | 4 |
| Epicor 10 Cloud Customer to account lookup | Account | Commercient AR customer code | 5 |
| Epicor 10 Cloud Shipping address | Commercient Epicor 10 Ship To Managed Custom Object | Commercient external key (Epicor package) | 6 |
| Epicor 10 Cloud Sales order header | Commercient Epicor 10 Sales Order Header Managed Custom Object | Commercient external key (Epicor package) | 7 |
| Epicor 10 Cloud Sales order detail | Commercient Epicor 10 Sales Order Detail Managed Custom Object | Commercient external key (Epicor package) | 8 |
| Epicor 10 Cloud Invoice header | Commercient Epicor 10 Invoice Header Managed Custom Object | Commercient external key (Epicor package) | 9 |
| Epicor 10 Cloud Invoice detail | Commercient Epicor 10 Invoice Detail Managed Custom Object | Commercient external key (Epicor package) | 10 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| salesperson feed | insert + update | sales reps |
| account feed | insert + update | customers, shipping addresses |
| contact feed | insert + update | customer contacts, customers |
| customer feed | insert + update | customers |
| customer account lookup feed | insert + update | customers |
| shipping address feed | insert + update | shipping addresses, customers |
| sales order feed | insert + update | sales order headers, customers |
| sales order line feed | insert + update | sales order lines |
| invoice feed | insert + update | invoice headers, customers |
| invoice line feed | insert + update | invoice lines |

## 4. Order of work

The templates set run sequence from 1 to 10. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 1 — Epicor 10 Cloud Salesperson
- 2 — Account
- 3 — Contact
- 4 — Epicor 10 Cloud Customer
- 5 — Epicor 10 Cloud Customer to account lookup
- 6 — Epicor 10 Cloud Shipping address
- 7 — Epicor 10 Cloud Sales order header
- 8 — Epicor 10 Cloud Sales order detail
- 9 — Epicor 10 Cloud Invoice header
- 10 — Epicor 10 Cloud Invoice detail

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- account feed reads salesperson sync output
- customer account lookup feed reads customer sync output
- contact feed reads account sync output
- customer feed reads account sync output, salesperson sync output
- shipping address feed reads account sync output, customer sync output
- invoice feed reads account sync output, customer sync output, salesperson sync output
- invoice line feed reads invoice sync output
- sales order feed reads account sync output, customer sync output, salesperson sync output
- sales order line feed reads sales order sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Epicor 10 Cloud Salesperson | Commercient Epicor 10 Sales Rep Managed Custom Object | 49 | Address 1 → Commercient address line 1, Address 2 → Commercient address line 2, Address 3 → Commercient address line 3, Alert flag → Commercient alert flag, Cell phone number → Commercient cell phone number |
| Account | Account | 12 | Commercient AR customer code column → Commercient AR customer code, Phone → Phone, Billing street → Billing street, Billing city → Billing city, Billing country → Billing country |
| Contact | Contact | 8 | account lookup → account lookup, Family name → Family name, Given name → Given name, Suffix → Suffix, Email → Email |
| Epicor 10 Cloud Customer | Commercient Epicor 10 Customer Managed Custom Object | 113 | Branch identifier → Branch identifier (custom field), Customer pricing schema → Customer pricing schema (custom field), →, VAT status code → VAT status code (custom field), Bill to province code → Bill to province code (custom field) |
| Epicor 10 Cloud Customer to account lookup | Account | 2 | Commercient AR customer code column → Commercient AR customer code, Commercient Epicor 10 customer master (related record) → Commercient Epicor 10 customer master (related record) |
| Epicor 10 Cloud Shipping address | Commercient Epicor 10 Ship To Managed Custom Object | 92 | Additional handling flag → Commercient additional handling flag, Address 1 → Commercient address line 1, Address 2 → Commercient address line 2, Address 3 → Commercient address line 3, Address valid flag → Commercient address valid flag |
| Epicor 10 Cloud Sales order header | Commercient Epicor 10 Sales Order Header Managed Custom Object | 96 | Apply charges → Apply charges, AR letter of credit identifier → AR letter of credit identifier, Bill to contact number → Bill to contact number, Bill to customer number → Bill to customer number, Cancel after date → Cancel after date |
| Epicor 10 Cloud Sales order detail | Commercient Epicor 10 Sales Order Detail Managed Custom Object | 92 | Advance billing balance → Advance billing balance, Base part number → Commercient base part number, Base revision number → Commercient base revision number, Break list code → Break list code, Change date → Commercient change date |
| Epicor 10 Cloud Invoice header | Commercient Epicor 10 Invoice Header Managed Custom Object | 96 | Legal number → Commercient legal number, Apply date → Commercient apply date, Billing contact number → Billing contact number, Billing contact number 1 → Billing contact number 1, Billing date → Commercient billing date |
| Epicor 10 Cloud Invoice detail | Commercient Epicor 10 Invoice Detail Managed Custom Object | 90 | →, Advance billing credit → Advance billing credit, Advance billing gain or loss → Commercient advance billing gain or loss, Asset number → Commercient asset number, Bill to customer number → Bill to customer number |

## 6. Community templates

The catalogue carries 79 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 79
- Default operations: insert on 79, update on 79, delete on 79
- Marked as circular sync: 0
- Licence groups they span: 12
- Destination objects: Account, Commercient Epicor 10 Sales Order Header Managed Custom Object,
  Commercient Epicor 10 Invoice Detail Managed Custom Object, Commercient Epicor 10 Invoice Header
  Managed Custom Object, Commercient Epicor 10 Sales Order Detail Managed Custom Object, Commercient
  Epicor 10 Customer Managed Custom Object, Commercient Epicor 10 Quote Header Managed Custom
  Object, Commercient Epicor 10 Sales Rep Managed Custom Object, Commercient Epicor 10 Ship To
  Managed Custom Object, Opportunity, Product, Commercient Epicor 10 Item Warehouse Managed Custom
  Object, Commercient Epicor 10 Quote Detail Managed Custom Object, Contact, Commercient Epicor 10
  Item Master Managed Custom Object, Price book entry, Epicor 10 Price List (custom object), Epicor
  10 Serial Number (custom object), Epicor 10 Ship Detail (custom object), 3 more and 2 custom
  objects
- Object display names: Account, Contacts, Epicor 10 Cloud Account, Epicor 10 Cloud Customer, Epicor
  10 Cloud Customer to account lookup, Epicor 10 Cloud Invoice detail, Epicor 10 Cloud Invoice
  header, Epicor 10 Cloud Item master, 35 more and 10 further templates
- Template groups: Account, Product, Invoice, Sales order, Opportunity, Customer Multi Ship
  Addresses, CRM Opportunity and Line

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/epicor-10-cloud`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Epicor 10 Cloud → Salesforce templates set up. dlake-crmpro-salesforce is the
destination skill this page sits under: its own text is the authority for the Salesforce conventions
that hold across every ERP, and its ERP table lists this page alongside every sibling ERP page for
this destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/epicor-10-cloud`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
