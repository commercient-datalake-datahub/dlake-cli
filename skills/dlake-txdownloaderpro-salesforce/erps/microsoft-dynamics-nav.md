---
name: dlake-txdownloaderpro-salesforce/erps/microsoft-dynamics-nav
kind: erp-summary
description: >-
  What the shipped default TxDownloaderPro templates set up when Salesforce is the writeback
  destination and Microsoft Dynamics NAV is the source: the 16 default templates the catalogue ships
  for this pair, the 9 process versions they are identified by, what each template family delivers,
  the shape of the query that finds flagged records and the objects and marker columns it reads, the
  structure of the inbound mapping document, and which outbound mapping parts the templates fill for
  the write back to the CRM. It also lists the 49 community templates the catalogue carries for this
  pair. Use it when importing or reading this pair's template set, when deciding which templates to
  import and activate, or when a process runs and writes nothing and the answer is in the query or
  in the mapping document. It extends dlake-txdownloaderpro, which is the authority for operating
  TxDownloaderPro generally, and dlake-txdownloaderpro-salesforce, the destination skill this page
  is a child of, which carries the Salesforce conventions that hold across every ERP.
---
# TxDownloaderPro ← Salesforce — Microsoft Dynamics NAV: what the shipped default templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-txdownloaderpro-salesforce/erps/microsoft-dynamics-nav` (or
`list_skills`) against the Commercient admin plane. Existing customers who need access or help:
contact support@commercient.com. New customers: contact sales@commercient.com to become a customer
and be whitelisted.

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
| Create Blanket Order | Salesforce NAV order header to Microsoft Dynamics NAV Blanket Sales Order | Microsoft Dynamics NAV Order Header → Blanket sales order | 2 | create / update |
| Create Contact | Salesforce Contact To Contact Microsoft Dynamics NAV | Contact → contact | 2 | create / update |
| Create Customer | Salesforce Account To Customer Microsoft Dynamics NAV | Account → Customer | 2 | create / update |
| Create Customer Address | Salesforce NAV address to Microsoft Dynamics NAV Shipping Address | Microsoft Dynamics NAV Address → Shipping address | 2 | create / update |
| Create Opportunity | Salesforce Opportunity to Microsoft Dynamics NAV Opportunity | Opportunity → Opportunity | 2 | create / update |
| Create Quote | Salesforce Quote to Microsoft Dynamics NAV Sales Quote | Quote → Sales quote | 2 | create / update |
| Create Sales order Return | Salesforce NAV order header to Microsoft Dynamics NAV Sales Return Order | Microsoft Dynamics NAV Order Header → Sales return order | 2 | create / update |
| Create Customer comment | — | — | 1 | create |
| Create Order comment | — | Ordercomment → Ordercomment | 1 | create |

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

- **Objects read:** Commercient Dynamics NAV order headers (related records), contact, Account,
  Commercient Dynamics NAV Address Managed Custom Object, Comment (custom object), Opportunity,
  quote line items.
- **Child collections pulled in the same query:** Commercient Dynamics NAV order headers (related
  records), quote line items. A header retrieved without its lines is a query that does not name the
  child collection.
- **Marker and key columns the queries name:** Commercient document type, Commercient external key
  (Dynamics NAV package), Commercient number, Commercient sell to customer number, Commercient sell
  to customer name, Commercient sell to address, Commercient sell to city, Commercient sell to
  county, Commercient sell to post code, Commercient sell to country or region code, Commercient
  your reference, Commercient order date, Commercient due date, Commercient currency code,
  Commercient salesperson code, Commercient location code, Commercient payment terms code,
  Commercient bill to name, Commercient bill to address, Commercient bill to city, and 27 more.
  These are the columns a user’s flag lands in and the columns the run writes an outcome back to;
  which ones are in the filter is what decides whether a record is in scope at all.
- **Operators present:** `=`, and, a test for an empty value, `!=`, a null test, `>`. The
  operational skill is the authority on the vocabulary; the point here is only which of it these
  templates use.
- **Where the filtering happens:** in the query, on the CRM side, before anything reaches the source
  system. Narrowing a template means editing its query — not its mapping.

