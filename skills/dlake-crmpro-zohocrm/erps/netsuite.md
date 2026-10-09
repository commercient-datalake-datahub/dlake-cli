---
name: dlake-crmpro-zohocrm/erps/netsuite
kind: erp-summary
description: >-
  Use it when standing up or reading a NetSuite → Zoho CRM template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-zohocrm, the destination skill this page is a child of, which carries
  the Zoho CRM conventions that hold across every ERP.
---
# CRMPro → Zoho CRM — NetSuite: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-zohocrm/erps/netsuite` (or `list_skills`) against the
Commercient admin plane. Existing customers who need access or help: contact
support@commercient.com. New customers: contact sales@commercient.com to become a customer and be
whitelisted.

dlake-crmpro is the parent skill and the authority for everything general: the CRMPro tools, process
configuration and field list, the sync history, how source data is selected, and what a run that
finds nothing does. Read it first; this page does not repeat it. dlake-crmpro-zohocrm is the
destination skill this page is a child of, and the authority for the Zoho CRM conventions that hold
across every ERP: read it first, then come back here for what this source's own templates set. This
page grows as the catalogue does.

## 1. What the templates deliver

| Group | Business outcome | Objects | Source tables and views |
|---|---|---|---|
| **Netsuite Term** | The templates push Commercient NetSuite Terms object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient NetSuite Terms object | terms |
| **Netsuite Salesperson** | The templates push Commercient NetSuite Salesperson object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient NetSuite Salesperson object | employees |
| **Account** | The templates push Accounts to Zoho CRM. New records are created and existing ones updated; none are deleted. | Accounts | customers, customer address book entries, custom field values |
| **Child Account** | The templates push Accounts to Zoho CRM. New records are created and existing ones updated; none are deleted. | Accounts | customers, customer address book entries, custom field values |
| **Netsuite Customer** | The templates push Commercient NetSuite Customer object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient NetSuite Customer object | customers |
| **Netsuite Customer address** | The templates push Commercient NetSuite Address object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient NetSuite Address object | customer address book entries, customers |
| **Product** | The templates push Products to Zoho CRM. New records are created and existing ones updated; none are deleted. | Products | lot numbered inventory items, inventory items |
| **Netsuite Item** | The templates push Commercient NetSuite Item object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient NetSuite Item object | lot numbered inventory items |
| **Netsuite Item warehouse** | The templates push Commercient NetSuite Item Warehouse object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient NetSuite Item Warehouse object | inventory items, lot numbered inventory items |
| **Netsuite Sales order** | The templates push Commercient NetSuite Sales Order object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient NetSuite Sales Order object | sales orders |
| **Netsuite Sales order line** | The templates push Commercient NetSuite Sales Order Line object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient NetSuite Sales Order Line object | sales order items, sales orders |
| **Netsuite Invoice** | The templates push Commercient NetSuite Invoice object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient NetSuite Invoice object | invoices |
| **Netsuite invoice line** | The templates push Commercient NetSuite Invoice Line object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient NetSuite Invoice Line object | invoice items, invoices |
| **Contact** | The templates push Contacts to Zoho CRM. New records are created and existing ones updated; none are deleted. | Contacts | contacts |
| **Product Pricebook** | The templates push Zoho product price book relation to Zoho CRM. New records are created and existing ones updated; none are deleted. | Zoho product price book relation | inventory items |
| **Netsuite Tax Type** | The templates push NetSuite Tax Type (custom object) to Zoho CRM. New records are created and existing ones updated; none are deleted. | NetSuite Tax Type (custom object) | tax types |
| **Netsuite Tax Group** | The templates push NetSuite Tax Group (custom object) to Zoho CRM. New records are created and existing ones updated; none are deleted. | NetSuite Tax Group (custom object) | tax groups |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Netsuite Term | Commercient NetSuite Terms object | Commercient external key column | 2 |
| Netsuite Salesperson | Commercient NetSuite Salesperson object | Commercient external key column | 3 |
| Account | Accounts | Commercient AR customer code (Zoho field) | 4 |
| Child Account | Accounts | Commercient AR customer code (Zoho field) | 5 |
| Netsuite Customer | Commercient NetSuite Customer object | Commercient external key column | 6 |
| Netsuite Customer address | Commercient NetSuite Address object | Commercient external key column | 7 |
| Product | Products | Commercient external key column | 8 |
| Netsuite Item | Commercient NetSuite Item object | Commercient AR customer code (Zoho field) | 9 |
| Netsuite Item warehouse | Commercient NetSuite Item Warehouse object | Commercient external key column | 10 |
| Netsuite Sales order | Commercient NetSuite Sales Order object | Commercient external key column | 11 |
| Netsuite Sales order line | Commercient NetSuite Sales Order Line object | Commercient external key column | 12 |
| Netsuite Invoice | Commercient NetSuite Invoice object | Commercient external key column | 13 |
| Netsuite invoice line | Commercient NetSuite Invoice Line object | Commercient external key column | 14 |
| Contact | Contacts | Commercient external key column | 15 |
| Product Pricebook | Zoho product price book relation | Commercient external key column | 16 |
| Netsuite Tax Type | NetSuite Tax Type (custom object) | Commercient external key column | 17 |
| Netsuite Tax Group | NetSuite Tax Group (custom object) | Commercient external key column | 18 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| terms feed | insert + update | terms |
| salesperson feed | insert + update | employees |
| account feed | insert + update | customers, customer address book entries, custom field values |
| account feed | insert + update | customers, customer address book entries, custom field values |
| customer feed | insert only | customers |
| customer address book feed | insert only | customer address book entries, customers |
| product feed | insert only | lot numbered inventory items, inventory items |
| item feed | insert only | lot numbered inventory items |
| item warehouse feed | insert only | inventory items, lot numbered inventory items |
| sales order feed | insert only | sales orders |
| sales order line feed | insert only | sales order items, sales orders |
| invoice feed | insert only | invoices |
| invoice line feed | insert only | invoice items, invoices |
| contact feed | insert + update | contacts |
| product price book feed | insert + update | inventory items |
| tax type feed | insert + update | tax types |
| tax group feed | insert + update | tax groups |

