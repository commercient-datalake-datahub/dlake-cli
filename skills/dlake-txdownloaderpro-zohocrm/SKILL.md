---
name: dlake-txdownloaderpro-zohocrm
description: >-
  What the shipped default TxDownloaderPro templates set up when Zoho CRM is the writeback
  destination: the three-member JSON `Query` naming the module to retrieve, the six templates
  that carry a `SELECT` instead, why the stored `Where` member must not be read as documentation,
  the modules the default set names, the flat `ProcessStructure` mapping with its `Line.` section
  and `Line.mainXml` collection member, the `$FUN_` value-token names the documents carry, the
  `ResultStructure` parts the templates fill for the write back to ZOHO, and the
  `TxDownloaderPro` process row each template becomes on import. Use it when importing or reading
  a Zoho CRM writeback template set, when a process retrieves nothing, when a mapped field
  arrives empty, or when deciding where a change belongs. It extends `dlake-txdownloaderpro`,
  which covers operating TxDownloaderPro generally; the per-ERP pages
  `dlake-txdownloaderpro-<erp>-zohocrm` carry each ERP's own default template set.
---

# TxDownloaderPro ← Zoho CRM: what the shipped default templates set up

> **Keep this skill current.** `dlake` ships updates often and this text is embedded in the CLI
> you have installed. Before relying on it, compare `dlake --version` with
> `npm view @commercient/dlake version`; if they differ, run `npm install -g @commercient/dlake@latest`
> and then `dlake skills install`, which overwrites the installed skill files with the current text.

**The source ERP has its own page under this skill.** `erps/<erp>.md` is a child file of this
skill and describes what the shipped templates for that ERP → Zoho CRM pair set up. §7 lists every
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

The default set ships **126 templates across 25 ERP names, in 68 field-process versions** — the
second largest destination after Salesforce. Each of those ERPs has its own page,
`dlake-txdownloaderpro-<erp>-zohocrm`.

## 1. What the templates deliver

| Transaction family | What the templates deliver | Operations the flags carry |
|---|---|---|
| **Customer / account** | An `Accounts` record becomes a receivable customer in the ERP, with its addresses, and the ERP's customer code comes back onto the account | create, update, delete |
| **Contact** | A `Contacts` record under a synced account becomes an ERP customer contact | create, update |
| **Sales order** | A `Sales_Orders` record with its `Product_Details` becomes an ERP sales order | create, update |
| **Quote** | A `Quotes` record with its product details becomes an ERP quote or estimate | create, update |
| **Invoice** | An order or quote becomes a receivable invoice, with lines | create, update |
| **Product / item** | `Products` records become ERP items | create, update |
| **Vendor** | An account becomes an ERP vendor | create, update |
| **Job** | An account or order becomes the ERP's job object, on the ERPs that have one | create, update |

Across the 126 default templates, **66 carry `IsInsert`, 53 carry `IsUpdate`, 8 carry `IsDelete`
and 3 carry `IsCustomization`**. A flag decides which operation the process is allowed to
perform, not which one it performs on a given record. 18 rows carry a licence-group id.

**The catalogue carries no `BusinessDescription` on any ZOHO row.** The business language on the
per-ERP pages is each row's `Message` — direction of travel and two object names — and nearly all
of the processes have one that survives publication.

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

**120 of the 126 default templates carry a JSON `Query` with three members** — `ModuleName`,
`selectedFields` and `Where` — and **6 carry a `SELECT` statement** instead. Check which shape a
process holds before editing it; the parent's §9 is explicit that `Query` is not one format
across the product.

- **`ModuleName`** names the module to retrieve. The default set names `Accounts`, `Contacts`,
  `Quotes`, `Sales_Orders` and `Products`, and a few templates use lower-case spellings of them.
  The spelling is also the path root the mapping document has to use (section 4).
- **`selectedFields`** is non-empty on all 120 and holds the field list to request.
- **`Where` is non-empty on all 120, and what it holds is not a filter you can rely on.** Some of
  those values are stored record identifiers rather than conditions. Their contents are not
  reproduced here and are not documentation of anything: **do not copy a `Where` value from one
  tenant's template into another's**, and do not expect putting a condition there to narrow a
  run.
- **Operators the six `SELECT` templates use:** `=`, `!=`, `AND`, a null test and an
  empty-string test. Only 5 of the 126 templates carry a condition at all.

### The marker conventions

The default ZOHO queries name few marker columns, and the conventions are this destination's own
spellings rather than the managed-package ones:

| Convention | What it is for |
|---|---|
| an import marker — `Commercient_Import` | the user's own "send this" flag, on the templates that filter at all |
| a customer-code field — `Commercient_ArCustomer_Code`, `CommercientSF__Commercient_ArCustomerCode__c` | the ERP's customer code on the account, written by an earlier run |
| an external-key field — `Commercient_ExternalKey` | the ERP's key for the record, and the usual `Part1` writeback target (section 5) |

