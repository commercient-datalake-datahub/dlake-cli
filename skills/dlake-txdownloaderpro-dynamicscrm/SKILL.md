---
name: dlake-txdownloaderpro-dynamicscrm
description: >-
  What the shipped default TxDownloaderPro templates set up when Dynamics CRM is the writeback
  destination: the two query shapes the default set uses — a FetchXML document on most templates
  and a `SELECT` statement on the rest — the FetchXML elements and condition operators those
  documents use, the marker and external-key column conventions they read and the run writes back
  to, the flat `ProcessStructure` mapping with its `Line.` section and `Line.mainXml` collection
  member, the `$FUN_` value-token names the documents carry, the single `ResultStructure` part
  the templates fill, and the `TxDownloaderPro` process row each template becomes on import. Use
  it when importing or reading a Dynamics CRM writeback template set, when a process retrieves
  nothing, when a mapped field arrives empty, or when deciding where a change belongs. It extends
  `dlake-txdownloaderpro`, which covers operating TxDownloaderPro generally; the per-ERP pages
  `dlake-txdownloaderpro-<erp>-dynamicscrm` carry each ERP's own default template set.
---

# TxDownloaderPro ← Dynamics CRM: what the shipped default templates set up

> **Keep this skill current.** `dlake` ships updates often and this text is embedded in the CLI
> you have installed. Before relying on it, compare `dlake --version` with
> `npm view @commercient/dlake version`; if they differ, run `npm install -g @commercient/dlake@latest`
> and then `dlake skills install`, which overwrites the installed skill files with the current text.

**The source ERP has its own page under this skill.** `erps/<erp>.md` is a child file of this
skill and describes what the shipped templates for that ERP → Dynamics CRM pair set up. §7 lists
every one of them and how to pick the right row; read this page first, then that one.

`dlake-txdownloaderpro` is the parent skill and the authority for everything general: what the
writeback objects are and how they are exposed to the Data API (§1–§7), how a key is scoped to
them, the `TxDownloaderPro` and `TxDownloaderProTrans` tables and their columns (§9, §10), the
`SFUpdated` state machine (§10), the field-mapping columns and the `{{Object.Field}}` template
paths that read them (§11), the filter-operator vocabulary (§12), and the `txdownloaderpro_*`
tools that are the preferred way to configure any of it (§14). Read it first; this page does not
repeat it.

What follows is only what the shipped **default** templates for this destination themselves set,
described from their `DefaultQuery`, `DefaultProcessStructure` and `DefaultResultStructure`
columns — structure only. **No template text is reproduced.** This page grows as the default
catalogue does.

The default set ships **79 templates across 18 ERP names, in 47 field-process versions**. Each of
those ERPs has its own page, `dlake-txdownloaderpro-<erp>-dynamicscrm`. The catalogue spells this
destination's name without an `s`; the product's display name for it is Dynamics CRM.

## 1. What the templates deliver

| Transaction family | What the templates deliver | Operations the flags carry |
|---|---|---|
| **Customer / account** | An `account` becomes a receivable customer in the ERP, with its bill-to and ship-to addresses, and the ERP's customer code comes back onto the account | create, update, delete |
| **Contact** | A `contact` under a synced account becomes an ERP customer contact | create, update |
| **Sales order** | A `salesorder`, `quote` or `opportunity` with its detail records becomes an ERP sales order | create, update |
| **Quote / estimate / prospect** | The same shape aimed at the ERP's quote, estimate or prospect object | create, update |
| **Invoice** | An order or quote becomes a receivable invoice, with lines | create, update |
| **Product / product price** | Product records become ERP items and prices | create, update |
| **Job** | An opportunity becomes the ERP's job object, on the ERPs that have one | create, update |

Across the 79 default templates, **61 carry `IsInsert`, 25 carry `IsUpdate` and 4 carry
`IsDelete`**. A flag decides which operation the process is allowed to perform, not which one it
performs on a given record. 37 of the rows carry a licence-group id, so what a tenant is offered
is narrower than what the catalogue holds.

