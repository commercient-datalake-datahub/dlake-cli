---
name: dlake-crmpro-zohocrm/erps/sage-50-canada
kind: erp-summary
description: >-
  Use it when standing up or reading a Sage 50 Canada → Zoho CRM template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-zohocrm, the destination skill this page is a child of, which carries
  the Zoho CRM conventions that hold across every ERP.
---
# CRMPro → Zoho CRM — Sage 50 Canada: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-zohocrm/erps/sage-50-canada` (or `list_skills`) against the
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
| **Sage 50 Canada Salesperson** | The templates push Commercient Sage 50 Canada Salesperson object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Sage 50 Canada Salesperson object | employees |
| **Account** | The templates push Accounts to Zoho CRM. New records are created and existing ones updated; none are deleted. | Accounts | customers, customer shipping addresses |
| **Sage 50 Canada Customer** | The templates push Commercient Sage 50 Canada Customer object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Sage 50 Canada Customer object | customers |
| **Sage 50 Canada Customer to account lookup** | The templates push Accounts to Zoho CRM. New records are created and existing ones updated; none are deleted. | Accounts | customers |
| **Sage 50 Canada Shipping address** | The templates push Commercient Sage 50 Canada Ship To Address object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Sage 50 Canada Ship To Address object | customer shipping addresses |
| **Product** | The templates push Products to Zoho CRM. New records are created and existing ones updated; none are deleted. | Products | inventory items |
| **Sage 50 Canada Item** | The templates push Commercient Sage 50 Canada Item object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Sage 50 Canada Item object | inventory items |
| **Sage 50 Canada Item to product lookup** | The templates push Products to Zoho CRM. New records are created and existing ones updated; none are deleted. | Products | inventory items |
| **Sage 50 Canada Item warehouse** | The templates push Commercient Sage 50 Canada Item Warehouse object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Sage 50 Canada Item Warehouse object | inventory quantities by location |
| **Sage 50 Canada Sales order header** | The templates push Commercient Sage 50 Canada Sales Order Header object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Sage 50 Canada Sales Order Header object | sales order headers |
| **Sage 50 Canada Sales order detail** | The templates push Commercient Sage 50 Canada Sales Order Detail object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Sage 50 Canada Sales Order Detail object | sales order lines, sales order headers |
| **Sage 50 Canada Invoice header** | The templates push Commercient Sage 50 Canada Invoice Header object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Sage 50 Canada Invoice Header object | invoice headers, customers |
| **Sage 50 Canada Invoice detail** | The templates push Commercient Sage 50 Canada Invoice Detail object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Sage 50 Canada Invoice Detail object | invoice lines, inventory items |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Sage 50 Canada Salesperson | Commercient Sage 50 Canada Salesperson object | Commercient external key column | 1 |
| Account | Accounts | Commercient AR customer code (Zoho field) | 2 |
| Sage 50 Canada Customer | Commercient Sage 50 Canada Customer object | Commercient external key column | 3 |
| Sage 50 Canada Customer to account lookup | Accounts | Commercient AR customer code (Zoho field) | 4 |
| Sage 50 Canada Shipping address | Commercient Sage 50 Canada Ship To Address object | Commercient external key column | 5 |
| Product | Products | Commercient external key column | 6 |
| Sage 50 Canada Item | Commercient Sage 50 Canada Item object | Commercient external key column | 7 |
| Sage 50 Canada Item to product lookup | Products | Commercient external key column | 8 |
| Sage 50 Canada Item warehouse | Commercient Sage 50 Canada Item Warehouse object | Commercient external key column | 9 |
| Sage 50 Canada Sales order header | Commercient Sage 50 Canada Sales Order Header object | Commercient external key column | 10 |
| Sage 50 Canada Sales order detail | Commercient Sage 50 Canada Sales Order Detail object | Commercient external key column | 11 |
| Sage 50 Canada Invoice header | Commercient Sage 50 Canada Invoice Header object | Commercient external key column | 12 |
| Sage 50 Canada Invoice detail | Commercient Sage 50 Canada Invoice Detail object | Commercient external key column | 13 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| salesperson feed | insert only | employees |
| account feed | insert only | customers, customer shipping addresses |
| customer feed | insert only | customers |
| customer account lookup feed | insert + update | customers |
| shipping address feed | insert only | customer shipping addresses |
| product feed | insert only | inventory items |
| item feed | insert only | inventory items |
| item product lookup feed | insert + update | inventory items |
| item warehouse feed | insert + update | inventory quantities by location |
| sales order feed | insert + update | sales order headers |
| sales order line feed | insert + update | sales order lines, sales order headers |
| invoice feed | insert + update | invoice headers, customers |
| invoice line feed | insert + update | invoice lines, inventory items |

