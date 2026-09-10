---
name: dlake-txdownloaderpro-salesforce
description: >-
  What the shipped default TxDownloaderPro templates set up when Salesforce is the writeback
  destination: the `SELECT` query shape that finds the records a user flagged, the marker and
  external-key column conventions those queries read and the run writes back to, the flat
  `ProcessStructure` mapping with its `Line.` section and `Line.mainXml` collection member, the
  `$FUN_` value-token names the mapping documents carry, which `ResultStructure` parts the
  templates fill for the write back to Salesforce, and the `TxDownloaderPro` process row each
  template becomes on import. Use it when importing or reading a Salesforce writeback template
  set, when a process runs and writes nothing, when a mapped field arrives empty, or when
  deciding whether a change belongs in a query or in a mapping. It extends
  `dlake-txdownloaderpro`, which covers operating TxDownloaderPro generally; the per-ERP pages
  `dlake-txdownloaderpro-<erp>-salesforce` carry each ERP's own default template set.
---

# TxDownloaderPro ← Salesforce: what the shipped default templates set up

> **Keep this skill current.** `dlake` ships updates often and this text is embedded in the CLI
> you have installed. Before relying on it, compare `dlake --version` with
> `npm view @commercient/dlake version`; if they differ, run `npm install -g @commercient/dlake@latest`
> and then `dlake skills install`, which overwrites the installed skill files with the current text.

`dlake-txdownloaderpro` is the parent skill and the authority for everything general: what the
writeback objects are and how they are exposed to the Data API (§1–§7), how a key is scoped to
them, the `TxDownloaderPro` configuration table and the `TxDownloaderProTrans` transaction table
and their columns (§9, §10), the `SFUpdated` state machine and its transitions (§10), the
field-mapping columns — `ProcessStructure`, `ResultStructure`, `XMLResult`, `ERPResponse`,
`JsonRequest`, `JsonResponse` — and the `{{Object.Field}}` template paths that read them (§11),
the filter-operator vocabulary (§12), and the `txdownloaderpro_*` tools that are the preferred
way to configure any of it (§14). Read it first; this page does not repeat it.

What follows is only what the shipped **default** templates for this destination themselves set,
described from their `DefaultQuery`, `DefaultProcessStructure` and `DefaultResultStructure`
columns. It is a description of structure — which objects and marker columns are read, which
parts and members are filled, which token names appear. **No template text is reproduced.** This page grows
as the default catalogue does.

Salesforce is the largest destination in the default set: **295 default templates across 38 ERP
names, in 149 field-process versions**. The CRM catalogue registers Salesforce with OAuth
authentication and a loopback callback. Every ERP that ships default templates for Salesforce
has its own page, `dlake-txdownloaderpro-<erp>-salesforce`.

## 1. What the templates deliver

Every Salesforce default template is a **writeback**: a user flags a record in Salesforce, the
process picks it up, and the record is created, updated or removed in the customer's source
system. The families the default set covers, and the operations the `IsInsert` / `IsUpdate` /
`IsDelete` flags on those templates allow:

| Transaction family | What the templates deliver | Operations the flags carry |
|---|---|---|
| **Customer / account** | A Salesforce `Account` becomes a receivable customer in the ERP; the account's bill-to and ship-to addresses travel with it, and the ERP's customer code comes back onto the account | create, update, and a small delete set |
| **Contact** | A `Contact` under an already-synced account becomes an ERP customer contact | create, update |
| **Sales order** | An `Opportunity`, `Quote` or `Order` with its line items becomes an ERP sales order, and the ERP's order number comes back onto the CRM record | create, update, delete |
| **Quote / opportunity / estimate** | The same shape aimed at the ERP's quote or estimate object rather than its order | create, update, delete |
| **Invoice** | An `Order`, `Quote` or `WorkOrder` becomes a receivable invoice, with lines | create, update |
| **Product / item / pricebook** | `Product2` and `PricebookEntry` records become ERP items and prices | create, update |
| **Vendor / purchase** | An `Account` becomes an ERP vendor; purchase orders follow the order shape | create, update |
| **Job / project / work order / service order** | `WorkOrder`, project and timesheet records become the ERP's job, service-order or payroll object | create, update |
| **Ship-to address** | A shipping address on the account, or a managed-package ship-to object, becomes an ERP ship-to address | create, update |

