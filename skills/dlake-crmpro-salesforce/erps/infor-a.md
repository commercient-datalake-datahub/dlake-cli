---
name: dlake-crmpro-salesforce/erps/infor-a
kind: erp-summary
description: >-
  Use it when standing up or reading an Infor A+ → Salesforce template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-salesforce, the destination skill this page is a child of, which
  carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Infor A+: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/infor-a` (or `list_skills`) against the
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
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Commercient Customer Master Managed Custom Object, Commercient Sales Rep Master Managed Custom Object | customers, addresses, sales reps |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient address | addresses |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Open Order Header Managed Custom Object, Commercient Open Order Detail Managed Custom Object, Commercient Order History Header Managed Custom Object, Commercient Order History Detail Managed Custom Object | open order headers, open order lines, order history headers, order history lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Salesperson | Commercient Sales Rep Master Managed Custom Object | Commercient external key (Infor package) | 1 |
| Account | Account | Commercient AR customer code | 2 |
| Customer | Commercient Customer Master Managed Custom Object | Commercient external key (Infor package) | 3 |
| Account To Customer Reverse Lookup | Account | Commercient AR customer code | 4 |
| Ship To Address | Commercient address | Commercient external key (Infor package) | 5 |
| Open Order Header | Commercient Open Order Header Managed Custom Object | Commercient external key (Infor package) | 9 |
| Open Order Detail | Commercient Open Order Detail Managed Custom Object | Commercient external key (Infor package) | 10 |
| Order History Header | Commercient Order History Header Managed Custom Object | Commercient external key (Infor package) | 11 |
| Order History Detail | Commercient Order History Detail Managed Custom Object | Commercient external key (Infor package) | 12 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| salesperson feed | insert + update | sales reps |
| account feed | insert + update | customers, addresses |
| customer feed | insert + update | customers |
| customer account lookup feed | insert + update | customers |
| shipping address feed | insert + update | addresses |
| open order feed | insert + update | open order headers |
| open order line feed | insert + update | open order lines |
| order history feed | insert + update | order history headers |
| order history line feed | insert + update | order history lines |

## 4. Order of work

The templates set run sequence from 1 to 12. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 1 — Salesperson
- 2 — Account
- 3 — Customer
- 4 — Account To Customer Reverse Lookup
- 5 — Ship To Address
- 9 — Open Order Header
- 10 — Open Order Detail
- 11 — Order History Header
- 12 — Order History Detail

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- account feed reads salesperson sync output
- customer account lookup feed reads customer sync output, account sync output
- customer feed reads account sync output, salesperson sync output
- shipping address feed reads account sync output, customer sync output
- open order feed reads account sync output, salesperson sync output, customer sync output
- open order line feed reads product sync output, item master sync output, open order sync output;
  no template in this set writes product sync output, item master sync output
- order history feed reads account sync output, salesperson sync output, customer sync output
- order history line feed reads product sync output, item master sync output, order history sync
  output; no template in this set writes product sync output, item master sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Salesperson | Commercient Sales Rep Master Managed Custom Object | 29 | Salesperson company number → Commercient salesperson company number, Sales rep number → Commercient sales rep number, Sales rep name → Commercient sales rep name, Sales month to date → Commercient sales month to date, Sales year to date → Commercient sales year to date |
| Account | Account | 14 | Commercient AR customer code column → Commercient AR customer code, Phone → Phone, Type → Type, Billing street → Billing street, Billing city → Billing city |
| Customer | Commercient Customer Master Managed Custom Object | 212 | Account → Account, the linked Infor A+ sales rep master → Commercient Infor A+ sales rep master (related record), Customer master company number → Customer master company number, Customer number → Customer number, Customer name → Customer name |
| Account To Customer Reverse Lookup | Account | 2 | Commercient AR customer code column → Commercient AR customer code, the linked Commercient Infor A+ customer master → Commercient Infor A+ customer master (related record) |
| Ship To Address | Commercient address | 86 | Account → Account, the linked Infor A+ customer master → the linked Infor A+ customer master, Shipping address company number → Shipping address company number, Shipping address customer number → Shipping address customer number, Shipping address number → Commercient shipping address number |
| Open Order Header | Commercient Open Order Header Managed Custom Object | 300 | Account → Account, the linked Infor A+ sales rep master → Commercient Infor A+ sales rep master (related record), the linked Infor A+ customer master → the linked Infor A+ customer master, Open order header customer number → Open order header customer number, Open order header company number → Open order header company number |
| Open Order Detail | Commercient Open Order Detail Managed Custom Object | 174 | Product → Product, the linked Infor A+ item master → Commercient Infor A+ item master (related record), the linked Infor A+ open order header → Commercient Infor A+ open order header (related record), Open order line customer number → Open order line customer number, Open order line company number → Open order line company number |
| Order History Header | Commercient Order History Header Managed Custom Object | 252 | Account → Account, the linked Infor A+ sales rep master → Commercient Infor A+ sales rep master (related record), the linked Infor A+ customer master → the linked Infor A+ customer master, Order history sequence → Commercient order history sequence, Order history header customer number → Order history header customer number |
| Order History Detail | Commercient Order History Detail Managed Custom Object | 151 | Product → Commercient product (related record), the linked Infor A+ item master → Commercient Infor A+ item master (related record), the linked Infor A+ order history header → Commercient Infor A+ order history header (related record), Order line company number → Commercient order line company number, Order history line customer number → Commercient order history line customer number |

## 6. Community templates

The catalogue carries 47 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 47
- Default operations: insert on 47, update on 47, delete on 47
- Marked as circular sync: 0
- Licence groups they span: 9
- Destination objects: Account, Product, Price book entry, Commercient address, Commercient Customer
  Master Managed Custom Object, Commercient Order History Detail Managed Custom Object, Commercient
  Order History Header Managed Custom Object, Commercient Open Order Detail Managed Custom Object,
  Commercient Open Order Header Managed Custom Object, Commercient Sales Rep Master Managed Custom
  Object, Opportunity, Opportunity line item, Price book object, User and 3 custom objects
- Object display names: Account, Account To Customer Reverse Lookup, Customer, Item Master, Open
  Order Detail, Order History Detail, Order History Header, Product, Product To Item Reverse Lookup,
  Salesperson, Ship To Address, Open Order Header, 8 more and 3 further templates
- Template groups: Product, Account, Sales order, CRM Opportunity and Line, Customer Multi Ship
  Addresses

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/infor-a`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Infor A+ → Salesforce templates set up. dlake-crmpro-salesforce is the destination skill
this page sits under: its own text is the authority for the Salesforce conventions that hold across
every ERP, and its ERP table lists this page alongside every sibling ERP page for this destination.
For the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/infor-a`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
