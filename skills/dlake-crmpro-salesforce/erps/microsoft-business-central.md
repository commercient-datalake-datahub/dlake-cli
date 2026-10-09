---
name: dlake-crmpro-salesforce/erps/microsoft-business-central
kind: erp-summary
description: >-
  Use it when standing up or reading a Microsoft Business Central → Salesforce template set, when
  deciding which templates to import and activate, or when a run completes without pushing records
  and the answer is in the view or the configuration row. It extends dlake-crmpro, which covers
  operating CRMPro generally, and dlake-crmpro-salesforce, the destination skill this page is a
  child of, which carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Microsoft Business Central: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/microsoft-business-central` (or `list_skills`)
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
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Contact, Commercient Business Central Customer Managed Custom Object, Business Central Salesperson (custom object) | customers, shipping addresses, contacts, sales reps |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created, existing ones updated, and records removed in the ERP are deleted. | Business Central Shipping Address (custom object) | shipping addresses, customers |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Business Central Sales Invoice Managed Custom Object, Commercient Business Central Sales Invoice Line Managed Custom Object | sales invoices, sales invoice lines |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Business Central Sales Order Managed Custom Object, Commercient Business Central Sales Order Line Managed Custom Object | sales orders, sales order lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Get Salesforce User | User | Commercient salesperson code | 0 |
| MS Business Central Salesperson | Business Central Salesperson (custom object) | External key (custom field) | 1 |
| Accounts | Account | Commercient AR customer code | 2 |
| Business Central customer | Commercient Business Central Customer Managed Custom Object | Commercient external key (Dynamics NAV package) | 2 |
| Customer To Account Lookup | Account | Commercient AR customer code | 5 |
| MS Business Central Ship To Address | Business Central Shipping Address (custom object) | External key (custom field) | 6 |
| MS Business Central Sales Order | Commercient Business Central Sales Order Managed Custom Object | Commercient external key (Dynamics NAV package) | 7 |
| MS Business Central Sales Order Line | Commercient Business Central Sales Order Line Managed Custom Object | Commercient external key (Dynamics NAV package) | 8 |
| MS Business Central Sales Invoice | Commercient Business Central Sales Invoice Managed Custom Object | Commercient external key (Dynamics NAV package) | 9 |
| MS Business Central Sales Invoice Line | Commercient Business Central Sales Invoice Line Managed Custom Object | Commercient external key (Dynamics NAV package) | 10 |
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
| account feed | insert + update | customers, shipping addresses |
| customer feed | insert + update | customers |
| customer account lookup feed | insert + update | customers |
| shipping address feed | insert + update | shipping addresses, customers |
| sales order feed | insert + update | sales orders |
| sales order line feed | insert + update | sales order lines |
| invoice feed | insert + update | sales invoices |
| invoice line feed | insert + update | sales invoice lines |
| contact feed | insert + update | contacts, customers |

## 4. Order of work

The templates set run sequence from 0 to 16. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 0 — Get Salesforce User
- 1 — MS Business Central Salesperson
- 2 — Accounts, Business Central customer
- 5 — Customer To Account Lookup
- 6 — MS Business Central Ship To Address
- 7 — MS Business Central Sales Order
- 8 — MS Business Central Sales Order Line
- 9 — MS Business Central Sales Invoice
- 10 — MS Business Central Sales Invoice Line
- 16 — Contact

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- account feed reads salesperson sync output, user lookup sync output
- customer account lookup feed reads customer sync output, account sync output
- contact feed reads account sync output
- customer feed reads account sync output, salesperson sync output, user lookup sync output
- shipping address feed reads account sync output, customer sync output
- invoice feed reads account sync output, customer sync output
- invoice line feed reads invoice sync output
- sales order feed reads account sync output, customer sync output
- sales order line feed reads sales order sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| MS Business Central Salesperson | Business Central Salesperson (custom object) | 3 | Code → Code (custom field), Email → Email (custom field), Phone number → Phone number (custom field) |
| Accounts | Account | 13 | Commercient AR customer code column → Commercient AR customer code, Phone → Phone, Billing street → Billing street, Billing city → Billing city, Billing state code → Billing state code |
| Business Central customer | Commercient Business Central Customer Managed Custom Object | 22 | Account → Account, Address country letter code → Address country letter code, Address city → Address city, Address postal code → Address postal code, Address state → Commercient address state |
| Customer To Account Lookup | Account | 2 | Commercient AR customer code column → Commercient AR customer code, Business Central customer → Business Central Customer (custom object) |
| MS Business Central Ship To Address | Business Central Shipping Address (custom object) | 16 | Account → Account (custom field), Business Central customer → Business Central Customer (custom object), Customer number → Customer number (custom field), Code → Code (custom field), Name line 2 → Name 2 (custom field) |
| MS Business Central Sales Order | Commercient Business Central Sales Order Managed Custom Object | 43 | Account → Account, Business Central customer → Business Central customer, Billing postal address city → Billing postal address city, Billing postal address postal code → Billing postal address postal code, Billing postal address state → Commercient billing postal address state |
| MS Business Central Sales Order Line | Commercient Business Central Sales Order Line Managed Custom Object | 18 | Business Central sales order header → Commercient Business Central sales order header (related record), Document type → Document type (custom field), Document number → Document number (custom field), Line number → Line number (custom field), account lookup → Commercient account identifier |
| MS Business Central Sales Invoice | Commercient Business Central Sales Invoice Managed Custom Object | 44 | Account → Account, Business Central customer → Business Central customer, Billing postal address city → Billing postal address city, Billing postal address postal code → Billing postal address postal code, Billing postal address state → Commercient billing postal address state |
| MS Business Central Sales Invoice Line | Commercient Business Central Sales Invoice Line Managed Custom Object | 14 | Business Central invoice header → Business Central invoice header, account lookup → account lookup, description → description, Discount applied before tax → Discount applied before tax, Document record identifier → Document record identifier |
| Contact | Contact | 11 | account lookup → account lookup, Given name → Given name, Family name → Family name, Email → Email, Phone → Phone |

## 6. Community templates

The catalogue carries 45 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 45
- Default operations: insert on 45, update on 45, delete on 45
- Marked as circular sync: 3
- Licence groups they span: 11
- Destination objects: Account, Price book entry, Product, Contact, Business Central Customer
  (custom object), Business Central Item (custom object), Business Central Sales Invoice (custom
  object), Business Central Sales Invoice Line (custom object), Business Central Shipping Address
  (custom object), User, Account Matching (custom object), Commercient Business Central Customer
  Managed Custom Object, Commercient Business Central Sales Invoice Managed Custom Object,
  Commercient Business Central Sales Invoice Line Managed Custom Object, Commercient Business
  Central Sales Order Managed Custom Object, Commercient Business Central Sales Order Line Managed
  Custom Object, Contact Matching (custom object), External key (custom field), Business Central
  Item Location (custom object), Business Central Sales Order Header (custom object) and 7 more
- Object display names: Accounts, Contact, Customer To Account Lookup, MS Business Central Sales
  Invoice, MS Business Central Sales Invoice Line, MS Business Central Sales Order, MS Business
  Central Sales Order Line, MS Business Central Ship To Address, Business Central customer, Account,
  Child Accounts, Custom Price Book Create, 23 more and a further template
- Template groups: Account, Product, Invoice, Sales order, Customer Multi Ship Addresses,
  Opportunity

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/microsoft-business-central`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Microsoft Business Central → Salesforce templates set up. dlake-crmpro-salesforce is the
destination skill this page sits under: its own text is the authority for the Salesforce conventions
that hold across every ERP, and its ERP table lists this page alongside every sibling ERP page for
this destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/microsoft-business-central`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