## 4. Order of work

The templates set run sequence from 1 to 13. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 1 — Sage 50 Canada Salesperson
- 2 — Account
- 3 — Sage 50 Canada Customer
- 4 — Sage 50 Canada Customer to account lookup
- 5 — Sage 50 Canada Shipping address
- 6 — Product
- 7 — Sage 50 Canada Item
- 8 — Sage 50 Canada Item to product lookup
- 9 — Sage 50 Canada Item warehouse
- 10 — Sage 50 Canada Sales order header
- 11 — Sage 50 Canada Sales order detail
- 12 — Sage 50 Canada Invoice header
- 13 — Sage 50 Canada Invoice detail

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- salesperson feed reads salesperson sync output; no template in this set writes salesperson sync
  output
- account feed reads salesperson sync output, account sync output; no template in this set writes
  salesperson sync output, account sync output
- customer feed reads account sync output, customer sync output; no template in this set writes
  account sync output, customer sync output
- customer account lookup feed reads customer sync output, customer account lookup sync output; no
  template in this set writes customer sync output, customer account lookup sync output
- shipping address feed reads account sync output, customer sync output, shipping address sync
  output; no template in this set writes account sync output, customer sync output, shipping address
  sync output
- product feed reads product sync output; no template in this set writes product sync output
- item feed reads product sync output, item sync output; no template in this set writes product sync
  output, item sync output
- item product lookup feed reads item sync output, item product lookup sync output; no template in
  this set writes item sync output, item product lookup sync output
- item warehouse feed reads product sync output, item sync output, item warehouse sync output; no
  template in this set writes product sync output, item sync output, item warehouse sync output
- sales order feed reads account sync output, customer sync output, sales order sync output; no
  template in this set writes account sync output, customer sync output, sales order sync output
- sales order line feed reads sales order sync output, item sync output, sales order line sync
  output; no template in this set writes sales order sync output, item sync output, sales order line
  sync output
- invoice feed reads account sync output, invoice sync output; no template in this set writes
  account sync output, invoice sync output
- invoice line feed reads invoice sync output, product sync output, invoice line sync output; no
  template in this set writes invoice sync output, product sync output, invoice line sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Zoho CRM pairs |
