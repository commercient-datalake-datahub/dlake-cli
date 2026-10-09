---
name: dlake-txdownloaderpro-dynamicscrm/erps/sage-100-us
kind: erp-summary
description: >-
  What the shipped default TxDownloaderPro templates set up when Dynamics CRM is the writeback
  destination and Sage 100 (US) is the source: the 15 default templates the catalogue ships for this
  pair, the 9 process versions they are identified by, what each template family delivers, the shape
  of the query that finds flagged records and the objects and marker columns it reads, the structure
  of the inbound mapping document, and which outbound mapping parts the templates fill for the write
  back to the CRM. It also lists the 8 community templates the catalogue carries for this pair. Use
  it when importing or reading this pair's template set, when deciding which templates to import and
  activate, or when a process runs and writes nothing and the answer is in the query or in the
  mapping document. It extends dlake-txdownloaderpro, which is the authority for operating
  TxDownloaderPro generally, and dlake-txdownloaderpro-dynamicscrm, the destination skill this page
  is a child of, which carries the Dynamics CRM conventions that hold across every ERP.
---
# TxDownloaderPro ← Dynamics CRM — Sage 100 (US): what the shipped default templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-txdownloaderpro-dynamicscrm/erps/sage-100-us` (or `list_skills`)
against the Commercient admin plane. Existing customers who need access or help: contact
support@commercient.com. New customers: contact sales@commercient.com to become a customer and be
whitelisted.

dlake-txdownloaderpro-dynamicscrm is the Dynamics CRM page and carries the destination detail: it is
the skill this page is a child of, and the authority for the conventions that hold across every ERP,
so read it before this page.

What follows is only what the shipped **default** templates for this pair themselves set, described
from their default query, default inbound mapping and default outbound mapping columns. It is a
description of structure: which objects and marker columns are read, which parts and members are
filled, which token names appear. No template text is reproduced. This page grows as the default
catalogue does. The community templates the catalogue also carries for this pair are listed in a
section of their own below, by count, object and name only.

## 1. What the templates deliver

One row per process the default set ships for this pair. The business language is the catalogue's
own, where it carries any; where it does not, the row says what the source and destination objects
are and what the operation flags allow. Operations are the union of the insert, update and delete
flags over that process's templates.

| Process | What it delivers | Source → destination | Templates | Operations |
|---|---|---|---|---|
| Create New Sales Invoice | MS Dynamic Invoice To Sage 100 Invoice | Invoice, Order, Quote → Invoice | 4 | create |
| Create New Sales Order | Dynamic Opportunity to Sage 100 Sales Order | Opportunity, Quote, Order → Order | 3 | create |
| Create New Customer | Dynamic CRM Account to Sage 100 Customer | Account → Customer | 2 | create / update |
| Create and Update Contact | Create Sage 100 Contact From Dynamic CRM Contact | Contact → Contact | 1 | create |
| Delete Contact | Salesforce Contact to Sage 100 Contact | — | 1 | delete |
| Delete Customer | Salesforce Account to Sage Customers | — | 1 | delete |
| Delete Product | MS Dynamic Product to Sage 100 Item - Delete Item | product → Item | 1 | delete |
| Delete Ship To Address | MS Dynamic Account to Sage 100 Ship To Address - Delete Ship To Address | account → Shipping address | 1 | delete |
| Update Ship To Address | MS Dynamic Account to Sage 100 Ship To Address - Update Ship To Address | account → Shipping address | 1 | update |

Across the 15 default templates: 9 carry insert, 3 carry update, 4 carry delete. A flag decides
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

Query does not have one shape across the product (in the operational skill). For this pair, 13 carry
a FetchXML document, 2 carry a query statement in the CRM's own query language. **No query text is
reproduced here**; what follows is what those queries read and filter on.

- **Objects read:** contact, account, opportunity, opportunitydetail, quote, quotedetail, order,
  orderdetail, product.
- **Child collections pulled in the same query:** account, contact, opportunitydetail, quotedetail,
  orderdetail. A header retrieved without its lines is a query that does not name the child
  collection.
