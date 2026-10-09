---
name: dlake-txdownloaderpro-salesforce/erps/sage-100-us
kind: erp-summary
description: >-
  What the shipped default TxDownloaderPro templates set up when Salesforce is the writeback
  destination and Sage 100 (US) is the source: the 41 default templates the catalogue ships for this
  pair, the 19 process versions they are identified by, what each template family delivers, the
  shape of the query that finds flagged records and the objects and marker columns it reads, the
  structure of the inbound mapping document, and which outbound mapping parts the templates fill for
  the write back to the CRM. It also lists the 151 community templates the catalogue carries for
  this pair. Use it when importing or reading this pair's template set, when deciding which
  templates to import and activate, or when a process runs and writes nothing and the answer is in
  the query or in the mapping document. It extends dlake-txdownloaderpro, which is the authority for
  operating TxDownloaderPro generally, and dlake-txdownloaderpro-salesforce, the destination skill
  this page is a child of, which carries the Salesforce conventions that hold across every ERP.
---
# TxDownloaderPro ← Salesforce — Sage 100 (US): what the shipped default templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-txdownloaderpro-salesforce/erps/sage-100-us` (or `list_skills`) against
the Commercient admin plane. Existing customers who need access or help: contact
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
| Create New Sales Order | Salesforce Opportunity to Sage 100 Sales Order | Opportunity, Order, Quote → Order, Sales order | 6 | create / update |
| (unnamed) | Salesforce Opportunity To Update Sage 100 AR Invoice | Opportunity, Opportunity, Quote → AR invoice, Invoice, Salesorder | 6 | create / update |
| Create New Sales Invoice | Salesforce Work order To Sage 100 Invoice | Work order, Opportunity, Order → Invoice | 3 | create |
| Delete Sales Invoice | Salesforce Order to Sage 100 Invoice - Delete Invoice | Order, Quote, Opportunity → Invoice | 3 | delete |
| Delete Sales Order | Salesforce Order to Sage 100 Sales Order - Delete Sales Order | Order, Quote, Opportunity → Sales Order | 3 | delete |
| Update Sales Order Header Only | Update Sales order Header from Sage 100 Opportunity | Opportunity, Order, Quote → Sales order, Sales order | 3 | update |
| Create and Update Contact | Sage 100 Account(child) To Sage 100 Contact | Account, Contact → Contact, Contact | 2 | create |
| Create New Customer | Salesforce Account To Update Sage 100 Customer | Account → Customer | 2 | create / update |
| Create new Product | Salesforce Product To Update Product Sage 100 | Product → Common information item | 2 | create / update |
| Create Purchase Order | Salesforce Order to Sage 100 Purchase Order | Order → Purchase Order | 2 | create / update |
| Create New Ship To Address | Salesforce Account(shipping Address) To Sage 100 Shipping address | Account → Shipping address | 1 | create |
| Create Return merchandise authorization | Salesforce Order to Sage 100 Return merchandise authorization | Order → Return merchandise authorization | 1 | create |
| Create Service Order | Salesforce Work order to Sage 100 Service Order | Work order → Service Order | 1 | create |
| Create Vendor | Salesforce Account to Sage 100 Vendor | Account → Vendor | 1 | create |
| Delete Product | Salesforce Product to Sage 100 Item - Delete Item | Product → Item | 1 | delete |
| Delete Purchase Order | Salesforce Order to Sage 100 Purchase Order - Delete Purchase Order | Order → Purchase Order | 1 | delete |
| Delete Ship To Address | Salesforce Account to Sage 100 Ship To Address - Delete Ship To Address | Account → Shipping address | 1 | delete |
| (unnamed) | Salesforce Order to Sage 100 AP Invoice | Order → AP invoice | 1 | create |
| Update Ship To Address | Salesforce Account to Sage 100 Ship To Address - Update Ship To Address | Account → Shipping address | 1 | update |

Across the 41 default templates: 20 carry insert, 12 carry update, 9 carry delete. A flag decides
which operation the process is allowed to perform, not which one it performs on a given record. 1
catalogue description was not printed because it is a placeholder or carries text that is not ours
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

9 of these template rows carry a licence group, so what a given tenant is offered in the picker is
narrower than what the catalogue holds.

## 3. What the query retrieves

Query does not have one shape across the product (in the operational skill). For this pair, 41 carry
a query statement in the CRM's own query language. **No query text is reproduced here**; what
follows is what those queries read and filter on.

- **Objects read:** Account, contact, order products, opportunity line items, quote line items,
  Product, work order line items, order line items, Order, Quote, Opportunity.
- **Child collections pulled in the same query:** order products, opportunity line items, quote line
  items, work order line items, order line items. A header retrieved without its lines is a query
  that does not name the child collection.
