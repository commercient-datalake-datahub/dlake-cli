---
name: dlake-crmpro-salesforce/erps/exact-globe-next
kind: erp-summary
description: >-
  Use it when standing up or reading an Exact Globe Next → Salesforce template set, when deciding
  which templates to import and activate, or when a run completes without pushing records and the
  answer is in the view or the configuration row. It extends dlake-crmpro, which covers operating
  CRMPro generally, and dlake-crmpro-salesforce, the destination skill this page is a child of,
  which carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Exact Globe Next: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/exact-globe-next` (or `list_skills`) against the
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
| **Get Users** | The templates push User to Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | User | — |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Contact, Commercient Macola Globe Customer Managed Custom Object, Commercient Macola Globe Sales Rep Managed Custom Object | accounts, countries, address states, contact persons |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Macola Globe Addresses Managed Custom Object | addresses |
| **Invoice History Headers** | The Invoices from the ERP invoice module are synchronized to the Commercient Invoice Header (MCO) object in CRM. Customer service and sales people can visualize the status of the Invoice such as open, closed, as well as the balance remaining and the due date. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Macola Globe Invoice Header Managed Custom Object, Commercient Macola Globe Invoice Lines Managed Custom Object | invoice history headers, accounts, invoice history lines |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Macola Globe Sales Order Header Managed Custom Object, Commercient Macola Globe Sales Order Lines Managed Custom Object | sales order headers, accounts, sales order lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Get Users | User | — | 0 |
| Sync Account | Account | Commercient AR customer code | 1 |
| Sync Salesperson | Commercient Macola Globe Sales Rep Managed Custom Object | Commercient external key (Exact package) | 1 |
| Sync Ship to account | Account | Commercient AR customer code | 2 |
| Sync Customer | Commercient Macola Globe Customer Managed Custom Object | Commercient external key (Exact package) | 3 |
| Sync Customer TO Account Lookup | Account | Commercient AR customer code | 4 |
| Sync Address | Commercient Macola Globe Addresses Managed Custom Object | Commercient external key (Exact package) | 5 |
| Sync Contact | Contact | External key (custom field) | 6 |
| Sync Sales Order Header | Commercient Macola Globe Sales Order Header Managed Custom Object | Commercient external key (Exact package) | 16 |
| Sync Sales Order Detail | Commercient Macola Globe Sales Order Lines Managed Custom Object | Commercient external key (Exact package) | 17 |
| Sync invoice record sync output (generic name) History Header | Commercient Macola Globe Invoice Header Managed Custom Object | Commercient external key (Exact package) | 18 |
| Sync Invoice history detail | Commercient Macola Globe Invoice Lines Managed Custom Object | Commercient external key (Exact package) | 19 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| account feed | insert + update | accounts, countries, address states |
| salesperson feed | insert + update | employees |
| shipping account feed | insert + update | accounts, countries, address states |
| customer feed | insert + update | accounts |
| customer account lookup feed | insert + update | accounts |
| address feed | insert + update | addresses |
| contact feed | insert + update | contact persons, accounts |
| sales order feed | insert + update | sales order headers, accounts |
| sales order line feed | insert + update | sales order lines, sales order headers, accounts |
| invoice history feed | insert + update | invoice history headers, accounts |
| invoice history line feed | insert + update | invoice history lines, invoice history headers, accounts |

## 4. Order of work

The templates set run sequence from 0 to 19. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 0 — Get Users
- 1 — Sync Account, Sync Salesperson
- 2 — Sync Ship to account
- 3 — Sync Customer
- 4 — Sync Customer TO Account Lookup
- 5 — Sync Address
- 6 — Sync Contact
- 16 — Sync Sales Order Header
- 17 — Sync Sales Order Detail
- 18 — Sync invoice record sync output (generic name) History Header
- 19 — Sync Invoice history detail

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- account feed reads user sync output
- shipping account feed reads account sync output, user sync output
- customer account lookup feed reads account sync output, customer sync output, shipping account
  sync output
- contact feed reads account sync output, customer sync output, user sync output, shipping account
  sync output
- customer feed reads account sync output, shipping account sync output
- address feed reads customer sync output
- invoice history feed reads account sync output, customer sync output, user sync output, shipping
  account sync output
- invoice history line feed reads account sync output, invoice history sync output, user sync
  output, product sync output, item sync output, shipping account sync output; no template in this
  set writes product sync output, item sync output
- sales order feed reads account sync output, customer sync output, user sync output, shipping
  account sync output
- sales order line feed reads sales order sync output, account sync output, user sync output,
  product sync output, item sync output, shipping account sync output; no template in this set
  writes product sync output, item sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Sync Account | Account | 14 | Commercient AR customer code column → Commercient AR customer code, Billing street → Billing street, Billing postal code → Billing postal code, Billing city → Billing city, Billing state → Billing state |
| Sync Salesperson | Commercient Macola Globe Sales Rep Managed Custom Object | 160 | Resource identifier → Commercient resource identifier, Full name → Commercient full name, Surname → Commercient surname, First name → Commercient first name, Middle name → Commercient middle name |
| Sync Ship to account | Account | 14 | Commercient AR customer code column → Commercient AR customer code, Billing street → Billing street, Billing postal code → Billing postal code, Billing city → Billing city, Billing state → Billing state |
| Sync Customer | Commercient Macola Globe Customer Managed Custom Object | 342 | Company website → Commercient company website, Account code → Account code, Contact identifier → Commercient contact identifier, Parent company → Commercient parent company, Account name → Commercient company name |
| Sync Customer TO Account Lookup | Account | 2 | Commercient AR customer code column → Commercient AR customer code, Commercient Macola Globe customer (related record) → Commercient Macola Globe Customer Managed Custom Object |
| Sync Address | Commercient Macola Globe Addresses Managed Custom Object | 66 | Row identifier → Row identifier, Type → Commercient address type, Contact person → Contact person, Address line 1 → Commercient address line 1, Address line 2 → Commercient address line 2 |
| Sync Contact | Contact | 12 | Salutation → Salutation, Given name → Given name, Family name → Family name, Fax → Fax, Phone → Phone |
| Sync Sales Order Header | Commercient Macola Globe Sales Order Header Managed Custom Object | 168 | Order number → Commercient order number, Invoice debtor number → Commercient invoice debtor number, Delivery debtor number → Commercient delivery debtor number, →, End debtor number → End debtor number (custom field) |
| Sync Sales Order Detail | Commercient Macola Globe Sales Order Lines Managed Custom Object | 130 | Order number → Commercient order number, Line number → Line number (custom field), Delivery date → Delivery date (custom field), Item code → Item code, Item type → Commercient item type |
| Sync invoice record sync output (generic name) History Header | Commercient Macola Globe Invoice Header Managed Custom Object | 143 | Invoice number → Invoice number (custom field), Invoice date → Invoice date (custom field), Journal number → Journal number (custom field), Debtor number → Debtor number (custom field), Invoice debtor number → Invoice debtor number (custom field) |
| Sync Invoice history detail | Commercient Macola Globe Invoice Lines Managed Custom Object | 80 | Invoice date → Invoice date (custom field), Journal number → Journal number (custom field), Line number → Line number (custom field), Item code → Item code, Item type → Commercient item type |

## 6. Community templates

The catalogue carries 138 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 138
- Default operations: insert on 138, update on 138, delete on 138
- Marked as circular sync: 18
- Licence groups they span: 11
- Destination objects: Account, Product, Commercient Macola Globe Customer Managed Custom Object,
  Price book entry, Commercient Macola Globe Invoice Header Managed Custom Object, Commercient
  Macola Globe Addresses Managed Custom Object, Commercient Macola Globe Invoice Lines Managed
  Custom Object, Commercient Macola Globe Sales Order Header Managed Custom Object, Commercient
  Macola Globe Sales Order Lines Managed Custom Object, Contact, Commercient Macola Globe Items
  Managed Custom Object, Commercient Macola Globe Sales Rep Managed Custom Object, User, Commercient
  Account Matching Managed Custom Object, Commercient Address Managed Custom Object, Commercient
  Contact Managed Custom Object, Commercient Invoice Detail Managed Custom Object, Commercient Item
  Managed Custom Object, Commercient Item To Product Lookup Managed Custom Object, Commercient
  Product Managed Custom Object, 2 more and 5 custom objects
- Object display names: Sync Account, Sync Customer, Sync Customer TO Account Lookup, Sync Address,
  Sync Contact, Sync Ship to account, Sync invoice record sync output (generic name) History Header,
  Sync Invoice history detail, Sync Item, Sync Item TO Product object Lookup, Sync Product object,
  Sync Sales Order Detail, 14 more and 6 further templates
- Template groups: Account, Product, Invoice History Headers, Sales order, Customer Multi Ship
  Addresses, Invoice

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/exact-globe-next`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Exact Globe Next → Salesforce templates set up. dlake-crmpro-salesforce is the
destination skill this page sits under: its own text is the authority for the Salesforce conventions
that hold across every ERP, and its ERP table lists this page alongside every sibling ERP page for
this destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/exact-globe-next`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