**The catalogue carries no `BusinessDescription` on any Dynamics CRM row.** The business language
on the per-ERP pages is each row's `Message` — direction of travel and two object names, a label
rather than a specification — and about three quarters of the processes have one that survives
publication.

## 2. The process rows the import creates

| What the import sets | Where it comes from |
|---|---|
| `Query` | the template's `DefaultQuery` — section 3 |
| `ProcessStructure` | the template's `DefaultProcessStructure` — section 4 |
| `ResultStructure` | the template's `DefaultResultStructure` — section 5 |
| `IsInsert` / `IsUpdate` / `IsDelete` | the template's own flags — section 1 |
| the DLL and `erpProcessId` | the **field-process version**, not the template |

**A default template carries no `TxDownloaderDllName` and no template name of its own**; the
field-process version is its identity and supplies the DLL on a create. In-flight state is in
`TxDownloaderProTrans`, keyed by `SFUpdated` — parent §10.

## 3. The queries

**This destination is the one where `Query` has two shapes in the default set**: 65 templates
carry a **FetchXML document** and 14 carry a **`SELECT` statement**. 76 of the 79 carry a filter
of some kind. Knowing which shape a process holds before editing it is the whole point of this
section — the parent's §9 is explicit that `Query` is not one format across the product.

- **Objects the queries read:** `account`, `contact`, `salesorder`, `salesorderdetail`, `quote`,
  `quotedetail`, `opportunity`, `opportunitydetail`, `order`, `orderdetail`, `systemuser`, and a
  number of per-ERP managed entities. Case is not consistent between templates — both `account`
  and `Account` appear — and a mapping path has to match the document the engine emits.
- **FetchXML elements the documents use:** `fetch`, `entity`, `attribute`, `filter`, `condition`,
  `order`, `link-entity`. A `link-entity` is how the detail records and the related account or
  salesperson entity are pulled in the same retrieve; a header that arrives with no lines is
  usually a document with no `link-entity` for the detail entity.
- **Condition operators the default set uses:** `eq`, `null`, `not-null`, and one document
  spelling it without the hyphen. That last spelling is worth knowing about, because it is a
  different string from the supported one.
- **Operators the `SELECT` templates use:** `=`, `!=`, `AND`, `OR`, a null test and an
  empty-string test.

### The marker conventions

The columns these queries name, and what each convention is for — product managed fields and
per-ERP key fields. What lands in them is per record and is not published here.

| Convention | What it is for |
|---|---|
| an import marker — `Commercient_Import__c` and its per-package spellings | the user's own "send this" flag; the query's condition on it is what puts a record in scope |
| an update marker — `Commercient_Update__c` | separates a record that has already gone across from one that has changed since |
| a customer-code field — `CommercientSF__Commercient_ArCustomerCode__c`, `Commercient_ArCustomer_Code` | the ERP's customer code on the account, written by an earlier run |
| an external-key field — `Commercient_ExternalKey__c`, `ExternalKey__c` and their per-package spellings | the ERP's key for the record, and the usual `Part1` writeback target (section 5) |
| an outcome field — `Commercient_bCompleted__c`, `Commercient_Message__c` | what the run reports back to the CRM user |

**Where the filtering happens:** in the query, on the CRM side, before anything is retrieved —
whichever of the two shapes the template uses. Narrowing what a process picks up means editing
its query, and editing it means editing the shape it actually holds.

## 4. The inbound mapping document

`ProcessStructure` is a flat JSON object: each member names a field on the source side and its
value is a template resolved against the retrieved record's XML document (parent §11). All 79
default templates carry one; **5 do not parse as JSON** and are counted but not described. A
parseable document carries about 18 members — the largest average of any destination.

- **Path roots the documents use:** `account`, `contact`, `quote`, `quotedetail`, `opportunity`,
  `opportunitydetail`, `salesorder`, `order`, `orderdetail`, `invoice`, and the capitalised
  spellings of several of them. The root has to match what the engine emitted for that retrieve;
  with FetchXML that is the entity name in the document, not the display name of the entity.
