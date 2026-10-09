---
name: dlake-crmpro-salesforce/erps/infor10-distribution-business
kind: erp-summary
description: >-
  Use it when standing up or reading an Infor 10 Distribution Business → Salesforce template set,
  when deciding which templates to import and activate, or when a run completes without pushing
  records and the answer is in the view or the configuration row. It extends dlake-crmpro, which
  covers operating CRMPro generally, and dlake-crmpro-salesforce, the destination skill this page is
  a child of, which carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Infor 10 Distribution Business: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/infor10-distribution-business` (or
`list_skills`) against the Commercient admin plane. Existing customers who need access or help:
contact support@commercient.com. New customers: contact sales@commercient.com to become a customer
and be whitelisted.

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
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Contact, Commercient AR Customer Managed Custom Object, Commercient Salesperson Managed Custom Object | AR customers, customer shipping addresses, contacts, salespeople |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Invoice Header Managed Custom Object, Commercient Invoice Detail Managed Custom Object | AR transactions, order entry lines, order entry headers |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Order Header Managed Custom Object, Commercient Order Line Managed Custom Object | order entry headers, AR customers, order entry lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Get Users | User | Commercient salesperson code | 1 |
| Sync Salesperson | Commercient Salesperson Managed Custom Object | Commercient external key (Infor package) | 1 |
| Sync Account | Account | Commercient AR customer code | 2 |
| Sync Customer | Commercient AR Customer Managed Custom Object | Commercient external key (Infor package) | 3 |
| Sync Customer to account lookup | Account | Commercient AR customer code | 4 |
| Sync Standard Contact | Contact | External key (custom field) | 5 |
| Order Headers | Commercient Order Header Managed Custom Object | Commercient external key (Infor package) | 10 |
| Order Details | Commercient Order Line Managed Custom Object | Commercient external key (Infor package) | 11 |
| Sync invoice header | Commercient Invoice Header Managed Custom Object | Commercient external key (Infor package) | 12 |
| Sync Invoice line item | Commercient Invoice Detail Managed Custom Object | Commercient external key (Infor package) | 13 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| salesperson feed | insert + update | salespeople |
| account feed | insert + update | AR customers, customer shipping addresses |
| customer feed | insert + update | AR customers |
| customer account lookup feed | insert + update | AR customers |
| standard contact feed | insert + update | contacts |
| sales order feed | insert + update | order entry headers, AR customers |
| sales order line feed | insert + update | order entry lines, order entry headers |
| invoice feed | insert + update | AR transactions |
| invoice line feed | insert + update | order entry lines, order entry headers, AR transactions |

## 4. Order of work

The templates set run sequence from 1 to 13. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 1 — Get Users, Sync Salesperson
- 2 — Sync Account
- 3 — Sync Customer
- 4 — Sync Customer to account lookup
- 5 — Sync Standard Contact
- 10 — Order Headers
- 11 — Order Details
- 12 — Sync invoice header
- 13 — Sync Invoice line item

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- account feed reads salesperson sync output, user sync output
- customer account lookup feed reads customer sync output
- standard contact feed reads Infor 10 Distribution Business account sync output; no template in
  this set writes Infor 10 Distribution Business account sync output
- customer feed reads account sync output, salesperson sync output
- invoice feed reads account sync output, customer sync output
- invoice line feed reads invoice sync output, item master sync output, product sync output; no
  template in this set writes item master sync output, product sync output
- sales order feed reads account sync output, customer sync output, salesperson sync output
- sales order line feed reads sales order sync output, item master sync output; no template in this
  set writes item master sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Sync Salesperson | Commercient Salesperson Managed Custom Object | 71 | Company number → Commercient company number, Sales rep → Commercient sales rep, Address → Commercient address, city property → Commercient city, state → Commercient state |
| Sync Account | Account | 19 | Commercient AR customer code column → Commercient AR customer code, the linked Commercient Infor SXe salesperson → Commercient Infor SXe salesperson (related record), owner lookup → owner lookup, Phone → Phone, Billing street → Billing street |
| Sync Customer | Commercient AR Customer Managed Custom Object | 294 | Company number → Commercient company number, Customer number → Commercient customer number, Address → Commercient address, city property → Commercient city, state → Commercient state |
| Sync Customer to account lookup | Account | 2 | Commercient AR customer code column → Commercient AR customer code, the linked Commercient Infor SXe customer → Commercient Infor SXe customer (related record) |
| Sync Standard Contact | Contact | 7 | Title → Title, Given name → Given name, Family name → Family name, Phone → Phone, Email → Email |
| Order Headers | Commercient Order Header Managed Custom Object | 304 | Account → Account, the linked Infor SXe customer → Commercient Infor SXe customer (related record), the linked Infor SXe salesperson → Commercient Infor SXe salesperson (related record), Order number → Commercient order number, Order suffix → Commercient order suffix |
| Order Details | Commercient Order Line Managed Custom Object | 245 | the linked Infor SXe sales order header → Commercient Infor SXe sales order header (related record), the linked Infor SXe item master → Commercient Infor SXe item master (related record), Product → Product (custom field), Opportunity → Opportunity (custom field), Order number → Commercient order number |
| Sync invoice header | Commercient Invoice Header Managed Custom Object | 79 | Company number → Commercient company number, Customer number → Commercient customer number, Status type → Commercient status type, Invoice number → Commercient invoice number, Invoice suffix → Commercient invoice suffix |
| Sync Invoice line item | Commercient Invoice Detail Managed Custom Object | 200 | the linked Infor SXe invoice header → Commercient Infor SXe invoice header (related record), the linked Infor SXe item master → Commercient Infor SXe item master (related record), Product → Commercient product (related record), Advertising code → Advertising code, Alternate warehouse → Commercient alternate warehouse |

## 6. Community templates

The catalogue carries 43 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 43
- Default operations: insert on 43, update on 43, delete on 43
- Marked as circular sync: 0
- Licence groups they span: 12
- Destination objects: Account, Product, Opportunity, Commercient Order Header Managed Custom
  Object, Opportunity line item, Price book entry, Commercient Baan Customer Managed Custom Object,
  Commercient Baan Customer Address Managed Custom Object, Commercient Baan Invoice Detail Managed
  Custom Object, Commercient Baan Invoice Header Managed Custom Object, Commercient Baan Item
  Managed Custom Object, Commercient Baan Sales Order Detail Managed Custom Object, Commercient Baan
  Sales Order Header Managed Custom Object, Commercient Invoice Header Managed Custom Object,
  Commercient AR Customer Managed Custom Object, Commercient Order Line Managed Custom Object,
  Commercient Invoice Detail Managed Custom Object, Commercient Quote Header Managed Custom Object,
  Commercient Salesperson Managed Custom Object, Commercient Infor SXe Quote Managed Custom Object,
  4 more and 8 custom objects
- Object display names: Sync Account, Sync Customer, Sync Customer to account lookup, Sync invoice
  header, Sync Item, Sync Item to product lookup, Sync Product object, Get Users, Infor SXe Quote
  header, Infor SXe Quote line number, Opportunity Header, Opportunity Header Update, 20 more and 4
  further templates
- Template groups: Account, Product, CRM Opportunity and Line, Invoice, Sales order, Opportunity,
  Customer Multi Ship Addresses

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/infor10-distribution-business`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Infor 10 Distribution Business → Salesforce templates set up. dlake-crmpro-salesforce is
the destination skill this page sits under: its own text is the authority for the Salesforce
conventions that hold across every ERP, and its ERP table lists this page alongside every sibling
ERP page for this destination. For the extract leg that fills the source data, see dlake-normalsync;
for the on-premises agent that runs it, dlake-syncagent; for the writeback leg,
dlake-txdownloaderpro; for standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/infor10-distribution-business`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
