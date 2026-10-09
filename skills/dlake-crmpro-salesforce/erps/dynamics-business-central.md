---
name: dlake-crmpro-salesforce/erps/dynamics-business-central
kind: erp-summary
description: >-
  Use it when standing up or reading a Dynamics Business Central → Salesforce template set, when
  deciding which templates to import and activate, or when a run completes without pushing records
  and the answer is in the view or the configuration row. It extends dlake-crmpro, which covers
  operating CRMPro generally, and dlake-crmpro-salesforce, the destination skill this page is a
  child of, which carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Dynamics Business Central: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/dynamics-business-central` (or `list_skills`)
against the Commercient admin plane. Existing customers who need access or help: contact
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
| **Sync Employee** | ERP Employee data becomes Dynamics Business Central Employee (custom object) in Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | Dynamics Business Central Employee (custom object) | employees |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Contact, Commercient Dynamics NAV Customer Managed Custom Object | customers, contacts |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Dynamics NAV Invoice Header Managed Custom Object, Commercient Dynamics NAV Invoice Lines Managed Custom Object | sales invoices, sales invoice lines |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Dynamics NAV Order Header Managed Custom Object, Commercient Dynamics NAV Order Lines Managed Custom Object | sales orders, sales order lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Sync Employee | Dynamics Business Central Employee (custom object) | External key (custom field) | 0 |
| Sync Account | Account | Commercient AR customer code | 1 |
| Sync Customer | Commercient Dynamics NAV Customer Managed Custom Object | Commercient external key (Dynamics NAV package) | 2 |
| Sync Customer to account lookup | Account | Commercient AR customer code | 3 |
| Sync Contact records | Contact | External key (custom field) | 4 |
| Sync Sales order header | Commercient Dynamics NAV Order Header Managed Custom Object | Commercient external key (Dynamics NAV package) | 10 |
| Sync sales order detail | Commercient Dynamics NAV Order Lines Managed Custom Object | Commercient external key (Dynamics NAV package) | 11 |
| Sync invoice header | Commercient Dynamics NAV Invoice Header Managed Custom Object | Commercient external key (Dynamics NAV package) | 12 |
| Sync invoice detail | Commercient Dynamics NAV Invoice Lines Managed Custom Object | Commercient external key (Dynamics NAV package) | 13 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| employee feed | insert + update | employees |
| account feed | insert + update | customers |
| customer feed | insert + update | customers |
| customer account lookup feed | insert + update | customers |
| contact feed | insert + update | contacts |
| sales order feed | insert + update | sales orders |
| sales order line feed | insert + update | sales order lines |
| invoice feed | insert + update | sales invoices |
| invoice line feed | insert + update | sales invoice lines |

## 4. Order of work

The templates set run sequence from 0 to 13. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 0 — Sync Employee
- 1 — Sync Account
- 2 — Sync Customer
- 3 — Sync Customer to account lookup
- 4 — Sync Contact records
- 10 — Sync Sales order header
- 11 — Sync sales order detail
- 12 — Sync invoice header
- 13 — Sync invoice detail

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- customer account lookup feed reads customer sync output
- contact feed reads account sync output, customer sync output
- customer feed reads account sync output
- invoice feed reads account sync output, customer sync output
- invoice line feed reads invoice sync output
- sales order feed reads account sync output, customer sync output
- sales order line feed reads sales order sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Sync Employee | Dynamics Business Central Employee (custom object) | 4 | Code → Code (custom field), →, Phone number → Phone number (custom field), Privacy blocked → Privacy blocked |
| Sync Account | Account | 8 | Commercient AR customer code column → Commercient AR customer code, Billing street → Billing street, Billing city → Billing city, Billing state → Billing state, Billing country → Billing country |
| Sync Customer | Commercient Dynamics NAV Customer Managed Custom Object | 103 | Name 2 (custom field) → Name line 2 (custom field), Search name → Search name (custom field), Inter company partner code → Inter company partner code (custom field), Balance in local currency → Balance in local currency (custom field), Balance due in local currency → Balance due in local currency (custom field) |
| Sync Customer to account lookup | Account | 2 | Commercient AR customer code column → Commercient AR customer code, Commercient Dynamics NAV customer (related record) → Commercient Dynamics NAV customer (related record) |
| Sync Contact records | Contact | 8 | Name → Name (custom field), Type → Type (custom field), Title → Title (custom field), Phone → Phone (custom field), Email → Email (custom field) |
| Sync Sales order header | Commercient Dynamics NAV Order Header Managed Custom Object | 98 | Document type → Document type (custom field), Sell to customer number → Sell to customer number (custom field), Sell to customer name → Sell to customer name (custom field), Quote number → Quote number (custom field), Posting description → Posting description (custom field) |
| Sync sales order detail | Commercient Dynamics NAV Order Lines Managed Custom Object | 97 | Document type → Document type (custom field), Document number → Document number (custom field), Line number → Line number (custom field), →, No → Commercient number |
| Sync invoice header | Commercient Dynamics NAV Invoice Header Managed Custom Object | 84 | Document type → Document type (custom field), No → Commercient number, Sell to customer number → Sell to customer number (custom field), Sell to customer name → Sell to customer name (custom field), Posting description → Posting description (custom field) |
| Sync invoice detail | Commercient Dynamics NAV Invoice Lines Managed Custom Object | 71 | Document type → Document type (custom field), Document number → Document number (custom field), Line number → Line number (custom field), →, No → Commercient number |

## 6. Community templates

The catalogue carries 147 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 147
- Default operations: insert on 147, update on 147, delete on 147
- Marked as circular sync: 0
- Licence groups they span: 12
- Destination objects: Account, Product, Price book entry, Commercient Dynamics NAV Invoice Header
  Managed Custom Object, Commercient Dynamics NAV Invoice Lines Managed Custom Object, Contact,
  Commercient Dynamics NAV Customer Managed Custom Object, Commercient Dynamics NAV Item Managed
  Custom Object, Commercient Dynamics NAV Order Header Managed Custom Object, Commercient Dynamics
  NAV Order Lines Managed Custom Object, Price book object, Dynamics Business Central Employee
  (custom object), Account Matching (custom object), Dynamics Business Central Shipping Address
  (custom object), User, Account (custom object), Commercient Dynamics NAV Payment Terms Managed
  Custom Object, Commercient Dynamics NAV Sales Invoice Line Managed Custom Object, Contact Matching
  (custom object), 1 more and 20 custom objects
- Object display names: Sync Account, Sync Customer, Sync Customer to account lookup, Sync sales
  order detail, Sync Sales order header, Sync standard price book sync output (generic name), Sync
  Contact records, Sync Item to product lookup, Sync Product object, Sync Standard price book
  update, Sync Items, Sync Sales invoice, 32 more and 7 further templates
- Template groups: Account, Product, Invoice, Sales order, Customer Multi Ship Addresses, Pricebook

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/dynamics-business-central`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Dynamics Business Central → Salesforce templates set up. dlake-crmpro-salesforce is the
destination skill this page sits under: its own text is the authority for the Salesforce conventions
that hold across every ERP, and its ERP table lists this page alongside every sibling ERP page for
this destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/dynamics-business-central`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
