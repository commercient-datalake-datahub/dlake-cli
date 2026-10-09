---
name: dlake-crmpro-mdc/erps/epicor-eagle
kind: erp-summary
description: >-
  Use it when standing up or reading an Epicor Eagle → MDC template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-mdc, the destination skill this page is a child of, which carries the
  MDC conventions that hold across every ERP.
---
# CRMPro → MDC — Epicor Eagle: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-mdc/erps/epicor-eagle` (or `list_skills`) against the
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
| **CRM Price Book** | The templates push price level to MDC. | price level | — |
| **CRM Product** | The templates push product to MDC. | product | inventory records |
| **CRM sales order** | The templates push sales order to MDC. | sales order | customer shipping addresses, point of sale transaction headers, customers |
| **CRM sales order detail** | The templates push sales order detail to MDC. | sales order detail | customer shipping addresses, point of sale transaction lines, point of sale transaction headers |
| **CRM invoice** | The templates push invoice to MDC. | invoice | customer shipping addresses, point of sale transaction headers, customers |
| **CRM invoice detail** | The templates push invoice detail to MDC. | invoice detail | customer shipping addresses, point of sale transaction lines, point of sale transaction headers |
| **Epicor Eagle Sales order** | The templates push Commercient Epicor Eagle sales order object to MDC. | Commercient Epicor Eagle sales order object | customer shipping addresses, point of sale transaction headers, customers |
| **Epicor Eagle Sales order Detail** | The templates push Commercient Epicor Eagle sales order detail object to MDC. | Commercient Epicor Eagle sales order detail object | customer shipping addresses, point of sale transaction lines, point of sale transaction headers |
| **Epicor Eagle Invoice** | The templates push Commercient Epicor Eagle invoice object to MDC. | Commercient Epicor Eagle invoice object | customer shipping addresses, point of sale transaction headers, customers |
| **Epicor Eagle Invoice Detail** | The templates push Commercient Epicor Eagle invoice detail object to MDC. | Commercient Epicor Eagle invoice detail object | customer shipping addresses, point of sale transaction lines, point of sale transaction headers |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| CRM Unit of measure schedule | unit of measure schedule | name | 10 |
| CRM unit of measure | uom | name | 10 |
| CRM Price Book | price level | Commercient external key | 10 |
| CRM Product | product | Commercient external key | 11 |
| CRM sales order | sales order | Commercient external key | 24 |
| CRM sales order detail | sales order detail | Commercient external key | 25 |
| CRM invoice | invoice | Commercient external key | 26 |
| CRM invoice detail | invoice detail | Commercient external key | 27 |
| Epicor Eagle Sales order | Commercient Epicor Eagle sales order object | Commercient external key | 28 |
| Epicor Eagle Sales order Detail | Commercient Epicor Eagle sales order detail object | Commercient external key | 29 |
| Epicor Eagle Invoice | Commercient Epicor Eagle invoice object | Commercient external key | 30 |
| Epicor Eagle Invoice Detail | Commercient Epicor Eagle invoice detail object | Commercient external key | 31 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| unit of measure schedule feed | — | — |
| unit of measure feed | — | — |
| price level feed | — | — |
| product feed | insert + update | inventory records |
| sales order feed (standard objects) | insert + update | customer shipping addresses, point of sale transaction headers, customers |
| sales order line feed (standard objects) | insert + update | customer shipping addresses, point of sale transaction lines, point of sale transaction headers |
| invoice feed (standard objects) | insert + update | customer shipping addresses, point of sale transaction headers, customers |
| invoice line feed (standard objects) | insert + update | customer shipping addresses, point of sale transaction lines, point of sale transaction headers |
| sales order feed (Commercient objects) | insert + update | customer shipping addresses, point of sale transaction headers, customers |
| sales order line feed (Commercient objects) | insert + update | customer shipping addresses, point of sale transaction lines, point of sale transaction headers |
| invoice feed (Commercient objects) | insert + update | customer shipping addresses, point of sale transaction headers, customers |
| invoice line feed (Commercient objects) | insert + update | customer shipping addresses, point of sale transaction lines, point of sale transaction headers |

## 4. Order of work

The templates set run sequence from 10 to 31. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 10 — CRM Unit of measure schedule, CRM unit of measure, CRM Price Book
- 11 — CRM Product
- 24 — CRM sales order
- 25 — CRM sales order detail
- 26 — CRM invoice
- 27 — CRM invoice detail
- 28 — Epicor Eagle Sales order
- 29 — Epicor Eagle Sales order Detail
- 30 — Epicor Eagle Invoice
- 31 — Epicor Eagle Invoice Detail

Several of these rows share a run sequence value: each template group carries its own numbering, so
give the imported processes an order across the whole set rather than taking the template values as
one.

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- product feed reads CRM department sync output (generic name), CRM class sync output (generic
  name), CRM unit of measure schedule sync output (generic name), CRM unit of measure sync output
  (generic name), store sync output, CRM product sync output (Epicor Eagle); no template in this set
  writes CRM department sync output (generic name), CRM class sync output (generic name), CRM unit
  of measure schedule sync output (generic name), CRM unit of measure sync output (generic name)
