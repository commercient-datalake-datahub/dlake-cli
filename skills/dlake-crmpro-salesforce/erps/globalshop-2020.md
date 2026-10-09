---
name: dlake-crmpro-salesforce/erps/globalshop-2020
kind: erp-summary
description: >-
  Use it when standing up or reading a GlobalShop 2020 → Salesforce template set, when deciding
  which templates to import and activate, or when a run completes without pushing records and the
  answer is in the view or the configuration row. It extends dlake-crmpro, which covers operating
  CRMPro generally, and dlake-crmpro-salesforce, the destination skill this page is a child of,
  which carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — GlobalShop 2020: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/globalshop-2020` (or `list_skills`) against the
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
| **Sync Sales People** | ERP salesperson list data becomes Commercient Salespeople Managed Custom Object in Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Salespeople Managed Custom Object | salespeople |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Contact, Commercient Customer Master Managed Custom Object | customer master records, customer shipping addresses, salespeople, contacts |
| **CRM Ownership** | The templates push user to Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | user | — |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Customer Ship To Managed Custom Object | customer shipping addresses |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Order History Header Managed Custom Object, Commercient Order History Line Managed Custom Object | order history headers, salespeople, order history lines, inventory items (second master table) |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Order Header Managed Custom Object, Commercient Order Lines Managed Custom Object | order headers, salespeople, order lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| GET USER | user | — | 0 |
| Sync Sales People | Commercient Salespeople Managed Custom Object | Commercient external key (GlobalShop package) | 2 |
| Sync Account | Account | Commercient AR customer code | 3 |
| Sync Customer | Commercient Customer Master Managed Custom Object | Commercient external key (GlobalShop package) | 4 |
| Sync Customer to account lookup | Account | Commercient AR customer code | 5 |
| Sync Ship To Address | Commercient Customer Ship To Managed Custom Object | Commercient external key (GlobalShop package) | 6 |
| Sync Contact | Contact | External key (custom field) | 7 |
| Sync Sales Order Header | Commercient Order Header Managed Custom Object | Commercient external key (GlobalShop package) | 17 |
| Sync Sales Order Detail | Commercient Order Lines Managed Custom Object | Commercient external key (GlobalShop package) | 18 |
| Sync invoice record sync output (generic name) Header | Commercient Order History Header Managed Custom Object | Commercient external key (GlobalShop package) | 19 |
| Sync invoice record sync output (generic name) Detail | Commercient Order History Line Managed Custom Object | Commercient external key (GlobalShop package) | 20 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| salespeople feed | insert + update | salespeople |
| account feed | insert + update | customer master records, customer shipping addresses, salespeople |
| customer feed | insert + update | customer master records, salespeople |
| customer account lookup feed | insert + update | customer master records |
| shipping address feed | insert + update | customer shipping addresses |
| contact feed | insert + update | contacts |
| sales order feed | insert + update | order headers, salespeople |
| sales order line feed | insert + update | order lines |
| invoice feed | insert + update | order history headers, salespeople |
| invoice line feed | insert + update | order history lines, inventory items (second master table) |

## 4. Order of work

The templates set run sequence from 0 to 20. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 0 — GET USER
- 2 — Sync Sales People
- 3 — Sync Account
- 4 — Sync Customer
- 5 — Sync Customer to account lookup
- 6 — Sync Ship To Address
- 7 — Sync Contact
- 17 — Sync Sales Order Header
- 18 — Sync Sales Order Detail
- 19 — Sync invoice record sync output (generic name) Header
- 20 — Sync invoice record sync output (generic name) Detail

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- account feed reads user sync output (generic name), salespeople sync output
- customer account lookup feed reads customer sync output
- contact feed reads account sync output
- customer feed reads account sync output, salespeople sync output
- shipping address feed reads account sync output, customer sync output
- invoice feed reads account sync output, customer sync output, salespeople sync output
- invoice line feed reads invoice sync output, product sync output, item sync output; no template in
  this set writes product sync output, item sync output
- sales order feed reads account sync output, customer sync output, salespeople sync output
- sales order line feed reads sales order sync output, product sync output, item sync output; no
  template in this set writes product sync output, item sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Sync Sales People | Commercient Salespeople Managed Custom Object | 7 | Key 1 → Commercient key 1, Key 2 → Commercient key 2, Salesperson code → Salesperson code, Filler → Commercient filler, Salesperson → Commercient salesperson |
| Sync Account | Account | 15 | Commercient AR customer code column → Commercient AR customer code, Phone → Phone, Billing street → Billing street, Billing city → Billing city, Billing country → Billing country |
| Sync Customer | Commercient Customer Master Managed Custom Object | 29 | Account → Account, the linked GlobalShop salesperson → Commercient GlobalShop salesperson (related record), Customer → Commercient customer, Record → Record, Name of customer → Name of customer |
| Sync Customer to account lookup | Account | 2 | Commercient AR customer code column → Commercient AR customer code, the linked Commercient GlobalShop customer master → Commercient GlobalShop customer master (related record) |
| Sync Ship To Address | Commercient Customer Ship To Managed Custom Object | 28 | Customer → Commercient customer, Record → Record, Shipping address name → Shipping address name, Shipping address line 1 → Commercient shipping address line 1, Shipping address line 2 → Commercient shipping address line 2 |
| Sync Contact | Contact | 13 | Salutation → Salutation, Given name → Given name, Family name → Family name, Phone → Phone, mobile phone property → mobile phone property |
| Sync Sales Order Header | Commercient Order Header Managed Custom Object | 99 | Account → Account, the linked GlobalShop customer master → the linked GlobalShop customer master, the linked GlobalShop salesperson → Commercient GlobalShop salesperson (related record), Order number → Commercient order number, Record number → Record number |
| Sync Sales Order Detail | Commercient Order Lines Managed Custom Object | 99 | the linked GlobalShop sales order header → Commercient GlobalShop sales order header (related record), Product → Product (custom field), the linked GlobalShop inventory master → GlobalShop inventory master (custom field), Order number → Commercient order number, Record number → Record number |
| Sync invoice record sync output (generic name) Header | Commercient Order History Header Managed Custom Object | 99 | Account → Account, the linked GlobalShop customer master → the linked GlobalShop customer master, the linked GlobalShop salesperson → Commercient GlobalShop salesperson (related record), invoice record sync output (generic name) → invoice record sync output (generic name), Order number → Commercient order number |
| Sync invoice record sync output (generic name) Detail | Commercient Order History Line Managed Custom Object | 100 | the linked GlobalShop invoice header → the linked GlobalShop invoice header, Product → Commercient product (related record), the linked GlobalShop inventory master → GlobalShop inventory master (custom field), invoice record sync output (generic name) → invoice record sync output (generic name), Order number → Commercient order number |

## 6. Community templates

The catalogue carries 27 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 27
- Default operations: insert on 27, update on 27, delete on 27
- Marked as circular sync: 0
- Licence groups they span: 13
- Destination objects: Account, Commercient Order History Header Managed Custom Object, Commercient
  Quote Header Managed Custom Object, Commercient Quote Lines Managed Custom Object, Opportunity
  line item, Price book entry, Product, Commercient Customer Master Managed Custom Object,
  Commercient Customer Ship To Managed Custom Object, Commercient Order Header Managed Custom
  Object, Commercient Order History Line Managed Custom Object, Commercient Order Lines Managed
  Custom Object, Commercient Salespeople Managed Custom Object, Contact, Opportunity, user and 4
  custom objects
- Object display names: Sync quote sync output (generic name) Header, GET USER, Sync Account, Sync
  AR terms, Sync Contact, Sync Customer, Sync Customer to account lookup, Sync invoice record sync
  output (generic name) Detail, Sync invoice record sync output (generic name) Header, Sync Item,
  Sync Item to product lookup, Sync Product object, 9 more and 4 further templates
- Template groups: Product, Account, Opportunity, CRM Opportunity and Line, Invoice, Sales order,
  Customer Multi Ship Addresses

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/globalshop-2020`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped GlobalShop 2020 → Salesforce templates set up. dlake-crmpro-salesforce is the
destination skill this page sits under: its own text is the authority for the Salesforce conventions
that hold across every ERP, and its ERP table lists this page alongside every sibling ERP page for
this destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/globalshop-2020`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