Across the 295 default templates, **194 carry `IsInsert`, 109 carry `IsUpdate` and 23 carry
`IsDelete`**. A flag decides which operation a process is *allowed* to perform, not which one it
performs on a given record. Delete templates are the rare case, and they are the ones whose query
selects on a deletion marker rather than an import marker — section 3.

The catalogue attaches a `BusinessDescription` to a small number of Salesforce default templates,
and only to Salesforce. Where a template has one, its leading sentences are what the per-ERP page
prints: the product's own statement that Commercient syncs the CRM's accounts, products, orders,
purchase orders, invoices and credit memos into the equivalent standard ERP records, with the
bill-to and ship-to addresses mapped onto the ERP's address fields. The later sentences of those
texts address the customer directly rather than describing the template, and are not published.
Every other row's business language is its `Message`, which is the direction of travel and the
two object names — useful as a label, and not a specification.

## 2. The process rows the import creates

Importing a template writes one `TxDownloaderPro` row. Which column each template artefact lands
in is the parent's §9; what matters here is where the identity comes from:

| What the import sets | Where it comes from |
|---|---|
| `Query` | the template's `DefaultQuery` — section 3 |
| `ProcessStructure` | the template's `DefaultProcessStructure` — section 4 |
| `ResultStructure` | the template's `DefaultResultStructure` — section 5 |
| `IsInsert` / `IsUpdate` / `IsDelete` | the template's own flags — section 1 |
| the DLL and `erpProcessId` | the **field-process version**, not the template |

**A default template carries no `TxDownloaderDllName` and no template name of its own.** It is
identified by its field-process version, and that version is what supplies the DLL on a create.
A create against the wrong version produces a row that no run ever matches, and the symptom is a
process that exists and never moves a record.

63 of the 295 rows carry a licence-group id, so what a given tenant is offered in the picker is
narrower than what the catalogue holds; 22 carry a help-document reference, which is not
published on these pages.

In-flight state is never in `TxDownloaderPro`. It is in `TxDownloaderProTrans`, keyed by
`SFUpdated`, and the parent's §10 is the authority on that state machine. Nothing on this page
changes what those states mean.

## 3. The queries

**Every Salesforce default template's `Query` is a `SELECT` statement** in the CRM's own query
language — 295 of 295. 198 of them carry a `WHERE`; the rest retrieve the object unconditionally
and rely on the process being pointed at the right records another way.

- **Objects the queries read:** `Account`, `Contact`, `Opportunity`, `Quote`, `Order`,
  `Product2`, `PricebookEntry`, `WorkOrder`, `TimeSheetEntry`, and a number of managed-package
  and per-ERP custom objects. Object-name case is not consistent between templates — both
  `Account` and `account`, both `Contact` and `contact` appear — and a mapping path has to match
  the document the engine emits, not the spelling in the query.
- **Child collections pulled in the same query:** `OpportunityLineItems`, `OrderItems`,
  `QuoteLineItems`, `WorkOrderLineItems`, `OrderLineItems`, `ProductsConsumed`,
  `TimeSheetEntries`, `PricebookEntries`, `FulfillmentOrders` and the managed-package line
  relationships. **A header that reaches the ERP with no lines is nearly always a query that does
  not name the child collection** — the lines were never retrieved, so no mapping could have
  found them.
- **Operators the default set uses:** `=`, `!=`, `AND`, `LIKE`, `>`, a null test and an
  empty-string test. The parent's §12 is the authority on the vocabulary; the point here is only
  which of it these templates use.

### The marker conventions

