---
name: dlake-txdownloaderpro-dynamicscrm/erps/sage-50-us
kind: erp-summary
description: >-
  What the shipped default TxDownloaderPro templates set up when Dynamics CRM is the writeback
  destination and Sage 50 US is the source: the 13 default templates the catalogue ships for this
  pair, the 6 process versions they are identified by, what each template family delivers, the shape
  of the query that finds flagged records and the objects and marker columns it reads, the structure
  of the inbound mapping document, and which outbound mapping parts the templates fill for the write
  back to the CRM. Use it when importing or reading this pair's template set, when deciding which
  templates to import and activate, or when a process runs and writes nothing and the answer is in
  the query or in the mapping document. It extends dlake-txdownloaderpro, which is the authority for
  operating TxDownloaderPro generally, and dlake-txdownloaderpro-dynamicscrm, the destination skill
  this page is a child of, which carries the Dynamics CRM conventions that hold across every ERP.
---
# TxDownloaderPro ← Dynamics CRM — Sage 50 US: what the shipped default templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-txdownloaderpro-dynamicscrm/erps/sage-50-us` (or `list_skills`) against
the Commercient admin plane. Existing customers who need access or help: contact
support@commercient.com. New customers: contact sales@commercient.com to become a customer and be
whitelisted.

dlake-txdownloaderpro-dynamicscrm is the Dynamics CRM page and carries the destination detail: it is
the skill this page is a child of, and the authority for the conventions that hold across every ERP,
so read it before this page.

What follows is only what the shipped **default** templates for this pair themselves set, described
from their default query, default inbound mapping and default outbound mapping columns. It is a
description of structure: which objects and marker columns are read, which parts and members are
filled, which token names appear. No template text is reproduced. This page grows as the default
catalogue does.

## 1. What the templates deliver

One row per process the default set ships for this pair. The business language is the catalogue's
own, where it carries any; where it does not, the row says what the source and destination objects
are and what the operation flags allow. Operations are the union of the insert, update and delete
flags over that process's templates.

| Process | What it delivers | Source → destination | Templates | Operations |
|---|---|---|---|---|
| Create New Sales invoice | MS Dynamic Opportunity to Sage 50 US Sales Invoice | Opportunity, Quote, Order → Invoice | 4 | create |
| Create New Sales order | Dynamic Quote to Sage 50 Sales Order | Quote, Order, Opportunity → Order | 3 | create |
| Update Sales order | Dynamic Quote to Sage 50 Sales Order - Update Sales Order | Quote, Order, Opportunity → Sales Order | 3 | update |
| Create New Customer | Dynamic CRM Account to Sage 50 US Customer | account → Customer | 1 | create |
| Delete Sales invoice | Salesforce Opportunity to Sage 50 US Invoice - Delete Invoice | — | 1 | delete |
| Delete Sales order | Salesforce Opportunity to Sage 50 US Sales Order - Delete Sales Order | — | 1 | delete |

Across the 13 default templates: 8 carry insert, 3 carry update, 2 carry delete. A flag decides
which operation the process is allowed to perform, not which one it performs on a given record.

## 2. The process rows the import creates

Importing one of these templates writes one TxDownloaderPro row. The columns the import fills from
the template are the ones the operational skill describes: the query, the inbound and outbound
mappings and the operation flags are all stored on that row. In-flight state is never in this table
— it is in the writeback transaction log (in the operational skill), keyed by its state.

| What the import sets | Where it comes from |
|---|---|
| Query | the template’s default query — section 3 |
| inbound mapping | the template’s default inbound mapping — section 4 |
| outbound mapping | the template’s default outbound mapping — section 5 |
| insert / update / delete | the template’s own flags — section 1 |

7 of these template rows carry a licence group, so what a given tenant is offered in the picker is
narrower than what the catalogue holds.

## 3. What the query retrieves

Query does not have one shape across the product (in the operational skill). For this pair, 11 carry
a FetchXML document, 2 carry a query statement in the CRM's own query language. **No query text is
reproduced here**; what follows is what those queries read and filter on.

- **Objects read:** account, contact, quote, quotedetail, salesorder, salesorderdetail, order,
  orderdetail, opportunity, opportunitydetail.
- **Child collections pulled in the same query:** contact, account, quotedetail, salesorderdetail,
  orderdetail, opportunitydetail. A header retrieved without its lines is a query that does not name
  the child collection.
- **FetchXML elements used:** fetch, entity, attribute, linked entity, filter, condition, order;
  condition operators: null, not null, equals.
- **Where the filtering happens:** in the query, on the CRM side, before anything reaches the source
  system. Narrowing a template means editing its query — not its mapping.

## 4. The inbound mapping document

Inbound mapping is a flat object: each member names a field on the source side and its value is a
template resolved against the retrieved record’s document (in the operational skill). Of this pair’s
13 default templates, 13 carry a default inbound mapping. A parseable document carries about 15
members.

- **Template path roots used:** opportunity, quote, order, account, opportunitydetail, quotedetail,
  orderdetail, salesorderdetail, invoicedetail, salesorder, invoice, Opportunity. A path’s first
  segment has to match the element the engine emits, and the document root itself is never part of
  the path.
- **Line members present:** line quantity, line unit price, line tax type, line item identifier
  field, a collection member, line general ledger account, line sales order description, line
  description, line amount, line stocking quantity. 10 templates name the collection through a
  collection member; the members beside it are resolved against that collection’s own root rather
  than through the header.

## 5. Result structure — what goes back to the CRM

Outbound mapping is the outbound half: up to four parts, each optional, filled from the source
system’s response after the write (in the operational skill). Of this pair’s 13 default templates, 1
carries a parseable default outbound mapping, 12 carry none.

| Part | Filled by | What it addresses | Members present |
|---|---|---|---|
| Part 1 | 1 template | the record the run is already working with | a map from source path to CRM field |
| Part 2 | 0 templates (1 explicitly null) | the line records under it | — |
| Part 3 | 0 templates (1 explicitly null) | a **new** record, matched on an external id field | — |
| Part 4 | 0 templates (1 explicitly null) | a **different** record, addressed by an id field | — |

- **CRM fields Part 1 writes to:** Commercient AR customer code (Dynamics). These are the fields on
  the flagged record that carry the source system’s key or outcome once the write has happened — the
  names only; what lands in them is the response, per record.
- **Response fields it reads them from:** Customer identifier. The map is written **source path
  first, CRM field second** (in the operational skill); the wrong way round resolves to the same
  silent empty string as a mistyped path.

## 6. Verifying

Read the imported row before a run, not after. The operational skill is the authority on the
TxDownloaderPro tools and on the state they report.

The two failures this pair’s templates actually produce: a run that retrieves nothing, which is the
query’s own condition and not the mapping; and a record that arrives with fields empty, which is a
path that does not match the emitted document.

## 7. Where this sits

- dlake-txdownloaderpro — the parent: exposure, key scoping, the two tables, its state, the mapping
  columns, the filter vocabulary, the TxDownloaderPro tools. **Read it first.**
- dlake-txdownloaderpro-dynamicscrm — the Dynamics CRM destination page, which this page is a child
  of: the query shape, marker conventions and mapping conventions this destination uses across every
  ERP, and the ERP table that lists this page and its siblings.
- dlake-integration-setup — registration, CRM choice and the ERP connector, of which writeback is
  one step.
- dlake-crmpro and dlake-normalsync — the inbound leg, going the other way.
- dlake — general tenant operation.

This page describes the shipped default template set for this pair, and grows as that set does.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-txdownloaderpro-dynamicscrm/erps/sage-50-us`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
