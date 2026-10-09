---
name: dlake-crmpro-mdc/erps/sage-100-us-sage100us
kind: erp-summary
description: >-
  Use it when standing up or reading a Sage 100 US (Sage 100 US) → MDC template set, when deciding
  which templates to import and activate, or when a run completes without pushing records and the
  answer is in the view or the configuration row. It extends dlake-crmpro, which covers operating
  CRMPro generally, and dlake-crmpro-mdc, the destination skill this page is a child of, which
  carries the MDC conventions that hold across every ERP.
---
# CRMPro → MDC — Sage 100 US (Sage 100 US): what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-mdc/erps/sage-100-us-sage100us` (or `list_skills`) against the
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
| **CRM Unit of measure schedule** | The templates push unit of measure schedule to MDC. New records are created and existing ones updated; none are deleted. | unit of measure schedule | — |
| **CRM unit of measure** | The templates push uom to MDC. New records are created and existing ones updated; none are deleted. | uom | — |
| **CRM Price level** | The templates push price level to MDC. New records are created and existing ones updated; none are deleted. | price level | items |
| **CRM Product** | The templates push product to MDC. New records are created and existing ones updated; none are deleted. | product | items |
| **CRM sales order** | The templates push sales order to MDC. New records are created and existing ones updated; none are deleted. | sales order | sales order history lines, e, items, sales order headers, customers |
| **CRM sales order detail** | The templates push sales order detail to MDC. New records are created and existing ones updated; none are deleted. | sales order detail | sales order history lines, sales order headers |
| **CRM invoice** | The templates push invoice to MDC. New records are created and existing ones updated; none are deleted. | invoice | invoice history lines, e, items, invoice history headers, customers |
| **CRM invoice detail** | The templates push invoice detail to MDC. New records are created and existing ones updated; none are deleted. | invoice detail | invoice history lines, invoice history headers |
| **CRM Account** | The templates push account to MDC. New records are created and existing ones updated; none are deleted. | account | customers, shipping addresses |
| **CRM Contact** | The templates push account to MDC. New records are created and existing ones updated; none are deleted. | account | customer contacts, customers |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| CRM Account | account | Commercient AR customer code (Dynamics) | 1 |
| CRM Contact | account | Commercient external key | 4 |
| CRM Unit of measure schedule | unit of measure schedule | name | 20 |
| CRM unit of measure | uom | name | 21 |
| CRM Price level | price level | Commercient external key | 22 |
| CRM Product | product | Commercient external key | 23 |
| CRM sales order | sales order | Commercient external key | 24 |
| CRM sales order detail | sales order detail | Commercient external key | 25 |
| CRM invoice | invoice | Commercient external key | 26 |
| CRM invoice detail | invoice detail | Commercient external key | 27 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| CRM account feed (generic name) | insert + update | customers, shipping addresses |
| CRM unit of measure schedule feed | — | — |
| CRM unit of measure feed | — | — |
| price level feed | — | items |
| product feed | insert + update | items |
| sales order feed (standard objects) | insert + update | sales order history lines, e, items, sales order headers |
| sales order line feed (standard objects) | insert + update | sales order history lines, sales order headers |
| invoice feed (standard objects) | insert + update | invoice history lines, e, items, invoice history headers |
| invoice line feed (standard objects) | insert + update | invoice history lines, invoice history headers |

## 4. Order of work

The templates set run sequence from 1 to 27. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 1 — CRM Account
- 4 — CRM Contact
- 20 — CRM Unit of measure schedule
- 21 — CRM unit of measure
- 22 — CRM Price level
- 23 — CRM Product
- 24 — CRM sales order
- 25 — CRM sales order detail
- 26 — CRM invoice
- 27 — CRM invoice detail

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
- CRM account feed (generic name) reads salesperson sync output, CRM account sync output (generic
  name); no template in this set writes salesperson sync output, CRM account sync output (generic
  name)
- CRM Contact reads CRM account sync output (generic name), user sync output, customer sync output,
  CRM contact sync output; no template in this set writes CRM account sync output (generic name),
  user sync output, customer sync output, CRM contact sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → MDC pairs |
|---|---|---|---|
| CRM Account | account | 11 | AR division number, Customer number → Commercient AR customer code (Dynamics), Customer name → name, Address line 1 → Address 1 street 1, Address line 2 → Address 1 street 2, Address line 3 → Address 1 street 3 |
| CRM Contact | account | 16 | AR division number, Customer number, Contact code → Commercient external key, Contact name → first name property, Contact name → last name property, Email address → Email address 1, Telephone number 1 → Telephone 1 |
| CRM Unit of measure schedule | unit of measure schedule | 2 | name → name, Base unit name → Base unit name |
| CRM unit of measure | uom | 2 | name → name, unit of measure schedule lookup → unit of measure schedule lookup |
| CRM Price level | price level | 3 | Item code → Commercient external key, Item code → Name, Item description → Description |
| CRM Product | product | 6 | Item code → name, Item code → Product number, Item code → Commercient external key, Item description → description, the linked default unit of measure schedule → default unit of measure schedule lookup |
| CRM sales order | sales order | 20 | returned sales order number → Commercient external key, Bill to name → Name, Bill to address line 1 → Bill to street 1, Bill to address line 2 → Bill to street 2, Bill to address line 3 → Bill to street 3 |
| CRM sales order detail | sales order detail | 13 | returned sales order number, Sequence number → Commercient external key, Shipping name → Ship to name, Ship to address line 1 → Ship to street 1, Ship to address line 2 → Ship to street 2, Ship to address line 3 → Ship to street 3 |
| CRM invoice | invoice | 21 | Bill to address line 1 → Bill to street 1, Bill to address line 2 → Bill to street 2, Bill to address line 3 → Bill to street 3, Bill to address city → Bill to city, Bill to address state → Bill to state or province |
| CRM invoice detail | invoice detail | 14 | Invoice number, Header sequence number, Detail sequence number → Commercient external key, Shipping name → name, Ship to address line 1 → Ship to street 1, Ship to address line 2 → Ship to street 2, Ship to address line 3 → Ship to street 3 |

## 6. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-mdc/erps/sage-100-us-sage100us`.

## 7. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Sage 100 US (Sage 100 US) → MDC templates set up. dlake-crmpro-mdc is the destination
skill this page sits under: its own text is the authority for the MDC conventions that hold across
every ERP, and its ERP table lists this page alongside every sibling ERP page for this destination.
For the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-mdc/erps/sage-100-us-sage100us`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
