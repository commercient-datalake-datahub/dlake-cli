---
name: dlake-crmpro-salesforce/erps/aptean-intuitive
kind: erp-summary
description: >-
  Use it when standing up or reading an Aptean Intuitive → Salesforce template set, when deciding
  which templates to import and activate, or when a run completes without pushing records and the
  answer is in the view or the configuration row. It extends dlake-crmpro, which covers operating
  CRMPro generally, and dlake-crmpro-salesforce, the destination skill this page is a child of,
  which carries the Salesforce conventions that hold across every ERP.
---
# CRMPro → Salesforce — Aptean Intuitive: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/aptean-intuitive` (or `list_skills`) against the
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
| **Account** | In addition to creating the Account record from your ERP customer records, Commercient syncs over the entire Accounting AR Customer record into a Commercient AR Customer object which is a Managed Custom Object (MCO). A lookup field is provided to lookup to the default AR Customer record MCO from the Account record. New records are created, existing ones updated, and records removed in the ERP are deleted. | Account, Contact, Commercient Customer Managed Custom Object | customers, customer bill to addresses, customer shipping addresses, commission definition headers, commission definition lines |
| **Customer Multi Ship Addresses** | If you are using multiple ship to addresses for a given AR Customer in your ERP system then you will be able to see all the addresses inside the CRM Account screen in a Commercient Multi Ship To Address MCO related list. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Customer Ship To Managed Custom Object | customer shipping addresses, customers, commission definition headers |
| **Invoice** | Both Open and Historical invoices are synchronized. Open invoice changes, such as a balance change, or a terms change are reflected in the CRM as they sync based on the frequency of the sync you have chosen (every hour, for example). New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Invoice Managed Custom Object, Commercient Invoice Line Managed Custom Object | invoices, customers, sales orders, payment terms, invoice lines |
| **Sales order** | Commercient will sync the Sales Orders from the ERP Sales Order Entry module to the Commercient Sales Order Header (MCO) objects in CRM. In the CRM, customer service and sales people can visualize the status of the order such as on hold, backorder, forward order, scheduled for delivery, whether it has shipped, and completion status. New records are created, existing ones updated, and records removed in the ERP are deleted. | Commercient Sales Order Managed Custom Object, Commercient Sales Order Line Managed Custom Object | sales orders, customers, payment terms, sales order lines, items |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Account | Account | Commercient AR customer code | 1 |
| Aptean Intuitive Customer | Commercient Customer Managed Custom Object | Commercient external key (Aptean package) | 2 |
| Aptean Intuitive Customer to account lookup | Account | Commercient AR customer code | 3 |
| Aptean Intuitive Shipping address | Commercient Customer Ship To Managed Custom Object | Commercient external key (Aptean package) | 6 |
| Aptean Intuitive Sales order | Commercient Sales Order Managed Custom Object | Commercient external key (Aptean package) | 7 |
| Aptean Intuitive Sales order line | Commercient Sales Order Line Managed Custom Object | Commercient external key (Aptean package) | 8 |
| Aptean Intuitive Invoice | Commercient Invoice Managed Custom Object | Commercient external key (Aptean package) | 9 |
| Aptean Intuitive invoice line | Commercient Invoice Line Managed Custom Object | Commercient external key (Aptean package) | 10 |
| Contact | Contact | External key (custom field) | 16 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| account feed | insert + update | customers, customer bill to addresses, customer shipping addresses, commission definition headers, commission definition lines |
| customer feed | insert + update | customers |
| customer account lookup feed | insert + update | customers |
| shipping address feed | insert + update | customer shipping addresses, customers |
| sales order feed | insert only | sales orders, customers, payment terms |
| sales order line feed | insert + update | sales order lines, sales orders, items |
| invoice feed | insert + update | invoices, customers, sales orders, payment terms |
| invoice line feed | insert + update | invoice lines, invoices, items |
| contact feed | insert only | CRM company locations, CRM companies, CRM contact links, CRM contacts, customers |

## 4. Order of work

The templates set run sequence from 1 to 16. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 1 — Account
- 2 — Aptean Intuitive Customer
- 3 — Aptean Intuitive Customer to account lookup
- 6 — Aptean Intuitive Shipping address
- 7 — Aptean Intuitive Sales order
- 8 — Aptean Intuitive Sales order line
- 9 — Aptean Intuitive Invoice
- 10 — Aptean Intuitive invoice line
- 16 — Contact

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- customer account lookup feed reads customer sync output
- contact feed reads account sync output, customer sync output
- customer feed reads account sync output
- shipping address feed reads sold to address sync output, account sync output, customer sync
  output, bill to address sync output; no template in this set writes sold to address sync output,
  bill to address sync output
- invoice feed reads account sync output, customer sync output
- invoice line feed reads invoice sync output
- sales order feed reads account sync output, customer sync output, project group sync output, bill
  to address sync output, shipping address sync output; no template in this set writes project group
  sync output, bill to address sync output
