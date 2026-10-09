---
name: dlake-crmpro-zohocrm/erps/infor-cloudsuite
kind: erp-summary
description: >-
  Use it when standing up or reading an Infor CloudSuite → Zoho CRM template set, when deciding
  which templates to import and activate, or when a run completes without pushing records and the
  answer is in the view or the configuration row. It extends dlake-crmpro, which covers operating
  CRMPro generally, and dlake-crmpro-zohocrm, the destination skill this page is a child of, which
  carries the Zoho CRM conventions that hold across every ERP.
---
# CRMPro → Zoho CRM — Infor CloudSuite: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-zohocrm/erps/infor-cloudsuite` (or `list_skills`) against the
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
| **Infor Cloud Terms** | The templates push Commercient Infor Cloud Suite terms object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Infor Cloud Suite terms object | terms |
| **Infor Cloud Salesperson** | The templates push Commercient Infor Cloud Suite salesperson object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Infor Cloud Suite salesperson object | salespeople |
| **Account** | The templates push Accounts to Zoho CRM. New records are created and existing ones updated; none are deleted. | Accounts | customers, customer addresses |
| **Infor Cloud Customer** | The templates push Commercient Infor Cloud Suite customer object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Infor Cloud Suite customer object | customers, customer addresses |
| **Infor Cloud Customer to account lookup** | The templates push Accounts to Zoho CRM. New records are created and existing ones updated; none are deleted. | Accounts | customers |
| **Infor Cloud Address** | The templates push Commercient Infor Cloud Suite shipping address object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Infor Cloud Suite shipping address object | customer addresses |
| **Product** | The templates push Products to Zoho CRM. New records are created and existing ones updated; none are deleted. | Products | items |
| **Infor Cloud Item master** | The templates push Commercient Infor Cloud Suite item master object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Infor Cloud Suite item master object | items |
| **Infor Cloud Item warehouse** | The templates push Commercient Infor Cloud Suite item warehouse object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Infor Cloud Suite item warehouse object | item warehouses |
| **Infor Cloud Item location** | The templates push Commercient Infor Cloud Suite item location object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Infor Cloud Suite item location object | item locations |
| **Infor Cloud Sales order** | The templates push Commercient Infor Cloud Suite sales order object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Infor Cloud Suite sales order object | customer orders |
| **Infor Cloud Sales order line** | The templates push Commercient Infor Cloud Suite sales order line object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Infor Cloud Suite sales order line object | shipments, customer order shipments, customer order lines, tracking information |
| **Infor Cloud Invoice** | The templates push Commercient Infor Cloud Suite invoice object to Zoho CRM. New records are created and existing ones updated; none are deleted. | Commercient Infor Cloud Suite invoice object | AR transactions, customer orders |
| **Contact** | The templates push Contacts to Zoho CRM. New records are created and existing ones updated; none are deleted. | Contacts | contacts, customer contacts |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Infor Cloud Terms | Commercient Infor Cloud Suite terms object | Commercient external key column | 1 |
| Infor Cloud Salesperson | Commercient Infor Cloud Suite salesperson object | Commercient external key column | 2 |
| Account | Accounts | Commercient AR customer code (Zoho field) | 3 |
| Infor Cloud Customer | Commercient Infor Cloud Suite customer object | Commercient external key column | 4 |
| Infor Cloud Customer to account lookup | Accounts | Commercient AR customer code (Zoho field) | 5 |
| Infor Cloud Address | Commercient Infor Cloud Suite shipping address object | Commercient external key column | 6 |
| Product | Products | Commercient external key column | 7 |
| Infor Cloud Item master | Commercient Infor Cloud Suite item master object | Commercient external key column | 8 |
| Infor Cloud Item warehouse | Commercient Infor Cloud Suite item warehouse object | Commercient external key column | 9 |
| Infor Cloud Item location | Commercient Infor Cloud Suite item location object | Commercient external key column | 10 |
| Infor Cloud Sales order | Commercient Infor Cloud Suite sales order object | Commercient external key column | 11 |
| Infor Cloud Sales order line | Commercient Infor Cloud Suite sales order line object | Commercient external key column | 12 |
| Infor Cloud Invoice | Commercient Infor Cloud Suite invoice object | Commercient external key column | 13 |
| Contact | Contacts | Commercient external key column | 15 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| terms feed | insert + update | terms |
| salesperson feed | insert + update | salespeople |
| account feed | insert + update | customers, customer addresses |
| customer feed | insert + update | customers, customer addresses |
| customer account lookup feed | insert + update | customers |
| address feed | insert + update | customer addresses |
| product feed | insert only | items |
| item feed | insert only | items |
| item warehouse feed | insert only | item warehouses |
| item location feed | insert only | item locations |
| sales order feed | insert + update | customer orders |
| sales order line feed | insert + update | shipments, customer order shipments, customer order lines, tracking information |
| invoice feed | insert + update | AR transactions, customer orders |
| contact feed | insert only | contacts, customer contacts |

## 4. Order of work

The templates set run sequence from 1 to 15. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 1 — Infor Cloud Terms
- 2 — Infor Cloud Salesperson
- 3 — Account
- 4 — Infor Cloud Customer
- 5 — Infor Cloud Customer to account lookup
- 6 — Infor Cloud Address
- 7 — Product
- 8 — Infor Cloud Item master
- 9 — Infor Cloud Item warehouse
- 10 — Infor Cloud Item location
- 11 — Infor Cloud Sales order
- 12 — Infor Cloud Sales order line
- 13 — Infor Cloud Invoice
- 15 — Contact

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- terms feed reads terms sync output; no template in this set writes terms sync output
- salesperson feed reads salesperson sync output; no template in this set writes salesperson sync
  output