|---|---|---|---|
| Sage 50 Canada Salesperson | Commercient Sage 50 Canada Salesperson object | 61 | Internal record number → Commercient external key (custom field), ERP name → Name, Internal record number → Employee identifier, Wage expense account → Wage expense account (custom field), Wage expense department → Department for wage expense account (custom field) |
| Account | Accounts | 16 | Internal record number → Commercient AR customer code (Zoho field), ERP name → Account name, Internal record number → Commercient account number, Street 1,Street 2 → Billing street (Zoho field), City → Billing city (Zoho field) |
| Sage 50 Canada Customer | Commercient Sage 50 Canada Customer object | 63 | Internal record number → Commercient external key (custom field), the linked Salesforce record → account (related record), ERP name → Name, Can save credit card (ERP) → Can save credit card (custom field), Print contact on cheque → Print contact on check (custom field) |
| Sage 50 Canada Customer to account lookup | Accounts | 2 | Internal record number → Commercient AR customer code (Zoho field), the linked Salesforce record → Sage 50 Canada customer (related record) |
| Sage 50 Canada Shipping address | Commercient Sage 50 Canada Ship To Address object | 20 | Internal record number → Commercient external key (custom field), Customer record number → Account, Customer record number → Sage 50 Canada customer (related record), Address name → Name, Customer record number → returned customer identifier |
| Product | Products | 5 | Internal record number → Commercient external key (custom field), ERP name → Product name (Zoho field), Part code → ERP product code, ERP name → Description, Inactive flag → Product active (Zoho field) |
| Sage 50 Canada Item | Commercient Sage 50 Canada Item object | 52 | Internal record number → Commercient external key (custom field), the linked Salesforce record → Product (custom field), ERP name → Name, Buying units same as stocking units → Buying units are same as stocking units (custom field), Buying unit to stocking unit relationship → Buying relationship is buying unit to stocking unit (custom field) |
| Sage 50 Canada Item to product lookup | Products | 2 | Internal record number → Commercient external key column, the linked Salesforce record → Sage 50 Canada item (related record) |
| Sage 50 Canada Item warehouse | Commercient Sage 50 Canada Item Warehouse object | 15 | Inventory item identifier, Inventory location identifier → Commercient external key (custom field), Inventory item identifier, Inventory location identifier → Name, Inventory item identifier → Product (custom field), Inventory item identifier → Sage 50 Canada item (related record), Inventory item identifier → Inventory identifier (custom field) |
| Sage 50 Canada Sales order header | Commercient Sage 50 Canada Sales Order Header object | 61 | Internal record number, Order customer identifier → Commercient external key (custom field), Internal record number → Name, Order customer identifier → account (related record), Order customer identifier → customer (related record), Order cleared → Order cleared from the system (custom field) |
| Sage 50 Canada Sales order detail | Commercient Sage 50 Canada Sales Order Detail object | 27 | Sales order identifier, Line number → Commercient external key (custom field), Sales order identifier, Line number → Name, the linked Salesforce record → sales order header (related record), Default base price (ERP) → Base price is default price from price list (custom field), Default price used → Price is default price from price list (custom field) |
| Sage 50 Canada Invoice header | Commercient Sage 50 Canada Invoice Header object | 37 | Invoice record identifier → Commercient external key (custom field), Invoice record identifier → Name, the linked Salesforce record → Account, →, Allocate to all (ERP) → Is allocated to all (custom field) |
| Sage 50 Canada Invoice detail | Commercient Sage 50 Canada Invoice Detail object | 26 | Invoice record identifier,Line number → Commercient external key (custom field), Invoice record identifier,Line number → Name, Invoice record identifier → invoice header (related record), Item → Product (custom field), Default description used → Is default description (custom field) |

## 6. Community templates

The catalogue carries 48 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 48
- Default operations: insert on 48, update on 48, delete on 48
- Marked as circular sync: 0
- Licence groups they span: 4
- Destination objects: Accounts, Products, Commercient Sage 50 Canada Customer object, Commercient
  Sage 50 Canada Invoice Detail object, Commercient Sage 50 Canada Invoice Header object,
  Commercient Sage 50 Canada Item object, Commercient Sage 50 Canada Item Warehouse object,
  Commercient Sage 50 Canada Salesperson object, Commercient Sage 50 Canada Ship To Address object,
  Commercient Sage 50 Canada Sales Order Detail object, Commercient Sage 50 Canada Sales Order
  Header object, users, Contacts, Zoho price books, Vendors and 3 custom objects
- Object display names: Product, Sage 50 Canada Customer, Sage 50 Canada Customer to account lookup,
  Sage 50 Canada Invoice detail, Sage 50 Canada Invoice header, Sage 50 Canada Item, Sage 50 Canada
  Item to product lookup, Sage 50 Canada Item warehouse, Sage 50 Canada Salesperson, Sage 50 Canada
  Sales order detail, Sage 50 Canada Sales order header, Sage 50 Canada Shipping address, 10 more
  and a further template
- Template groups: Account, CRM Order and Line

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-zohocrm/erps/sage-50-canada`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Sage 50 Canada → Zoho CRM templates set up. dlake-crmpro-zohocrm is the destination
skill this page sits under: its own text is the authority for the Zoho CRM conventions that hold
across every ERP, and its ERP table lists this page alongside every sibling ERP page for this
destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-zohocrm/erps/sage-50-canada`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
