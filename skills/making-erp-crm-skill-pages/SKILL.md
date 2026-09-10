---
name: making-erp-crm-skill-pages
description: >-
  Produce or refresh a `dlake-crmpro-<erp>-<crm>` sub-skill for an ERP × CRM pair from the shipped
  template catalogue: which catalogue data to pull (the `crmpro_templates` admin tool scoped by
  `crmName`, or the shared-catalogue export from the registration database), how to run
  `gen-crmpro-skill.js` and `split-and-generate-all.js`, the contract the generated page is held to —
  state what the templates show, omit everything else, never leave a placeholder, always defer to the
  parent `dlake-crmpro` — the naming rule for the skill folder, the publication rules these files are
  held to because they are served publicly, and the review checklist to run before the skill ships.
  Use it when asked to write, refresh or review a CRMPro destination sub-skill, or when a new ERP × CRM
  pair gets a template set.
---

# Making a `dlake-crmpro-<erp>-<crm>` skill page

A CRMPro sub-skill exists because **the same engine behaves differently per destination and per ERP**,
and the differences are not documented anywhere else: object-name tokens, the repository key
separator, what carries record identity, how a destination id reaches a child record, and which
processes have to run before which. `dlake-crmpro` covers what is common. A sub-skill covers exactly
what the templates for one pair add to it.

## 1. The contract these pages are written to

From the product owner, and it governs everything below:

> "these skills must be based on the templates and state what we do know. Not what we don't have. We
> will incrementally improve them but they must reference their parent."

In practice:

- **Only what the templates show.** Every statement is a fact about the shipped `Insert_Query`,
  `CreateViewQuery` or `MappingJson`, stated in the present tense as a fact about the templates —
  "the templates create…", "each process row sets…" — never as a finding or a discovery.
- **Omit what the catalogue cannot source.** No `<!-- AUTHOR -->` placeholders, no "unknown", no "to
  be written", no gap lists, no open questions in the page. A section with nothing to say is not
  emitted at all, and the page's sections are numbered as they are emitted, so there are no holes.
- **Open questions go to the maintainers**, in the handover notes (`REPORT.md`) — never into a page.
- **Every page names its parent.** Right after the frontmatter and the "Keep this skill current"
  banner, a short paragraph names `dlake-crmpro` as the authority for the tools, the tables, the
  repository and the source-view contract, names the hand-written sibling for that CRM where one
  exists (`dlake-crmpro-hubspot`, `dlake-crmpro-syspro-salesforce`, `dlake-crmpro-syspro-shopify`),
  and says the page adds only what the templates for this pair set.
- **Incremental by design.** The page says it grows as the catalogue does. A thin page for a thin
  template set is the correct output, not a failure.

**Generated pages are marked as generated.** The frontmatter carries `generated: true` and the line
after it is an HTML comment saying the page came from the generator and that a hand-edit promotes it
to hand-written and it must then be reviewed line by line. Leave both in place while a page is
generated; remove them only as part of a deliberate promotion, and then review the whole file.

## 2. Pull the catalogue

Two sources; both give the same rows.

**Per pair, through the admin plane** — the normal route:

```bash
dlake admin crmpro_templates --profile <tenant> --crmName Salesforce > templates-<erp>-Salesforce.json
```

`crmName` overrides the CRM the tenant is registered against, so one tenant on the right ERP can dump
every CRM's catalogue for that ERP. The result is grouped: an array of
`{ type: "MainGroup", groupName, subTemplates: [...] }`.

**Whole estate, from the registration database** — when you need pairs no tenant is registered for.
Query `CRM_Default_Configuration` left-joined to `LicenceGroup` (and to its parent group), selecting
`ERPName`, `CRMName`, `TemplateType`, `CRM_Object_Display_Name`, `CRM_Object_API_Name`, `Source`,
`Notes`, `GroupName`, `ParentGroupName`, `DetailDescription`, `MappingJson`, `CreateViewQuery`,
`Insert_Query` and the `IsDefault*` flags, `FOR JSON PATH`. Run a census first — group by
`ERPName, CRMName` — so you know which pairs have a template set at all. It is a read-only query
against a shared catalogue: no customer data, no credentials.

Two things to know about that export. `FOR JSON PATH` **omits NULL columns**, so a row without a
`TemplateType` is a Standard template, and a row without a `GroupName` is ungrouped. And the export is
large — split it per pair and stream it; never read it into a conversation.

Also pull, once:

- the ERP catalogue (`code` + display name) — it is where the ERP slug and the page's ERP name come
  from;
- the CRM catalogue (`name` + `displayName`, auth mode, callback mode) — the display name is how a
  page says what a catalogue spelling such as `MEGENTO` is the name of.

**Never quote a customer-derived field from these rows.** `ImportedCount`, `SyncedRecordCount` and
`Rating` are counts and opinions collected from installs; they do not belong in a published page. The
generator never reads them.

## 3. Name the skill

`dlake-crmpro-<erp-slug>-<crm-slug>`, all lower case, hyphen-separated.

- **CRM slug**: the CRM catalogue's name, lower-cased, non-alphanumerics removed — `salesforce`,
  `hubspot`, `shopify`, `zohocrm`, `dynamiccrm`, `megento`.
- **ERP slug**: the ERP catalogue's **code**, lower-cased, **digits kept** — `sage100`, `sage1000`,
  `epicorp21`, `msdynamicgp2017`. The digits are load-bearing: `SAGE100` and `SAGE1000` are different
  products.