**Where the filtering happens** is therefore the thing to understand on this destination: for the
120 module-named templates the query names a module, not a condition, so a run retrieves what the
module returns and the selection the parent's §12 describes is applied after retrieval. Only the
six `SELECT` templates filter on the CRM side.

## 4. The inbound mapping document

`ProcessStructure` is a flat JSON object: each member names a field on the source side and its
value is a template resolved against the retrieved record's XML document (parent §11). All 126
default templates carry one; **5 do not parse as JSON** and are counted but not described. A
parseable document carries about 12 members.

- **Path roots the documents use:** `Accounts`, `Contacts`, `Sales_Orders`, `Quotes`, `Products`,
  `Product_Details`, `Account`, `Order`, and lower-case spellings of several of them. `Accounts`
  and `accounts` are different strings; the root has to match what the engine emitted.
- **25 of the parseable documents carry a `Line.` section and 23 name the collection through
  `Line.mainXml`.** The collection is normally the order's or quote's `Product_Details`. The
  members beside it — item, quantity, amount, unit price, tax and description in each ERP's
  spelling — resolve against that collection's own root, not through the header. **Two templates
  carry a `Line.` section with no `Line.mainXml`**, so nothing tells the engine which collection
  to loop, and one spells the member with a different capitalisation of `mainXml`, which is a
  different key.
- **`$FUN_` value tokens the documents carry:** `$FUN_SUBSTR`, `$FUN_UNESCAPEXML`,
  `$FUN_ISNULL`. **Names only; no semantics are claimed.** The parent's §12 is explicit that the
  platform-side resolver is dotted path substitution only — these are evaluated by the service on
  the customer's own host.

## 5. Result structure — what goes back to Zoho CRM

Of the 126 default templates, **56 carry a parseable `DefaultResultStructure` and 69 carry none**;
one does not parse.

| Part | Filled by | What it addresses | Members the default set uses |
|---|---|---|---|
| `Part1` | 56 templates | the record the run is already working with | a source-path-to-CRM-field map |
| `Part2` | 1 template | the child/line records under it | `ObjectAPIName`, `LoopFieldTagName`, `LoopFieldIDName`, `FieldName` |
| `Part3` | none (explicitly null on all 56) | a **new** record | — |
| `Part4` | none (explicitly null on all 56) | a **different** record | — |

- **CRM fields `Part1` writes to:** `Commercient_ExternalKey`, `arcustomercode`,
  `comrcint_arcustomercode`, `Commercient_ArCustomer_Code`, and a per-ERP document-number field
  on two sets. Three naming styles coexist here — the ZOHO field style, the lower-case
  `comrcint_` style carried over from another destination, and the managed-package style — and
  they are not interchangeable.
- **Response members it reads them from:** `internalId`, `ClientId`, `ObjectID`, `CustomerID`,
  `VendorId`, `CustID`, `CustNum`, `UID`, `NewCustomerCode`, `ItemCode`, `Company`, `LineID`,
  `number`, `EstimateGuid`. Which exists depends on the source system's response.
- **The one `Part2` template** names `Product_Details` as its child object and writes
  `Commercient_ExternalKey` onto the line records.

**The map is written source-path first, CRM-field second** (parent §11); the wrong way round
resolves to the same silent empty string as a mistyped path. **A template with no
`ResultStructure` writes nothing back** — over half of this destination's default set — so check
that column before investigating anything else when the ERP's number does not appear on the CRM
record.

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

The order of diagnosis: which query shape is this process holding, and is the module name right
(section 3); does each path root match the emitted document, and is there a `Line.mainXml` where
lines are expected (section 4); is there a `ResultStructure` at all (section 5). The parent's §14
is the authority on the tools, §10 on the state.

## 7. The source ERP’s own page

One row per source ERP the catalogue ships default Zoho CRM templates for. Each page is a child
file of this skill, addressed as `dlake-txdownloaderpro-zohocrm/erps/<erp>` — `dlake skills show
dlake-txdownloaderpro-zohocrm/erps/<erp>` prints one, and `dlake skills install` writes them
beside this file.

