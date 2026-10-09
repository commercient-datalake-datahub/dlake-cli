---
name: dlake-crmpro-mdc/erps/aptean-made2manage
kind: erp-summary
description: >-
  Use it when standing up or reading an Aptean Made2Manage → MDC template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-mdc, the destination skill this page is a child of, which carries the
  MDC conventions that hold across every ERP.
---
# CRMPro → MDC — Aptean Made2Manage: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-mdc/erps/aptean-made2manage` (or `list_skills`) against the
Commercient admin plane. Existing customers who need access or help: contact
support@commercient.com. New customers: contact sales@commercient.com to become a customer and be
whitelisted.

dlake-crmpro is the parent skill and the authority for everything general: the CRMPro tools, process
configuration and field list, the sync history, how source data is selected, and what a run that
finds nothing does. Read it first; this page does not repeat it. dlake-crmpro-mdc is the destination
skill this page is a child of, and the authority for the MDC conventions that hold across every ERP:
read it first, then come back here for what this source's own templates set. This page grows as the
catalogue does.

## 1. What the templates deliver

| Group | Business outcome | Objects | Source tables and views |
|---|---|---|---|
| **CRM Unit of measure schedule** | The templates push unit of measure schedule to MDC. | unit of measure schedule | — |
| **CRM unit of measure** | The templates push uom to MDC. | uom | — |
| **CRM Price level** | The templates push price level to MDC. | price level | inventory master items |
| **CRM Product** | The templates push product to MDC. | product | inventory master extensions |
| **CRM sales order** | The templates push sales order to MDC. | sales order | sales order items, e, inventory master items, sales order headers, customer extensions |
| **CRM sales order detail** | The templates push sales order detail to MDC. | sales order detail | sales order items, sales order headers, customer extensions, inventory master items |
| **CRM invoice** | The templates push invoice to MDC. | invoice | AR invoice lines, e, inventory master items, AR invoice headers, customer extensions |
| **CRM invoice detail** | The templates push invoice detail to MDC. | invoice detail | AR invoice lines, AR invoice headers, customer extensions, inventory master items |
| **Aptean Sales Person** | The templates push Commercient Aptean salesperson object to MDC. | Commercient Aptean salesperson object | salespeople |
| **CRM account** | The templates push account to MDC. | account | customer extensions, salespeople |
| **Aptean customer** | The templates push Commercient Aptean customer object to MDC. | Commercient Aptean customer object | customer extensions |
| **CRM Contact** | The templates push contact to MDC. | contact | customer extensions, phone numbers |
| **Aptean Contact** | The templates push Commercient Aptean contact object to MDC. | Commercient Aptean contact object | customer extensions, phone numbers |
| **Aptean Address** | The templates push Commercient Aptean address object to MDC. | Commercient Aptean address object | addresses, customer extensions |
| **Aptean Sales order Header** | The templates push Commercient Aptean sales order header object to MDC. | Commercient Aptean sales order header object | sales order headers, phone numbers, customer extensions |
| **Aptean Sales order Detail** | The templates push Commercient Aptean sales order detail object to MDC. | Commercient Aptean sales order detail object | sales order items, sales order headers, inventory master items |
| **Aptean Invoice Header** | The templates push Commercient Aptean invoice header object to MDC. | Commercient Aptean invoice header object | AR invoice headers, customer extensions |
| **Aptean Invoice Detail** | The templates push Commercient Aptean invoice detail object to MDC. | Commercient Aptean invoice detail object | AR invoice lines, AR invoice headers, inventory master items |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Aptean Sales Person | Commercient Aptean salesperson object | Commercient external key | 14 |
| CRM Account | account | Commercient AR customer code (Dynamics) | 15 |
| Aptean customer | Commercient Aptean customer object | Commercient external key | 16 |
| CRM Contact | contact | Commercient external key | 17 |
| Aptean Contact | Commercient Aptean contact object | Commercient external key | 18 |
| Aptean Address | Commercient Aptean address object | Commercient external key | 19 |
| CRM Unit of measure schedule | unit of measure schedule | name | 20 |
| CRM unit of measure | uom | name | 21 |
| CRM Price level | price level | Commercient external key | 22 |
| CRM Product | product | Commercient external key | 23 |
| CRM sales order | sales order | Commercient external key | 24 |
| CRM sales order detail | sales order detail | Commercient external key | 25 |
| CRM invoice | invoice | Commercient external key | 26 |
| CRM invoice detail | invoice detail | Commercient external key | 27 |
| Aptean Sales order Header | Commercient Aptean sales order header object | Commercient external key | 28 |
| Aptean Sales order Detail | Commercient Aptean sales order detail object | Commercient external key | 29 |
| Aptean Invoice Header | Commercient Aptean invoice header object | Commercient external key | 30 |
| Aptean Invoice Detail | Commercient Aptean invoice detail object | Commercient external key | 31 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| salesperson feed | insert + update | salespeople |
| CRM account feed (generic name) | insert only | customer extensions, salespeople |
| customer feed | insert + update | customer extensions |
| CRM contact feed | insert + update | customer extensions, phone numbers |
| contact feed | insert + update | customer extensions, phone numbers |
| address feed | insert + update | addresses, customer extensions |
| CRM unit of measure schedule feed | — | — |
| CRM unit of measure feed | — | — |
| price level feed | — | inventory master items |
| product feed | insert + update | inventory master extensions |
| sales order feed (standard objects) | insert + update | sales order items, e, inventory master items, sales order headers, customer extensions |
| sales order line feed (standard objects) | insert + update | sales order items, sales order headers, customer extensions, inventory master items |
| invoice feed (standard objects) | insert + update | AR invoice lines, e, inventory master items, AR invoice headers |
| invoice line feed (standard objects) | insert + update | AR invoice lines, AR invoice headers, customer extensions, inventory master items |
| sales order feed (Commercient objects) | insert + update | sales order headers, phone numbers, customer extensions |
| sales order line feed (Commercient objects) | insert + update | sales order items, sales order headers, inventory master items |
| invoice feed (Commercient objects) | insert + update | AR invoice headers, customer extensions |
| invoice line feed (Commercient objects) | insert + update | AR invoice lines, AR invoice headers, inventory master items |

