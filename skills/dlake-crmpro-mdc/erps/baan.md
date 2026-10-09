---
name: dlake-crmpro-mdc/erps/baan
kind: erp-summary
description: >-
  Use it when standing up or reading a Baan → MDC template set, when deciding which templates to
  import and activate, or when a run completes without pushing records and the answer is in the view
  or the configuration row. It extends dlake-crmpro, which covers operating CRMPro generally, and
  dlake-crmpro-mdc, the destination skill this page is a child of, which carries the MDC conventions
  that hold across every ERP.
---
# CRMPro → MDC — Baan: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-mdc/erps/baan` (or `list_skills`) against the Commercient admin
plane. Existing customers who need access or help: contact support@commercient.com. New customers:
contact sales@commercient.com to become a customer and be whitelisted.

dlake-crmpro is the parent skill and the authority for everything general: the CRMPro tools, process
configuration and field list, the sync history, how source data is selected, and what a run that
finds nothing does. Read it first; this page does not repeat it. dlake-crmpro-mdc is the destination
skill this page is a child of, and the authority for the MDC conventions that hold across every ERP:
read it first, then come back here for what this source's own templates set. This page grows as the
catalogue does.

## 1. What the templates deliver

| Group | Business outcome | Objects | Source tables and views |
|---|---|---|---|
| **CRM Unit of measure schedule** | The templates push unit of measure schedule to MDC. | unit of measure schedule | items |
| **CRM unit of measure** | The templates push uom to MDC. | uom | items |
| **CRM Product** | The templates push product to MDC. | product | items |
| **CRM Price level** | The templates push price level to MDC. | price level | — |
| **CRM sales order** | The templates push sales order to MDC. | sales order | delivery addresses, sales orders, customers |
| **CRM sales order detail** | The templates push sales order detail to MDC. | sales order detail | delivery addresses, sales order lines, customers |
| **CRM invoice** | The templates push invoice to MDC. | invoice | delivery addresses, receivables transactions, customers |
| **Baan Sales Person** | The templates push Commercient Baan salesperson object to MDC. | Commercient Baan salesperson object | employees |
| **CRM Account** | The templates push account to MDC. | account | customers |
| **Baan Customer** | The templates push Commercient Baan customer object to MDC. | Commercient Baan customer object | customers |
| **Baan Ship to Address** | The templates push Commercient Baan ship to address object to MDC. | Commercient Baan ship to address object | delivery addresses |
| **Baan Sales order** | The templates push Commercient Baan sales order object to MDC. | Commercient Baan sales order object | sales orders |
| **Baan Sales order detail** | The templates push Commercient Baan sales order detail object to MDC. | Commercient Baan sales order detail object | sales order lines |
| **Baan Invoice** | The templates push Commercient Baan invoice object to MDC. | Commercient Baan invoice object | receivables transactions |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Baan Sales Person | Commercient Baan salesperson object | Commercient external key | 4 |
| CRM Account | account | Commercient AR customer code (Dynamics) | 5 |
| Baan Customer | Commercient Baan customer object | Commercient external key | 6 |
| Baan Ship to Address | Commercient Baan ship to address object | Commercient external key | 7 |
| CRM Unit of measure schedule | unit of measure schedule | name | 8 |
| CRM unit of measure | uom | name | 9 |
| CRM Product | product | Commercient external key | 10 |
| CRM Price level | price level | Commercient external key | 22 |
| CRM sales order | sales order | Commercient external key | 24 |
| CRM sales order detail | sales order detail | Commercient external key | 25 |
| CRM invoice | invoice | Commercient external key | 26 |
| Baan Sales order | Commercient Baan sales order object | Commercient external key | 27 |
| Baan Sales order detail | Commercient Baan sales order detail object | Commercient external key | 28 |
| Baan Invoice | Commercient Baan invoice object | Commercient external key | 29 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| salesperson feed | insert + update | employees |
| CRM account feed (generic name) | insert + update | customers |
| customer feed | insert + update | customers |
| shipping address feed | insert + update | delivery addresses |
| CRM unit of measure schedule feed | insert + update | items |
| CRM unit of measure feed | insert + update | items |
| product feed | insert + update | items |
| price level feed | — | — |
| sales order feed (standard objects) | insert + update | delivery addresses, sales orders, customers |
| sales order line feed (standard objects) | insert + update | delivery addresses, sales order lines, customers |
| invoice feed (standard objects) | insert + update | delivery addresses, receivables transactions, customers |
| sales order feed (Commercient objects) | insert + update | sales orders |
| sales order line feed (Commercient objects) | insert + update | sales order lines |
| invoice feed (Commercient objects) | insert + update | receivables transactions |

## 4. Order of work

The templates set run sequence from 4 to 29. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 4 — Baan Sales Person
- 5 — CRM Account
- 6 — Baan Customer
- 7 — Baan Ship to Address
- 8 — CRM Unit of measure schedule
- 9 — CRM unit of measure
- 10 — CRM Product
- 22 — CRM Price level
- 24 — CRM sales order
- 25 — CRM sales order detail
- 26 — CRM invoice
- 27 — Baan Sales order
- 28 — Baan Sales order detail
- 29 — Baan Invoice

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- CRM unit of measure schedule feed reads Unit of measure schedule; no template in this set writes
  Unit of measure schedule
- CRM unit of measure feed reads Unit of measure schedule, unit of measure; no template in this set
  writes Unit of measure schedule, unit of measure
