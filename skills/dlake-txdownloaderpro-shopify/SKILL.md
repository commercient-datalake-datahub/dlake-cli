---
name: dlake-txdownloaderpro-shopify
description: >-
  What the shipped default TxDownloaderPro templates set up when Shopify is the writeback source:
  the one-member JSON `Query` that names the module and nothing else, the three modules the
  default set names, the flat `ProcessStructure` mapping with its `Line.` section and
  `Line.mainXml` collection member over the order's line items, the single `$FUN_` value-token
  name these documents carry, the fact that no default Shopify template fills a
  `ResultStructure` part and therefore nothing is written back to Shopify, and the
  `TxDownloaderPro` process row each template becomes on import. Use it when importing or reading
  a Shopify template set, when an order reaches the ERP with no lines, when a mapped field
  arrives empty, or when someone expects the ERP's order number to appear in Shopify. It extends
  `dlake-txdownloaderpro`, which covers operating TxDownloaderPro generally; the per-ERP pages
  `dlake-txdownloaderpro-<erp>-shopify` carry each ERP's own default template set.
---

# TxDownloaderPro ← Shopify: what the shipped default templates set up

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

The default set ships **64 templates across 21 ERP names, in 61 field-process versions** — almost
one version per template, which is what a set built ERP by ERP looks like. Each of those ERPs has
its own page, `dlake-txdownloaderpro-<erp>-shopify`.

**Shopify is the one destination in this family that is really a source.** The templates move a
store's customers, products and orders into the ERP, and nothing comes back — see section 5.

## 1. What the templates deliver

| Transaction family | What the templates deliver | Operations the flags carry |
|---|---|---|
| **Customer** | A Shopify customer becomes a receivable customer in the ERP, with the bill-to address from the customer's address list | create |
| **Product / item** | A Shopify product becomes an ERP item, with its price | create |
| **Sales order** | A Shopify order with its line items becomes an ERP sales order | create |
| **Sales invoice** | The same order aimed at the ERP's invoice object instead | create |
| **Contact** | A customer becomes an ERP customer contact, on the ERPs whose contact object is separate | create |

**All 64 default templates carry `IsInsert`, and none carries `IsUpdate` or `IsDelete`.** This set
creates records in the ERP; it does not maintain them. 63 of the 64 also carry
`IsCustomization`, so the picker treats nearly the whole set as customisable, and 11 carry a
licence-group id.

**The catalogue carries no `BusinessDescription` on any Shopify row.** The business language on
the per-ERP pages is each row's `Message` — direction of travel and two object names — and nearly
every process has one that survives publication.

## 2. The process rows the import creates

| What the import sets | Where it comes from |
|---|---|
| `Query` | the template's `DefaultQuery` — section 3 |
| `ProcessStructure` | the template's `DefaultProcessStructure` — section 4 |
| `ResultStructure` | the template's `DefaultResultStructure` — section 5 |
| `IsInsert` / `IsUpdate` / `IsDelete` | the template's own flags — section 1 |
| the DLL and `erpProcessId` | the **field-process version**, not the template |

**A default template carries no `TxDownloaderDllName` and no template name of its own**; the
field-process version is its identity and supplies the DLL on a create. With 61 versions behind
64 templates, picking the version is effectively picking the template — a create against the
wrong one produces a process that no run matches.

In-flight state is in `TxDownloaderProTrans`, keyed by `SFUpdated` — parent §10.

## 3. The queries

**A Shopify `Query` is a JSON object with a single member, `ModuleName`, and nothing else** — all
64 templates. There is no `selectedFields` and no `Where`: unlike the other JSON-query
destinations in this family, the default Shopify templates do not carry those members at all.

- **The modules the default set names:** `customer`, `order`, `product`.
- **There is no marker column convention here, and no filter.** A run retrieves what the module
  returns. Nothing in the query decides which records are in scope, so a process that picks up
  more than expected cannot be narrowed by editing its query the way a Salesforce one can — the
  selection the parent's §12 describes is applied after retrieval.
- The module name is also the path root the mapping document has to use, in the capitalisation
  the engine emits rather than the lower-case spelling in the query (section 4).

## 4. The inbound mapping document

`ProcessStructure` is a flat JSON object: each member names a field on the source side and its
value is a template resolved against the retrieved record's XML document (parent §11). **All 64
default templates carry one and all 64 parse** — the only destination in this family with no
unparseable documents. A document carries about 13 members.

- **Path roots the documents use:** `Order`, `Customer`, `Product`, and `line_items` for the
  order's lines. Four roots for the whole destination, because there are only three modules.
- **29 documents carry a `Line.` section and 25 name the collection through `Line.mainXml`.** On
  this destination the collection is always the order's line items. The members beside it — item
  or SKU, quantity, unit price, amount and description in each ERP's spelling — resolve against
  the line-items root, **not** through the order. **An order that reaches the ERP with a header
  and no lines is almost always one of two things: a missing `Line.mainXml`, which four of these
  documents show, or line members written through the header root instead of the collection
  root.**
- **`$FUN_` value tokens:** `$FUN_STRREPLACE`, and that is the only one this destination's default
  documents carry. **The name is all that is stated, and no semantics are claimed** — the
  parent's §12 is explicit that the platform-side resolver is dotted path substitution only, and
  the token is evaluated by the service on the customer's own host.

## 5. Result structure — nothing goes back to Shopify

**No default Shopify template carries a `DefaultResultStructure` at all** — 64 of 64 are empty,
not merely four explicit nulls the way the other destinations store them.

So none of `Part1` through `Part4` is filled anywhere in this set, and **nothing is written back
into the store**: the ERP's customer code, item number or order number stays in
`TxDownloaderProTrans` and the Shopify record is left exactly as it was. That is the designed
behaviour of this set, not a gap in a particular template.

If a tenant needs the ERP's number to appear against the Shopify record, `ResultStructure` is the
column to fill and the parent's §11 gives its shape — but be clear with them that no shipped
default template does this, so it is new configuration and not a repair.

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

The order of diagnosis here is short, because there is little to configure: is the module name
right and does the preview return a record (section 3); does each path root match the emitted
document, and is there a `Line.mainXml` where lines are expected (section 4). A question about
something appearing back in Shopify is answered by section 5, not by a mapping change.

## 7. Where this sits

- `dlake-txdownloaderpro` — the parent: exposure, key scoping, the two tables, `SFUpdated`, the
  mapping columns, the filter vocabulary, the `txdownloaderpro_*` tools. **Read it first.**
- `dlake-txdownloaderpro-<erp>-shopify` — one page per ERP that ships default templates for this
  destination.
- `dlake-integration-setup` — registration, CRM choice and the ERP connector.
- `dlake-crmpro` and `dlake-normalsync` — the inbound leg for the CRM destinations.
- `dlake` — general tenant operation.