## 4. Order of work

The templates set run sequence from 2 to 18. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 2 — Netsuite Term
- 3 — Netsuite Salesperson
- 4 — Account
- 5 — Child Account
- 6 — Netsuite Customer
- 7 — Netsuite Customer address
- 8 — Product
- 9 — Netsuite Item
- 10 — Netsuite Item warehouse
- 11 — Netsuite Sales order
- 12 — Netsuite Sales order line
- 13 — Netsuite Invoice
- 14 — Netsuite invoice line
- 15 — Contact
- 16 — Product Pricebook
- 17 — Netsuite Tax Type
- 18 — Netsuite Tax Group

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- terms feed reads terms sync output; no template in this set writes terms sync output
- salesperson feed reads salesperson sync output; no template in this set writes salesperson sync
  output
- account feed reads salesperson sync output, terms sync output, user sync output (generic name),
  account sync output; no template in this set writes salesperson sync output, terms sync output,
  user sync output (generic name), account sync output
- account feed reads salesperson sync output, terms sync output, user sync output (generic name),
  account sync output; no template in this set writes salesperson sync output, terms sync output,
  user sync output (generic name), account sync output
- customer feed reads account sync output, customer sync output; no template in this set writes
  account sync output, customer sync output
- customer address book feed reads account sync output, customer sync output, customer address book
  sync output; no template in this set writes account sync output, customer sync output, customer
  address book sync output
- product feed reads product sync output; no template in this set writes product sync output
- item feed reads product sync output, item sync output; no template in this set writes product sync
  output, item sync output
- item warehouse feed reads product sync output, item sync output, item warehouse sync output; no
  template in this set writes product sync output, item sync output, item warehouse sync output
- sales order feed reads account sync output, customer sync output, sales order sync output; no
  template in this set writes account sync output, customer sync output, sales order sync output
- sales order line feed reads sales order sync output, item sync output, sales order line sync
  output; no template in this set writes sales order sync output, item sync output, sales order line
  sync output
- invoice feed reads account sync output, customer sync output, invoice sync output; no template in
  this set writes account sync output, customer sync output, invoice sync output
- invoice line feed reads invoice sync output, item sync output, invoice line sync output; no
  template in this set writes invoice sync output, item sync output, invoice line sync output
- contact feed reads account sync output, contact sync output; no template in this set writes
  account sync output, contact sync output
- product price book feed reads product sync output, product price book sync output; no template in
  this set writes product sync output, product price book sync output
- tax type feed reads tax type sync output; no template in this set writes tax type sync output
- tax group feed reads tax type sync output, tax group sync output; no template in this set writes
  tax type sync output, tax group sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Zoho CRM pairs |