## 4. Order of work

The templates set run sequence from 14 to 31. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 14 — Aptean Sales Person
- 15 — CRM Account
- 16 — Aptean customer
- 17 — CRM Contact
- 18 — Aptean Contact
- 19 — Aptean Address
- 20 — CRM Unit of measure schedule
- 21 — CRM unit of measure
- 22 — CRM Price level
- 23 — CRM Product
- 24 — CRM sales order
- 25 — CRM sales order detail
- 26 — CRM invoice
- 27 — CRM invoice detail
- 28 — Aptean Sales order Header
- 29 — Aptean Sales order Detail
- 30 — Aptean Invoice Header
- 31 — Aptean Invoice Detail

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- price level feed reads CRM price book sync output (generic name); no template in this set writes
  CRM price book sync output (generic name)
- product feed reads product sync output; no template in this set writes product sync output
- sales order feed (standard objects) reads CRM price book sync output (generic name), CRM account
  sync output (generic name), customer sync output, sales order sync output (standard objects); no
  template in this set writes CRM price book sync output (generic name), CRM account sync output
  (generic name), customer sync output, sales order sync output (standard objects)
- sales order line feed (standard objects) reads sales order sync output (standard objects), product
  sync output, sales order line sync output (standard objects); no template in this set writes sales
  order sync output (standard objects), product sync output, sales order line sync output (standard
  objects)
- invoice feed (standard objects) reads CRM price book sync output (generic name), CRM account sync
  output (generic name), customer sync output, invoice sync output (standard objects); no template
  in this set writes CRM price book sync output (generic name), CRM account sync output (generic
  name), customer sync output, invoice sync output (standard objects)
- invoice line feed (standard objects) reads invoice sync output (standard objects), product sync
  output, invoice line sync output (standard objects); no template in this set writes invoice sync
  output (standard objects), product sync output, invoice line sync output (standard objects)
- salesperson feed reads salesperson sync output; no template in this set writes salesperson sync
  output
- CRM account feed (generic name) reads salesperson sync output, CRM account sync output (generic
  name); no template in this set writes salesperson sync output, CRM account sync output (generic
  name)
- customer feed reads CRM account sync output (generic name), customer sync output; no template in
  this set writes CRM account sync output (generic name), customer sync output
- CRM contact feed reads CRM account sync output (generic name), CRM contact sync output; no
  template in this set writes CRM account sync output (generic name), CRM contact sync output
- contact feed reads CRM account sync output (generic name), CRM contact sync output, contact sync
  output; no template in this set writes CRM account sync output (generic name), CRM contact sync
  output, contact sync output
- address feed reads customer sync output, address tracker sync output, address sync output; no
  template in this set writes customer sync output, address tracker sync output, address sync output
- sales order feed (Commercient objects) reads customer sync output, CRM account sync output
  (generic name), sales order sync output (Commercient objects); no template in this set writes
  customer sync output, CRM account sync output (generic name), sales order sync output (Commercient
  objects)