The columns these queries name, and what each convention is for. These are the product's own
managed-package and per-ERP fields; what lands in them is per record and is not published here.

| Convention | What it is for |
|---|---|
| an import marker — `Commercient_Import__c` and its per-package spellings | the user's own "send this" flag; the query's marker condition is what puts a record in scope at all |
| an update marker — `Commercient_Update__c`, `Commercient_IsRecordUpdated__c` | separates a record that has already gone across from one that has changed since |
| a customer-code field — `CommercientSF__Commercient_ArCustomerCode__c` | the ERP's customer code on the account, written back by an earlier run; a query that requires it is a query that only sees accounts the ERP already knows |
| an external-key field — `ExternalKey__c` and its per-package spellings | the ERP's key for the record itself, and the usual `Part1` writeback target (section 5) |
| an outcome field — `Commercient_bCompleted__c`, `Commercient_Message__c` | what the run reports back to the CRM user |

**Two things about these conventions catch people out.** First, a query that requires the customer
code on the *parent* account will not return a child record whose account has never synced —
there is nothing wrong with the child. Second, the empty-string test and the null test are not
the same test: a managed-package field that has never been written is null, not empty, and a
query changed from one to the other silently changes which records are in scope.

### Where the filtering happens

For Salesforce it is in the query, on the CRM side, before anything is retrieved. Narrowing or
widening what a process picks up means editing its query — not its mapping. The parent's §12
describes what can be done to a record *after* retrieval; nothing in the default Salesforce set
relies on it.

## 4. The inbound mapping document

`ProcessStructure` is a flat JSON object: each member names a field on the source side, and its
value is a template resolved against the retrieved record's XML document (parent §11). Of the 295
default templates, **279 carry one and 16 do not**; 24 of those 279 do not parse as JSON and are
counted but not described. A parseable document carries about 15 members.

- **Path roots the documents use:** `Account`, `Contact`, `Opportunity`, `Quote`, `Order`,
  `WorkOrder`, `Product2`, `OrderItems`, `QuoteLineItems`, `OpportunityLineItems`,
  `OrderLineItems`, `OrderSummary` and the per-ERP line relationships. A path's first segment has
  to match the element the engine emits; the document root itself is never part of the path.
- **Lines live in a `Line.` section, and `Line.mainXml` names the collection.** 105 of the
  parseable documents carry a `Line.` section and 71 name the collection through `Line.mainXml`.
  The members beside it — quantity, unit price, amount, item code, description, discount, tax and
  GL-account members, in each ERP's own spelling — are resolved against that collection's own
  root, not through the header. **An order that arrives with a header and no lines, when the query
  did retrieve the lines, is a `Line.mainXml` that does not resolve, or line members written
  through the header instead of the collection root.**
- **`$FUN_` value tokens the documents carry:** `$FUN_UNESCAPEXML`, `$FUN_SUBSTR`,
  `$FUN_ISNULL`, `$FUN_STRREPLACE`, `$FUN_SPLIT`, `$FUN_IF`, `$FUN_NOW`. **The names are all
  that is stated here, and no semantics are claimed for them.** The parent's §12 is explicit
  that the platform-side resolver is dotted path substitution only; these tokens are carried in
  the stored document and evaluated by the TxDownloaderPro service on the customer's own host.
  Nothing in the Data Lake interprets them, so nothing in the Data Lake can tell you what an
  argument to one means. See the maintainer notes if you need that contract.

The silent-empty-string rule from the parent's §11 is the single most common mapping fault on
this destination: a mistyped path and a genuinely blank CRM field produce byte-identical output.
Verify a path against a real record's emitted document, never against the CRM's field list.

## 5. Result structure — what goes back to Salesforce

`ResultStructure` is the outbound half: up to four parts, each optional, filled from the source
system's response after the write (parent §11). Of the 295 default templates, **92 carry a
parseable `DefaultResultStructure` and 187 carry none**; 16 do not parse, and one parses into a
flat field document rather than the four-part shape at all.