|---|---|---|---|
| Netsuite Term | Commercient NetSuite Terms object | 12 | Internal identifier → Commercient external key (custom field), name → Name, Date driven → Date driven (custom field), Day discount expires → Day discount expires (custom field), Day of month net due → Day of month net due (custom field) |
| Netsuite Salesperson | Commercient NetSuite Salesperson object | 53 | Internal identifier → Commercient external key (custom field), Account number → Account number, Payroll identifier → Payroll identifier, Alternate name → Alternate name, Approval limit → Approval limit |
| Account | Accounts | 22 | Internal identifier → Commercient AR customer code (Zoho field), Entity identifier → Account name, phone → Phone, url → Website, Address 1, Address 2, Address line 3 → Billing street (Zoho field) |
| Child Account | Accounts | 22 | Internal identifier → Commercient AR customer code (Zoho field), Entity identifier → Account name, phone → Phone, url → Website, Address 1, Address 2, Address line 3 → Billing street (Zoho field) |
| Netsuite Customer | Commercient NetSuite Customer object | 86 | Internal identifier → Commercient external key (custom field), Entity identifier → Name, the linked Salesforce record → Account, Account number → Commercient account number, Customer aging → Customer aging |
| Netsuite Customer address | Commercient NetSuite Address object | 19 | parent account lookup, Internal identifier → Commercient external key (custom field), addressee, Entity identifier → Name, the linked Salesforce record → Account, the linked Salesforce record → NetSuite customer (related record), Internal identifier → Internal identifier |
| Product | Products | 6 | Internal identifier → Commercient external key column, Item identifier → Product name (Zoho field), Item identifier → ERP product code, Sales description → Description, Inactive status → Product active (Zoho field) |
| Netsuite Item | Commercient NetSuite Item object | 117 | Internal identifier → Commercient AR customer code (Zoho field), Item identifier → Name, the linked Salesforce record → Product, Automatic lead time → Automatic lead time, Automatic preferred stock level → Automatic preferred stock level |
| Netsuite Item warehouse | Commercient NetSuite Item Warehouse object | 5 | Internal identifier → Commercient external key (custom field), Internal identifier → Name, the linked Salesforce record → Product, the linked Salesforce record → NetSuite item (related record), Quantity on hand → Quantity on hand (custom field) |
| Netsuite Sales order | Commercient NetSuite Sales Order object | 112 | Internal identifier → Commercient external key (custom field), Transaction number → Name, Entity internal identifier → Account, Entity internal identifier → NetSuite customer (related record), Actual ship date → Actual ship date |
| Netsuite Sales order line | Commercient NetSuite Sales Order Line object | 60 | Internal identifier, line → Commercient external key (custom field), Internal identifier, line, Transaction number → Name, the linked Salesforce record → Sales order (single record), the linked Salesforce record → NetSuite item (related record), Item internal identifier → Item internal identifier |
| Netsuite Invoice | Commercient NetSuite Invoice object | 99 | Internal identifier → Commercient external key (custom field), Transaction number → Name, Entity internal identifier → Account, Entity internal identifier → NetSuite customer (related record), Alternate handling cost → Alternate handling cost |
| Netsuite invoice line | Commercient NetSuite Invoice Line object | 46 | Internal identifier, line → Commercient external key (custom field), Internal identifier, line, Transaction number → Name, the linked Salesforce record → NetSuite invoice (related record), the linked Salesforce record → NetSuite item (related record), amount → amount |
| Contact | Contacts | 9 | Company internal identifier → Account name, Internal identifier → Commercient external key column, email → Email, Entity identifier → First name, Entity identifier → Last name |
| Product Pricebook | Zoho product price book relation | 4 | Internal identifier → Commercient external key column, the linked Salesforce record → Product lookup (Zoho field), the linked Salesforce record → Price book lookup (Zoho field), Base price → List price |
| Netsuite Tax Type | NetSuite Tax Type (custom object) | 8 | Internal identifier → Commercient external key column, name → Name, description → Description, Does not add to total → Does not add to total, Inactive status → Inactive status |
| Netsuite Tax Group | NetSuite Tax Group (custom object) | 16 | Internal identifier → Commercient external key column, Tax type internal identifier → NetSuite Tax Type (custom object), Internal identifier → Name, city property → City, County → County |

## 6. Community templates

The catalogue carries 64 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 64
- Default operations: insert on 64, update on 64, delete on 64
- Marked as circular sync: 0
- Licence groups they span: 5
- Destination objects: Accounts, Commercient NetSuite Address object, Commercient NetSuite Customer
  object, Commercient NetSuite Salesperson object, Contacts, Products, Commercient NetSuite Invoice
  object, Commercient NetSuite Invoice Line object, Commercient NetSuite Item object, Commercient
  NetSuite Sales Order object, Commercient NetSuite Sales Order Line object, Commercient NetSuite
  Terms object, NetSuite Inventory Location (custom object), users, Commercient NetSuite Item
  Warehouse object, NetSuite Tax Group (custom object), NetSuite Tax Type (custom object), Zoho
  price books, Commercient Account Matching Managed Custom Object, Commercient Contact Matching
  object, 1 more and 2 custom objects
- Object display names: Account, Contact, Netsuite Customer, Netsuite Customer address, Netsuite
  Salesperson, Netsuite Invoice, Netsuite invoice line, Netsuite Item, Netsuite Sales order,
  Netsuite Sales order line, Netsuite Term, Product, 15 more and 2 further templates
- Template groups: Account, CRM Order and Line, Product

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-zohocrm/erps/netsuite`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped NetSuite → Zoho CRM templates set up. dlake-crmpro-zohocrm is the destination skill this
page sits under: its own text is the authority for the Zoho CRM conventions that hold across every
ERP, and its ERP table lists this page alongside every sibling ERP page for this destination. For
the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-zohocrm/erps/netsuite`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
