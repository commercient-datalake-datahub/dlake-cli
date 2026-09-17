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

**The source ERP has its own page under this skill.** `erps/<erp>.md` is a child file of this
skill and describes what the shipped templates for that ERP → HubSpot pair set up. §7 lists every
one of them and how to pick the right row; read this page first, then that one.

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

HubSpot is a large destination in the default set: **404 default templates across 64 ERP names, in
319 field-process versions**. The CRM catalogue registers HubSpot with OAuth authentication. Each
of those ERPs has its own page, `dlake-txdownloaderpro-<erp>-hubspot`.

## 1. What the templates deliver

| Transaction family | What the templates deliver | Operations the flags carry |
|---|---|---|
| **Customer / company** | A HubSpot company becomes a receivable customer in the ERP, with its addresses | create, update, delete |
| **Contact** | A HubSpot contact under a synced company becomes an ERP customer contact | create, update |
| **Vendor** | A company becomes an ERP vendor | create, update, delete |
| **Sales order / invoice** | A deal, with its line items where the template maps them, becomes an ERP sales order or invoice | create, update |
| **Job** | A deal becomes the ERP's job object, on the ERPs that have one | create, update |

Across the 404 default templates, **260 carry `IsInsert`, 135 carry `IsUpdate` and 16 carry
`IsDelete`**. A flag decides which operation the process is allowed to perform, not which one it
performs on a given record.

**The catalogue carries no `BusinessDescription` on any HubSpot row.** The business language on
the per-ERP pages is each row's `Message`, which is the direction of travel and the two object
names — a label, not a specification. Nearly all of the processes have a `Message` that
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
the 404 HubSpot rows carries a licence-group id, so the picker offers the whole set to any tenant
whose ERP matches.

In-flight state is in `TxDownloaderProTrans`, keyed by `SFUpdated` — parent §10.

## 3. The queries

**A HubSpot `Query` is a JSON object with three members**, on all 404 templates:
`ModuleName`, `selectedFields` and `Where`.

- **`ModuleName`** names the module to retrieve. The default set names `companies`, `contacts`,
  `deals` and `products`, and three templates spell the contacts module with a leading capital. The
  spelling matters twice: it is what the engine calls, and it is the path root the mapping document
  has to use (section 4).
- **`selectedFields`** is non-empty on all 404 and holds the field list to request.
- **`Where` is non-empty on 44 of the 404, and it is not a filter.** What those rows store in it
  is not a condition — it is a leftover of the stored shape, and the platform's own HubSpot
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
value is a template resolved against the retrieved record's XML document (parent §11). All 404
default templates carry one, but **12 of them do not parse as JSON** — and those are counted,
never described. A parseable document carries about 12 members.

- **Path roots the documents use:** `companies`, `contacts`, `deals`, `products`, `LineItem`, and
  the capitalised contacts spelling. Eight documents are rooted on a misspelling of the contacts
  module, which resolves to nothing; a path root has to match the element the engine emits.
- **125 templates carry a `Line.` section**, and 123 of them name the collection through
  `Line.mainXml`; the other two spell that member with a different capitalisation, which is a
  different key. The members beside it are the usual item, quantity, amount and description
  members in each ERP's spelling, resolved against the collection's own root rather than through
  the header.
- **`$FUN_` value tokens the documents carry:** `$FUN_UNESCAPEXML`, `$FUN_SUBSTR`, `$FUN_SPLIT`,
  `$FUN_DATEFORMAT`, `$FUN_ISNULL`, `$FUN_IF`, `$FUN_TIMESTAMPTODATE`, `$FUN_STRREPLACE`.
  **Names only; no semantics are claimed.** The parent's §12 is explicit that the platform-side
  resolver is dotted path substitution only — these tokens are evaluated by the TxDownloaderPro
  service on the customer's own host.

## 5. Result structure — what goes back to HubSpot

Of the 404 default templates, **206 carry a parseable `DefaultResultStructure` and 198 carry
none**. None fails to parse.

| Part | Filled by | What it addresses | Members the default set uses |
|---|---|---|---|
| `Part1` | 206 templates | the record the run is already working with | a source-path-to-property map |
| `Part2` | none (explicitly null on all 206) | the child/line records under it | — |
| `Part3` | none (explicitly null on all 206) | a **new** record | — |
| `Part4` | none (explicitly null on all 206) | a **different** record | — |

**`Part1` and nothing else.** Even the 125 templates that map lines inbound write nothing back to
the line records.

