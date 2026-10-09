---
name: dlake-crmpro-dynamicscrm/erps/epicor-10
kind: erp-summary
description: >-
  Use it when standing up or reading an Epicor 10 → Dynamics CRM template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-dynamicscrm, the destination skill this page is a child of, which
  carries the Dynamics CRM conventions that hold across every ERP.
---
# CRMPro → Dynamics CRM — Epicor 10: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-dynamicscrm/erps/epicor-10` (or `list_skills`) against the
Commercient admin plane. Existing customers who need access or help: contact
support@commercient.com. New customers: contact sales@commercient.com to become a customer and be
whitelisted.

dlake-crmpro is the parent skill and the authority for everything general: the CRMPro tools, process
configuration and field list, the sync history, how source data is selected, and what a run that
finds nothing does. Read it first; this page does not repeat it. dlake-crmpro-dynamicscrm is the
destination skill this page is a child of, and the authority for the Dynamics CRM conventions that
hold across every ERP: read it first, then come back here for what this source's own templates set.
This page grows as the catalogue does.

## 1. What the templates deliver

| Group | Business outcome | Objects | Source tables and views |
|---|---|---|---|
| **salesorder** | The templates push salesorder to Dynamics CRM. | salesorder | sales order lines, price list part prices, e, sales order headers |
| **salesorderdetail** | The templates push salesorderdetail to Dynamics CRM. | salesorderdetail | sales order lines, customers |
| **invoice** | The templates push invoice to Dynamics CRM. | invoice | invoice lines, price list part prices, e, invoice headers |
| **invoicedetail** | The templates push invoicedetail to Dynamics CRM. | invoicedetail | invoice lines, customers |
| **quotedetail** | The templates push quotedetail to Dynamics CRM. | quotedetail | quote lines, customers |
| **Epicor 10 Sales order** | The templates push Commercient Epicor 10 sales order object to Dynamics CRM. | Commercient Epicor 10 sales order object | sales order lines, price list part prices, e, sales order headers |
| **Epicor 10 Sales order Detail** | The templates push Commercient Epicor 10 sales order detail object to Dynamics CRM. | Commercient Epicor 10 sales order detail object | sales order lines, customers |
| **Epicor 10 Invoice** | The templates push Commercient Epicor 10 invoice object to Dynamics CRM. | Commercient Epicor 10 invoice object | invoice lines, price list part prices, e, invoice headers |
| **Epicor 10 Invoice Detail** | The templates push Commercient Epicor 10 invoice detail object to Dynamics CRM. | Commercient Epicor 10 invoice detail object | invoice lines, customers |
| **CRM Quote and Line** | The templates push quote to Dynamics CRM. | quote | quote lines, price list part prices, e, quote headers |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| salesorder | salesorder | Commercient external key | 20 |
| salesorderdetail | salesorderdetail | Commercient external key | 21 |
| invoice | invoice | Commercient external key | 22 |
| invoicedetail | invoicedetail | Commercient external key | 23 |
| quote | quote | Commercient external key | 24 |
| quotedetail | quotedetail | Commercient external key | 25 |
| Epicor 10 Sales order | Commercient Epicor 10 sales order object | Commercient external key | 26 |
| Epicor 10 Sales order Detail | Commercient Epicor 10 sales order detail object | Commercient external key | 27 |
| Epicor 10 Invoice | Commercient Epicor 10 invoice object | Commercient external key | 28 |
| Epicor 10 Invoice Detail | Commercient Epicor 10 invoice detail object | Commercient external key | 29 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| sales order feed (standard objects) | insert + update | sales order lines, price list part prices, e, sales order headers |
| sales order line feed (standard objects) | insert + update | sales order lines, customers |
| invoice feed (standard objects) | insert + update | invoice lines, price list part prices, e, invoice headers |
| invoice line feed (standard objects) | insert + update | invoice lines, customers |
| quote feed (standard objects) | insert only | quote lines, price list part prices, e, quote headers |
| quote line feed (standard objects) | insert only | quote lines, customers |
| sales order feed (Commercient objects) | insert + update | sales order lines, price list part prices, e, sales order headers |
| sales order line feed (Commercient objects) | insert + update | sales order lines, customers |
| invoice feed (Commercient objects) | insert + update | invoice lines, price list part prices, e, invoice headers |
| invoice line feed (Commercient objects) | insert + update | invoice lines, customers |

