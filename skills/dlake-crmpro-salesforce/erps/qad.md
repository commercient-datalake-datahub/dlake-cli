---
name: dlake-crmpro-salesforce/erps/qad
kind: erp-summary
description: >-
  Use it when standing up or reading a QAD → Salesforce template set, when deciding which templates
  to import and activate, or when a run completes without pushing records and the answer is in the
  view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro generally,
  and dlake-crmpro-salesforce, the destination skill this page is a child of, which carries the
  Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — QAD: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/qad` (or `list_skills`) against the Commercient
admin plane. Existing customers who need access or help: contact support@commercient.com. New
customers: contact sales@commercient.com to become a customer and be whitelisted.

dlake-crmpro is the parent skill and the authority for everything general: the CRMPro tools, process
configuration and field list, the sync history, how source data is selected, and what a run that
finds nothing does. Read it first; this page does not repeat it. dlake-crmpro-salesforce is the
destination skill this page is a child of, and the authority for the Salesforce conventions that
hold across every ERP: read it first, then come back here for what this source's own templates set.
This page grows as the catalogue does.

## 1. What the templates deliver

| Group | Business outcome | Objects | Source tables and views |
|---|---|---|---|
| **GET USER** | The templates push users to Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | users | — |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Contact, Commercient QAD Customer Managed Custom Object, Commercient QAD Salesperson Managed Custom Object | customer master records, address master records, contacts, addresses |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient QAD Address Master Managed Custom Object | address master records, customer master records |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient QAD Invoice Managed Custom Object, Commercient QAD Invoice Detail Managed Custom Object | invoice history headers, invoice history lines |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient QAD Sales Order Managed Custom Object, Commercient QAD Sales Order Detail Managed Custom Object | sales order headers, invoice history headers, address master records, sales order lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| GET USER | users | — | 0 |
| QAD Salesperson | Commercient QAD Salesperson Managed Custom Object | Commercient external key (Infor package) | 1 |
| Account | Account | Commercient AR customer code | 2 |
| QAD Customer | Commercient QAD Customer Managed Custom Object | Commercient external key (Infor package) | 4 |
| QAD Customer to account lookup | Account | Commercient AR customer code | 5 |
| QAD Sales order | Commercient QAD Sales Order Managed Custom Object | Commercient external key (Infor package) | 6 |
| QAD Sales order line | Commercient QAD Sales Order Detail Managed Custom Object | Commercient external key (Infor package) | 7 |
| QAD Invoice | Commercient QAD Invoice Managed Custom Object | Commercient external key (Infor package) | 8 |
| QAD invoice line | Commercient QAD Invoice Detail Managed Custom Object | Commercient external key (Infor package) | 9 |
| QAD Address | Commercient QAD Address Master Managed Custom Object | Commercient external key (Infor package) | 10 |
| Contact | Contact | External key (custom field) | 11 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| salesperson feed | insert + update | address master records |
| account feed | insert only | customer master records, address master records |
| customer feed | insert + update | customer master records |
| customer account lookup feed | insert + update | customer master records |
| sales order feed | insert + update | sales order headers, invoice history headers, address master records |
| sales order line feed | insert + update | sales order lines |
| invoice feed | insert + update | invoice history headers |
| invoice line feed | insert + update | invoice history lines |
| address feed | insert + update | address master records, customer master records |
| contact feed | insert + update | contacts, addresses |

## 4. Order of work

The templates set run sequence from 0 to 11. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 0 — GET USER
- 1 — QAD Salesperson
- 2 — Account
- 4 — QAD Customer
- 5 — QAD Customer to account lookup
- 6 — QAD Sales order
- 7 — QAD Sales order line
- 8 — QAD Invoice
- 9 — QAD invoice line
- 10 — QAD Address
- 11 — Contact

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- account feed reads salesperson sync output, user sync output
- customer account lookup feed reads customer sync output
- contact feed reads address sync output
- customer feed reads salesperson sync output, account sync output
- address feed reads account sync output, customer sync output
- invoice feed reads account sync output, customer sync output
- invoice line feed reads invoice sync output
- sales order feed reads account sync output, customer sync output, address sync output, salesperson
  sync output
- sales order line feed reads sales order sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| QAD Salesperson | Commercient QAD Salesperson Managed Custom Object | 6 | Salesperson address code → Commercient salesperson address code, Salesperson sort name → Commercient salesperson sort name, Salesperson user field 1 → Commercient salesperson user field 1, Salesperson user field 2 → Commercient salesperson user field 2, Salesperson domain → Commercient salesperson domain |
| Account | Account | 12 | Commercient AR customer code column → Commercient AR customer code, Type → Type, QAD salesperson (source column) → QAD salesperson (custom field), Active → Active (custom field), QAD code (source column) → QAD code (custom field) |
| QAD Customer | Commercient QAD Customer Managed Custom Object | 98 | Account → Account, Salesperson → Commercient salesperson (related record), Customer active → Customer active, Submit proposal → Commercient submit proposal, Draft approval → Commercient draft approval |
| QAD Customer to account lookup | Account | 2 | Commercient AR customer code column → Commercient AR customer code, Commercient QAD customer (source column) → Commercient customer (related record) |
| QAD Sales order | Commercient QAD Sales Order Managed Custom Object | 116 | QAD salesperson (source column) → QAD salesperson (custom field), Account → Account, Customer → Commercient customer (related record), Sales order status → Commercient sales order status, Sales order AR account → Sales order AR account |
| QAD Sales order line | Commercient QAD Sales Order Detail Managed Custom Object | 97 | Sales order → Commercient sales order (related record), Sales order line record identifier → Commercient sales order line record identifier, Abnormal demand → Commercient abnormal demand, Sales order line account → Sales order line account, Sales order line actual price → Sales order line actual price |
| QAD Invoice | Commercient QAD Invoice Managed Custom Object | 100 | Account → Account, QAD customer (source column) → QAD customer (source column), Invoice status → Commercient invoice status, Invoice AR account → Invoice AR account, Invoice AR sub account → Commercient invoice AR sub account |
| QAD invoice line | Commercient QAD Invoice Detail Managed Custom Object | 96 | Invoice → Invoice, Invoice line actual price → Invoice line actual price, Invoice line bonus → Commercient invoice line bonus, Invoice line calculation field → Invoice line calculation field, → |
| QAD Address | Commercient QAD Address Master Managed Custom Object | 68 | Account → Account (custom field), QAD customer (source column) → QAD customer (custom field), Address code → Commercient address code, Advance ship notice data → Commercient advance ship notice data, Attention → Commercient attention |
| Contact | Contact | 13 | QAD address (source column) → QAD address (custom field), Family name → Family name, Given name → Given name, Email → Email, Phone → Phone |

## 6. Community templates

The catalogue carries 12 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 12
- Default operations: insert on 12, update on 12, delete on 12
- Marked as circular sync: 0
- Licence groups they span: 7
- Destination objects: Account, Commercient QAD Address Master Managed Custom Object, Commercient
  QAD Address Managed Custom Object, Commercient QAD Customer Managed Custom Object, Commercient QAD
  Invoice Managed Custom Object, Commercient QAD Invoice Detail Managed Custom Object, Commercient
  QAD Sales Order Managed Custom Object, Commercient QAD Sales Order Detail Managed Custom Object,
  Commercient QAD Salesperson Managed Custom Object, Contact
- Object display names: QAD Address, Account, Account Update, Contact, QAD Customer, QAD Customer to
  account lookup, QAD Invoice, QAD invoice line, QAD Sales order, QAD Sales order line, QAD
  Salesperson
- Template groups: Account, Customer Multi Ship Addresses, Invoice, Sales order

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/qad`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped QAD → Salesforce templates set up. dlake-crmpro-salesforce is the destination skill this
page sits under: its own text is the authority for the Salesforce conventions that hold across every
ERP, and its ERP table lists this page alongside every sibling ERP page for this destination. For
the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/qad`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