- **HubSpot properties `Part1` writes to:** `arcustomercode`, `externalkey` and a capitalised
  spelling of the external key — the customer code and the external key, in HubSpot's lower-case
  property style bar that one spelling. That is the whole outbound surface of this destination's
  default set.
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

## 7. The source ERP’s own page

One row per source ERP the catalogue ships default HubSpot templates for. Each page is a child
file of this skill, addressed as `dlake-txdownloaderpro-hubspot/erps/<erp>` — `dlake skills show
dlake-txdownloaderpro-hubspot/erps/<erp>` prints one, and `dlake skills install` writes them
beside this file.

<!-- ERP-TABLE:BEGIN dlake-txdownloaderpro-hubspot -->
| ERP | Page | What its templates deliver |
|---|---|---|
| Abas Business Software | [`erps/abas-business-software.md`](erps/abas-business-software.md) | 8 default templates in 8 processes, mostly JSON module objects; writes back through `Part1` |
| Acumatica Cloud | [`erps/acumatica-cloud.md`](erps/acumatica-cloud.md) | 6 default templates in 5 processes, mostly JSON module objects; writes back through `Part1` |
| Applied Epic | [`erps/applied-epic.md`](erps/applied-epic.md) | 2 default templates in 2 processes, mostly JSON module objects; writes back through `Part1`; 2 community templates |
| Aptean Encompix | [`erps/aptean-encompix.md`](erps/aptean-encompix.md) | 1 default template in 1 process, mostly JSON module objects; nothing written back |
| Aptean Made2Manage | [`erps/aptean-made2manage.md`](erps/aptean-made2manage.md) | 6 default templates in 3 processes, mostly JSON module objects; nothing written back |
| Aptean Ross | [`erps/aptean-ross.md`](erps/aptean-ross.md) | no default templates; 1 community template |
| Commercient CPQ | [`erps/commercient-cpq.md`](erps/commercient-cpq.md) | 2 default templates in 2 processes, mostly JSON module objects; nothing written back |
| DELMIAworks | [`erps/delmiaworks.md`](erps/delmiaworks.md) | 1 default template in 1 process, mostly JSON module objects; writes back through `Part1` |
| Deltek Vantagepoint | [`erps/deltek-vantagepoint.md`](erps/deltek-vantagepoint.md) | 3 default templates in 3 processes, mostly JSON module objects; writes back through `Part1`; 6 community templates |
| Deltek Vision | [`erps/deltek-vision.md`](erps/deltek-vision.md) | 7 default templates in 6 processes, mostly JSON module objects; writes back through `Part1` |
| EBMS | [`erps/ebms.md`](erps/ebms.md) | 6 default templates in 3 processes, mostly JSON module objects; writes back through `Part1`; 3 community templates |
| ECi Spruce | [`erps/eci-spruce.md`](erps/eci-spruce.md) | 2 default templates in 2 processes, mostly JSON module objects; writes back through `Part1` |
| Epicor 10 | [`erps/epicor-10.md`](erps/epicor-10.md) | 8 default templates in 7 processes, mostly JSON module objects; writes back through `Part1`; 16 community templates |
| Epicor 9 and 9.5 | [`erps/epicor-9-and-9-5.md`](erps/epicor-9-and-9-5.md) | 5 default templates in 3 processes, mostly JSON module objects; writes back through `Part1` |
| Epicor BisTrack | [`erps/epicor-bistrack.md`](erps/epicor-bistrack.md) | 2 default templates in 2 processes, mostly JSON module objects; writes back through `Part1` |
| Epicor Cloud | [`erps/epicor-cloud.md`](erps/epicor-cloud.md) | 8 default templates in 4 processes, mostly JSON module objects; writes back through `Part1`; 27 community templates |
| Epicor Eclipse | [`erps/epicor-eclipse.md`](erps/epicor-eclipse.md) | 1 default template in 1 process, mostly JSON module objects; writes back through `Part1` |
| Epicor Prophet 21 (P21) | [`erps/epicor-prophet-21-p21.md`](erps/epicor-prophet-21-p21.md) | 10 default templates in 7 processes, mostly JSON module objects; writes back through `Part1`; 9 community templates |
| Exact Globe Next | [`erps/exact-globe-next.md`](erps/exact-globe-next.md) | 4 default templates in 4 processes, mostly JSON module objects; writes back through `Part1` |
| Exact MAX | [`erps/exact-max.md`](erps/exact-max.md) | 1 default template in 1 process, mostly JSON module objects; nothing written back |
| GenericDLLs | [`erps/genericdlls.md`](erps/genericdlls.md) | no default templates; 41 community templates |
| GlobalShop | [`erps/globalshop.md`](erps/globalshop.md) | 5 default templates in 3 processes, mostly JSON module objects; writes back through `Part1` |
| IFS | [`erps/ifs.md`](erps/ifs.md) | 22 default templates in 22 processes, mostly JSON module objects; writes back through `Part1` |
| IFS 9 | [`erps/ifs-9.md`](erps/ifs-9.md) | 4 default templates in 4 processes, mostly JSON module objects; writes back through `Part1` |
| IFS Cloud | [`erps/ifs-cloud.md`](erps/ifs-cloud.md) | 5 default templates in 5 processes, mostly JSON module objects; writes back through `Part1` |
| Infor SXe | [`erps/infor-sxe.md`](erps/infor-sxe.md) | 1 default template in 1 process, mostly JSON module objects; writes back through `Part1`; 1 community template |
| Infor SyteLine | [`erps/infor-syteline.md`](erps/infor-syteline.md) | 24 default templates in 21 processes, mostly JSON module objects; writes back through `Part1`; 13 community templates |
| Infor Visual 9 | [`erps/infor-visual-9.md`](erps/infor-visual-9.md) | 2 default templates in 2 processes, mostly JSON module objects; nothing written back |
| Infor10 Distribution Business | [`erps/infor10-distribution-business.md`](erps/infor10-distribution-business.md) | 1 default template in 1 process, mostly JSON module objects; writes back through `Part1` |
| Infusionsoft | [`erps/infusionsoft.md`](erps/infusionsoft.md) | 1 default template in 1 process, mostly JSON module objects; writes back through `Part1` |
| JD Edwards | [`erps/jd-edwards.md`](erps/jd-edwards.md) | 5 default templates in 3 processes, mostly JSON module objects; writes back through `Part1` |
| JobBOSS | [`erps/jobboss.md`](erps/jobboss.md) | 4 default templates in 2 processes, mostly JSON module objects; writes back through `Part1` |
| Macola ES | [`erps/macola-es.md`](erps/macola-es.md) | 1 default template in 1 process, mostly JSON module objects; nothing written back |
| Maconomy | [`erps/maconomy.md`](erps/maconomy.md) | 4 default templates in 2 processes, mostly JSON module objects; writes back through `Part1` |
| Microsoft Business Central | [`erps/microsoft-business-central.md`](erps/microsoft-business-central.md) | 8 default templates in 5 processes, mostly JSON module objects; writes back through `Part1`; 12 community templates |
| Microsoft Dynamics AX | [`erps/microsoft-dynamics-ax.md`](erps/microsoft-dynamics-ax.md) | 4 default templates in 4 processes, mostly JSON module objects; writes back through `Part1` |
| Microsoft Dynamics GP | [`erps/microsoft-dynamics-gp.md`](erps/microsoft-dynamics-gp.md) | 6 default templates in 5 processes, mostly JSON module objects; writes back through `Part1` |
| Microsoft Dynamics NAV | [`erps/microsoft-dynamics-nav.md`](erps/microsoft-dynamics-nav.md) | 15 default templates in 8 processes, mostly JSON module objects; writes back through `Part1` |
| MYOB AccountRight | [`erps/myob-accountright.md`](erps/myob-accountright.md) | 8 default templates in 7 processes, mostly JSON module objects; writes back through `Part1`; 2 community templates |
| NetSuite | [`erps/netsuite.md`](erps/netsuite.md) | 14 default templates in 7 processes, mostly JSON module objects; writes back through `Part1`; 13 community templates |
| Plex | [`erps/plex.md`](erps/plex.md) | 4 default templates in 4 processes, mostly JSON module objects; nothing written back |
| Print EPS | [`erps/print-eps.md`](erps/print-eps.md) | 3 default templates in 3 processes, mostly JSON module objects; writes back through `Part1` |
| QAD | [`erps/qad.md`](erps/qad.md) | 2 default templates in 2 processes, mostly JSON module objects; writes back through `Part1` |
| QuickBooks Desktop | [`erps/quickbooks-desktop.md`](erps/quickbooks-desktop.md) | 9 default templates in 6 processes, mostly JSON module objects; writes back through `Part1`; 67 community templates |
| QuickBooks Online | [`erps/quickbooks-online.md`](erps/quickbooks-online.md) | 19 default templates in 18 processes, mostly JSON module objects; writes back through `Part1` |
| QuickBooks POS | [`erps/quickbooks-pos.md`](erps/quickbooks-pos.md) | 1 default template in 1 process, mostly JSON module objects; writes back through `Part1` |
| Sage 100 (US) | [`erps/sage-100-us.md`](erps/sage-100-us.md) | 29 default templates in 22 processes, mostly JSON module objects; writes back through `Part1`; 22 community templates |
| Sage 100 Contractor | [`erps/sage-100-contractor.md`](erps/sage-100-contractor.md) | 8 default templates in 4 processes, mostly JSON module objects; writes back through `Part1`; 2 community templates |
| Sage 100 Contractor 2018 | [`erps/sage-100-contractor-2018.md`](erps/sage-100-contractor-2018.md) | 6 default templates in 2 processes, mostly JSON module objects; writes back through `Part1` |
| Sage 200 UK | [`erps/sage-200-uk.md`](erps/sage-200-uk.md) | 4 default templates in 3 processes, mostly JSON module objects; writes back through `Part1` |
| Sage 300 | [`erps/sage-300.md`](erps/sage-300.md) | 6 default templates in 6 processes, mostly JSON module objects; nothing written back; 8 community templates |
| Sage 50 Canada | [`erps/sage-50-canada.md`](erps/sage-50-canada.md) | 10 default templates in 8 processes, mostly JSON module objects; writes back through `Part1` |
| Sage 50 UK | [`erps/sage-50-uk.md`](erps/sage-50-uk.md) | 2 default templates in 2 processes, mostly JSON module objects; nothing written back; 6 community templates |
| Sage 50 US | [`erps/sage-50-us.md`](erps/sage-50-us.md) | 10 default templates in 10 processes, mostly JSON module objects; writes back through `Part1`; 10 community templates |
| Sage 500 | [`erps/sage-500.md`](erps/sage-500.md) | 5 default templates in 4 processes, mostly JSON module objects; writes back through `Part1` |
| Sage Intacct | [`erps/sage-intacct.md`](erps/sage-intacct.md) | 8 default templates in 8 processes, mostly JSON module objects; writes back through `Part1` |
| Sage Live | [`erps/sage-live.md`](erps/sage-live.md) | 5 default templates in 4 processes, mostly JSON module objects; writes back through `Part1` |
| Sage X3 | [`erps/sage-x3.md`](erps/sage-x3.md) | 1 default template in 1 process, mostly JSON module objects; writes back through `Part1`; 2 community templates |
| SAP B1 | [`erps/sap-b1.md`](erps/sap-b1.md) | 8 default templates in 5 processes, mostly JSON module objects; writes back through `Part1`; 4 community templates |
| SAP Business ByDesign | [`erps/sap-business-bydesign.md`](erps/sap-business-bydesign.md) | 6 default templates in 3 processes, mostly JSON module objects; writes back through `Part1`; 4 community templates |
| SouthWare | [`erps/southware.md`](erps/southware.md) | 2 default templates in 2 processes, mostly JSON module objects; nothing written back |
| SQLConnector | [`erps/sqlconnector.md`](erps/sqlconnector.md) | no default templates; 8 community templates |
| SYSPRO 6 | [`erps/syspro-6.md`](erps/syspro-6.md) | 19 default templates in 18 processes, mostly JSON module objects; writes back through `Part1`; 13 community templates |
| Traverse 11 | [`erps/traverse-11.md`](erps/traverse-11.md) | 4 default templates in 4 processes, mostly JSON module objects; nothing written back |
| VAI S2K | [`erps/vai-s2k.md`](erps/vai-s2k.md) | 7 default templates in 4 processes, mostly JSON module objects; writes back through `Part1` |
| Workday | [`erps/workday.md`](erps/workday.md) | 5 default templates in 3 processes, mostly JSON module objects; writes back through `Part1` |
| Xero | [`erps/xero.md`](erps/xero.md) | 4 default templates in 3 processes, mostly JSON module objects; writes back through `Part1` |
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
- `dlake-txdownloaderpro-<erp>-hubspot` — one page per ERP that ships default templates for
  HubSpot.
- `dlake-integration-setup` — registration, CRM choice and the ERP connector.
- `dlake-crmpro` and `dlake-normalsync` — the inbound leg.
- `dlake` — general tenant operation.
