---
name: dlake-txdownloaderpro-hubspot
description: >-
  What the shipped default TxDownloaderPro templates set up when HubSpot is the writeback
  destination: the three-member JSON `Query` naming the module to retrieve, why the stored
  `Where` member is not a filter on this destination, the modules the default set names, the flat
  `ProcessStructure` mapping and its small `Line.` section, the `$FUN_` value-token names the
  mapping documents carry, the single `ResultStructure` part the templates fill for the write
  back to HubSpot, and the `TxDownloaderPro` process row each template becomes on import. Use it
  when importing or reading a HubSpot writeback template set, when a process retrieves nothing,
  when a mapped field arrives empty, or when deciding where a change belongs. It extends
  `dlake-txdownloaderpro`, which covers operating TxDownloaderPro generally; the per-ERP pages
  `dlake-txdownloaderpro-<erp>-hubspot` carry each ERP's own default template set.
---

# TxDownloaderPro ← HubSpot: what the shipped default templates set up

> **Keep this skill current.** `dlake` ships updates often and this text is embedded in the CLI
> you have installed. Before relying on it, compare `dlake --version` with
> `npm view @commercient/dlake version`; if they differ, run `npm install -g @commercient/dlake@latest`
> and then `dlake skills install`, which overwrites the installed skill files with the current text.

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

HubSpot is a small destination in the default set: **44 default templates across 8 ERP names, in
25 field-process versions**. The CRM catalogue registers HubSpot with OAuth authentication. Each
of those ERPs has its own page, `dlake-txdownloaderpro-<erp>-hubspot`.

## 1. What the templates deliver

| Transaction family | What the templates deliver | Operations the flags carry |
|---|---|---|
| **Customer / company** | A HubSpot company becomes a receivable customer in the ERP, with its addresses | create, update, delete |
| **Contact** | A HubSpot contact under a synced company becomes an ERP customer contact | create, update |
| **Vendor** | A company becomes an ERP vendor | create, update, delete |
| **Sales order / invoice** | A deal, with its line items where the template maps them, becomes an ERP sales order or invoice | create, update |
| **Job** | A deal becomes the ERP's job object, on the ERPs that have one | create, update |

Across the 44 default templates, **23 carry `IsInsert`, 19 carry `IsUpdate` and 2 carry
`IsDelete`**. A flag decides which operation the process is allowed to perform, not which one it
performs on a given record.

**The catalogue carries no `BusinessDescription` on any HubSpot row.** The business language on
the per-ERP pages is each row's `Message`, which is the direction of travel and the two object
names — a label, not a specification. Roughly a third of the processes have a `Message` that
survives publication; the rest are named by their field-process version instead. If you need a
sentence about what one of these templates is *for*, the source is the ERP's own page or the
integration owner, not this catalogue.

## 2. The process rows the import creates

| What the import sets | Where it comes from |
|---|---|
| `Query` | the template's `DefaultQuery` — section 3 |
| `ProcessStructure` | the template's `DefaultProcessStructure` — section 4 |
| `ResultStructure` | the template's `DefaultResultStructure` — section 5 |
| `IsInsert` / `IsUpdate` / `IsDelete` | the template's own flags — section 1 |
| the DLL and `erpProcessId` | the **field-process version**, not the template |

**A default template carries no `TxDownloaderDllName` and no template name of its own**; it is
identified by its field-process version, and that version supplies the DLL on a create. None of
the 44 HubSpot rows carries a licence-group id, so the picker offers the whole set to any tenant
whose ERP matches.

In-flight state is in `TxDownloaderProTrans`, keyed by `SFUpdated` — parent §10.

## 3. The queries

**A HubSpot `Query` is a JSON object with three members**, on all 44 templates:
`ModuleName`, `selectedFields` and `Where`.

- **`ModuleName`** names the module to retrieve. The default set names `companies`, `contacts`
  and `deals`, and one template spells the contacts module with a leading capital. The spelling
  matters twice: it is what the engine calls, and it is the path root the mapping document has to
  use (section 4).
- **`selectedFields`** is non-empty on all 44 and holds the field list to request.
- **`Where` is non-empty on all 44, and it is not a filter.** What the default rows store in it is
  not a condition — it is a leftover of the stored shape, and the platform's own HubSpot
  retrieval path does not read it on the preview route. Its contents are not reproduced here and
  should not be treated as documentation of anything. **Do not put a filter in `Where` and expect
  it to narrow a run.**

