---
name: dlake-txdownloaderpro-salesforce/erps/aptean-encompix
kind: erp-summary
description: >-
  What the shipped default TxDownloaderPro templates set up when Salesforce is the writeback
  destination and Aptean Encompix is the source: the 1 default template the catalogue ships for this
  pair, the 1 process version they are identified by, what each template family delivers, the shape
  of the query that finds flagged records and the objects and marker columns it reads, the structure
  of the inbound mapping document, and which outbound mapping parts the templates fill for the write
  back to the CRM. Use it when importing or reading this pair's template set, when deciding which
  templates to import and activate, or when a process runs and writes nothing and the answer is in
  the query or in the mapping document. It extends dlake-txdownloaderpro, which is the authority for
  operating TxDownloaderPro generally, and dlake-txdownloaderpro-salesforce, the destination skill
  this page is a child of, which carries the Salesforce conventions that hold across every ERP.
---
# TxDownloaderPro ← Salesforce — Aptean Encompix: what the shipped default templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-txdownloaderpro-salesforce/erps/aptean-encompix` (or `list_skills`)
against the Commercient admin plane. Existing customers who need access or help: contact
support@commercient.com. New customers: contact sales@commercient.com to become a customer and be
whitelisted.

dlake-txdownloaderpro-salesforce is the Salesforce page and carries the destination detail: it is
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
| (unnamed) | Salesforce Quote to Aptean Encompix Quote | Quote → Quote | 1 | create |

Across the single default template: 1 carries insert, 0 carry update, 0 carry delete. A flag decides
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

## 3. What the query retrieves

Query does not have one shape across the product (in the operational skill). For this pair, 1
carries a query statement in the CRM's own query language. **No query text is reproduced here**;
what follows is what those queries read and filter on.

- **Objects read:** quote line items.
- **Child collections pulled in the same query:** quote line items. A header retrieved without its
  lines is a query that does not name the child collection.
- **Marker and key columns the queries name:** Commercient AR customer code, External key (custom
  field). These are the columns a user’s flag lands in and the columns the run writes an outcome
  back to; which ones are in the filter is what decides whether a record is in scope at all.
- **Operators present:** `!=`, `=`, and, a test for an empty value. The operational skill is the
  authority on the vocabulary; the point here is only which of it these templates use.
- **Where the filtering happens:** in the query, on the CRM side, before anything reaches the source
  system. Narrowing a template means editing its query — not its mapping.

## 4. The inbound mapping document

Inbound mapping is a flat object: each member names a field on the source side and its value is a
template resolved against the retrieved record’s document (in the operational skill). Of this pair’s
1 default template, 1 carries a default inbound mapping. A parseable document carries about 8
members.

- **Template path roots used:** Quote, quote line items. A path’s first segment has to match the
  element the engine emits, and the document root itself is never part of the path.
- **Line members present:** line item number, line item description, line quote quantity, line base
  price, line due date, a collection member. 1 template name the collection through a collection
  member; the members beside it are resolved against that collection’s own root rather than through
  the header.

## 5. Result structure — what goes back to the CRM

Outbound mapping is the outbound half: up to four parts, each optional, filled from the source
system’s response after the write (in the operational skill). Of this pair’s 1 default template, 0
carry a parseable default outbound mapping, 1 carries none.

**No default template for this pair fills a part.** Nothing is written back to Salesforce by these
templates: the source system’s key stays in the writeback transaction log and the CRM record is left
as the user flagged it. If a tenant needs the key on the CRM record, outbound mapping is the column
to fill, and the operational skill gives its shape.

## 6. Verifying

Read the imported row before a run, not after. The operational skill is the authority on the
TxDownloaderPro tools and on the state they report.

The two failures this pair’s templates actually produce: a run that retrieves nothing, which is the
query’s own condition and not the mapping; and a record that arrives with fields empty, which is a
path that does not match the emitted document.

## 7. Where this sits

- dlake-txdownloaderpro — the parent: exposure, key scoping, the two tables, its state, the mapping
  columns, the filter vocabulary, the TxDownloaderPro tools. **Read it first.**
- dlake-txdownloaderpro-salesforce — the Salesforce destination page, which this page is a child of:
  the query shape, marker conventions and mapping conventions this destination uses across every
  ERP, and the ERP table that lists this page and its siblings.
- dlake-integration-setup — registration, CRM choice and the ERP connector, of which writeback is
  one step.
- dlake-crmpro and dlake-normalsync — the inbound leg, going the other way.
- dlake — general tenant operation.

This page describes the shipped default template set for this pair, and grows as that set does.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-txdownloaderpro-salesforce/erps/aptean-encompix`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