| Part | Filled by | What it addresses | Members the default set uses |
|---|---|---|---|
| `Part1` | 89 templates | the record the run is already working with | a source-path-to-CRM-field map |
| `Part2` | 11 templates | the child/line records under it | `ObjectAPIName`, `LoopFieldTagName`, `LoopFieldIDName`, `FieldName` |
| `Part3` | none | a **new** record, matched on an external id field | — |
| `Part4` | none | a **different** record, addressed by an id field | — |

So the default Salesforce set writes back to the flagged record, and sometimes to its lines, and
never creates or addresses a third record. `Part3` and `Part4` are present in the stored document
as explicit nulls on 91 of the 92 — the shape is always four parts, whether or not they are used.

- **CRM fields `Part1` writes to:** `ExternalKey__c`,
  `CommercientSF__Commercient_ArCustomerCode__c`, `CommercientSF8__ExternalKey__c`,
  `CommercientSF__ExternalKey__c`, `External_Key__c`, `Commercient_ExternalKey`, `AccountNumber`,
  and a per-ERP document-number field on several sets. These are the fields that carry the source
  system's key once the write has happened — the names only.
- **Response members it reads them from:** `ObjectID`, `internalId`, `UID`, `CustomerID`,
  `CustID`, `NewCustomerCode`, `CustomerNo`, `InvoiceNo`, `SalesOrderNo`, `NewOrderNumber`,
  `NewQuoteNumber`, `NewInvoiceNumber`, `DocNum`, `LineID`. Which of them exists depends entirely
  on the source system's response.
- **Child objects `Part2` names:** `OpportunityLineItems`, `OrderLineItems`, `QuoteLineItem`,
  `WorkOrderLineItem`, `OrderItem`, and one custom object. The fields its `FieldName` maps
  target: `ExternalKey__c`, `LineID__c`, `Contract_Line_Reference__c`.

**The `FieldName` map is written source-path first, CRM-field second** (parent §11). The wrong way
round resolves to nothing — the same silent empty string as a mistyped path, and with no error.

**A template with no `ResultStructure` writes nothing back.** That is not a fault: the key lives in
`TxDownloaderProTrans` either way, and the CRM record stays as the user left it. But if a tenant
expects the ERP's number to appear on the Salesforce record and it does not, this column being
empty is the first thing to check, before anything about the mapping or the query.

## 6. Verifying

Read the imported row before a run, not after. The parent's §14 is the authority on these tools
and §10 on the state they report.

```bash
# the processes this tenant has, with their unresolved-error counts
dlake admin txdownloaderpro_list_processes

# one process in its edit shape, including the query and both mapping documents
dlake admin txdownloaderpro_get_process --processId <id>

# run the SAVED query against the live CRM and render one record as the engine's XML
dlake admin txdownloaderpro_preview_xml --processId <id>

# the state breakdown the parent §10 reads
dlake txdownloaderpro transactions <processId> --status <state>
```

The order of diagnosis on this destination: does the query return the record at all (marker
conditions, section 3); did it bring the lines (child collection, section 3); does each path
resolve against the emitted document (section 4); and is there a `ResultStructure` at all
(section 5). Each of those is a different column, and answering them in that order avoids
editing the mapping to fix a query.

## 7. Where this sits

- `dlake-txdownloaderpro` — the parent: exposure, key scoping, the two tables, `SFUpdated`, the
  mapping columns, the filter vocabulary, the `txdownloaderpro_*` tools. **Read it first.**
- `dlake-txdownloaderpro-<erp>-salesforce` — one page per ERP that ships default templates for
  Salesforce, with that ERP's own processes, objects, mapping structure and result structure.
- `dlake-integration-setup` — registration, CRM choice and the ERP connector, of which writeback
  is one step.
- `dlake-crmpro` and `dlake-normalsync` — the inbound leg, going the other way.
- `dlake` — general tenant operation.
