---
name: dlake-crmpro-salesforce/erps/infor-xa
kind: erp-summary
description: >-
  Use it when standing up or reading an Infor XA → Salesforce template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-salesforce, the destination skill this page is a child of, which
  carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Infor XA: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/infor-xa` (or `list_skills`) against the
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
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Commercient Customer Master Managed Custom Object, Commercient Salesperson Master Managed Custom Object | customer master records, latest shipping address |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Ship To Address Managed Custom Object | shipping addresses |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Invoice Header Managed Custom Object, Commercient Invoice Detail Managed Custom Object | invoice headers |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Sales Order Header Managed Custom Object, Commercient Sales Order Detail Managed Custom Object | sales order headers, sales order lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Get Users | User | — | 0 |
| Sync Salesperson | Commercient Salesperson Master Managed Custom Object | Commercient external key (Infor package) | 1 |
| Sync Account | Account | Commercient AR customer code | 2 |
| Sync Customer | Commercient Customer Master Managed Custom Object | Commercient external key (Infor package) | 3 |
| Sync Customer to account lookup | Account | Commercient AR customer code | 4 |
| Sync Ship To Address | Commercient Ship To Address Managed Custom Object | Commercient external key (Infor package) | 5 |
| Sync Sales order header | Commercient Sales Order Header Managed Custom Object | Commercient external key (Infor package) | 17 |
| Sync sales order detail | Commercient Sales Order Detail Managed Custom Object | Commercient external key (Infor package) | 18 |
| Sync invoice header | Commercient Invoice Header Managed Custom Object | Commercient external key (Infor package) | 19 |
| Sync invoice detail | Commercient Invoice Detail Managed Custom Object | Commercient external key (Infor package) | 20 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| salesperson feed | insert + update | salesperson master records |
| account feed | insert + update | customer master records |
| customer feed | insert + update | customer master records, latest effective price book |
| customer account lookup feed | insert + update | customer master records |
| shipping address feed | insert + update | shipping addresses |
| sales order feed | insert + update | sales order headers |
| sales order line feed | insert + update | sales order lines |
| invoice feed | insert + update | invoice headers |
| invoice line feed | insert + update | — |

## 4. Order of work

The templates set run sequence from 0 to 20. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 0 — Get Users
- 1 — Sync Salesperson
- 2 — Sync Account
- 3 — Sync Customer
- 4 — Sync Customer to account lookup
- 5 — Sync Ship To Address
- 17 — Sync Sales order header
- 18 — Sync sales order detail
- 19 — Sync invoice header
- 20 — Sync invoice detail

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- account feed reads salesperson sync output, user sync output
- customer account lookup feed reads account sync output, customer sync output
- customer feed reads account sync output, salesperson sync output
- shipping address feed reads account sync output, customer sync output
- invoice feed reads account sync output, customer sync output, salesperson sync output
- invoice line feed reads invoice sync output
- sales order feed reads account sync output, customer sync output, salesperson sync output,
  shipping address sync output
- sales order line feed reads sales order sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Sync Salesperson | Commercient Salesperson Master Managed Custom Object | 10 | Active record code → Active record code, Salesperson number → Commercient salesperson number, Salesperson name → Commercient salesperson name, →, → |
| Sync Account | Account | 3 | Commercient AR customer code column → Commercient AR customer code, linked Infor XA salesperson → Commercient Infor XA salesperson (related record), owner lookup → owner lookup |
| Sync Customer | Commercient Customer Master Managed Custom Object | 83 | Company number → Commercient company number, Customer number → Commercient customer number, Backorder code → Commercient backorder code, →, Credit limit amount → Commercient credit limit amount |
| Sync Customer to account lookup | Account | 2 | Commercient AR customer code column → Commercient AR customer code, linked Infor XA customer → Commercient Infor XA customer (related record) |
| Sync Ship To Address | Commercient Ship To Address Managed Custom Object | 67 | →, →, →, →, → |
| Sync Sales order header | Commercient Sales Order Header Managed Custom Object | 96 | →, →, →, →, → |
| Sync sales order detail | Commercient Sales Order Detail Managed Custom Object | 103 | →, →, →, →, → |
| Sync invoice header | Commercient Invoice Header Managed Custom Object | 140 | →, →, →, →, → |
| Sync invoice detail | Commercient Invoice Detail Managed Custom Object | 121 | →, →, →, →, → |

## 6. Community templates

The catalogue carries 24 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 24
- Default operations: insert on 24, update on 24, delete on 24
- Marked as circular sync: 0
- Licence groups they span: 10
- Destination objects: Price book entry, Account, Product, Commercient Customer Master Managed
  Custom Object, Commercient Sales Order Header Managed Custom Object, Commercient Sales Order
  Detail Managed Custom Object, Commercient Invoice Detail Managed Custom Object, Commercient
  Invoice Header Managed Custom Object, Commercient Ship To Address Managed Custom Object,
  Commercient Salesperson Master Managed Custom Object, Infor XA Customer Contract (custom object),
  Infor XA Customer Contract Detail (custom object), Infor XA Discount (custom object), Infor XA
  Item Price (custom object), Opportunity, Opportunity line item, Price book object, User and 2
  custom objects
- Object display names: Create List Price Book, Create Standard Price Book, Get Price Book, Get
  Users, Sync Account, Sync Customer, Sync Customer contract, Sync Customer contract detail, Sync
  Customer to account lookup, Sync Discount, Sync invoice detail, Sync invoice header and 12 more
- Template groups: Product, Account, CRM Opportunity and Line, Invoice, Sales order, Customer Multi
  Ship Addresses

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/infor-xa`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Infor XA → Salesforce templates set up. dlake-crmpro-salesforce is the destination skill
this page sits under: its own text is the authority for the Salesforce conventions that hold across
every ERP, and its ERP table lists this page alongside every sibling ERP page for this destination.
For the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/infor-xa`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