- **The one exception**: where the ERP's display name says the entry covers a family rather than a
  single release — "SYSPRO 7 and Above" — the generator drops the trailing version digits, giving
  `syspro`. That is the right name for a hand-written page. **For bulk generation the driver passes
  `--erp-slug` with the plain code slug** (`syspro7`), so a generated page never lands on the folder
  of a hand-written one.

## 4. Generate

One pair:

```bash
node --max-old-space-size=6144 gen-crmpro-skill.js pairs/syspro7-salesforce.json \
     --erp "SYSPRO 7 and Above" --erp-code SYSPRO7 --crm Salesforce \
     --crm-display Salesforce --erp-slug syspro7 \
     --out generated-all/dlake-crmpro-syspro7-salesforce
```

Every pair, from the estate export:

```bash
node --max-old-space-size=6144 split-and-generate-all.js            # splits the export, then generates
node --max-old-space-size=6144 split-and-generate-all.js --reuse-pairs   # regenerate from pairs/ only
```

`split-and-generate-all.js` writes `generated-all/INDEX.tsv`: ERP, CRM, skill name, template count,
Standard count, page line count, detected key separator, section count, the sections emitted, and the
generator's own sweep result per page.

The generator reads either input shape and emits, **only where the templates support it**:

1. frontmatter — `name`, a `description` naming the ERP, the CRM, the objects and the symptom,
   `generated: true`;
2. the title, the currency banner, and the parent-deference paragraph;
3. **What the templates deliver** — one row per template group, in business language, from
   `DetailDescription`/`Notes` plus the `IsDefault*` flags, with objects and source tables;
4. **The process rows the templates create** — display name, object API name, PK field, `Sync_Order`,
   `TimeStamp_Prefix`, `SQL_Query` target, the field prefix/postfix pair, the operation type, and the
   uniform flag values as prose. Only columns the inserts actually set appear;
5. **The views** — kind (by the parent's three definitions), the repository key literal and separator,
   the other processes' prefixes it reads, identity columns, whether it emits `SFDCID` and source tables.
   No template SQL is quoted: shipped view definitions can carry customer-specific values, and a skill
   is generic by rule;
6. **Order of work** — the `Sync_Order` ladder and the parent/child dependencies the prefix reads
   imply, including any prefix no template in the set writes;
7. **Field mapping** — per template the mapped-field count and the first ERP → CRM pairs, plus the
   `CRM_FieldList` rule from the parent and the instruction to check `crmpro_field_mapping` before
   activating;
8. **Verifying** — the per-prefix repository count query and the `withId = n` success condition;
9. **Where this sits** — parent, siblings, and the extract and writeback skills.

Only `Standard` templates are described. Community templates are customer-authored and are not published;
a pair with no Standard template gets no page, and the driver records it as skipped in `INDEX.tsv`.

**Judgement the generator applies, so you do not have to re-derive it.** A column/value count mismatch
in an `Insert_Query` makes that row unparsed rather than guessed at. A view's change-detection kind is
only stated when the `WHERE` matches one of the parent's three shapes — with the cursor equality
inside the `ON` clause, `[Key] IS NULL` is the insert + update shape. A prefix counts as written only
with the separator its own view builds, so a `NAME::` lookup against a process writing `NAME:` shows
up as unwritten. Catalogue descriptions that are authoring notes ("Click the view name…", "Do not
import this until…") are not published.

## 5. Publication rules

These files are published to a public repository and served anonymously. Every one is a hard rule.

- **No incident narrative.** No "we found", no dates, no ticket ids, no counts observed on any
  install. Describe the behaviour, not the discovery.
- **No customer or tenant names**, in any form — not in an example, not in a query, not in a path.
- **No credentials, keys, tokens or connection strings**; no addresses; no hostnames beyond the public
  front doors the existing skills already use.
- **Product artefact names are exact**: clone tables, view names, object API names, column names, flag
  names — including any misspelling the catalogue ships. Copy, do not correct. Where a spelling is
  plainly the catalogue's own, the page may say so; it never silently fixes it.
- **Business language in the group table.** Say what the customer gets, derived from the template's
  description and its insert/update/delete defaults — not "syncs ArCustomer to Account".

## 6. Review checklist

Before a page ships:

1. **No placeholders, no gap language.** Search for `AUTHOR`, `TODO`, "unknown", "to be written".
   The generator emits none; a hand-edit must not introduce any.
2. **Frontmatter**: `name` matches the folder, `generated: true` is present while the page is
   generated, and the `description` names the pair, the objects and the symptom.
3. **The parent is named** in the opening paragraph and in "Where this sits", and the sibling
   hand-written page for that CRM is named where one exists.
4. **Every claim traces to a template**, the parent skill, or the platform code. Anything else belongs
   in `REPORT.md` as an open question.
5. **The key separator is right** in the view table, in `TimeStamp_Prefix`, and in the verification
   query — the most common copy-across error between destinations.
6. **Object names are copied, not retyped**, including catalogue misspellings.
7. **The sweep**: run `node sweep-generated.js <dir>`. It applies the nine shapes — hex runs, base64
   runs, long mixed alphanumeric tokens, `@` addresses, tokens beside user/login/email/password,
   quoted literals, GUIDs, addresses and hostnames, the banned names, and the banned words. Add the
   shapes of customer identifiers — CRM record ids, customer codes, company suffixes — because a bare
   value beside a column name matches no label pattern. Fix **the generator**, never the page.
8. **Against a working install.** The catalogue is what ships, not necessarily what works. Where a
   live install disagrees, the install wins; record the disagreement in `REPORT.md`.
9. **The README rows and the cross-references** in the sibling skills' "Where this sits" sections
   mention the new page.
