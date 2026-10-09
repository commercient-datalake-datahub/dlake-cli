---
name: dlake-crmpro-mdc/erps/infor-syteline-v9
kind: erp-summary
description: >-
  Use it when standing up or reading an Infor SyteLine version 9 → MDC template set, when deciding
  which templates to import and activate, or when a run completes without pushing records and the
  answer is in the view or the configuration row. It extends dlake-crmpro, which covers operating
  CRMPro generally, and dlake-crmpro-mdc, the destination skill this page is a child of, which
  carries the MDC conventions that hold across every ERP.
---
# CRMPro → MDC — Infor SyteLine version 9: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-mdc/erps/infor-syteline-v9` (or `list_skills`) against the
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
| **CRM unit of measure** | The templates push uom to MDC. | uom | items |
| **CRM Price level** | The templates push price level to MDC. | price level | item prices |
| **CRM Product** | The templates push product to MDC. | product | record notes, specific notes, items |
| **CRM sales order** | The templates push sales order to MDC. | sales order | customer order lines, e, item prices, customer orders, customers |
| **CRM sales order detail** | The templates push sales order detail to MDC. | sales order detail | customer order lines, customer addresses |
| **CRM invoice** | The templates push invoice to MDC. | invoice | invoice lines, e, item prices, invoice headers, customers |
| **CRM invoice detail** | The templates push invoice detail to MDC. | invoice detail | invoice lines, invoice headers, customer addresses |
| **CRM Account** | The templates push account to MDC. | account | customers, terms, customer addresses |
| **Infor SyteLine version 9 Customer** | The templates push Commercient SyteLine customer object to MDC. | Commercient SyteLine customer object | customers, customer addresses |
| **Infor SyteLine version 9 Address** | The templates push Commercient SyteLine address object to MDC. | Commercient SyteLine address object | customer addresses, customers |
| **Infor SyteLine version 9 Sales order** | The templates push Commercient SyteLine sales order object to MDC. | Commercient SyteLine sales order object | customer orders |
| **Infor SyteLine version 9 Sales order line** | The templates push Commercient SyteLine sales order line object to MDC. | Commercient SyteLine sales order line object | customer order lines |
| **Infor SyteLine version 9 Invoice** | The templates push Commercient SyteLine invoice object to MDC. | Commercient SyteLine invoice object | invoice headers |
| **Infor SyteLine version 9 Invoice line item** | The templates push Commercient SyteLine invoice line item object to MDC. | Commercient SyteLine invoice line item object | invoice lines |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| CRM Account | account | Commercient AR customer code (Dynamics) | 5 |
| Infor SyteLine version 9 Customer | Commercient SyteLine customer object | Commercient external key | 6 |
| Infor SyteLine version 9 Address | Commercient SyteLine address object | Commercient external key | 7 |
| CRM Unit of measure schedule | unit of measure schedule | name | 8 |
| CRM unit of measure | uom | name | 9 |
| CRM Price level | price level | Commercient external key | 22 |
| CRM Product | product | Commercient external key | 23 |
| CRM sales order | sales order | Commercient external key | 24 |
| CRM sales order detail | sales order detail | Commercient external key | 25 |
| CRM invoice | invoice | Commercient external key | 26 |
| CRM invoice detail | invoice detail | Commercient external key | 27 |
| Infor SyteLine version 9 Sales order | Commercient SyteLine sales order object | Commercient external key | 28 |
| Infor SyteLine version 9 Sales order line | Commercient SyteLine sales order line object | Commercient external key | 29 |
| Infor SyteLine version 9 Invoice | Commercient SyteLine invoice object | Commercient external key | 30 |
| Infor SyteLine version 9 Invoice line item | Commercient SyteLine invoice line item object | Commercient external key | 31 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| CRM account feed (generic name) | insert + update | customers, terms, customer addresses |
| customer feed | insert + update | customers, customer addresses |
| address feed | insert + update | customer addresses, customers |
| unit of measure schedule feed | — | — |
| unit of measure feed | — | items |
| price level feed | — | item prices |
| product feed | insert + update | record notes, specific notes, items |
| sales order feed (standard objects) | insert + update | customer order lines, e, item prices, customer orders |
| sales order line feed (standard objects) | insert + update | customer order lines, customer addresses |
| invoice feed (standard objects) | insert + update | invoice lines, e, item prices, invoice headers |
| invoice line feed (standard objects) | insert + update | invoice lines, invoice headers, customer addresses |
| sales order feed (Commercient objects) | insert + update | customer orders |
| sales order line feed (Commercient objects) | insert + update | customer order lines |
| invoice feed (Commercient objects) | insert + update | invoice headers |
| invoice line item feed | insert + update | invoice lines |

## 4. Order of work

The templates set run sequence from 5 to 31. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 5 — CRM Account
- 6 — Infor SyteLine version 9 Customer
- 7 — Infor SyteLine version 9 Address
- 8 — CRM Unit of measure schedule
- 9 — CRM unit of measure
- 22 — CRM Price level
- 23 — CRM Product
- 24 — CRM sales order
- 25 — CRM sales order detail
- 26 — CRM invoice
- 27 — CRM invoice detail
- 28 — Infor SyteLine version 9 Sales order
- 29 — Infor SyteLine version 9 Sales order line
- 30 — Infor SyteLine version 9 Invoice
- 31 — Infor SyteLine version 9 Invoice line item

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- unit of measure feed reads unit of measure; no template in this set writes unit of measure
- price level feed reads CRM price book sync output (generic name); no template in this set writes
  CRM price book sync output (generic name)
