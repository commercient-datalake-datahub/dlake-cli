---
name: dlake-txdownloaderpro-salesforce/erps/quickbooks-desktop
kind: erp-summary
description: >-
  What the shipped default TxDownloaderPro templates set up when Salesforce is the writeback
  destination and QuickBooks Desktop is the source: the 45 default templates the catalogue ships for
  this pair, the 20 process versions they are identified by, what each template family delivers, the
  shape of the query that finds flagged records and the objects and marker columns it reads, the
  structure of the inbound mapping document, and which outbound mapping parts the templates fill for
  the write back to the CRM. It also lists the 74 community templates the catalogue carries for this
  pair. Use it when importing or reading this pair's template set, when deciding which templates to
  import and activate, or when a process runs and writes nothing and the answer is in the query or
  in the mapping document. It extends dlake-txdownloaderpro, which is the authority for operating
  TxDownloaderPro generally, and dlake-txdownloaderpro-salesforce, the destination skill this page
  is a child of, which carries the Salesforce conventions that hold across every ERP.
---
# TxDownloaderPro ← Salesforce — QuickBooks Desktop: what the shipped default templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-txdownloaderpro-salesforce/erps/quickbooks-desktop` (or `list_skills`)
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
| Sales Invoice Import | Commercient sync's your CRM Invoice with item list and creates standard Order records in the ERP. The Customer's ship to and a bill to addresses are imported and mapped to the ERP fields. | Opportunity, Order, Quote → Invoice | 6 | create / update |
| Sales Order Import | Commercient sync's your CRM Orders with item list and creates standard Order records in the ERP. The Customer's ship to and a bill to addresses are imported and mapped to the ERP fields. | Opportunity, Order, Quote → Order | 4 | create / update |
| Create Item and Sales Order | Salesforce Opportunity to QuickBooks Sales order | Opportunity, Order, Quote → QuickBooks Sales Order | 3 | create / update |
| Customer Import | Commercient sync's your CRM Accounts and creates Receivable Customer (AR Customer) list in the ERP. The AR Customer's ship to and a bill to addresses are imported and mapped to the ERP fields. | Account, Customer → Customer | 3 | create / update |
| New Bill Import | Commercient sync's your CRM Orders with item list and creates standard Order records in the ERP. The Customer's ship to and a bill to addresses are imported and mapped to the ERP fields. | Opportunity, Order, Quote → Bill | 3 | create / update |
| Purchase Order Import | Commercient sync's your CRM Purchase Orders with item list and creates standard Order records in the ERP. The Customer's ship to and a bill to addresses are imported and mapped to the ERP fields. | Opportunity, Order, Quote → Purchase Order | 3 | create / update |
| Vendor Credit Memo Import | Commercient sync's your CRM Orders with item list and creates standard Credit Memo records in the ERP. The Customer's ship to and a bill to addresses are imported and mapped to the ERP fields. | Opportunity, Order, Quote → Vendor Credit Memo | 3 | create / update |
| Create New Customer with Contact | Salesforce Account to QuickBooks Customer Contact | Account → Customer, Customer Contact | 2 | create / update |
| Create New Lead | Salesforce Lead to QuickBooks Lead | Lead → Lead | 2 | create / update |
| Create New Non Inventory Item | Salesforce Product to QuickBooks Non Inventory Item | Product → Non inventory item | 2 | create / update |
| Create New Sales Receipt | Salesforce Order to QuickBooks Sales Receipt | Order → Sales receipt | 2 | create / update |
| Create or Update Payment | — | Order → Payment | 2 | create / update |
| Create New Estimate | Salesforce Quote to QuickBooks Estimate | Quote → Estimate | 2 | create / update |
| New Job Import | Commercient sync's your CRM Accounts and creates Receivable Customer (AR Customer) list in the ERP. The AR Customer's ship to and a bill to addresses are imported and mapped to the ERP fields. | Account → Customer, Job | 2 | create / update |
| Customer Ship To Address | Salesforce Account to QuickBooks Customer Ship To Address | Account → Ship to | 1 | update |
| Delete List Entry | Salesforce Account to QuickBooks Customer - Delete List Entry | Account → List Entry | 1 | delete |
| Delete Transaction Entry | Salesforce Order to QuickBooks Sales Order - Delete Transaction Entry | Order → Transaction Entry | 1 | delete |
| Inventory Product Import | Commercient sync's your CRM products or items with item list and creates standard product or item records in the ERP. Item stock, item related taxes, item unit price, unit of measure, description will be imported to its an equivalent field of ERP. | Product → Inventory Product | 1 | create / update |
| New Vendor Import | Commercient sync's your CRM Accounts and creates Receivable Customer (AR Customer) list in the ERP. The AR Customer's ship to and a bill to addresses are imported and mapped to the ERP fields. | Account → Vendor | 1 | create / update |
| Service Product Import | Salesforce Product to QuickBooks Service Product | Product → Service Product | 1 | create / update |

Across the 45 default templates: 31 carry insert, 35 carry update, 2 carry delete. A flag decides
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

10 of these template rows carry a licence group, so what a given tenant is offered in the picker is
narrower than what the catalogue holds.

## 3. What the query retrieves

Query does not have one shape across the product (in the operational skill). For this pair, 45 carry
a query statement in the CRM's own query language. **No query text is reproduced here**; what
follows is what those queries read and filter on.

