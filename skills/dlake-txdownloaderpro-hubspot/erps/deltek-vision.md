---
name: dlake-txdownloaderpro-hubspot/erps/deltek-vision
kind: erp-summary
description: >-
  What the shipped default TxDownloaderPro templates set up when HubSpot is the writeback
  destination and Deltek Vision is the source: the 7 default templates the catalogue ships for this
  pair, the 6 process versions they are identified by, what each template family delivers, the shape
  of the query that finds flagged records and the objects and marker columns it reads, the structure
  of the inbound mapping document, and which outbound mapping parts the templates fill for the write
  back to the CRM. Use it when importing or reading this pair's template set, when deciding which
  templates to import and activate, or when a process runs and writes nothing and the answer is in
  the query or in the mapping document. It extends dlake-txdownloaderpro, which is the authority for
  operating TxDownloaderPro generally, and dlake-txdownloaderpro-hubspot, the destination skill this
  page is a child of, which carries the HubSpot conventions that hold across every ERP.
---
# TxDownloaderPro ← HubSpot — Deltek Vision: what the shipped default templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-txdownloaderpro-hubspot/erps/deltek-vision` (or `list_skills`) against
the Commercient admin plane. Existing customers who need access or help: contact
support@commercient.com. New customers: contact sales@commercient.com to become a customer and be
whitelisted.

dlake-txdownloaderpro-hubspot is the HubSpot page and carries the destination detail: it is the
skill this page is a child of, and the authority for the conventions that hold across every ERP, so
read it before this page.

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
| Opportunity | HubSpot deals to Deltek Vision Opportunity | deals → Opportunity | 2 | create / update |
| Create Contacts | HubSpot contacts to Deltek Vision Contact | contacts → Contacts | 1 | create |
| Create New Client | HubSpot companies to Deltek Vision Client | companies → Client | 1 | create |
| Projects | HubSpot deals to Deltek Vision Project | deals → Projects | 1 | create |
| Update Client | HubSpot Update companies to Deltek Vision Update Client | companies → Client | 1 | update |
| Update Contact | HubSpot Update contacts to Deltek Vision Update Contact | contacts → Contacts | 1 | update |

Across the 7 default templates: 4 carry insert, 3 carry update, 0 carry delete. A flag decides which
operation the process is allowed to perform, not which one it performs on a given record.

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

Query does not have one shape across the product (in the operational skill). For this pair, 7 carry
an object naming the module to retrieve. **No query text is reproduced here**; what follows is what
those queries read and filter on.

- **Objects read:** contacts, companies, deals.
- **Members present in the query object:** selected fields (7), module name (7), filter (7). Where a
  filter member is present it is empty. 7 carry a populated selected fields list.
- **Where the filtering happens:** the query names a module rather than a condition, so the
  selection the operational skill describes is applied after retrieval, not by the query.

## 4. The inbound mapping document

Inbound mapping is a flat object: each member names a field on the source side and its value is a
template resolved against the retrieved record’s document (in the operational skill). Of this pair’s
7 default templates, 7 carry a default inbound mapping. A parseable document carries about 10
members.

- **Template path roots used:** contacts, deals, companies. A path’s first segment has to match the
  element the engine emits, and the document root itself is never part of the path.
- **No line section.** These templates map a single record, with no repeating child collection.

## 5. Result structure — what goes back to the CRM

Outbound mapping is the outbound half: up to four parts, each optional, filled from the source
system’s response after the write (in the operational skill). Of this pair’s 7 default templates, 4
carry a parseable default outbound mapping, 3 carry none.

| Part | Filled by | What it addresses | Members present |
|---|---|---|---|
| Part 1 | 4 templates | the record the run is already working with | a map from source path to CRM field |
| Part 2 | 0 templates (4 explicitly null) | the line records under it | — |
| Part 3 | 0 templates (4 explicitly null) | a **new** record, matched on an external id field | — |
| Part 4 | 0 templates (4 explicitly null) | a **different** record, addressed by an id field | — |

- **CRM fields Part 1 writes to:** external key property, arcustomercode. These are the fields on
  the flagged record that carry the source system’s key or outcome once the write has happened — the
  names only; what lands in them is the response, per record.
- **Response fields it reads them from:** Opportunity identifier, Contact identifier, Client
  identifier. The map is written **source path first, CRM field second** (in the operational skill);
  the wrong way round resolves to the same silent empty string as a mistyped path.

## 6. Verifying

Read the imported row before a run, not after. The operational skill is the authority on the
TxDownloaderPro tools and on the state they report.

The two failures this pair’s templates actually produce: a run that retrieves nothing, which is the
module the query names, since these queries carry no condition of their own; and a record that
arrives with fields empty, which is a path that does not match the emitted document.

## 7. Where this sits

- dlake-txdownloaderpro — the parent: exposure, key scoping, the two tables, its state, the mapping
  columns, the filter vocabulary, the TxDownloaderPro tools. **Read it first.**
- dlake-txdownloaderpro-hubspot — the HubSpot destination page, which this page is a child of: the
  query shape, marker conventions and mapping conventions this destination uses across every ERP,
  and the ERP table that lists this page and its siblings.
- dlake-integration-setup — registration, CRM choice and the ERP connector, of which writeback is
  one step.
- dlake-crmpro and dlake-normalsync — the inbound leg, going the other way.
- dlake — general tenant operation.

This page describes the shipped default template set for this pair, and grows as that set does.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-txdownloaderpro-hubspot/erps/deltek-vision`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