- sales order line feed reads sales order sync output, product sync output, item sync output; no
  template in this set writes product sync output, item sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Salesforce pairs |
|---|---|---|---|
| Account | Account | 32 | Commercient AR customer code column → Commercient AR customer code, Active in Intuitive → Active in Intuitive (custom field), Customer since → Customer since (custom field), Billing street → Billing street, Billing city → Billing city |
| Aptean Intuitive Customer | Commercient Customer Managed Custom Object | 57 | Account → Commercient account (related record), Customer record identifier → Customer record identifier, Customer identifier → Customer identifier, Customer sort reference → Commercient customer sort reference, Customer credit limit amount → Customer credit limit amount |
| Aptean Intuitive Customer to account lookup | Account | 2 | Commercient AR customer code column → Commercient AR customer code, Commercient Intuitive customer column → Commercient Intuitive customer (related record) |
| Aptean Intuitive Shipping address | Commercient Customer Ship To Managed Custom Object | 54 | Account → Commercient account (related record), Intuitive customer column → Commercient Intuitive customer (related record), Intuitive bill to address column → Intuitive bill to address (custom field), Intuitive sold to address column → Intuitive sold to address (custom field), Ship to address record identifier → Ship to address record identifier |
| Aptean Intuitive Sales order | Commercient Sales Order Managed Custom Object | 106 | Account → Commercient account (related record), Intuitive customer column → Commercient Intuitive customer (related record), Intuitive ship to address column → Intuitive ship to address (custom field), Intuitive project group column → Intuitive project group (custom field), Intuitive bill to address column → Intuitive bill to address (custom field) |
| Aptean Intuitive Sales order line | Commercient Sales Order Line Managed Custom Object | 88 | Product → Product (custom field), Intuitive item column → Commercient Intuitive item (related record), Intuitive sales order column → Commercient Intuitive sales order (related record), Sales order item record identifier → Sales order item record identifier, Sales order line number → Commercient sales order line number |
| Aptean Intuitive Invoice | Commercient Invoice Managed Custom Object | 82 | Account → Commercient account (related record), Intuitive customer column → Commercient Intuitive customer (related record), Invoice record identifier → Invoice record identifier, Invoice identifier → Invoice identifier, Invoice type → Invoice type |
| Aptean Intuitive invoice line | Commercient Invoice Line Managed Custom Object | 83 | Intuitive invoice column → Commercient Intuitive invoice (related record), Invoice line invoice record identifier → Invoice line invoice record identifier, Invoice line discount percent 1 → Invoice line discount percent 1 (custom field), Invoice line record identifier → Invoice line record identifier, Invoice line source → Invoice line source |
| Contact | Contact | 30 | Customer record identifier → Customer record identifier (custom field), Customer identifier → Customer identifier (custom field), Customer corporate name → Customer corporate name (custom field), Customer created date → Customer created date (custom field), Last order date → Last order date (custom field) |

## 6. Community templates

The catalogue carries 51 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 51
- Default operations: insert on 51, update on 51, delete on 51
- Marked as circular sync: 0
- Licence groups they span: 11
- Destination objects: Account, Commercient Customer Managed Custom Object, Commercient Invoice
  Managed Custom Object, Product, Commercient Customer Ship To Managed Custom Object, Commercient
  Invoice Line Managed Custom Object, Commercient Sales Order Managed Custom Object, Commercient
  Sales Order Line Managed Custom Object, Commercient Item Managed Custom Object, Price book entry,
  Aptean Intuitive AR detail (custom object), Aptean Intuitive invoice line lot detail (custom
  object), Commercient Account Matching object, Commercient Bill To Address Managed Custom Object,
  Commercient Payment Terms Managed Custom Object, Commercient Project Group Managed Custom Object,
  Commercient Shipment Managed Custom Object, Commercient Sold To Address Managed Custom Object,
  contact, 2 more and 2 custom objects
- Object display names: Account, Aptean Intuitive Customer, Aptean Intuitive Customer to account
  lookup, Aptean Intuitive Invoice, Aptean Intuitive invoice line, Aptean Intuitive Sales order,
  Aptean Intuitive Sales order line, Aptean Intuitive Shipping address, Aptean Intuitive Item,
  Aptean Intuitive Product to item reverse lookup, Contact, Product, 16 more and 3 further templates
- Template groups: Account, Invoice, Product, Customer Multi Ship Addresses, Sales order, CRM
  Opportunity and Line

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-salesforce/erps/aptean-intuitive`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Aptean Intuitive → Salesforce templates set up. dlake-crmpro-salesforce is the
destination skill this page sits under: its own text is the authority for the Salesforce conventions
that hold across every ERP, and its ERP table lists this page alongside every sibling ERP page for
this destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-salesforce/erps/aptean-intuitive`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