- account feed reads account sync output, terms sync output, salesperson sync output; no template in
  this set writes account sync output, terms sync output, salesperson sync output
- customer feed reads account sync output, terms sync output, salesperson sync output, customer sync
  output; no template in this set writes account sync output, terms sync output, salesperson sync
  output, customer sync output
- customer account lookup feed reads customer sync output, customer account lookup sync output; no
  template in this set writes customer sync output, customer account lookup sync output
- address feed reads account sync output, customer sync output, address sync output; no template in
  this set writes account sync output, customer sync output, address sync output
- product feed reads product sync output; no template in this set writes product sync output
- item feed reads product sync output, item sync output; no template in this set writes product sync
  output, item sync output
- item warehouse feed reads product sync output, item sync output, item warehouse sync output; no
  template in this set writes product sync output, item sync output, item warehouse sync output
- item location feed reads product sync output, item sync output, item warehouse sync output, item
  location sync output; no template in this set writes product sync output, item sync output, item
  warehouse sync output, item location sync output
- sales order feed reads account sync output, customer sync output, sales order sync output; no
  template in this set writes account sync output, customer sync output, sales order sync output
- sales order line feed reads sales order sync output, product sync output, item sync output, sales
  order line sync output; no template in this set writes sales order sync output, product sync
  output, item sync output, sales order line sync output
- invoice feed reads account sync output, customer sync output, invoice sync output; no template in
  this set writes account sync output, customer sync output, invoice sync output
- contact feed reads account sync output, contact sync output; no template in this set writes
  account sync output, contact sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Zoho CRM pairs |
|---|---|---|---|
| Infor Cloud Terms | Commercient Infor Cloud Suite terms object | 24 | Commercient terms code → Commercient external key (custom field), description → Name, Delete indicator → Delete indicator, In workflow → In workflow, Note exists flag → Note exists flag |
| Infor Cloud Salesperson | Commercient Infor Cloud Suite salesperson object | 25 | Salesperson code → Commercient external key (custom field), Salesperson code → Name, Delete indicator → Delete indicator, In workflow → In workflow, Note exists flag → Note exists flag |
| Account | Accounts | 15 | ERP customer number → Commercient AR customer code (Zoho field), ERP customer number → Customer number, Salesperson code → Commercient Infor Cloud salesperson, Commercient terms code → Commercient Infor Cloud term, ERP customer number → Account name |
| Infor Cloud Customer | Commercient Infor Cloud Suite customer object | 103 | ERP customer number → Commercient external key (custom field), the linked account record → Account, the linked terms record → Commercient Infor Cloud Suite terms (related record), the linked salesperson record → Commercient Infor Cloud Suite salesperson (related record), Customer address name → Name |
| Infor Cloud Customer to account lookup | Accounts | 2 | ERP customer number → Commercient AR customer code (Zoho field), the linked Salesforce record → Commercient Infor Cloud customer (related record) |
| Infor Cloud Address | Commercient Infor Cloud Suite shipping address object | 49 | ERP customer number, Customer sequence number → Commercient external key (custom field), name → Name, ERP customer number → Account, ERP customer number → Infor Cloud customer, Delete indicator → Delete indicator |
| Product | Products | 6 | item → Commercient external key (custom field), item → Product name (Zoho field), ERP product code → ERP product code, description → Description, Unit cost → Unit price |
| Infor Cloud Item master | Commercient Infor Cloud Suite item master object | 89 | item → Commercient external key (custom field), item → Name, the linked Salesforce record → Product, Item tax category → Item tax category, Delete indicator → Delete indicator |
| Infor Cloud Item warehouse | Commercient Infor Cloud Suite item warehouse object | 28 | item, Warehouse → Commercient external key (custom field), item, Warehouse → Name, item → Product, item → Commercient Infor Cloud Suite item master (related record), Delete indicator → Delete indicator |
| Infor Cloud Item location | Commercient Infor Cloud Suite item location object | 40 | item, Warehouse, Location → Commercient external key (custom field), item, Warehouse, Location → Name, item → Product, item → Commercient Infor Cloud Suite item master (related record), item, Warehouse → Commercient Infor Cloud Suite item warehouse (related record) |
| Infor Cloud Sales order | Commercient Infor Cloud Suite sales order object | 82 | Customer order number → Commercient external key (custom field), Customer order number → Name, ERP customer number → Account, ERP customer number → Infor Cloud customer, Delete indicator → Delete indicator |
| Infor Cloud Sales order line | Commercient Infor Cloud Suite sales order line object | 68 | Customer order number, Customer order line, Customer order release → Commercient external key (custom field), Customer order number, Customer order line, Customer order release → Name, the linked Salesforce record → Commercient Infor Cloud Suite sales order (related record), the linked Salesforce record → Product, the linked Salesforce record → Commercient Infor Cloud Suite item master (related record) |
| Infor Cloud Invoice | Commercient Infor Cloud Suite invoice object | 11 | Row pointer, ERP customer number → Commercient external key (custom field), Invoice number → Name, ERP customer number → Account, ERP customer number → Infor Cloud customer, Invoice date → Invoice date (custom field) |
| Contact | Contacts | 14 | Contact identifier → Commercient external key column, the linked Salesforce record → Account name, ERP last name → Last name, ERP first name → First name, email → Email |

## 6. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-zohocrm/erps/infor-cloudsuite`.

## 7. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Infor CloudSuite → Zoho CRM templates set up. dlake-crmpro-zohocrm is the destination
skill this page sits under: its own text is the authority for the Zoho CRM conventions that hold
across every ERP, and its ERP table lists this page alongside every sibling ERP page for this
destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-zohocrm/erps/infor-cloudsuite`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