- sales order line feed (Commercient objects) reads sales order sync output (Commercient objects),
  item sync output, sales order line sync output (Commercient objects); no template in this set
  writes sales order sync output (Commercient objects), item sync output, sales order line sync
  output (Commercient objects)
- invoice feed (Commercient objects) reads customer sync output, CRM account sync output (generic
  name), invoice sync output (Commercient objects); no template in this set writes customer sync
  output, CRM account sync output (generic name), invoice sync output (Commercient objects)
- invoice line feed (Commercient objects) reads invoice sync output (Commercient objects), item sync
  output, invoice line sync output (Commercient objects); no template in this set writes invoice
  sync output (Commercient objects), item sync output, invoice line sync output (Commercient
  objects)

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → MDC pairs |
|---|---|---|---|
| Aptean Sales Person | Commercient Aptean salesperson object | 23 | → external key column, →, Salesperson last name → name, Salesperson last name → Salesperson last name, Salesperson code → Salesperson code |
| CRM Account | account | 11 | → Commercient AR customer code (Dynamics), Aptean company name → name, ERP phone → Telephone 1, Street address → Address 1 street 1, Fax → Fax |
| Aptean customer | Commercient Aptean customer object | 86 | → external key column, Aptean customer number → Aptean customer number, Aptean company name → name, Aptean company name → Aptean company name, Aptean city → Aptean city |
| CRM Contact | contact | 17 | → Commercient external key, Contact first name → first name property, Contact person name → last name property, Contact first name, Contact person name → Full name, Salutation → suffix |
| Aptean Contact | Commercient Aptean contact object | 3 | → Commercient external key, the linked Salesforce record → Commercient account lookup, the linked Salesforce record → Commercient contact lookup |
| Aptean Address | Commercient Aptean address object | 34 | → Commercient external key, Address company name → name, Long distance → Long distance, Address key → Address key, Address type → Address type |
| CRM Unit of measure schedule | unit of measure schedule | 2 | name → name, Base unit name → Base unit name |
| CRM unit of measure | uom | 2 | name → name, unit of measure schedule lookup → unit of measure schedule lookup |
| CRM Price level | price level | 3 | → Commercient external key, Aptean part number → name, Aptean item description → description |
| CRM Product | product | 6 | Aptean item description → name, Aptean part number → Product number, → Commercient external key, Aptean item description → description, the linked default unit of measure schedule → default unit of measure schedule lookup |
| CRM sales order | sales order | 11 | → Commercient external key, Aptean sales order number → Name, Street address → Bill to street 1, Aptean city → Bill to city, Aptean state → Bill to state or province |
| CRM sales order detail | sales order detail | 13 | → Commercient external key, Street address → Ship to street 1, Aptean city → Ship to city, Aptean state → Ship to state or province, Aptean postal code → Ship to postal code |
| CRM invoice | invoice | 13 | Street address → Bill to street 1, Aptean city → Bill to city, Aptean state → Bill to state or province, Aptean postal code → Bill to postal code, Aptean country → Bill to country |
| CRM invoice detail | invoice detail | 12 | → Commercient external key, Street address → Ship to street 1, Aptean city → Ship to city, Aptean state → Ship to state or province, Aptean postal code → Ship to postal code |
| Aptean Sales order Header | Commercient Aptean sales order header object | 88 | → external key column, Aptean sales order number → Name, Aptean sales order number → Sales order number (custom field), Aptean customer number → Customer number (custom field), Aptean company name → Company (custom field) |
| Aptean Sales order Detail | Commercient Aptean sales order detail object | 83 | → Commercient external key (earlier package), Order item number → Order item number, Aptean part number → Aptean part number, Part revision → Part revision, Aptean sales order number → Aptean sales order number |
| Aptean Invoice Header | Commercient Aptean invoice header object | 71 | → external key column, Aptean bill to city → Billing city, Aptean bill to company → Billing company, Aptean bill to country → Billing country, Aptean bill to state → Billing state |
| Aptean Invoice Detail | Commercient Aptean invoice detail object | 61 | → external key column, Aptean invoice number, ERP user line → Name, Back order quantity → Back order quantity, Aptean invoice number → Aptean invoice number, Cost → Cost |

## 6. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-mdc/erps/aptean-made2manage`.

## 7. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Aptean Made2Manage → MDC templates set up. dlake-crmpro-mdc is the destination skill
this page sits under: its own text is the authority for the MDC conventions that hold across every
ERP, and its ERP table lists this page alongside every sibling ERP page for this destination. For
the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-mdc/erps/aptean-made2manage`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