**Where the filtering happens is therefore the important difference on this destination.** The
query names a module, not a condition, so a run retrieves what the module returns and the
selection described by the parent's §12 is applied after retrieval. There is no marker column
convention in the default HubSpot queries — none of them names one — so a process that picks up
more records than expected is not fixed by editing its query the way a Salesforce one is. Scope
it in the module, or in the process configuration the parent's §14 tools reach.

## 4. The inbound mapping document

`ProcessStructure` is a flat JSON object: each member names a field on the source side and its
value is a template resolved against the retrieved record's XML document (parent §11). All 44
default templates carry one, but **12 of them do not parse as JSON** — the highest proportion of
any destination — and those are counted, never described. A parseable document carries about 11
members.

- **Path roots the documents use:** `companies`, `contacts`, `deals`, `LineItem`, and the
  capitalised contacts spelling. One document's root is a misspelling of the contacts module,
  which resolves to nothing; a path root has to match the element the engine emits.
- **Only 6 templates carry a `Line.` section**, and all 6 name the collection through
  `Line.mainXml`. The members beside it are the usual item, quantity, amount and description
  members in each ERP's spelling, resolved against the collection's own root rather than through
  the header.
- **`$FUN_` value tokens the documents carry:** `$FUN_SUBSTR`, `$FUN_IF`, `$FUN_UNESCAPEXML`.
  **Names only; no semantics are claimed.** The parent's §12 is explicit that the platform-side
  resolver is dotted path substitution only — these tokens are evaluated by the TxDownloaderPro
  service on the customer's own host.

## 5. Result structure — what goes back to HubSpot

Of the 44 default templates, **26 carry a parseable `DefaultResultStructure` and 18 carry none**.
None fails to parse.

| Part | Filled by | What it addresses | Members the default set uses |
|---|---|---|---|
| `Part1` | 26 templates | the record the run is already working with | a source-path-to-property map |
| `Part2` | none (explicitly null on all 26) | the child/line records under it | — |
| `Part3` | none (explicitly null on all 26) | a **new** record | — |
| `Part4` | none (explicitly null on all 26) | a **different** record | — |

**`Part1` and nothing else.** Even the 6 templates that map lines inbound write nothing back to
the line records.

- **HubSpot properties `Part1` writes to:** `arcustomercode` and `externalkey` — the customer
  code and the external key, in HubSpot's lower-case property style. That is the whole outbound
  surface of this destination's default set.
- **Response members it reads them from:** `internalId`, `ObjectID`, `CustomerID`, `VendorId`,
  `ClientId`, `ListID`, `TxnID`, `LineID`, `number`, and several per-ERP response members. Which
  exists depends on the source system's response.

**A template with no `ResultStructure` writes nothing back**, and the ERP's key then lives only in
`TxDownloaderProTrans`. If a tenant expects the customer code to appear on the HubSpot company
and it does not, check for this column being empty before looking at anything else.

## 6. Verifying

```bash
# the processes this tenant has, with their unresolved-error counts
dlake admin txdownloaderpro_list_processes

# one process in its edit shape, including the query and both mapping documents
dlake admin txdownloaderpro_get_process --processId <id>

# retrieve one record through the SAVED query and render it as the engine sees it
dlake admin txdownloaderpro_preview_xml --processId <id>

# the state breakdown the parent §10 reads
dlake txdownloaderpro transactions <processId> --status <state>
```

The order of diagnosis here: is the module name right and does the preview return a record
(section 3); does each path root match the module the engine emitted (section 4) — this is where
the misspelled root and the unparseable documents bite; and is there a `ResultStructure` at all
(section 5). The parent's §14 is the authority on the tools, §10 on the state.

## 7. Where this sits

- `dlake-txdownloaderpro` — the parent: exposure, key scoping, the two tables, `SFUpdated`, the
  mapping columns, the filter vocabulary, the `txdownloaderpro_*` tools. **Read it first.**
- `dlake-txdownloaderpro-<erp>-hubspot` — one page per ERP that ships default templates for
  HubSpot.
- `dlake-integration-setup` — registration, CRM choice and the ERP connector.
- `dlake-crmpro` and `dlake-normalsync` — the inbound leg.
- `dlake` — general tenant operation.
