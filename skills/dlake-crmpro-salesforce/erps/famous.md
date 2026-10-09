---
name: dlake-crmpro-salesforce/erps/famous
kind: erp-summary
description: >-
  Use it when standing up or reading a Famous → Salesforce template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-salesforce, the destination skill this page is a child of, which
  carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Famous: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/famous` (or `list_skills`) against the
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
| **Get User** | The templates push User to Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | User | — |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Famous customer (custom object), Famous salesperson (custom object) | name categories, name roles, name records, AR customers, name locations, AR customer classes |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created, existing ones updated, and records removed in the ERP are deleted. | Famous ship to address (custom object) | name categories, name roles, name records, AR customers, name locations, AR customer classes |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created, existing ones updated, and records removed in the ERP are deleted. | Famous invoice header (custom object), Famous invoice detail (custom object) | AR transaction headers, name records, name locations, salespeople, sale terms, AR transaction lines |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created, existing ones updated, and records removed in the ERP are deleted. | Famous sales order header (custom object), Famous sales order detail (custom object), Famous sales order history header (custom object), Famous sales order history detail (custom object) | AR transaction headers, name records, name locations, salespeople, sale terms, AR transaction lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Account | Account | Commercient AR customer code | 1 |
| Child Account | Account | Commercient AR customer code | 2 |
| Famous Salesperson | Famous salesperson (custom object) | External key (custom field) | 3 |
| Famous Customer | Famous customer (custom object) | External key (custom field) | 4 |
| Customer To Account Reverse Lookup | Account | Commercient AR customer code | 5 |
| Famous Ship To Address | Famous ship to address (custom object) | External key (custom field) | 6 |
| Famous Sales Order Header | Famous sales order header (custom object) | External key (custom field) | 10 |
| Famous Sales Order Detail | Famous sales order detail (custom object) | External key (custom field) | 11 |
| Famous Invoice Header | Famous invoice header (custom object) | External key (custom field) | 12 |
| Famous Invoice Detail | Famous invoice detail (custom object) | External key (custom field) | 13 |
| Famous Sales Order History Header | Famous sales order history header (custom object) | External key (custom field) | 14 |
| Famous Sales Order History Detail | Famous sales order history detail (custom object) | External key (custom field) | 15 |
| Get User | User | Id | 17 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| account feed | insert + update | name categories, name roles, name records, AR customers, name locations |
| child account feed | insert + update | name categories, name roles, name records, AR customers, name locations |
| salesperson feed | insert + update | salespeople, name records |
| customer feed | insert + update | name categories, name roles, name records, AR customers, name locations |
| customer account reverse lookup feed | insert + update | name categories, name roles, name records, AR customers |
| shipping address feed | insert + update | name categories, name roles, name records, AR customers, name locations |
| sales order feed | insert + update | AR transaction headers, name records, name locations, salespeople, sale terms |
| sales order line feed | insert + update | AR transaction lines, AR transaction headers, products |
| invoice feed | insert + update | AR transaction headers, name records, name locations, salespeople, sale terms |
| invoice line feed | insert + update | AR transaction lines, AR transaction headers, products |
| sales order history feed | insert + update | AR transaction headers, name records, name locations, salespeople, sale terms |
| sales order history line feed | insert + update | AR transaction lines, AR transaction headers, products |

## 4. Order of work

The templates set run sequence from 1 to 17. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 1 — Account
- 2 — Child Account
- 3 — Famous Salesperson
- 4 — Famous Customer
- 5 — Customer To Account Reverse Lookup
- 6 — Famous Ship To Address
- 10 — Famous Sales Order Header
- 11 — Famous Sales Order Detail
- 12 — Famous Invoice Header
- 13 — Famous Invoice Detail
- 14 — Famous Sales Order History Header
- 15 — Famous Sales Order History Detail
- 17 — Get User

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- account feed reads user sync output
- child account feed reads account sync output, user sync output
- customer account reverse lookup feed reads account sync output, customer sync output
- customer feed reads salesperson sync output, account sync output
- shipping address feed reads salesperson sync output, child account sync output, customer sync
  output
- invoice feed reads account sync output, customer sync output
- invoice line feed reads invoice sync output, product sync output; no template in this set writes
  product sync output
- sales order feed reads account sync output, customer sync output
- sales order line feed reads sales order sync output, product sync output; no template in this set
  writes product sync output
- sales order history feed reads account sync output, customer sync output
- sales order history line feed reads sales order history sync output, product sync output; no
  template in this set writes product sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Account | Account | 15 | Commercient AR customer code column → Commercient AR customer code, Billing street → Billing street, Billing city → Billing city, Billing state → Billing state, Billing postal code → Billing postal code |
| Child Account | Account | 16 | Commercient AR customer code column → Commercient AR customer code, parent account lookup → parent account lookup, Billing street → Billing street, Billing city → Billing city, Billing state → Billing state |
| Famous Salesperson | Famous salesperson (custom object) | 2 | Salesperson identifier → Salesperson identifier (custom field), Salesperson name → Salesperson name (custom field) |
| Famous Customer | Famous customer (custom object) | 13 | account lookup value → Account (custom field), the linked Famous salesperson → Famous salesperson (custom field), returned customer identifier → Customer identifier (custom field), Customer name → Customer name (custom field), Customer class → Customer class (custom field) |
| Customer To Account Reverse Lookup | Account | 2 | Commercient AR customer code column → Commercient AR customer code, the matched Famous customer → Famous customer (custom object) |
| Famous Ship To Address | Famous ship to address (custom object) | 23 | account lookup value → Account (custom field), the linked Famous salesperson → Famous salesperson (custom field), the linked Famous customer → Famous customer (custom field), Location → Location (custom field), Location sequence → Location sequence (custom field) |
| Famous Sales Order Header | Famous sales order header (custom object) | 13 | account lookup value → Account (custom field), the linked Famous customer → Famous customer (custom field), Order number → Order number (custom field), returned customer identifier → Customer identifier (custom field), Customer name → Customer name (custom field) |
| Famous Sales Order Detail | Famous sales order detail (custom object) | 12 | the linked Famous sales order header → Famous sales order header (custom field), the linked product → Product (custom field), Order number → Order number (custom field), Line number → Line number (custom field), Line type → Line type (custom field) |
| Famous Invoice Header | Famous invoice header (custom object) | 20 | account lookup value → Account (custom field), the linked Famous customer → Famous customer (custom field), Order number → Order number (custom field), returned customer identifier → Customer identifier (custom field), Customer name → Customer name (custom field) |
| Famous Invoice Detail | Famous invoice detail (custom object) | 13 | the linked Famous invoice header → Famous invoice header (custom field), the linked product → Product (custom field), Order number → Order number (custom field), Line number → Line number (custom field), Line type → Line type (custom field) |
| Famous Sales Order History Header | Famous sales order history header (custom object) | 13 | account lookup value → Account (custom field), the linked Famous customer → Famous customer (custom field), Order number → Order number (custom field), returned customer identifier → Customer identifier (custom field), Customer name → Customer name (custom field) |
| Famous Sales Order History Detail | Famous sales order history detail (custom object) | 12 | the linked Famous sales order history header → Famous sales order history header (custom field), the linked product → Product (custom field), Order number → Order number (custom field), Line number → Line number (custom field), Line type → Line type (custom field) |

## 6. Community templates

The catalogue carries 15 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 15
- Default operations: insert on 15, update on 15, delete on 15
- Marked as circular sync: 0
- Licence groups they span: 8
- Destination objects: Account, Product, Famous customer (custom object), Famous invoice detail
  (custom object), Famous invoice header (custom object), Famous item (custom object), Famous sales
  order detail (custom object), Famous sales order header (custom object), Famous sales order
  history detail (custom object), Famous sales order history header (custom object), Famous
  salesperson (custom object), Famous ship to address (custom object)
- Object display names: Account, Child Account, Customer To Account Reverse Lookup, Famous Customer,
  Famous Invoice Detail, Famous Invoice Header, Famous Item, Famous Sales Order Detail, Famous Sales
  Order Header, Famous Sales Order History Detail, Famous Sales Order History Header, Famous
  Salesperson and 3 more
- Template groups: Account, Sales order, Product, Invoice, Customer Multi Ship Addresses

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/famous`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Famous → Salesforce templates set up. dlake-crmpro-salesforce is the destination skill
this page sits under: its own text is the authority for the Salesforce conventions that hold across
every ERP, and its ERP table lists this page alongside every sibling ERP page for this destination.
For the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/famous`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