## 4. Order of work

The templates set run sequence from 20 to 29. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 20 — salesorder
- 21 — salesorderdetail
- 22 — invoice
- 23 — invoicedetail
- 24 — quote
- 25 — quotedetail
- 26 — Epicor 10 Sales order
- 27 — Epicor 10 Sales order Detail
- 28 — Epicor 10 Invoice
- 29 — Epicor 10 Invoice Detail

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- sales order feed (standard objects) reads CRM price book sync output (generic name), CRM account
  sync output (generic name), customer sync output, sales order sync output (standard objects); no
  template in this set writes CRM price book sync output (generic name), CRM account sync output
  (generic name), customer sync output, sales order sync output (standard objects)
- sales order line feed (standard objects) reads sales order sync output (standard objects), CRM
  product sync output (generic name), sales order line sync output (standard objects); no template
  in this set writes sales order sync output (standard objects), CRM product sync output (generic
  name), sales order line sync output (standard objects)
- invoice feed (standard objects) reads CRM price book sync output (generic name), CRM account sync
  output (generic name), customer sync output, invoice sync output (standard objects); no template
  in this set writes CRM price book sync output (generic name), CRM account sync output (generic
  name), customer sync output, invoice sync output (standard objects)
- invoice line feed (standard objects) reads invoice sync output (standard objects), CRM product
  sync output (generic name), invoice line sync output (standard objects); no template in this set
  writes invoice sync output (standard objects), CRM product sync output (generic name), invoice
  line sync output (standard objects)
- quote line feed (standard objects) reads quote sync output (standard objects), CRM product sync
  output (generic name), quote line sync output (standard objects); no template in this set writes
  quote sync output (standard objects), CRM product sync output (generic name), quote line sync
  output (standard objects)
- sales order feed (Commercient objects) reads CRM price book sync output (generic name), CRM
  account sync output (generic name), customer sync output, sales order sync output (Commercient
  objects); no template in this set writes CRM price book sync output (generic name), CRM account
  sync output (generic name), customer sync output, sales order sync output (Commercient objects)
- sales order line feed (Commercient objects) reads sales order sync output (Commercient objects),
  CRM product sync output (generic name), sales order line sync output (Commercient objects); no
  template in this set writes sales order sync output (Commercient objects), CRM product sync output
  (generic name), sales order line sync output (Commercient objects)
- invoice feed (Commercient objects) reads CRM price book sync output (generic name), CRM account
  sync output (generic name), customer sync output, invoice sync output (Commercient objects); no
  template in this set writes CRM price book sync output (generic name), CRM account sync output
  (generic name), customer sync output, invoice sync output (Commercient objects)
- invoice line feed (Commercient objects) reads invoice sync output (Commercient objects), CRM
  product sync output (generic name), invoice line sync output (Commercient objects); no template in
  this set writes invoice sync output (Commercient objects), CRM product sync output (generic name),
  invoice line sync output (Commercient objects)
