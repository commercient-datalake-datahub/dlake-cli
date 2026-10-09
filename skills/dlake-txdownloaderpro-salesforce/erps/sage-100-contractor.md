---
name: dlake-txdownloaderpro-salesforce/erps/sage-100-contractor
kind: erp-summary
description: >-
  What the shipped default TxDownloaderPro templates set up when Salesforce is the writeback
  destination and Sage 100 Contractor is the source: the 16 default templates the catalogue ships
  for this pair, the 8 process versions they are identified by, what each template family delivers,
  the shape of the query that finds flagged records and the objects and marker columns it reads, the
  structure of the inbound mapping document, and which outbound mapping parts the templates fill for
  the write back to the CRM. It also lists the 22 community templates the catalogue carries for this
  pair. Use it when importing or reading this pair's template set, when deciding which templates to
  import and activate, or when a process runs and writes nothing and the answer is in the query or
  in the mapping document. It extends dlake-txdownloaderpro, which is the authority for operating
  TxDownloaderPro generally, and dlake-txdownloaderpro-salesforce, the destination skill this page
  is a child of, which carries the Salesforce conventions that hold across every ERP.
---
# TxDownloaderPro ← Salesforce — Sage 100 Contractor: what the shipped default templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-txdownloaderpro-salesforce/erps/sage-100-contractor` (or `list_skills`)
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
catalogue does. The community templates the catalogue also carries for this pair are listed in a
section of their own below, by count, object and name only.

## 1. What the templates deliver

One row per process the default set ships for this pair. The business language is the catalogue's
own, where it carries any; where it does not, the row says what the source and destination objects
are and what the operation flags allow. Operations are the union of the insert, update and delete
flags over that process's templates.

| Process | What it delivers | Source → destination | Templates | Operations |
|---|---|---|---|---|
| Create or Update Contact version 2019 | Salesforce Contact to Sage 100 Contractor Client contact | Contact → Client contact, Job contact | 4 | create / update |
| Create New Daily payroll version 2019 | — | Time sheet entry → Daily payroll | 2 | create / update |
| Create or Update Customer version 2019 | Salesforce Account to Sage 100 Contractor Customer | Account → Customer | 2 | create / update |
| Create or Update Job version 2019 | Salesforce Opportunity to Sage 100 Contractor 2019 Job | Opportunity → Job | 2 | create / update |
| Create or Update New Work order version 2019 | Salesforce Work order to Sage 100 Contractor Work order | Work order → Work order | 2 | create / update |
| (unnamed) | Salesforce Building to Sage 100 Contractor Client location | Building → Client location | 2 | create / update |
| Create New Employee version 2019 | Salesforce Contact to Sage 100 Contractor Employee | Contact → Employee | 1 | create |
| Create New Purchase order version 2019 | Salesforce Order to Sage 100 Contractor Purchase order | Order → Purchase order | 1 | create |

Across the 16 default templates: 9 carry insert, 7 carry update, 0 carry delete. A flag decides
which operation the process is allowed to perform, not which one it performs on a given record. 2
catalogue descriptions were not printed because they are placeholders or carry text that is not ours
to publish.

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

1 of these template rows carries a licence group, so what a given tenant is offered in the picker is
narrower than what the catalogue holds.

## 3. What the query retrieves

Query does not have one shape across the product (in the operational skill). For this pair, 16 carry
a query statement in the CRM's own query language. **No query text is reproduced here**; what
follows is what those queries read and filter on.

- **Objects read:** Building (custom object), Time sheet entry, Contact, order products, work order
  line items, account, Opportunity.
- **Child collections pulled in the same query:** order products, work order line items. A header
  retrieved without its lines is a query that does not name the child collection.
- **Marker and key columns the queries name:** External key (custom field), Commercient AR customer
  code, Commercient location identifier (custom field), Commercient external key. These are the
  columns a user’s flag lands in and the columns the run writes an outcome back to; which ones are
  in the filter is what decides whether a record is in scope at all.
- **Operators present:** `!=`, and, a test for an empty value, `=`, `>`. The operational skill is
  the authority on the vocabulary; the point here is only which of it these templates use.
- **Where the filtering happens:** in the query, on the CRM side, before anything reaches the source
  system. Narrowing a template means editing its query — not its mapping.

## 4. The inbound mapping document

Inbound mapping is a flat object: each member names a field on the source side and its value is a
template resolved against the retrieved record’s document (in the operational skill). Of this pair’s
16 default templates, 16 carry a default inbound mapping. A parseable document carries about 11
members.

- **Template path roots used:** Contact, Account, Work order, Building (custom object), Opportunity,
  Order, Time sheet entry, order products, work order line items. A path’s first segment has to
  match the element the engine emits, and the document root itself is never part of the path.
- **Line members present:** a collection member, line description, part reference. 3 templates name
  the collection through a collection member; the members beside it are resolved against that
  collection’s own root rather than through the header.

## 5. Result structure — what goes back to the CRM

Outbound mapping is the outbound half: up to four parts, each optional, filled from the source
system’s response after the write (in the operational skill). Of this pair’s 16 default templates,
11 carry a parseable default outbound mapping, 5 carry none.

| Part | Filled by | What it addresses | Members present |
|---|---|---|---|
| Part 1 | 9 templates (2 explicitly null) | the record the run is already working with | a map from source path to CRM field |
| Part 2 | 3 templates (8 explicitly null) | the line records under it | — |
| Part 3 | 0 templates (11 explicitly null) | a **new** record, matched on an external id field | — |
| Part 4 | 0 templates (11 explicitly null) | a **different** record, addressed by an id field | — |

- **CRM fields Part 1 writes to:** External key (custom field), Commercient external key, Line
  identifier (custom field), Sage work date (custom field), Commercient AR customer code. These are
  the fields on the flagged record that carry the source system’s key or outcome once the write has
  happened — the names only; what lands in them is the response, per record.
- **Response fields it reads them from:** Object identifier, returned line identifier, Location
  identifier, returned work date, Client identifier. The map is written **source path first, CRM
  field second** (in the operational skill); the wrong way round resolves to the same silent empty
  string as a mistyped path.

## 6. Community templates

The catalogue carries community templates for this pair as well as the default set above: templates
written on a tenant rather than shipped. Their content is not read and not described here — no
query, no mapping document, no field. What this section states is how many there are, which objects
they start from, what they are called where the name is a product artefact name, and which process
versions they belong to.

- **How many:** 22 community templates, across 3 process versions.
- **Operations:** 13 carry insert, 9 carry update, 0 carry delete. A flag decides which operation
  the template is allowed to perform, as it does for a default template.

| Source → destination | Templates |
|---|---|
| Opportunity → — | 11 |
| Contact → — | 7 |
| Customer → — | 4 |

None of these rows carries a destination object name in the catalogue, so the destination side of
every shape above is empty.

- **Template names the catalogue carries:** Create New Job (3), Create Contact for Job (2), Create
  New Customer (2), Update Contact for Job (2), Create Contact for Account (1), Create Customer (1),
  Create New Contact (1), Update Contact for Account (1), Update Customer (1), Update Job (1),
  Update New Job (1).
- **Names not reproduced:** 6 of these templates carry a name that is not a product artefact name,
  and it is not printed here.

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
  Fetch it with `dlake admin get_skill dlake-txdownloaderpro-salesforce/erps/sage-100-contractor`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
