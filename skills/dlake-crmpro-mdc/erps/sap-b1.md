---
name: dlake-crmpro-mdc/erps/sap-b1
kind: erp-summary
description: >-
  Use it when standing up or reading a SAP Business One → MDC template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-mdc, the destination skill this page is a child of, which carries the
  MDC conventions that hold across every ERP.
---
# CRMPro → MDC — SAP Business One: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-mdc/erps/sap-b1` (or `list_skills`) against the Commercient
admin plane. Existing customers who need access or help: contact support@commercient.com. New
customers: contact sales@commercient.com to become a customer and be whitelisted.

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
| **CRM Price level** | The templates push price level to MDC. | price level | item prices |
| **CRM Product** | The templates push product to MDC. | product | items |
| **CRM sales order** | The templates push sales order to MDC. | sales order | sales order lines, e, item prices, sales orders, business partners |
| **CRM sales order detail** | The templates push sales order detail to MDC. | sales order detail | sales order lines, sales orders, business partners, business partner addresses |
| **CRM invoice** | The templates push invoice to MDC. | invoice | invoice items, e, stock items, invoices, sales ledger customers |
| **CRM invoice detail** | The templates push invoice detail to MDC. | invoice detail | invoice lines, AR invoice documents, business partners, business partner addresses |
| **CRM quote detail** | The templates push quote detail to MDC. | quote detail | quotation lines, sales quotations, business partners, business partner addresses |
| **CRM Quote and Line** | The templates push quote to MDC. | quote | quotation lines, e, item prices, sales quotations, business partners |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| CRM Unit of measure schedule | unit of measure schedule | name | 20 |
| CRM unit of measure | uom | name | 21 |
| CRM Price level | price level | Commercient external key | 22 |
| CRM Product | product | Commercient external key | 23 |
| CRM sales order | sales order | Commercient external key | 24 |
| CRM sales order detail | sales order detail | Commercient external key | 25 |
| CRM invoice | invoice | Commercient external key | 26 |
| CRM invoice detail | invoice detail | Commercient external key | 27 |
| CRM quote | quote | Commercient external key | 28 |
| CRM quote detail | quote detail | Commercient external key | 29 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| CRM unit of measure schedule feed | — | — |
| CRM unit of measure feed | — | — |
| price level feed | — | item prices |
| product feed | insert + update | items |
| sales order feed (standard objects) | insert + update | sales order lines, e, item prices, sales orders |
| sales order line feed (standard objects) | insert + update | sales order lines, sales orders, business partners, business partner addresses |
| Sage 50 UK invoice feed (standard objects) | insert + update | invoice items, e, stock items, invoices |
| invoice line feed (standard objects) | insert + update | invoice lines, AR invoice documents, business partners, business partner addresses |
| quote feed (standard objects) | insert only | quotation lines, e, item prices, sales quotations |
| quote line feed (standard objects) | insert only | quotation lines, sales quotations, business partners, business partner addresses |

## 4. Order of work

The templates set run sequence from 20 to 29. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 20 — CRM Unit of measure schedule
- 21 — CRM unit of measure
- 22 — CRM Price level
- 23 — CRM Product
- 24 — CRM sales order
- 25 — CRM sales order detail
- 26 — CRM invoice
- 27 — CRM invoice detail
- 28 — CRM quote
- 29 — CRM quote detail

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
- Sage 50 UK invoice feed (standard objects) reads CRM price book sync output (generic name), CRM
  account sync output (generic name), Sage 50 UK customer sync output, Sage 50 UK invoice sync
  output (standard objects); no template in this set writes CRM price book sync output (generic
  name), CRM account sync output (generic name), Sage 50 UK customer sync output, Sage 50 UK invoice
  sync output (standard objects)
- invoice line feed (standard objects) reads invoice sync output (standard objects), product sync
  output, invoice line sync output (standard objects); no template in this set writes invoice sync
  output (standard objects), product sync output, invoice line sync output (standard objects)
- quote line feed (standard objects) reads quote sync output (standard objects), product sync
  output, quote line sync output (standard objects); no template in this set writes quote sync
  output (standard objects), product sync output, quote line sync output (standard objects)
- quote feed (standard objects) reads CRM price book sync output (generic name), CRM account sync
  output (generic name), customer sync output, quote sync output (standard objects); no template in
  this set writes CRM price book sync output (generic name), CRM account sync output (generic name),
  customer sync output, quote sync output (standard objects)

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → MDC pairs |
|---|---|---|---|
| CRM Unit of measure schedule | unit of measure schedule | 2 | name → name, Base unit name → Base unit name |
| CRM unit of measure | uom | 2 | name → name, unit of measure schedule lookup → unit of measure schedule lookup |
| CRM Price level | price level | 3 | Item code,Price list → Commercient external key, Item code,Price list → Name, Item code,Price list → Description |
| CRM Product | product | 6 | Item name → name, Item code → Product number, Item code → Commercient external key, Long description (user field) → description, the linked default unit of measure schedule → default unit of measure schedule lookup |
| CRM sales order | sales order | 18 | returned document entry → Commercient external key, document number → Name, Street → Bill to street 1, City → Bill to city, State → Bill to state or province |
| CRM sales order detail | sales order detail | 13 | returned document entry,ERP line number → Commercient external key, document number,ERP line number → name, Street → Ship to street 1, City → Ship to city, State → Ship to state or province |
| CRM invoice | invoice | 22 | Address 1 → Bill to street 1, Address 2 → Bill to street 2, Address 3 → Bill to city, Address line 4 → Bill to state or province, Address line 5 → Bill to postal code |
| CRM invoice detail | invoice detail | 13 | returned document entry,ERP line number → Commercient external key, document number,ERP line number → Name, Street → Ship to street 1, City → Ship to city, State → Ship to state or province |
| CRM quote | quote | 18 | returned document entry → Commercient external key, document number → Name, Street → Bill to street 1, City → Bill to city, State → Bill to state or province |
| CRM quote detail | quote detail | 13 | returned document entry,ERP line number → Commercient external key, document number,ERP line number → Name, Street → Ship to street 1, City → Ship to city, State → Ship to state or province |

## 6. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-mdc/erps/sap-b1`.

## 7. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped SAP Business One → MDC templates set up. dlake-crmpro-mdc is the destination skill this
page sits under: its own text is the authority for the MDC conventions that hold across every ERP,
and its ERP table lists this page alongside every sibling ERP page for this destination. For the
extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that runs
it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration up,
dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-mdc/erps/sap-b1`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