- quote feed (standard objects) reads CRM price book sync output (generic name), CRM account sync
  output (generic name), customer sync output, quote sync output (standard objects); no template in
  this set writes CRM price book sync output (generic name), CRM account sync output (generic name),
  customer sync output, quote sync output (standard objects)

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Dynamics CRM pairs |
|---|---|---|---|
| salesorder | salesorder | 22 | Company, Order number → Commercient external key, Company, Order number → Name, Epicor bill to address line 1 → Bill to street 1, Epicor bill to address line 2 → Bill to street 2, Epicor bill to address line 3 → Bill to street 3 |
| salesorderdetail | salesorderdetail | 14 | Company, Order line number, Order number → Commercient external key, Address 1 → Ship to street 1, Address 2 → Ship to street 2, Address 3 → Ship to street 3, City → Ship to city |
| invoice | invoice | 22 | Epicor bill to address line 1 → Bill to street 1, Epicor bill to address line 2 → Bill to street 2, Epicor bill to address line 3 → Bill to street 3, Epicor bill to city → Bill to city, Epicor bill to state → Bill to state or province |
| invoicedetail | invoicedetail | 14 | Company,invoice line,Invoice number → Commercient external key, Address 1 → Ship to street 1, Address 2 → Ship to street 2, Address 3 → Ship to street 3, City → Ship to city |
| quote | quote | 22 | Company, Quote number → Commercient external key, Company, Quote number → Name, Epicor bill to address line 1 → Bill to street 1, Epicor bill to address line 2 → Bill to street 2, Epicor bill to address line 3 → Bill to street 3 |
| quotedetail | quotedetail | 14 | Company, Quote number, Quote line number → Commercient external key, Address 1 → Ship to street 1, Address 2 → Ship to street 2, Address 3 → Ship to street 3, City → Ship to city |
| Epicor 10 Sales order | Commercient Epicor 10 sales order object | 3 | Company, Order number → external key property, Company, Order number → Name, the linked Salesforce record → price list lookup |
| Epicor 10 Sales order Detail | Commercient Epicor 10 sales order detail object | 5 | Company, Order line number, Order number → Commercient external key, Company, Order line number, Order number → name, the linked Salesforce record → Commercient Epicor 10 sales order lookup, the linked Salesforce record → product lookup, the linked Salesforce record → unit of measure lookup |
| Epicor 10 Invoice | Commercient Epicor 10 invoice object | 3 | Company,Invoice number → Commercient external key, Company,Invoice number → Name, the linked Salesforce record → price list lookup |
| Epicor 10 Invoice Detail | Commercient Epicor 10 invoice detail object | 3 | Company,invoice line,Invoice number → Commercient external key, Company,invoice line,Invoice number → name, the linked Salesforce record → Commercient Epicor 10 invoice lookup |

## 6. Community templates

The catalogue carries 23 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 23
- Default operations: insert on 23, update on 23, delete on 23
- Marked as circular sync: 0
- Licence groups they span: 5
- Destination objects: account, contact, Epicor 10 customer (custom object), Epicor 10 invoice
  detail (custom object), Epicor 10 invoice header (custom object), Epicor 10 item master (custom
  object), Epicor 10 item warehouse (custom object), Epicor 10 quote detail (custom object), Epicor
  10 quote header (custom object), Epicor 10 sales order detail (custom object), Epicor 10 sales
  order header (custom object), Epicor 10 salesperson (custom object), Epicor 10 ship to address
  (custom object), invoice, invoicedetail, product, quote, quotedetail, salesorder, salesorderdetail
  and 2 more
- Object display names: CRM Account, CRM Contact, CRM Product, CRM unit of measure, Epicor 10
  Customer, Epicor 10 Invoice detail, Epicor 10 Invoice header, Epicor 10 Item master, Epicor 10
  Item warehouse, Epicor 10 Quote detail, Epicor 10 Quote header, Epicor 10 Sales order detail, 9
  more and a further template
- Template groups: CRM Order and Line, CRM Quote and Line, Account

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-dynamicscrm/erps/epicor-10`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Epicor 10 → Dynamics CRM templates set up. dlake-crmpro-dynamicscrm is the destination
skill this page sits under: its own text is the authority for the Dynamics CRM conventions that hold
across every ERP, and its ERP table lists this page alongside every sibling ERP page for this
destination. For the extract leg that fills the source data, see dlake-normalsync; for the
on-premises agent that runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for
standing an integration up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-dynamicscrm/erps/epicor-10`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