- product feed reads product sync output; no template in this set writes product sync output
- sales order feed (standard objects) reads CRM account sync output (generic name), customer sync
  output, sales order sync output (standard objects); no template in this set writes CRM account
  sync output (generic name), customer sync output, sales order sync output (standard objects)
- sales order line feed (standard objects) reads sales order sync output (standard objects), CRM
  product sync output (generic name), sales order line sync output (standard objects); no template
  in this set writes sales order sync output (standard objects), CRM product sync output (generic
  name), sales order line sync output (standard objects)
- invoice feed (standard objects) reads CRM account sync output (generic name), customer sync
  output, invoice sync output (standard objects); no template in this set writes CRM account sync
  output (generic name), customer sync output, invoice sync output (standard objects)
- salesperson feed reads salesperson sync output; no template in this set writes salesperson sync
  output
- CRM account feed (generic name) reads payment term sync output (generic name), territory: CRM
  account sync output (generic name); no template in this set writes payment term sync output
  (generic name), territory: CRM account sync output (generic name)
- customer feed reads CRM account sync output (generic name), salesperson sync output, customer sync
  output; no template in this set writes CRM account sync output (generic name), salesperson sync
  output, customer sync output
- shipping address feed reads CRM account sync output (generic name), customer sync output, shipping
  address sync output; no template in this set writes CRM account sync output (generic name),
  customer sync output, shipping address sync output
- sales order feed (Commercient objects) reads salesperson sync output, CRM account sync output
  (generic name), customer sync output, sales order sync output; no template in this set writes
  salesperson sync output, CRM account sync output (generic name), customer sync output, sales order
  sync output
- sales order line feed (Commercient objects) reads sales order sync output, sales order line sync
  output; no template in this set writes sales order sync output, sales order line sync output
- invoice feed (Commercient objects) reads CRM account sync output (generic name), customer sync
  output, salesperson sync output, invoice sync output; no template in this set writes CRM account
  sync output (generic name), customer sync output, salesperson sync output, invoice sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → MDC pairs |
|---|---|---|---|
| Baan Sales Person | Commercient Baan salesperson object | 8 | Baan employee number → external key property, Baan name → name, Baan name 2 → Name line 2, Baan address line 1 → address, Baan address line 2 → address line 2 property |
| CRM Account | account | 35 | Baan customer number → Commercient AR customer code (Dynamics), Baan customer number → Account number, Baan customer number → name, Baan telephone → Address 1 telephone, Baan address line 1 → Address 1 street 1 |
| Baan Customer | Commercient Baan customer object | 41 | Baan customer number → external key property, Baan name → name, Baan name 2 → Name line 2, Baan title → title, Baan address line 1 → address |
| Baan Ship to Address | Commercient Baan ship to address object | 14 | Baan customer number, Baan delivery address code → Commercient external key, Baan customer number → Customer, Baan delivery address code → Delivery address (custom field), Baan customer number, Baan delivery address code → Name, Baan name 2 → Name 2 (custom field) |
| CRM Unit of measure schedule | unit of measure schedule | 2 | Baan inventory unit → name, Base unit name → Base unit name |
| CRM unit of measure | uom | 2 | Baan storage unit → name, Baan inventory unit → unit of measure schedule lookup |
| CRM Product | product | 6 | Baan item description → name, Baan item → Product number, Baan item → Commercient external key, Baan item description → description, the linked default unit of measure schedule → default unit of measure schedule lookup |
| CRM Price level | price level | 4 | Commercient external key → Commercient external key, Name → Name, Description → Description, Row timestamp → Row timestamp |
| CRM sales order | sales order | 18 | Baan order number → Commercient external key, Baan order number → Name, Baan address line 1 → Bill to street 1, Baan address line 2 → Bill to street 2, Baan city → Bill to city |
| CRM sales order detail | sales order detail | 11 | Baan order number, Baan order position → Commercient external key, Baan address line 1 → Ship to street 1, Baan address line 2 → Ship to street 2, Baan address line 3 → Ship to street 3, Baan city → Ship to city |
| CRM invoice | invoice | 18 | Baan address line 1 → Bill to street 1, Baan address line 2 → Bill to street 2, Baan city → Bill to city, Baan postal code → Bill to postal code, Baan country code → Bill to country |
| Baan Sales order | Commercient Baan sales order object | 29 | Baan order number → external key property, Baan order number → name, Baan customer number → Customer, Baan payment terms → Terms of payment (custom field), Baan order type → Order type (custom field) |
| Baan Sales order detail | Commercient Baan sales order detail object | 25 | Baan order number, Baan order position → Commercient external key, Baan order number, Baan order position → name, Baan order number → Order number field, Baan order position → Position number (custom field), Baan direct delivery → Direct delivery (custom field) |
| Baan Invoice | Commercient Baan invoice object | 23 | Baan transaction type, Baan invoice number, Baan invoice line, Baan document type, Baan document number, Baan document line number → external key property, Baan transaction type, Baan invoice number, Baan invoice line, Baan document type, Baan document number, Baan document line number → name, Baan transaction type → Transaction type (custom field), Baan invoice number → document, Baan customer number → customer |

## 6. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-mdc/erps/baan`.

## 7. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Baan → MDC templates set up. dlake-crmpro-mdc is the destination skill this page sits
under: its own text is the authority for the MDC conventions that hold across every ERP, and its ERP
table lists this page alongside every sibling ERP page for this destination. For the extract leg
that fills the source data, see dlake-normalsync; for the on-premises agent that runs it,
dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration up,
dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-mdc/erps/baan`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
