---
name: dlake-crmpro-salesforce/erps/microsoft-dynamics-gp-2017
kind: erp-summary
description: >-
  Use it when standing up or reading a Microsoft Dynamics GP 2017 → Salesforce template set, when
  deciding which templates to import and activate, or when a run completes without pushing records
  and the answer is in the view or the configuration row. It extends dlake-crmpro, which covers
  operating CRMPro generally, and dlake-crmpro-salesforce, the destination skill this page is a
  child of, which carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Microsoft Dynamics GP 2017: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/microsoft-dynamics-gp-2017` (or `list_skills`)
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
| **Microsoft Dynamics GP Dynamics GP** | The templates push Dynamics GP (custom object) to Salesforce. New records are created and existing ones updated; none are deleted. | Dynamics GP (custom object) | item serial numbers, items, warehouse sites, segment descriptions |
| **Sync Sales Transaction History** | ERP sales user defined work history, sales transaction work, sales transaction history data becomes Commercient Sales Transaction History Managed Custom Object in Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Sales Transaction History Managed Custom Object | sales transaction headers, sales user defined fields, sales transaction history headers, open receivables transactions, receivables transaction history |
| **Sync Sales Transaction Amount History** | ERP sales transaction amounts work, sales transaction amounts history data becomes Commercient Sales Transaction Amounts History Managed Custom Object in Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Sales Transaction Amounts History Managed Custom Object | sales transaction history lines, sales transaction lines |
| **GET USER** | The templates push users to Salesforce. New records are created, existing ones updated, and records removed in the ERP are deleted. | users | — |
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Contact, Commercient Customer Master Managed Custom Object | customers, customer addresses |
| **CRM Ownership** | The templates push users to Salesforce. New records are created and existing ones updated; none are deleted. | users | — |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Customer Master Address Managed Custom Object | customer addresses |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Sales Transaction Work Managed Custom Object, Commercient Sales Transaction Amounts Work Managed Custom Object | sales transaction headers, sales transaction lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| GET USER | users | — | 0 |
| GET USER | users | — | 0 |
| Microsoft Dynamics GP Dynamics GP | Dynamics GP (custom object) | External key (custom field) | 1 |
| Sync Account | Account | Commercient AR customer code | 1 |
| Sync Customer | Commercient Customer Master Managed Custom Object | Commercient customer number | 2 |
| Sync Customer TO Account Lookup | Account | Commercient AR customer code | 3 |
| Sync Ship To Address | Commercient Customer Master Address Managed Custom Object | Commercient external key (Dynamics NAV package) | 5 |
| Sync Contact | Contact | External key (custom field) | 6 |
| Sync Sales Order Header | Commercient Sales Transaction Work Managed Custom Object | Commercient external key (Dynamics NAV package) | 7 |
| Sync sales order detail | Commercient Sales Transaction Amounts Work Managed Custom Object | Commercient external key (Dynamics NAV package) | 8 |
| Sync Sales Transaction History | Commercient Sales Transaction History Managed Custom Object | Commercient external key (Dynamics NAV package) | 9 |
| Sync Sales Transaction Amount History | Commercient Sales Transaction Amounts History Managed Custom Object | Commercient external key (Dynamics NAV package) | 10 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| Dynamics GP feed | insert only | item serial numbers, items, warehouse sites, segment descriptions |
| account feed | insert + update | customers, customer addresses |
| customer feed | insert + update | customers |
| customer account lookup feed | insert + update | customers |
| shipping address feed | insert + update | customer addresses |
| contact feed | insert + update | customer addresses, customers |
| sales order feed | insert + update | sales transaction headers |
| sales order line feed | insert + update | sales transaction lines |
| sales transaction history feed | insert + update | sales transaction headers, sales user defined fields, sales transaction history headers, open receivables transactions |
| sales transaction amount history feed | insert + update | sales transaction history lines, sales transaction lines |

## 4. Order of work

The templates set run sequence from 0 to 10. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 0 — GET USER
- 1 — Microsoft Dynamics GP Dynamics GP, Sync Account
- 2 — Sync Customer
- 3 — Sync Customer TO Account Lookup
- 5 — Sync Ship To Address
- 6 — Sync Contact
- 7 — Sync Sales Order Header
- 8 — Sync sales order detail
- 9 — Sync Sales Transaction History
- 10 — Sync Sales Transaction Amount History

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- sales transaction history feed reads account sync output, user sync output (generic name)
- sales transaction amount history feed reads sales transaction history sync output
- account feed reads user sync output (generic name)
- customer account lookup feed reads customer sync output
- contact feed reads account sync output, customer sync output
- customer feed reads account sync output
- shipping address feed reads account sync output, customer sync output
- sales order feed reads account sync output, customer sync output
- sales order line feed reads sales order sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Microsoft Dynamics GP Dynamics GP | Dynamics GP (custom object) | 14 | Item number,Location code,Date received,Date sequence number,Quantity type → External key (custom field), Item number,Location code,Date received,Date sequence number,Quantity type → Name, Item description → Item description (custom field), Item short name → Item short name (custom field), Item class code → Item class code (custom field) |
| Sync Account | Account | 23 | Commercient AR customer code column → Commercient AR customer code, Shipping street → Shipping street, Shipping city → Shipping city, Shipping state code → Shipping state code, Shipping country → Shipping country |
| Sync Customer | Commercient Customer Master Managed Custom Object | 104 | returned customer number → Commercient customer number, Address 1 → Commercient address line 1, Address 2 → Commercient address line 2, Address 3 → Commercient address line 3, Address code → Address code |
| Sync Customer TO Account Lookup | Account | 2 | Commercient AR customer code column → Commercient AR customer code, → Commercient customer (related record) |
| Sync Ship To Address | Commercient Customer Master Address Managed Custom Object | 31 | Salesperson identifier → Commercient salesperson identifier, Shipping zone → Commercient shipping zone, Shipping method → Commercient shipping method, Tax schedule → Tax schedule, Contact person → Contact person |
| Sync Contact | Contact | 9 | Family name → Family name, Phone → Phone, Fax → Fax, Mailing street → Mailing street, Mailing city → Mailing city |
| Sync Sales Order Header | Commercient Sales Transaction Work Managed Custom Object | 91 | Original number → Commercient original number, Document identifier → Document identifier, Document date → Document date, Quote date → Commercient quote date, Quote expiration date → Commercient quote expiration date |
| Sync sales order detail | Commercient Sales Transaction Amounts Work Managed Custom Object | 94 | Sales document number → Commercient sales document number, Item number → Commercient item number, Item description → Item description, Unit of measure → Commercient unit of measure, Location code → Location code |
| Sync Sales Transaction History | Commercient Sales Transaction History Managed Custom Object | 18 | Dynamics GP invoice number → Dynamics GP invoice number (custom field), returned purchase order number → PO number (custom field), Contact person → Contact person, Shipping method → Commercient shipping method, Subtotal → Commercient subtotal |
| Sync Sales Transaction Amount History | Commercient Sales Transaction Amounts History Managed Custom Object | 10 | Item number → Commercient item number, Item description → Item description, Unit price → Unit price, Extended price → Extended price, Discount amount → Discount amount |

## 6. Community templates

The catalogue carries 32 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 32
- Default operations: insert on 32, update on 32, delete on 32
- Marked as circular sync: 0
- Licence groups they span: 8
- Destination objects: Account, Contact, Price book entry, Product, Tracking number (custom object),
  Dynamics GP (custom object), Price book object, User, users and 9 custom objects
- Object display names: GET USER, Sync Account, Sync Contact, Sync Customer, Sync Customer TO
  Account Lookup, Sync Sales Order Header, Sync Sales Transaction Amount History, Sync Sales
  Transaction History, Sync sales order detail, Sync Ship To Address, Sync Tracking number,
  Microsoft Dynamics GP Dynamics GP and 9 more
- Template groups: Account, Product, Sales order, Customer Multi Ship Addresses

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/microsoft-dynamics-gp-2017`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Microsoft Dynamics GP 2017 → Salesforce templates set up. dlake-crmpro-salesforce is the
destination skill this page sits under: its own text is the authority for the Salesforce conventions
that hold across every ERP, and its ERP table lists this page alongside every sibling ERP page for
this destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/microsoft-dynamics-gp-2017`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