- product feed reads uom: CRM product sync output (generic name); no template in this set writes
  uom: CRM product sync output (generic name)
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
- CRM account feed (generic name) reads user sync output (generic name), CRM account sync output
  (generic name); no template in this set writes user sync output (generic name), CRM account sync
  output (generic name)
- customer feed reads CRM account sync output (generic name), customer sync output; no template in
  this set writes CRM account sync output (generic name), customer sync output
- address feed reads CRM account sync output (generic name), customer sync output, address sync
  output; no template in this set writes CRM account sync output (generic name), customer sync
  output, address sync output
- sales order feed (Commercient objects) reads CRM account sync output (generic name), customer sync
  output, sales order sync output (Commercient objects); no template in this set writes CRM account
  sync output (generic name), customer sync output, sales order sync output (Commercient objects)
- sales order line feed (Commercient objects) reads sales order sync output (Commercient objects),
  item sync output, sales order line sync output (Commercient objects); no template in this set
  writes sales order sync output (Commercient objects), item sync output, sales order line sync
  output (Commercient objects)
- invoice feed (Commercient objects) reads CRM account sync output (generic name), customer sync
  output, invoice sync output (Commercient objects); no template in this set writes CRM account sync
  output (generic name), customer sync output, invoice sync output (Commercient objects)
- invoice line item feed reads invoice sync output (Commercient objects), item sync output, invoice
  line item sync output; no template in this set writes invoice sync output (Commercient objects),
  item sync output, invoice line item sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → MDC pairs |
|---|---|---|---|
| CRM Account | account | 19 | ERP customer number → Commercient AR customer code (Dynamics), ERP customer number → Account number, name → Name, city property → Address 1 city, state → Address 1 state or province |
| Infor SyteLine version 9 Customer | Commercient SyteLine customer object | 149 | Site reference, ERP customer number, Customer sequence number → external key property, name → Name, Site reference → Site reference, ERP customer number → ERP customer number, Customer sequence number → Customer sequence number |
| Infor SyteLine version 9 Address | Commercient SyteLine address object | 48 | Site reference, ERP customer number, Customer sequence number → external key property, name, Customer sequence number → Name, Site reference → Site reference, ERP customer number → ERP customer number, Customer sequence number → Customer sequence number |
| CRM Unit of measure schedule | unit of measure schedule | 2 | name → name, Base unit name → Base unit name |
| CRM unit of measure | uom | 2 | Unit of measure code → Name, the linked Salesforce record → unit of measure schedule lookup |
| CRM Price level | price level | 3 | item, Currency code, Effective date, Site reference → Commercient external key, item, Currency code, Effective date, Site reference → Name, item, Currency code, Effective date, Site reference → Description |
| CRM Product | product | 12 | Site reference, item → Commercient external key, description, Site reference, item → Name, Site reference, item → Product number, the linked unit of measure schedule → default unit of measure schedule lookup, the linked unit of measure, the linked default unit of measure → default unit of measure lookup |
| CRM sales order | sales order | 20 | Site reference, Customer order number → Commercient external key, Site reference, Customer order number → Name, city property → Bill to city, state → Bill to state or province, postal code property → Bill to postal code |
| CRM sales order detail | sales order detail | 15 | Site reference, Customer order number, Customer order line, Customer order release → Commercient external key, name → name, city property → Ship to city, state → Ship to state or province, postal code property → Ship to postal code |
| CRM invoice | invoice | 20 | city property → Bill to city, state → Bill to state or province, postal code property → Bill to postal code, country → Bill to country, name → Bill to name |
| CRM invoice detail | invoice detail | 13 | name → name, city property → Ship to city, state → Ship to state or province, postal code property → Ship to postal code, country → Ship to country |
| Infor SyteLine version 9 Sales order | Commercient SyteLine sales order object | 133 | Site reference, Customer order number → external key property, Site reference, Customer order number → Name, Site reference → Site reference, type → type, Customer order number → Customer order number |
| Infor SyteLine version 9 Sales order line | Commercient SyteLine sales order line object | 127 | Site reference, Customer order number, Customer order line, Customer order release → Commercient external key, Site reference, Customer order number, Customer order line, Customer order release → Name, Site reference → Site reference, Customer order number → Customer order number, Customer order line → Customer order line |
| Infor SyteLine version 9 Invoice | Commercient SyteLine invoice object | 4 | Site reference, Invoice number, Invoice sequence → external key property, Site reference, Invoice number, Invoice sequence → Name, ERP customer number → account lookup, Site reference, ERP customer number, Customer sequence number → Commercient SyteLine customer lookup |
| Infor SyteLine version 9 Invoice line item | Commercient SyteLine invoice line item object | 46 | Site reference,Invoice number,Invoice sequence,Invoice line number,Customer order number,Customer order line,Customer order release → Commercient external key, Site reference,Invoice number,Invoice sequence,Invoice line number,Customer order number,Customer order line,Customer order release → Name, Site reference → Site reference, Invoice number → Invoice number, Invoice sequence → Invoice sequence |

## 6. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-mdc/erps/infor-syteline-v9`.

## 7. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Infor SyteLine version 9 → MDC templates set up. dlake-crmpro-mdc is the destination
skill this page sits under: its own text is the authority for the MDC conventions that hold across
every ERP, and its ERP table lists this page alongside every sibling ERP page for this destination.
For the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-mdc/erps/infor-syteline-v9`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
