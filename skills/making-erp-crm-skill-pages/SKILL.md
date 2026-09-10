---
name: making-erp-crm-skill-pages
description: >-
  Produce a `dlake-crmpro-<erp>-<crm>` sub-skill for a new ERP × CRM pair: which catalogue data to
  pull (the `crmpro_templates` admin tool scoped by `crmName`, or the shared-catalogue export from the
  registration database), how to run `gen-crmpro-skill.js` to fill every catalogue-derivable section,
  which sections a person must write and why the generator refuses to guess them, the naming rule for
  the skill folder, the publication rules these files are held to because they are served publicly,
  and the review checklist to run before the skill ships. Use it when asked to write, refresh or
  review a CRMPro destination sub-skill, or when a new ERP × CRM pair gets a template set.
---

# Making a `dlake-crmpro-<erp>-<crm>` skill page

A CRMPro sub-skill exists because **the same engine behaves differently per destination**, and the
differences are not documented anywhere else: object-name tokens, the repository key separator, what
carries record identity, how a destination id reaches a child record, and which destination-side
prerequisites silently produce a zero-record run. `dlake-crmpro` covers what is common. A sub-skill
covers exactly what is not.

Write one when a pair has a shipped template set and someone has to stand a customer up on it. Do not
write one from the CRM's public API documentation alone — a sub-skill that is not sourced from the
templates and the platform's own code is worse than none, because it reads authoritative.

## 1. Pull the catalogue

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
`Notes`, `GroupName`, `DetailDescription`, `MappingJson`, `CreateViewQuery`, `Insert_Query` and the
`IsDefault*` flags, `FOR JSON PATH`. Run a census first — group by `ERPName, CRMName` — so you know
which pairs have a template set at all. It is a read-only query against a shared catalogue: no
customer data, no credentials.

Also pull, once:

- the ERP catalogue (`code` + display name) — it is where the ERP slug comes from;
- the CRM catalogue (auth mode, callback mode, whether an end-to-end CLI flow exists) — it is the
  source for the connection facts in §8 of the skill.

**Never quote a customer-derived field from these rows.** `ImportedCount`, `SyncedRecordCount` and
`Rating` are counts and opinions collected from installs; they do not belong in a published page.

## 2. Name the skill

`dlake-crmpro-<erp-slug>-<crm-slug>`, all lower case, hyphen-separated.

- **CRM slug**: the CRM catalogue's name, lower-cased, non-alphanumerics removed — `salesforce`,
  `hubspot`, `shopify`, `zohocrm`, `bigcommerce`.
- **ERP slug**: the ERP catalogue's **code**, lower-cased, **digits kept** — `sage100`, `sage1000`,
  `epicorp21`, `msdynamicgp2017`. The digits are load-bearing: `SAGE100` and `SAGE1000` are different
  products, and dropping the digit would collide them.
- **The one exception**: where the ERP's display name says the entry covers a family rather than a
  single release — "SYSPRO 7 and Above" — drop the trailing version digits, giving `syspro` rather
  than `syspro7`. The catalogue entry already means "this release and later", so a version number in
  the folder name would age into a lie the first time the next release ships, and an operator
  searching for "syspro" would miss it. Prefer the family name whenever the catalogue itself is
  telling you the entry is a family.

The generator applies exactly this rule. Pass `--erp-slug` when you want to override it, and say why
in the skill's own text if the result is not obvious.

## 3. Generate the skeleton

```bash
node gen-crmpro-skill.js templates-SYSPRO7-Salesforce.json \
     --erp "SYSPRO 7 and Above" --erp-code SYSPRO7 --crm "Salesforce" \
     --out skills/dlake-crmpro-syspro-salesforce
```

It reads either input shape (the grouped tool output or the flat export rows) and fills in:

- the **template-group table** — group, objects, source views, and the create/update/delete behaviour
  each group's `IsDefault*` flags describe;
- the **`CRM_Configuration` table** — display name, object API name, PK field, `Sync_Order`,
  `TimeStamp_Prefix`, `SQL_Query`, the field prefix/postfix pair and the operation type, all parsed
  out of each template's `Insert_Query`;
- the **view table** — view name, which of the three kinds its `WHERE` makes it, which repository
  prefixes it joins, its identity columns, whether it emits `SFDCID`, and its source tables;
- the **cross-process dependency list** — every prefix a view reads that is not its own, which is the
  parent/child ladder;