- **38 of the parseable documents carry a `Line.` section, and all 38 name the collection
  through `Line.mainXml`** — the tightest correspondence of any destination. The members beside
  it are the item, quantity, unit-price, discount, tax, GL-account and description members in
  each ERP's spelling, resolved against the collection's own root, not through the header.
- **`$FUN_` value tokens the documents carry:** `$FUN_STRREPLACE`, `$FUN_SUBSTR`,
  `$FUN_UNESCAPEXML`, `$FUN_ISNULL`, `$FUN_ROUND`, `$FUN_SPLIT`. **Names only; no semantics are
  claimed.** The parent's §12 is explicit that the platform-side resolver is dotted path
  substitution only — these are evaluated by the service on the customer's own host.

## 5. Result structure — what goes back to Dynamics CRM

Of the 79 default templates, **30 carry a parseable `DefaultResultStructure` and 48 carry none**;
one does not parse.

| Part | Filled by | What it addresses | Members the default set uses |
|---|---|---|---|
| `Part1` | 30 templates | the record the run is already working with | a source-path-to-CRM-field map |
| `Part2` | none (explicitly null on all 30) | the child/line records under it | — |
| `Part3` | none (explicitly null on all 30) | a **new** record | — |
| `Part4` | none (explicitly null on all 30) | a **different** record | — |

**`Part1` and nothing else**, even on the 38 templates that map lines inbound. The line records
get nothing back.

- **CRM fields `Part1` writes to:** `comrcint_arcustomercode`, `arcustomercode`,
  `Commercient_ExternalKey__c`, `Commercient_ExternalKey`, `comrcint_externalkey`,
  `externalkey`, `ExternalKey__c`, `CommercientSF__Commercient_ArCustomerCode__c`,
  `comrcint_estimateid`, `comrcint_prospectid`, and a per-ERP document-number field on several
  sets. Two naming styles coexist — the managed-package style and this destination's own
  lower-case `comrcint_` prefix — and they are not interchangeable.
- **Response members it reads them from:** `Company`, `CustID`, `CustomerNo`, `NewCustomerCode`,
  `ClientId`, `ObjectID`, `QuoteNum`, `CoNum`, `ProspectGuid`, `ProspectID`, `UID`, `internalId`,
  `InvoiceNo`, `orderNo`. Which exists depends on the source system's response.

**The map is written source-path first, CRM-field second** (parent §11); the wrong way round
resolves to the same silent empty string as a mistyped path, with no error. **A template with no
`ResultStructure` writes nothing back** — check that before anything else when the ERP's number
does not appear on the CRM record.

## 6. Verifying

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

The order of diagnosis: which query shape is this process holding (section 3); does it return the
record, and did it bring the detail entity (section 3); does each path root match the emitted
document (section 4); is there a `ResultStructure` at all (section 5). The parent's §14 is the
authority on the tools, §10 on the state.

## 7. The source ERP’s own page

One row per source ERP the catalogue ships default Dynamics CRM templates for. Each page is a
child file of this skill, addressed as `dlake-txdownloaderpro-dynamicscrm/erps/<erp>` — `dlake
skills show dlake-txdownloaderpro-dynamicscrm/erps/<erp>` prints one, and `dlake skills install`
writes them beside this file.