- **Objects read:** opportunity line items, order products, quote line items, Account, Lead,
  Product, Order, Commercient Customer Managed Custom Object, Commercient sales order lines (related
  records).
- **Child collections pulled in the same query:** opportunity line items, order products, quote line
  items, Commercient sales order lines (related records). A header retrieved without its lines is a
  query that does not name the child collection.
- **Marker and key columns the queries name:** Commercient AR customer code, Commercient external
  key (custom field), Commercient edit sequence (custom field), Commercient account (related
  record), Commercient account number, Commercient alternate contact, Commercient alternate phone,
  Commercient balance, Commercient billing address line 1, Commercient billing address line 2,
  Commercient billing address line 3, Commercient billing address line 4, Commercient billing
  address line 5, Commercient billing address block line 1, Commercient billing address block line
  2, Commercient billing address block line 3, Commercient billing address block line 4, Commercient
  billing address block line 5, Commercient billing city, Commercient billing country, and 148 more.
  These are the columns a user’s flag lands in and the columns the run writes an outcome back to;
  which ones are in the filter is what decides whether a record is in scope at all.
- **Operators present:** `!=`, a test for an empty value, `=`. The operational skill is the
  authority on the vocabulary; the point here is only which of it these templates use.
- **Where the filtering happens:** in the query, on the CRM side, before anything reaches the source
  system. Narrowing a template means editing its query — not its mapping.

## 4. The inbound mapping document

Inbound mapping is a flat object: each member names a field on the source side and its value is a
template resolved against the retrieved record’s document (in the operational skill). Of this pair’s
45 default templates, 43 carry a default inbound mapping and 2 carry none. A parseable document
carries about 14 members.

- **Template path roots used:** Order, Quote, Account, Opportunity, order products, quote line
  items, opportunity line items, Product, Lead. A path’s first segment has to match the element the
  engine emits, and the document root itself is never part of the path.
- **Line members present:** line quantity, line rate, line description, line amount, item reference,
  item list identifier, line unit of measure, a collection member, item reference full name. 4
  templates name the collection through a collection member; the members beside it are resolved
  against that collection’s own root rather than through the header.

## 5. Result structure — what goes back to the CRM

Outbound mapping is the outbound half: up to four parts, each optional, filled from the source
system’s response after the write (in the operational skill). Of this pair’s 45 default templates, 7
carry a parseable default outbound mapping, 36 carry none, and 2 do not parse and are counted but
not described. 1 parse but are not in the four part shape at all — a flat field document where the
parts should be.

| Part | Filled by | What it addresses | Members present |
|---|---|---|---|
| Part 1 | 6 templates | the record the run is already working with | a map from source path to CRM field |
| Part 2 | 0 templates (6 explicitly null) | the line records under it | — |
| Part 3 | 0 templates (6 explicitly null) | a **new** record, matched on an external id field | — |
| Part 4 | 0 templates (6 explicitly null) | a **different** record, addressed by an id field | — |

- **CRM fields Part 1 writes to:** Commercient external key (custom field), Commercient AR customer
  code. These are the fields on the flagged record that carry the source system’s key or outcome
  once the write has happened — the names only; what lands in them is the response, per record.
- **Response fields it reads them from:** returned list identifier, new customer code. The map is
  written **source path first, CRM field second** (in the operational skill); the wrong way round
  resolves to the same silent empty string as a mistyped path.

## 6. Community templates

The catalogue carries community templates for this pair as well as the default set above: templates
written on a tenant rather than shipped. Their content is not read and not described here — no
query, no mapping document, no field. What this section states is how many there are, which objects
they start from, what they are called where the name is a product artefact name, and which process
versions they belong to.

- **How many:** 74 community templates, across 14 process versions.
- **Operations:** 47 carry insert, 15 carry update, 0 carry delete. A flag decides which operation
  the template is allowed to perform, as it does for a default template.

| Source → destination | Templates |
|---|---|
| Customer → — | 30 |
| Sales Invoice → — | 11 |
| Sales Order → — | 9 |
| Job → — | 6 |
| Inventory Product → — | 5 |
| Service Product → — | 4 |
| Bill → — | 2 |
| Credit Memo → — | 1 |
| Deposit → — | 1 |
| Invoice → — | 1 |
| Payment → — | 1 |
| Purchase Order → — | 1 |
| Sales receipt → — | 1 |
| a custom object → — | 1 |

None of these rows carries a destination object name in the catalogue, so the destination side of
every shape above is empty.

- **Template names the catalogue carries:** Create New Customer (8), Update Customer (6), Create New
  Invoice (5), Create Customer (4), Customer Import (4), Create New Account (3), Create New Sales
  Order (3), Create New Product (2), Create New Service Product (2), Create Customer Job (1), Create
  Customer Job Project (1), Create Item and Sales Order (1), Create New Account from Contact (1),
  Create New Customer with Contact (1), Create New Deposit (1), Create New Inventory Product (1),
  Create New Purchase Order (1), Create New Sales Invoice (1), Create New Sales Receipt (1), Create
  Order and Item (1), Create Sales Order (1), Inventory Product Import (1), New Account (1), New
  Accounts (1), and 10 more.
- **Names not reproduced:** 12 of these templates carry a name that is not a product artefact name,
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
  Fetch it with `dlake admin get_skill dlake-txdownloaderpro-salesforce/erps/quickbooks-desktop`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
