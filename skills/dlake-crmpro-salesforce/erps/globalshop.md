---
name: dlake-crmpro-salesforce/erps/globalshop
kind: erp-summary
description: >-
  Use it when standing up or reading a GlobalShop → Salesforce template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-salesforce, the destination skill this page is a child of, which
  carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — GlobalShop: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/globalshop` (or `list_skills`) against the
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
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Commercient Customer Master Managed Custom Object | customer master records, customer sales, multiple shipping addresses |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Customer Ship To Managed Custom Object | multiple shipping addresses |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Order History Header Managed Custom Object, Commercient Order History Line Managed Custom Object, Commercient AR Open Items Managed Custom Object | order history headers, order history lines, AR open items |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Order Header Managed Custom Object, Commercient Order Lines Managed Custom Object | order headers, order lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Account | Account | Commercient AR customer code | 2 |
| Customer Master | Commercient Customer Master Managed Custom Object | Commercient external key (GlobalShop package) | 3 |
| Customer To Account Reverse Lookup | Account | Commercient AR customer code | 4 |
| Sales Order Header | Commercient Order Header Managed Custom Object | Commercient external key (GlobalShop package) | 9 |
| Sales Order Lines | Commercient Order Lines Managed Custom Object | Commercient external key (GlobalShop package) | 10 |
| Invoice Header | Commercient Order History Header Managed Custom Object | Commercient external key (GlobalShop package) | 11 |
| Invoice Lines | Commercient Order History Line Managed Custom Object | Commercient external key (GlobalShop package) | 12 |
| Invoice Payments | Commercient AR Open Items Managed Custom Object | Commercient external key (GlobalShop package) | 13 |
| Ship To Address | Commercient Customer Ship To Managed Custom Object | Commercient external key (GlobalShop package) | 14 |
| Get User | User | Id | 17 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| account feed | insert only | customer master records, customer sales, multiple shipping addresses |
| customer feed | insert + update | customer master records, customer sales |
| customer account lookup feed | insert + update | customer master records |
| sales order feed | insert + update | order headers |
| sales order line feed | insert + update | order lines |
| invoice feed | insert + update | order history headers |
| invoice line feed | insert + update | order history lines |
| invoice payment feed | insert + update | AR open items, order history headers |
| shipping address feed | insert + update | multiple shipping addresses |

## 4. Order of work

The templates set run sequence from 2 to 17. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 2 — Account
- 3 — Customer Master
- 4 — Customer To Account Reverse Lookup
- 9 — Sales Order Header
- 10 — Sales Order Lines
- 11 — Invoice Header
- 12 — Invoice Lines
- 13 — Invoice Payments
- 14 — Ship To Address
- 17 — Get User

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- account feed reads user sync output
- customer account lookup feed reads customer sync output (generic name)
- customer feed reads account sync output (generic name)
- shipping address feed reads account sync output (generic name), customer sync output (generic
  name)
- invoice feed reads account sync output (generic name), customer sync output (generic name)
- invoice line feed reads invoice sync output (generic name)
- invoice payment feed reads account sync output (generic name), customer sync output (generic
  name), invoice sync output (generic name)
- sales order feed reads account sync output (generic name), customer sync output (generic name)
- sales order line feed reads sales order header sync output (generic name)

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Account | Account | 11 | Commercient AR customer code column → Commercient AR customer code, Phone → Phone, Billing street → Billing street, Billing city → Billing city, Billing country → Billing country |
| Customer Master | Commercient Customer Master Managed Custom Object | 35 | Account → Account, Customer → Commercient customer, Record → Record, Name of customer → Name of customer, Address 1 → Commercient address line 1 |
| Customer To Account Reverse Lookup | Account | 2 | Commercient AR customer code column → Commercient AR customer code, the linked Commercient GlobalShop customer master → Commercient GlobalShop customer master (related record) |
| Sales Order Header | Commercient Order Header Managed Custom Object | 98 | Account → Account, the linked GlobalShop customer master → the linked GlobalShop customer master, Order number → Commercient order number, Record number → Record number, Order shipping identifier → Commercient order shipping identifier |
| Sales Order Lines | Commercient Order Lines Managed Custom Object | 97 | the linked GlobalShop sales order header → Commercient GlobalShop sales order header (related record), Order number → Commercient order number, Record number → Record number, Order shipping identifier → Commercient order shipping identifier, Record type → Record type |
| Invoice Header | Commercient Order History Header Managed Custom Object | 98 | Account → Account, the linked GlobalShop customer master → the linked GlobalShop customer master, invoice record sync output (generic name) → invoice record sync output (generic name), Order number → Commercient order number, Order suffix → Commercient order suffix |
| Invoice Lines | Commercient Order History Line Managed Custom Object | 97 | the linked GlobalShop invoice header → the linked GlobalShop invoice header, invoice record sync output (generic name) → invoice record sync output (generic name), Order number → Commercient order number, Order suffix → Commercient order suffix, Order line number → Commercient order line number |
| Invoice Payments | Commercient AR Open Items Managed Custom Object | 60 | Account → Account, the linked GlobalShop customer master → the linked GlobalShop customer master, the linked GlobalShop invoice header → the linked GlobalShop invoice header, Customer → Commercient customer, invoice record sync output (generic name) → invoice record sync output (generic name) |
| Ship To Address | Commercient Customer Ship To Managed Custom Object | 26 | Account → Account, the linked GlobalShop customer master → the linked GlobalShop customer master, Customer → Commercient customer, Shipping address sequence → Shipping address sequence (custom field), Shipping address name → Shipping address name |

## 6. Community templates

The catalogue carries 265 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 265
- Default operations: insert on 265, update on 265, delete on 265
- Marked as circular sync: 8
- Licence groups they span: 13
- Destination objects: Account, Product, Commercient Customer Master Managed Custom Object,
  Commercient Customer Ship To Managed Custom Object, Commercient Order Header Managed Custom
  Object, Commercient Order History Header Managed Custom Object, Commercient Order History Line
  Managed Custom Object, Commercient Order Lines Managed Custom Object, Commercient AR Open Items
  Managed Custom Object, Price book entry, Contact, Opportunity, Quote header (custom object), Quote
  lines (custom object), User, Commercient Sage Account Matching Managed Custom Object, GlobalShop
  salesperson (custom object), Part price code (custom object) and 10 custom objects
- Object display names: Account, Product, Customer To Account Reverse Lookup, Invoice Header,
  Invoice Lines, Item Master, Item To Product Reverse Lookup, Sales Order Header, Sales Order Lines,
  Customer Master, Invoice Payments, Ship To Address, 51 more and 6 further templates
- Template groups: Account, Product, Invoice, Sales order, Customer Multi Ship Addresses,
  Opportunity, CRM Opportunity and Line

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/globalshop`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped GlobalShop → Salesforce templates set up. dlake-crmpro-salesforce is the destination
skill this page sits under: its own text is the authority for the Salesforce conventions that hold
across every ERP, and its ERP table lists this page alongside every sibling ERP page for this
destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/globalshop`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