## 4. The inbound mapping document

Inbound mapping is a flat object: each member names a field on the source side and its value is a
template resolved against the retrieved record’s document (in the operational skill). Of this pair’s
16 default templates, 16 carry a default inbound mapping. A parseable document carries about 14
members.

- **Template path roots used:** Commercient Dynamics NAV Order Header Managed Custom Object, Quote,
  Commercient Dynamics NAV Address Managed Custom Object, Contact, Account, Commercient Dynamics NAV
  order headers (related records), Comment (custom object), quote line items, Opportunity. A path’s
  first segment has to match the element the engine emits, and the document root itself is never
  part of the path.
- **Line members present:** a collection member, line type, line item number, line description, line
  quantity, line unit price. 6 templates name the collection through a collection member; the
  members beside it are resolved against that collection’s own root rather than through the header.

## 5. Result structure — what goes back to the CRM

Outbound mapping is the outbound half: up to four parts, each optional, filled from the source
system’s response after the write (in the operational skill). Of this pair’s 16 default templates, 9
carry a parseable default outbound mapping, 7 carry none.

| Part | Filled by | What it addresses | Members present |
|---|---|---|---|
| Part 1 | 9 templates | the record the run is already working with | a map from source path to CRM field |
| Part 2 | 0 templates (9 explicitly null) | the line records under it | — |
| Part 3 | 0 templates (9 explicitly null) | a **new** record, matched on an external id field | — |
| Part 4 | 0 templates (9 explicitly null) | a **different** record, addressed by an id field | — |

- **CRM fields Part 1 writes to:** Commercient external key (Dynamics NAV package), External key
  (custom field), Dynamics key (custom field), Commercient AR customer code. These are the fields on
  the flagged record that carry the source system’s key or outcome once the write has happened — the
  names only; what lands in them is the response, per record.
- **Response fields it reads them from:** No, Key, returned customer number, Code. The map is
  written **source path first, CRM field second** (in the operational skill); the wrong way round
  resolves to the same silent empty string as a mistyped path.

## 6. Community templates

The catalogue carries community templates for this pair as well as the default set above: templates
written on a tenant rather than shipped. Their content is not read and not described here — no
query, no mapping document, no field. What this section states is how many there are, which objects
they start from, what they are called where the name is a product artefact name, and which process
versions they belong to.

- **How many:** 49 community templates, across 11 process versions.
- **Operations:** 25 carry insert, 24 carry update, 0 carry delete. A flag decides which operation
  the template is allowed to perform, as it does for a default template.

| Source → destination | Templates |
|---|---|
| Sales order → — | 8 |
| Contact → — | 5 |
| Opportunity → — | 4 |
| Shipping address → — | 4 |
| Blanket sales order → — | 2 |
| Customer Card → — | 2 |
| Customer → — | 2 |
| Customer comment → — | 2 |
| Order comment → — | 2 |
| Quote → — | 2 |
| Sales quote → — | 2 |
| Sales return order → — | 2 |
| Sales order return → — | 2 |
| Tracking Line → — | 2 |
| Tracking line → — | 2 |
| Update Blanket Order → — | 2 |

None of these rows carries a destination object name in the catalogue, so the destination side of
every shape above is empty.

- **Template names the catalogue carries:** Create Contact Card (2), Create New Blanket Order (2),
  Create New Customer (2), Create New Customer Comment (2), Create New Opportunity (2), Create New
  Order Comment (2), Create New Quote (2), Create New Return merchandise authorization (2), Create
  New Sales Order (2), Create New Ship to Address (2), Create New Tracking Line (2), Create Sales
  order from Salesforce (2), Update Blanket Order (2), Update Contact (2), Update Customer (2),
  Update Customer Comment (2), Update Opportunity (2), Update Order Comment (2), Update Quote (2),
  Update Return merchandise authorization (2), Update Sales Order (2), Update Ship To Address (2),
  Update Tracking Line (2), Create Contact card (1), and 2 more.

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
  Fetch it with `dlake admin get_skill dlake-txdownloaderpro-salesforce/erps/microsoft-dynamics-nav`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
