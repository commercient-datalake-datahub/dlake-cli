---
name: dlake-crmpro-salesforce/erps/acumatica
kind: erp-summary
description: >-
  Use it when standing up or reading an Acumatica → Salesforce template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-salesforce, the destination skill this page is a child of, which
  carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Acumatica: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/acumatica` (or `list_skills`) against the
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
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Contact, Commercient Acumatica Customer Managed Custom Object, Commercient Customer To Account Lookup Managed Custom Object | customers, business accounts, addresses, contacts |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Acumatica Address Managed Custom Object | addresses, business accounts, contacts |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Acumatica Sales Invoice Managed Custom Object, Commercient Acumatica Sales Invoice Detail Managed Custom Object | AR invoice lines, business accounts, AR invoices, AR addresses, terms |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Acumatica Sales Order Header Managed Custom Object, Commercient Acumatica Sales Order Detail Managed Custom Object | sales orders, addresses, contacts, customers, sales order lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Account | Account | Commercient AR customer code | 2 |
| Sync Customer | Commercient Acumatica Customer Managed Custom Object | Commercient external key (Acumatica and MYOB package) | 5 |
| Sync Customer to account lookup | Commercient Customer To Account Lookup Managed Custom Object | Commercient external key (Acumatica and MYOB package) | 5 |
| Sync Sales order header | Commercient Acumatica Sales Order Header Managed Custom Object | Commercient external key (Acumatica and MYOB package) | 6 |
| Sync sales order detail | Commercient Acumatica Sales Order Detail Managed Custom Object | Commercient external key (Acumatica and MYOB package) | 7 |
| Sync invoice detail | Commercient Acumatica Sales Invoice Detail Managed Custom Object | Commercient external key (Acumatica and MYOB package) | 10 |
| Sync Address | Commercient Acumatica Address Managed Custom Object | Commercient external key (Acumatica and MYOB package) | 11 |
| Contact | Contact | External key (custom field) | 21 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| invoice feed | insert + update | AR invoice lines, business accounts, AR invoices, AR addresses |
| account feed | insert + update | customers, business accounts, addresses, contacts |
| customer feed | insert + update | customers, business accounts |
| customer account lookup feed | insert + update | business accounts, customers |
| sales order feed | insert + update | sales orders, addresses, contacts, customers |
| sales order line feed | insert + update | sales order lines |
| invoice line feed | insert + update | AR invoice lines, AR invoices, business accounts, inventory items |
| address feed | insert + update | addresses, business accounts, contacts |
| contact feed | insert only | contacts, business accounts |

## 4. Order of work

The templates set run sequence to 2, 5, 6, 7, 10, 11, 21. A run processes active rows in ascending
run sequence, which is the order the templates put them in:

- 2 — Account
- 5 — Sync Customer, Sync Customer to account lookup
- 6 — Sync Sales order header
- 7 — Sync sales order detail
- 10 — Sync invoice detail
- 11 — Sync Address
- 21 — Contact

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- account feed reads terms sync output; no template in this set writes terms sync output
- contact feed reads account sync output
- customer feed reads account sync output
- customer account lookup feed reads customer sync output
- address feed reads account sync output, customer sync output
- invoice feed reads account sync output, customer sync output, terms sync output, sales order sync
  output, invoice sync output; no template in this set writes terms sync output, invoice sync output
- invoice line feed reads invoice sync output, sales order sync output, account sync output, product
  sync output; no template in this set writes invoice sync output, product sync output
- sales order line feed reads sales order sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Sync invoice header | Commercient Acumatica Sales Invoice Managed Custom Object | 6 | account lookup → account lookup, Commercient Acumatica customer (related record) → Commercient Acumatica customer (related record), Balance → Balance, Invoice date → Invoice date, Reference number → Reference number |
| Account | Account | 18 | Commercient AR customer code column → Commercient AR customer code, Account address street → Account address street, account address city column → Account address city, Account address state code → Account address state code, account address country code column → Account address country code |
| Sync Customer | Commercient Acumatica Customer Managed Custom Object | 50 | Account → Commercient account (related record), Account code → Account code (custom field), Account name → Account name (custom field), Legal name → Legal name (custom field), Account reference number → Account reference number (custom field) |
| Sync Customer to account lookup | Commercient Customer To Account Lookup Managed Custom Object | 2 | Commercient AR customer code column → Commercient AR customer code, Commercient Acumatica customer (related record) → Commercient Acumatica Customer Managed Custom Object |
| Sync Sales order header | Commercient Acumatica Sales Order Header Managed Custom Object | 75 | Approved → Commercient approved, Base currency → Base currency, Billing address line 1 → Commercient bill to address line 1, Billing address line 2 → Commercient bill to address line 2, Billing address city → Billing address city |
| Sync sales order detail | Commercient Acumatica Sales Order Detail Managed Custom Object | 144 | Commercient Acumatica sales order (related record) → Commercient Acumatica sales order (related record), Order type → Commercient order type, Order number → Commercient order number, Line number → Commercient line number, Sort order → Commercient sort order |
| Sync invoice detail | Commercient Acumatica Sales Invoice Detail Managed Custom Object | 30 | Company identifier → Company (custom field), Branch → Commercient branch, Line number → Commercient line number, Sort order → Sort order (custom field), Account → Commercient account (related record) |
| Sync Address | Commercient Acumatica Address Managed Custom Object | 27 | Account → Account, Commercient Acumatica customer (related record) → Commercient Acumatica customer (related record), Display name → Commercient display name, Full name → Commercient full name, Account code → Account code |
| Contact | Contact | 6 | Given name → Given name, Family name → Family name, Type → Type, Email → Email, Phone → Phone |

## 6. Community templates

The catalogue carries 51 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 51
- Default operations: insert on 51, update on 51, delete on 51
- Marked as circular sync: 0
- Licence groups they span: 10
- Destination objects: Account, Commercient Acumatica Sales Invoice Managed Custom Object, Product,
  Commercient Acumatica Customer Managed Custom Object, Commercient Acumatica Sales Invoice Detail
  Managed Custom Object, Commercient Acumatica Sales Order Managed Custom Object, Contact,
  Commercient Acumatica Sales Order Detail Managed Custom Object, Commercient Acumatica Sales Order
  Details Managed Custom Object, Commercient Acumatica Sales Order Header Managed Custom Object,
  Commercient Customer To Account Lookup Managed Custom Object, Commercient Stock Item Managed
  Custom Object, Price book entry, Account, Acumatica inventory site lot serial (custom object),
  Acumatica item warehouse (custom object), Acumatica warehouse list (custom object), Commercient
  Account Matching Managed Custom Object, Commercient Acumatica Address Managed Custom Object and 3
  more
- Object display names: Sync Customer to account lookup, Sync invoice header, Sync sales order
  detail, Sync Sales order header, Contact, Sync Customer, Sync invoice detail, Account, Account
  Update, Sync Item to product lookup, Account Create, Account update key and 18 more
- Template groups: Account, Product, Sales order, Invoice, Customer Multi Ship Addresses

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/acumatica`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Acumatica → Salesforce templates set up. dlake-crmpro-salesforce is the destination
skill this page sits under: its own text is the authority for the Salesforce conventions that hold
across every ERP, and its ERP table lists this page alongside every sibling ERP page for this
destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/acumatica`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