<!-- ERP-TABLE:BEGIN dlake-txdownloaderpro-zohocrm -->
| ERP | Page | What its templates deliver |
|---|---|---|
| Epicor 10 | [`erps/epicor-10.md`](erps/epicor-10.md) | 3 default templates in 2 processes, mostly JSON module objects; writes back through `Part1` |
| Epicor 9 and 9.5 | [`erps/epicor-9-and-9-5.md`](erps/epicor-9-and-9-5.md) | 2 default templates in 1 process, mostly JSON module objects; writes back through `Part1` |
| Epicor Prophet 21 (P21) | [`erps/epicor-prophet-21-p21.md`](erps/epicor-prophet-21-p21.md) | 4 default templates in 2 processes, mostly JSON module objects; writes back through `Part1` |
| Infor SyteLine | [`erps/infor-syteline.md`](erps/infor-syteline.md) | 3 default templates in 3 processes, mostly `SELECT` queries; writes back through `Part1` |
| Microsoft Business Central | [`erps/microsoft-business-central.md`](erps/microsoft-business-central.md) | 5 default templates in 3 processes, mostly JSON module objects; writes back through `Part1` |
| Microsoft Dynamics GP | [`erps/microsoft-dynamics-gp.md`](erps/microsoft-dynamics-gp.md) | 2 default templates in 1 process, mostly JSON module objects; writes back through `Part1` |
| Microsoft Dynamics NAV | [`erps/microsoft-dynamics-nav.md`](erps/microsoft-dynamics-nav.md) | 2 default templates in 1 process, mostly JSON module objects; writes back through `Part1` |
| MYOB AccountRight | [`erps/myob-accountright.md`](erps/myob-accountright.md) | 4 default templates in 1 process, mostly JSON module objects; writes back through `Part1` |
| NetSuite | [`erps/netsuite.md`](erps/netsuite.md) | 12 default templates in 4 processes, mostly JSON module objects; writes back through `Part1` |
| QuickBooks Desktop | [`erps/quickbooks-desktop.md`](erps/quickbooks-desktop.md) | 5 default templates in 3 processes, mostly JSON module objects; writes back through `Part1` |
| QuickBooks Online | [`erps/quickbooks-online.md`](erps/quickbooks-online.md) | 4 default templates in 3 processes, mostly JSON module objects; nothing written back |
| Sage 100 (US) | [`erps/sage-100-us.md`](erps/sage-100-us.md) | 10 default templates in 8 processes, mostly JSON module objects; writes back through `Part1` |
| Sage 100 Contractor | [`erps/sage-100-contractor.md`](erps/sage-100-contractor.md) | 10 default templates in 3 processes, mostly JSON module objects; writes back through `Part1` |
| Sage 100 Contractor 2018 | [`erps/sage-100-contractor-2018.md`](erps/sage-100-contractor-2018.md) | 10 default templates in 3 processes, mostly JSON module objects; writes back through `Part1` |
| Sage 200 UK | [`erps/sage-200-uk.md`](erps/sage-200-uk.md) | 2 default templates in 1 process, mostly JSON module objects; writes back through `Part1` |
| Sage 300 | [`erps/sage-300.md`](erps/sage-300.md) | 5 default templates in 3 processes, mostly JSON module objects; nothing written back |
| Sage 50 Canada | [`erps/sage-50-canada.md`](erps/sage-50-canada.md) | 2 default templates in 1 process, mostly JSON module objects; nothing written back |
| Sage 50 UK | [`erps/sage-50-uk.md`](erps/sage-50-uk.md) | 4 default templates in 3 processes, mostly JSON module objects; writes back through `Part1` |
| Sage 50 US | [`erps/sage-50-us.md`](erps/sage-50-us.md) | 17 default templates in 11 processes, mostly JSON module objects; writes back through `Part1` |
| Sage 500 | [`erps/sage-500.md`](erps/sage-500.md) | 2 default templates in 1 process, mostly JSON module objects; writes back through `Part1` |
| Sage Intacct | [`erps/sage-intacct.md`](erps/sage-intacct.md) | 1 default template in 1 process, mostly `SELECT` queries; writes back through `Part1` |
| Sage Live | [`erps/sage-live.md`](erps/sage-live.md) | 2 default templates in 1 process, mostly JSON module objects; writes back through `Part1` |
| SAP B1 | [`erps/sap-b1.md`](erps/sap-b1.md) | 7 default templates in 4 processes, mostly JSON module objects; writes back through `Part1` |
| SYSPRO 6 | [`erps/syspro-6.md`](erps/syspro-6.md) | 2 default templates in 1 process, mostly JSON module objects; writes back through `Part1` |
| VAI S2K | [`erps/vai-s2k.md`](erps/vai-s2k.md) | 6 default templates in 3 processes, mostly JSON module objects; writes back through `Part1`, `Part2` |
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
- `dlake-txdownloaderpro-<erp>-zohocrm` — one page per ERP that ships default templates for this
  destination.
- `dlake-integration-setup` — registration, CRM choice and the ERP connector.
- `dlake-crmpro` and `dlake-normalsync` — the inbound leg.
- `dlake` — general tenant operation.