- **Marker and key columns the queries name:** Commercient AR customer code. These are the columns a
  user’s flag lands in and the columns the run writes an outcome back to; which ones are in the
  filter is what decides whether a record is in scope at all.
- **Operators present:** `=`. The operational skill is the authority on the vocabulary; the point
  here is only which of it these templates use.
- **FetchXML elements used:** fetch, entity, attribute, linked entity, filter, condition, order;
  condition operators: not null, equals, null.
- **Where the filtering happens:** in the query, on the CRM side, before anything reaches the source
  system. Narrowing a template means editing its query — not its mapping.

## 4. The inbound mapping document

Inbound mapping is a flat object: each member names a field on the source side and its value is a
template resolved against the retrieved record’s document (in the operational skill). Of this pair’s
15 default templates, 15 carry a default inbound mapping. A parseable document carries about 16
members.

- **Template path roots used:** account, quote, opportunity, order, invoice, orderdetail,
  quotedetail, opportunitydetail, contact, invoicedetail, product. A path’s first segment has to
  match the element the engine emits, and the document root itself is never part of the path.
- **Line members present:** a collection member, line item code, line unit price, line discount,
  line quantity shipped, line discount percent, line comment, line item description, line number,
  line quantity ordered, line quote quantity, line opportunity quantity, line quantity ordered. 7
  templates name the collection through a collection member; the members beside it are resolved
  against that collection’s own root rather than through the header.

## 5. Result structure — what goes back to the CRM

Outbound mapping is the outbound half: up to four parts, each optional, filled from the source
system’s response after the write (in the operational skill). Of this pair’s 15 default templates, 6
carry a parseable default outbound mapping, 9 carry none.

| Part | Filled by | What it addresses | Members present |
|---|---|---|---|
| Part 1 | 6 templates | the record the run is already working with | a map from source path to CRM field |
| Part 2 | 0 templates (6 explicitly null) | the line records under it | — |
| Part 3 | 0 templates (6 explicitly null) | a **new** record, matched on an external id field | — |
| Part 4 | 0 templates (6 explicitly null) | a **different** record, addressed by an id field | — |

- **CRM fields Part 1 writes to:** Commercient AR customer code (Dynamics), Sage invoice number
  (custom field), Sage order number (custom field), Sage quote number (custom field), Sage
  opportunity number (custom field). These are the fields on the flagged record that carry the
  source system’s key or outcome once the write has happened — the names only; what lands in them is
  the response, per record.
- **Response fields it reads them from:** Customer number, Invoice number, Order number, Quote
  number, Opportunity number. The map is written **source path first, CRM field second** (in the
  operational skill); the wrong way round resolves to the same silent empty string as a mistyped
  path.

## 6. Community templates

The catalogue carries community templates for this pair as well as the default set above: templates
written on a tenant rather than shipped. Their content is not read and not described here — no
query, no mapping document, no field. What this section states is how many there are, which objects
they start from, what they are called where the name is a product artefact name, and which process
versions they belong to.

- **How many:** 8 community templates, across 2 process versions.
- **Operations:** 8 carry insert, 0 carry update, 0 carry delete. A flag decides which operation the
  template is allowed to perform, as it does for a default template.

| Source → destination | Templates |
|---|---|
| Customer → — | 4 |
| Sales Invoice → — | 4 |

None of these rows carries a destination object name in the catalogue, so the destination side of
every shape above is empty.

- **Template names the catalogue carries:** Create New Customer (3), Create New Sales Invoice (3),
  Create New Customer company 6 (1), Create New Invoice company 6 (1).

Importing one of these writes the same TxDownloaderPro row that importing a default template writes
(in the operational skill); what differs is where the template came from, not how it is stored. What
any one of them contains is read from the imported row itself.

## 7. Verifying

Read the imported row before a run, not after. The operational skill is the authority on the
TxDownloaderPro tools and on the state they report.

The two failures this pair’s templates actually produce: a run that retrieves nothing, which is the
query’s own condition and not the mapping; and a record that arrives with fields empty, which is a
path that does not match the emitted document.

## 8. Where this sits

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
  Fetch it with `dlake admin get_skill dlake-txdownloaderpro-dynamicscrm/erps/sage-100-us`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
