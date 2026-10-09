---
name: dlake-crmpro-salesforce/erps/aptean-encompix
kind: erp-summary
description: >-
  Use it when standing up or reading an Aptean Encompix → Salesforce template set, when deciding
  which templates to import and activate, or when a run completes without pushing records and the
  answer is in the view or the configuration row. It extends dlake-crmpro, which covers operating
  CRMPro generally, and dlake-crmpro-salesforce, the destination skill this page is a child of,
  which carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Aptean Encompix: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/aptean-encompix` (or `list_skills`) against the
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
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Commercient Aptean Encompix Customer Managed Custom Object, Commercient Aptean Encompix Salesperson Managed Custom Object | customers, customer shipping addresses, salespeople |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Aptean Encompix Customer Ship To Managed Custom Object | customer shipping addresses |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Aptean Encompix Invoice Managed Custom Object, Commercient Aptean Encompix Invoice Line Managed Custom Object | invoices |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Aptean Encompix Sales Order Header Managed Custom Object | sales orders |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Aptean Encompix Salesperson | Commercient Aptean Encompix Salesperson Managed Custom Object | Commercient external key (Aptean package) | 1 |
| Account Update | Account | Commercient AR customer code | 3 |
| Aptean Encompix Customer | Commercient Aptean Encompix Customer Managed Custom Object | Commercient external key (Aptean package) | 4 |
| Customer To Account Lookup | Account | Commercient AR customer code | 5 |
| Aptean Encompix Shipping address | Commercient Aptean Encompix Customer Ship To Managed Custom Object | Commercient external key (Aptean package) | 6 |
| Aptean Encompix Sales order header | Commercient Aptean Encompix Sales Order Header Managed Custom Object | Commercient external key (Aptean package) | 12 |
| Aptean Encompix Invoice header | Commercient Aptean Encompix Invoice Managed Custom Object | Commercient external key (Aptean package) | 13 |
| Aptean Encompix invoice line | Commercient Aptean Encompix Invoice Line Managed Custom Object | Commercient external key (Aptean package) | 15 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| salesperson feed | insert + update | salespeople |
| account feed | insert + update | customers, customer shipping addresses, salespeople |
| customer feed | insert + update | customers |
| customer account lookup feed | insert + update | customers |
| shipping address feed | insert + update | customer shipping addresses |
| sales order feed | insert + update | sales orders |
| invoice feed | insert + update | invoices |
| invoice line feed | insert + update | invoices |

## 4. Order of work

The templates set run sequence to 1, 3, 4, 5, 6, 12, 13, 15. A run processes active rows in
ascending run sequence, which is the order the templates put them in:

- 1 — Aptean Encompix Salesperson
- 3 — Account Update
- 4 — Aptean Encompix Customer
- 5 — Customer To Account Lookup
- 6 — Aptean Encompix Shipping address
- 12 — Aptean Encompix Sales order header
- 13 — Aptean Encompix Invoice header
- 15 — Aptean Encompix invoice line

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- account feed reads salesperson sync output, terms sync output; no template in this set writes
  terms sync output
- customer account lookup feed reads customer sync output, account sync output
- customer feed reads account sync output
- shipping address feed reads account sync output, customer sync output
- invoice feed reads account sync output, customer sync output, sales order sync output
- invoice line feed reads invoice sync output, job header sync output, item master sync output; no
  template in this set writes job header sync output, item master sync output
- sales order feed reads account sync output, customer sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Aptean Encompix Salesperson | Commercient Aptean Encompix Salesperson Managed Custom Object | 58 | Salesperson code → Commercient salesperson, Commission percent → Commission percent, Next level → Commercient next level, Change date → Commercient change date, Change time → Commercient change time |
| Account Update | Account | 17 | Commercient AR customer code column → Commercient AR customer code, Phone → Phone, Billing street → Billing street, Billing city → Billing city, Billing country code → Billing country code |
| Aptean Encompix Customer | Commercient Aptean Encompix Customer Managed Custom Object | 91 | Account → Account, Customer identifier → Commercient customer identifier, Address line 1 → Commercient address line 1, Second address line → Commercient second address line, city property → Commercient city |
| Customer To Account Lookup | Account | 2 | Commercient AR customer code column → Commercient AR customer code, Commercient Encompix customer column → Commercient Aptean Encompix Customer Managed Custom Object |
| Aptean Encompix Shipping address | Commercient Aptean Encompix Customer Ship To Managed Custom Object | 78 | Account → Account, Commercient Encompix customer (related record) → Commercient Encompix customer (related record), Customer identifier → Commercient customer identifier, ship to → Commercient ship to, Address line 1 → Commercient address line 1 |
| Aptean Encompix Sales order header | Commercient Aptean Encompix Sales Order Header Managed Custom Object | 94 | Account → Account, Commercient Encompix customer (related record) → Commercient Encompix customer (related record), Order number → Commercient order number, Customer identifier → Commercient customer identifier, Customer purchase order → Commercient customer purchase order |
| Aptean Encompix Invoice header | Commercient Aptean Encompix Invoice Managed Custom Object | 79 | Account → Account, Commercient Encompix customer (related record) → Commercient Encompix customer (related record), Encompix sales order header column → Encompix sales order header (custom field), AR account → AR account, Customer identifier → Commercient customer identifier |
| Aptean Encompix invoice line | Commercient Aptean Encompix Invoice Line Managed Custom Object | 86 | Commercient Encompix invoice header (related record) → Commercient Encompix invoice header (related record), job header column → Job header (custom object), Commercient Encompix item (related record) → Commercient Encompix item (related record), Discount amount → Discount amount, Item description → Item description |

## 6. Community templates

The catalogue carries 22 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 22
- Default operations: insert on 22, update on 22, delete on 22
- Marked as circular sync: 0
- Licence groups they span: 10
- Destination objects: Account, Product, Account Matching (custom object), Aptean Encompix employee
  (custom object), Commercient Aptean Encompix Customer Managed Custom Object, Commercient Aptean
  Encompix Customer Ship To Managed Custom Object, Commercient Aptean Encompix Invoice Line Managed
  Custom Object, Commercient Aptean Encompix Invoice Managed Custom Object, Commercient Aptean
  Encompix Item Managed Custom Object, Commercient Aptean Encompix Job Bill of Materials Managed
  Custom Object, Commercient Aptean Encompix Salesperson Managed Custom Object, Commercient Aptean
  Encompix Sales Order Header Managed Custom Object, Commercient Aptean Encompix Terms Managed
  Custom Object, Customer 1 (custom object), Item master (custom object), Job header (custom
  object), Labor log (custom object) and 2 custom objects
- Object display names: Account Create, Account Update, Aptean Encompix Customer, Aptean Encompix
  Employee, Aptean Encompix Invoice header, Aptean Encompix invoice line, Aptean Encompix Item
  Master, Aptean Encompix Job Header, Aptean Encompix Labor Log, Aptean Encompix Sales Job BOM,
  Aptean Encompix Sales order header, Aptean Encompix Salesperson, 8 more and 2 further templates
- Template groups: Account, Product, Invoice, Customer Multi Ship Addresses, Sales order

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/aptean-encompix`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Aptean Encompix → Salesforce templates set up. dlake-crmpro-salesforce is the
destination skill this page sits under: its own text is the authority for the Salesforce conventions
that hold across every ERP, and its ERP table lists this page alongside every sibling ERP page for
this destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/aptean-encompix`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
