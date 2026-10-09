---
name: dlake-crmpro-salesforce/erps/infor-cloudsuite
kind: erp-summary
description: >-
  Use it when standing up or reading an Infor CloudSuite → Salesforce template set, when deciding
  which templates to import and activate, or when a run completes without pushing records and the
  answer is in the view or the configuration row. It extends dlake-crmpro, which covers operating
  CRMPro generally, and dlake-crmpro-salesforce, the destination skill this page is a child of,
  which carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Infor CloudSuite: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/infor-cloudsuite` (or `list_skills`) against the
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
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Accounts, Commercient Customer Managed Custom Object, Commercient Salesperson Managed Custom Object | customers, customer addresses, salespeople |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Customer Address Managed Custom Object | customer addresses |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Customer Order Managed Custom Object, Commercient Customer Order Line Managed Custom Object | customer orders, customer order lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Get Users | User | Commercient salesperson code | 1 |
| Infor Cloud Salesperson | Commercient Salesperson Managed Custom Object | Commercient external key (Infor package) | 1 |
| Account | Accounts | Commercient AR customer code | 2 |
| Infor Cloud Customer | Commercient Customer Managed Custom Object | Commercient external key (Infor package) | 3 |
| Infor Cloud Customer to account lookup | Accounts | Commercient AR customer code | 4 |
| Infor Cloud Address | Commercient Customer Address Managed Custom Object | Commercient external key (Infor package) | 5 |
| Infor Cloud Sales order | Commercient Customer Order Managed Custom Object | Commercient external key (Infor package) | 6 |
| Infor Cloud Sales order line | Commercient Customer Order Line Managed Custom Object | Commercient external key (Infor package) | 7 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| salesperson feed | insert + update | salespeople |
| account feed | insert + update | customers, customer addresses |
| customer feed | insert + update | customers, customer addresses |
| customer account lookup feed | insert + update | customers |
| address feed | insert + update | customer addresses |
| sales order feed | insert only | customer orders |
| sales order line feed | insert only | customer order lines |

## 4. Order of work

The templates set run sequence to 1, 2, 3, 4, 5, 6, 7. A run processes active rows in ascending run
sequence, which is the order the templates put them in:

- 1 — Get Users, Infor Cloud Salesperson
- 2 — Account
- 3 — Infor Cloud Customer
- 4 — Infor Cloud Customer to account lookup
- 5 — Infor Cloud Address
- 6 — Infor Cloud Sales order
- 7 — Infor Cloud Sales order line

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- customer account lookup feed reads customer sync output, customer account lookup sync output; no
  template in this set writes customer sync output, customer account lookup sync output
- account feed reads user sync output, salesperson sync output, account sync output; no template in
  this set writes salesperson sync output, account sync output
- customer feed reads account sync output, terms sync output, salesperson sync output, customer sync
  output; no template in this set writes account sync output, terms sync output, salesperson sync
  output, customer sync output
- address feed reads account sync output, customer sync output, address sync output; no template in
  this set writes account sync output, customer sync output, address sync output
- sales order feed reads account sync output, customer sync output, sales order sync output; no
  template in this set writes account sync output, customer sync output, sales order sync output
- sales order line feed reads sales order sync output, product sync output, item sync output, sales
  order line sync output; no template in this set writes sales order sync output, product sync
  output, item sync output, sales order line sync output
- salesperson feed reads salesperson sync output; no template in this set writes salesperson sync
  output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Infor Cloud Salesperson | Commercient Salesperson Managed Custom Object | 24 | Commercient external key column → Commercient external key (custom field), Delete indicator → Delete indicator, In workflow → Commercient in workflow, Note exists flag → Commercient note exists flag, Record date → Record date |
| Account | Accounts | 15 | Commercient AR customer code column → Commercient AR customer code, Customer number → Customer number, Commercient Infor Cloud salesperson → Commercient Infor Cloud salesperson, Commercient Infor Cloud term → Commercient Infor Cloud term, Account name → Account name |
| Infor Cloud Customer | Commercient Customer Managed Custom Object | 91 | Account → Account, the linked Infor SyteLine salesperson → Commercient Infor SyteLine salesperson (related record), Active for data integration → Commercient active for data integration, Advanced planning pull up → Commercient advanced planning pull up, Average balance outstanding → Commercient average balance outstanding |
| Infor Cloud Customer to account lookup | Accounts | 2 | Commercient AR customer code (Zoho field) → Commercient AR customer code (Zoho field), Commercient Infor Cloud customer (related record) → Commercient Infor Cloud customer (related record) |
| Infor Cloud Address | Commercient Customer Address Managed Custom Object | 46 | Commercient external key column → Commercient external key (custom field), Account → Account, Infor Cloud customer → Infor Cloud customer, Delete indicator → Delete indicator, In workflow → Commercient in workflow |
| Infor Cloud Sales order | Commercient Customer Order Managed Custom Object | 50 | Account → Account, the linked Infor SyteLine customer → the linked Infor SyteLine customer, Customer order number → Commercient customer order number, cost → Commercient cost, create date property → Commercient create date |
| Infor Cloud Sales order line | Commercient Customer Order Line Managed Custom Object | 73 | the linked Infor SyteLine customer order → the linked Infor SyteLine customer order, the linked Infor SyteLine item → Commercient Infor SyteLine item (related record), Customer order customer number → Customer order customer number, Customer order line → Commercient customer order line number, Customer order number → Commercient customer order number |

## 6. Community templates

The catalogue carries 12 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 12
- Default operations: insert on 12, update on 12, delete on 12
- Marked as circular sync: 0
- Licence groups they span: 6
- Destination objects: Accounts, Commercient Customer Order Managed Custom Object, Commercient
  Customer Order Line Managed Custom Object, Commercient Customer Address Managed Custom Object,
  Commercient Customer Managed Custom Object, Commercient Salesperson Managed Custom Object
- Object display names: Infor Cloud Address, Infor Cloud Customer, Infor Cloud Sales order, Infor
  Cloud Sales order line, Infor Cloud Salesperson, Account, Infor Cloud Customer to account lookup
- Template groups: Account, Sales order, Customer Multi Ship Addresses

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/infor-cloudsuite`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Infor CloudSuite → Salesforce templates set up. dlake-crmpro-salesforce is the
destination skill this page sits under: its own text is the authority for the Salesforce conventions
that hold across every ERP, and its ERP table lists this page alongside every sibling ERP page for
this destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/infor-cloudsuite`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