- the **mapping summary** — how many fields each template maps and the first few;
- the **key separator**, detected from the views, and a verification query built around it;
- the **prefix list** the verification query should show.

It reports how many `<!-- AUTHOR: ... -->` placeholders it left. **A skill still containing one is not
finished.**

By default it describes `Standard` templates only and reports the `Community` count. Pass
`--include-community` when the Community set is what the customer will actually import.

## 4. Write the parts a person must write

The generator will not guess engine behaviour, and neither should you. These sections are yours:

| Section | What only a person can supply |
|---|---|
| frontmatter `description` | The trigger. It must name the values and the symptom, so an agent picks the skill up without being told to. |
| §1 run structure | The order a run does things on this destination, and where the log is. From the engine or a live run — not from the templates. |
| §2 prose | Which values are dispatch tokens matched case-sensitively, which are free choices, and which one an operator will get wrong. |
| §3 contract prose | The rules the table implies, one worked `CREATE VIEW`, and how this destination's shipped shape satisfies or violates the NULL-cursor rule. |
| §3 value conventions | Booleans, dates, currency, owner resolution, and every hard-coded literal the templates ship that a real customer must change. |
| §4 ownership | Whether this destination needs a seed/upsert pair, a create/update pair, or neither — and what the dependency list means for run-to-run sequencing. |
| §6 by hand | What must exist before the first process: base views, a managed package, a parent process that writes a prefix others read. |
| §7 check 7 | The zero-record checks specific to this destination. |
| §8 destination facts | Tiers, plan caps, required fields, mirror tables, how the connection authenticates. |
| §9 confirmation | How to confirm on the destination side, and what a partial result means. |

**Source every behavioural claim.** Three acceptable sources: the template SQL ("the templates
show…"), the parent skill, or the platform's own code. If you cannot source it, it goes in the
handover notes as an open question — never into the skill as prose. A sentence you cannot attribute
is the one that will send an operator down a two-day dead end.

**Compare against a working install where one exists.** The catalogue is what ships, not necessarily
what works: a shipped template can carry a misspelled object name, a separator that matches nothing,
or an operation type that conflicts with the view it ships beside. Where they disagree, the install
wins, and the skill should say the catalogue differs rather than quietly picking one.

## 5. Publication rules

These files are published to a public repository and served anonymously. Every one of these is a hard
rule, not a preference.

- **No incident narrative.** No "we found", no dates, no ticket ids, no counts observed on any
  install. Describe the behaviour, not the discovery.
- **No customer or tenant names**, in any form — not in an example, not in a query, not in a path.
- **No credentials, keys, tokens or connection strings.** Placeholder addresses only, and only
  `127.0.0.1` or the `203.0.113.0/24` documentation range for addresses.
- **No hostnames** beyond the public front doors the existing skills already use.
- **Product artefact names are fine and should be exact**: clone table names, view names, object API
  names, column names, flag names. Those are what an operator greps for.
- **Business language in the group table.** Say what the customer gets — "ERP customers become CRM
  accounts; new accounts are created, existing ones updated, none deleted" — derived from the
  template's description and its insert/update/delete defaults. Not "syncs ArCustomer to Account".

## 6. Review checklist

Before the skill ships:

1. **No `AUTHOR` placeholders remain**; the generator's count is zero.
2. **Frontmatter**: `name` matches the folder, and the `description` names both the values and the
   symptom.
3. **Every behavioural claim is sourced** to the templates, the parent skill, or the platform code —
   and the ones that are not are in the handover notes as open questions instead.
4. **The key separator is right** in the view examples, in `TimeStamp_Prefix`, and in the
   verification query. This is the single most common copy-across error between destinations.
5. **The object names are copied, not retyped**, including any misspelling the catalogue ships.
6. **The parent-skill overlap is a reference, not a repeat.** A sub-skill that restates
   `dlake-crmpro` rots twice as fast.
7. **The sweep**: search the finished text for hex or base64-like runs, long mixed letter/digit
   tokens, `@` addresses, GUIDs, IP addresses, hostnames, quoted literals that look like secrets, and
   the words *incident*, *regression*, *bug*, *we found* and any ticket prefix. Fix before shipping.
8. **A reader who has never seen this destination can build one process from the skill alone**, and a
   reader debugging a silent zero-record run reaches the cause from §7 without opening the code.
9. **The README rows and the cross-references** in every sibling skill's "Where this sits" section
   mention the new skill.