- **Marker and key columns the queries name:** Commercient AR customer code, External key (custom
  field), Commercient external key (earlier package), Commercient external key column. These are the
  columns a user’s flag lands in and the columns the run writes an outcome back to; which ones are
  in the filter is what decides whether a record is in scope at all.
- **Operators present:** `=`, and, a null test, a test for an empty value, `!=`. The operational
  skill is the authority on the vocabulary; the point here is only which of it these templates use.
- **Where the filtering happens:** in the query, on the CRM side, before anything reaches the source
  system. Narrowing a template means editing its query — not its mapping.

## 4. The inbound mapping document

Inbound mapping is a flat object: each member names a field on the source side and its value is a
template resolved against the retrieved record’s document (in the operational skill). Of this pair’s
41 default templates, 41 carry a default inbound mapping; 2 of those do not parse as and are counted
but not described. A parseable document carries about 15 members.

- **Template path roots used:** Order, Opportunity, Quote, Account, opportunity line items, order
  products, quote line items, Work order, order line items, work order line items, contact, Product,
  External key (custom field). A path’s first segment has to match the element the engine emits, and
  the document root itself is never part of the path.
- **Line members present:** a collection member, line item code, line item description, line
  quantity ordered, line comment, line unit price, line discount, line number, line quantity, line
  price, line cost, unit cost, extension amount, line quantity shipped, line discount percent,
  quantity distributed, distribution amount, sales account key, quantity returned, invoice unit
  price, and 4 more. 18 templates name the collection through a collection member; the members
  beside it are resolved against that collection’s own root rather than through the header.

## 5. Result structure — what goes back to the CRM

Outbound mapping is the outbound half: up to four parts, each optional, filled from the source
system’s response after the write (in the operational skill). Of this pair’s 41 default templates,
20 carry a parseable default outbound mapping, 21 carry none.

| Part | Filled by | What it addresses | Members present |
|---|---|---|---|
| Part 1 | 20 templates | the record the run is already working with | a map from source path to CRM field |
| Part 2 | 6 templates (14 explicitly null) | the line records under it | — |
| Part 3 | 0 templates (20 explicitly null) | a **new** record, matched on an external id field | — |
| Part 4 | 0 templates (20 explicitly null) | a **different** record, addressed by an id field | — |

- **CRM fields Part 1 writes to:** External key (custom field), Commercient AR customer code,
  External key (second custom field), External key field, Commercient external key column, Sage
  invoice number (custom field), External key (second custom field). These are the fields on the
  flagged record that carry the source system’s key or outcome once the write has happened — the
  names only; what lands in them is the response, per record.
- **Response fields it reads them from:** Invoice number, returned sales order number, Customer
  number, AR division number, Item code, returned shipping address code, returned purchase order
  number, returned return authorization number, returned service order number. The map is written
  **source path first, CRM field second** (in the operational skill); the wrong way round resolves
  to the same silent empty string as a mistyped path.

## 6. Community templates

The catalogue carries community templates for this pair as well as the default set above: templates
written on a tenant rather than shipped. Their content is not read and not described here — no
query, no mapping document, no field. What this section states is how many there are, which objects
they start from, what they are called where the name is a product artefact name, and which process
versions they belong to.

- **How many:** 151 community templates, across 19 process versions.
- **Operations:** 98 carry insert, 44 carry update, 5 carry delete. A flag decides which operation
  the template is allowed to perform, as it does for a default template.

| Source → destination | Templates |
|---|---|
| Customer → — | 52 |
| Sales Order → — | 32 |
| Customer Contact → — | 25 |
| Product → — | 12 |
| Vendor → — | 8 |
| Shipping address → — | 6 |
| Job → — | 4 |
| Sales Invoice → — | 3 |
| AR invoice → — | 2 |
| Return merchandise authorization → — | 2 |
| Service Order → — | 2 |
| AR invoice → — | 1 |
| Cash receipt → — | 1 |
| Payment → — | 1 |

None of these rows carries a destination object name in the catalogue, so the destination side of
every shape above is empty.

- **Template names the catalogue carries:** Create New Customer (17), Create Customer (9), Create
  New Sales Order (9), Update Customer (8), Create Sales Order (7), Update Contact (5), Update
  Product (5), Create Contact (4), Create New Contact (4), Create New Product (4), Create New Ship
  to Address (4), Create New Vendor (4), Create or Update New Job (4), Update Sales Order (4),
  Update Vendor (4), Delete Contact (3), Create new Product (2), Create New Return merchandise
  authorization (2), New Contact (2), Create and Update Cash Receipt (1), Create and Update Customer
  (1), Create and Update Quotes (1), Create and Update Sales Order (1), Create customer contact (1),
  and 31 more.
- **Names not reproduced:** 14 of these templates carry a name that is not a product artefact name,
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
  Fetch it with `dlake admin get_skill dlake-txdownloaderpro-salesforce/erps/sage-100-us`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