- sales order feed (standard objects) reads CRM account sync output (generic name), customer sync
  output, sales order sync output (standard objects); no template in this set writes CRM account
  sync output (generic name), customer sync output, sales order sync output (standard objects)
- sales order line feed (standard objects) reads sales order sync output (standard objects), CRM
  product sync output (Epicor Eagle), sales order line sync output (standard objects); no template
  in this set writes sales order sync output (standard objects), CRM product sync output (Epicor
  Eagle), sales order line sync output (standard objects)
- invoice feed (standard objects) reads CRM account sync output (generic name), customer sync
  output, invoice sync output (standard objects); no template in this set writes CRM account sync
  output (generic name), customer sync output, invoice sync output (standard objects)
- invoice line feed (standard objects) reads invoice sync output (standard objects), CRM product
  sync output (Epicor Eagle), invoice line sync output (standard objects); no template in this set
  writes invoice sync output (standard objects), CRM product sync output (Epicor Eagle), invoice
  line sync output (standard objects)
- sales order feed (Commercient objects) reads CRM account sync output (generic name), customer sync
  output, sales order sync output (Commercient objects); no template in this set writes CRM account
  sync output (generic name), customer sync output, sales order sync output (Commercient objects)
- sales order line feed (Commercient objects) reads sales order sync output (Commercient objects),
  CRM product sync output (Epicor Eagle), sales order line sync output (Commercient objects); no
  template in this set writes sales order sync output (Commercient objects), CRM product sync output
  (Epicor Eagle), sales order line sync output (Commercient objects)
- invoice feed (Commercient objects) reads CRM account sync output (generic name), customer sync
  output, invoice sync output (Commercient objects); no template in this set writes CRM account sync
  output (generic name), customer sync output, invoice sync output (Commercient objects)
- invoice line feed (Commercient objects) reads invoice sync output (Commercient objects), CRM
  product sync output (Epicor Eagle), invoice line sync output (Commercient objects); no template in
  this set writes invoice sync output (Commercient objects), CRM product sync output (Epicor Eagle),
  invoice line sync output (Commercient objects)

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → MDC pairs |
|---|---|---|---|
| CRM Unit of measure schedule | unit of measure schedule | 2 | Name → Name, Base unit name → Base unit name |
| CRM unit of measure | uom | 2 | Name → Name, unit of measure schedule lookup → unit of measure schedule lookup |
| CRM Price Book | price level | 5 | Commercient external key → Commercient external key, name → name, Begin date → Begin date, Description → Description, Row timestamp → Row timestamp |
| CRM Product | product | 10 | Description → Name, Stock keeping unit → Product number, Stock keeping unit → Commercient external key (product custom field), the linked Salesforce record → Price level lookup (custom field), the linked Salesforce record → Department lookup (custom field) |
| CRM sales order | sales order | 20 | Document number (Epicor Eagle), Store → Commercient external key, → Name, Address 1 → Bill to street 1, Address 2 → Bill to street 2, City → Bill to city |
| CRM sales order detail | sales order detail | 13 | Document number (Epicor Eagle), Source line number, Store, Tally flag → Commercient external key, Eagle ship to address line 1 → Ship to street 1, Eagle ship to address line 2 → Ship to street 2, Eagle ship to city → Ship to city, Eagle ship to state → Ship to state or province |
| CRM invoice | invoice | 21 | Address 1 → Bill to street 1, Address 2 → Bill to street 2, City → Bill to city, State → Bill to state or province, Zip code → Bill to postal code |
| CRM invoice detail | invoice detail | 13 | Document number (Epicor Eagle), Source line number, Store, Tally flag → Commercient external key, Eagle ship to address line 1 → Ship to street 1, Eagle ship to address line 2 → Ship to street 2, Eagle ship to city → Ship to city, Eagle ship to state → Ship to state or province |
| Epicor Eagle Sales order | Commercient Epicor Eagle sales order object | 2 | Document number (Epicor Eagle), Store → external key property, → Name |
| Epicor Eagle Sales order Detail | Commercient Epicor Eagle sales order detail object | 2 | Document number (Epicor Eagle), Source line number, Store, Tally flag → Commercient external key |
| Epicor Eagle Invoice | Commercient Epicor Eagle invoice object | 5 | Document number (Epicor Eagle), Store → external key property, → Name, Customer, Job number → customer account lookup, Price book reference → price list lookup |
| Epicor Eagle Invoice Detail | Commercient Epicor Eagle invoice detail object | 3 | Document number (Epicor Eagle), Source line number, Store, Tally flag → Commercient external key, the linked Salesforce record → Commercient Epicor Eagle invoice lookup, the linked Salesforce record → product lookup |

## 6. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-mdc/erps/epicor-eagle`.

## 7. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Epicor Eagle → MDC templates set up. dlake-crmpro-mdc is the destination skill this page
sits under: its own text is the authority for the MDC conventions that hold across every ERP, and
its ERP table lists this page alongside every sibling ERP page for this destination. For the extract
leg that fills the source data, see dlake-normalsync; for the on-premises agent that runs it,
dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration up,
dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-mdc/erps/epicor-eagle`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