<!-- ERP-TABLE:BEGIN dlake-txdownloaderpro-dynamicscrm -->
| ERP | Page | What its templates deliver |
|---|---|---|
| Epicor 10 | [`erps/epicor-10.md`](erps/epicor-10.md) | 6 default templates in 5 processes, mostly `SELECT` queries; writes back through `Part1` |
| Infor SyteLine | [`erps/infor-syteline.md`](erps/infor-syteline.md) | 2 default templates in 2 processes, mostly FetchXML queries; writes back through `Part1` |
| Microsoft Dynamics GP | [`erps/microsoft-dynamics-gp.md`](erps/microsoft-dynamics-gp.md) | 1 default template in 1 process, mostly FetchXML queries; nothing written back |
| MYOB AccountRight | [`erps/myob-accountright.md`](erps/myob-accountright.md) | 2 default templates in 1 process, mostly FetchXML queries; writes back through `Part1` |
| NetSuite | [`erps/netsuite.md`](erps/netsuite.md) | 2 default templates in 1 process, mostly FetchXML queries; writes back through `Part1` |
| QuickBooks Desktop | [`erps/quickbooks-desktop.md`](erps/quickbooks-desktop.md) | 1 default template in 1 process, mostly FetchXML queries; nothing written back |
| QuickBooks Online | [`erps/quickbooks-online.md`](erps/quickbooks-online.md) | 2 default templates in 2 processes, mostly FetchXML queries; nothing written back |
| Sage 100 (US) | [`erps/sage-100-us.md`](erps/sage-100-us.md) | 12 default templates in 6 processes, mostly FetchXML queries; writes back through `Part1` |
| Sage 100 Contractor | [`erps/sage-100-contractor.md`](erps/sage-100-contractor.md) | 4 default templates in 2 processes, mostly FetchXML queries; writes back through `Part1` |
| Sage 100 Contractor 2018 | [`erps/sage-100-contractor-2018.md`](erps/sage-100-contractor-2018.md) | 4 default templates in 2 processes, mostly FetchXML queries; writes back through `Part1` |
| Sage 300 | [`erps/sage-300.md`](erps/sage-300.md) | 1 default template in 1 process, mostly FetchXML queries; nothing written back |
| Sage 50 Canada | [`erps/sage-50-canada.md`](erps/sage-50-canada.md) | 11 default templates in 4 processes, mostly FetchXML queries; writes back through `Part1` |
| Sage 50 UK | [`erps/sage-50-uk.md`](erps/sage-50-uk.md) | 5 default templates in 5 processes, mostly `SELECT` queries; writes back through `Part1` |
| Sage 50 US | [`erps/sage-50-us.md`](erps/sage-50-us.md) | 13 default templates in 6 processes, mostly FetchXML queries; writes back through `Part1` |
| Sage Live | [`erps/sage-live.md`](erps/sage-live.md) | 2 default templates in 2 processes, mostly FetchXML queries; nothing written back |
| SAP B1 | [`erps/sap-b1.md`](erps/sap-b1.md) | 5 default templates in 3 processes, mostly FetchXML queries; writes back through `Part1` |
| SYSPRO 6 | [`erps/syspro-6.md`](erps/syspro-6.md) | 4 default templates in 2 processes, mostly FetchXML queries; writes back through `Part1` |
| VAI S2K | [`erps/vai-s2k.md`](erps/vai-s2k.md) | 2 default templates in 1 process, mostly FetchXML queries; writes back through `Part1` |
<!-- ERP-TABLE:END -->

**Work out which row applies before reading one.** The source is the ERP the tenant was registered
with: `dlake register erps` lists the catalogue’s names and codes, and `dlake admin
crmpro_templates` shows what that tenant can actually import. Match that ERP to a row above, then
read its page alongside this one — this page for the conventions that hold across every source,
that page for what this source’s own templates set. If no row matches the tenant’s ERP, this skill
alone applies: the catalogue ships no default templates for that pair, so there is nothing
ERP-specific to read and nothing to import.

## 8. Where this sits

- `dlake-txdownloaderpro` — the parent: exposure, key scoping, the two tables, `SFUpdated`, the
  mapping columns, the filter vocabulary, the `txdownloaderpro_*` tools. **Read it first.**
- `dlake-txdownloaderpro-<erp>-dynamicscrm` — one page per ERP that ships default templates for
  this destination.
- `dlake-integration-setup` — registration, CRM choice and the ERP connector.
- `dlake-crmpro` and `dlake-normalsync` — the inbound leg.
- `dlake` — general tenant operation.
