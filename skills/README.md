# Agent skills for the `dlake` CLI

Drop-in **skills** that brief an AI coding agent on how to drive the Commercient Data Lake / Data Hub
CLI correctly — the right command *ordering*, the non-obvious gotchas, and the HTTP contract for the
Data API. A skill is a small folder your agent loads on demand, turning "figure `dlake` out by trial
and error" into "already knows the happy path."

## Available skills

| Skill | What it covers |
|-------|----------------|
| [`dlake/`](dlake/SKILL.md) | Build and operate a tenant end-to-end: schema → expose → restart → scoped key, the sharp edges (the scoped-key restart, exposed-vs-raw entities, IDENTITY-key limits), and the REST/GraphQL + events contract — all in one [`SKILL.md`](dlake/SKILL.md). |
| [`dlake-integration-setup/`](dlake-integration-setup/SKILL.md) | Stand up a NEW integration: register a tenant, seed it, then drive the setup wizard — server IP, CRM (connect now or later, OAuth included), and the ERP connector that declares Syspro/QuickBooks/SQL Server/ODBC. Covers the wizard's ordering and guards, credential lifetimes, and how to resume days later. |
| [`dlake-txdownloaderpro/`](dlake-txdownloaderpro/SKILL.md) | Set up **and operate TxDownloaderPro** — the CRM→source (writeback) sync agent. Expose its gateway objects to the Data API and scope a key to them, then CRUD the configuration and in-flight transaction rows, edit the field mapping in their JSON/XML columns, and use the filter-operator vocabulary that decides which retrieved CRM records reach the source. Covers exposure-vs-key-scope, `keyFields`, and the `SFUpdated` state machine. |
| [`dlake-txdownloaderpro-salesforce/`](dlake-txdownloaderpro-salesforce/SKILL.md) | The **Salesforce** specifics for a TxDownloaderPro writeback, from the shipped default templates: the query shape that finds flagged records and the marker columns it reads, the structure of the inbound mapping document and the `$FUN_` token names it carries, which `ResultStructure` parts the templates fill for the write back to Salesforce, and the process row each template becomes on import. Extends `dlake-txdownloaderpro/`. |
| [`dlake-txdownloaderpro-hubspot/`](dlake-txdownloaderpro-hubspot/SKILL.md) | The **HubSpot** specifics for a TxDownloaderPro writeback, from the shipped default templates: the query shape that finds flagged records and the marker columns it reads, the structure of the inbound mapping document and the `$FUN_` token names it carries, which `ResultStructure` parts the templates fill for the write back to HubSpot, and the process row each template becomes on import. Extends `dlake-txdownloaderpro/`. |
| [`dlake-txdownloaderpro-dynamicscrm/`](dlake-txdownloaderpro-dynamicscrm/SKILL.md) | The **Dynamics CRM** specifics for a TxDownloaderPro writeback, from the shipped default templates: the query shape that finds flagged records and the marker columns it reads, the structure of the inbound mapping document and the `$FUN_` token names it carries, which `ResultStructure` parts the templates fill for the write back to Dynamics CRM, and the process row each template becomes on import. Extends `dlake-txdownloaderpro/`. |
| [`dlake-txdownloaderpro-zohocrm/`](dlake-txdownloaderpro-zohocrm/SKILL.md) | The **Zoho CRM** specifics for a TxDownloaderPro writeback, from the shipped default templates: the query shape that finds flagged records and the marker columns it reads, the structure of the inbound mapping document and the `$FUN_` token names it carries, which `ResultStructure` parts the templates fill for the write back to Zoho CRM, and the process row each template becomes on import. Extends `dlake-txdownloaderpro/`. |
| [`dlake-txdownloaderpro-shopify/`](dlake-txdownloaderpro-shopify/SKILL.md) | The **Shopify** specifics for a TxDownloaderPro writeback, from the shipped default templates: the query shape that finds flagged records and the marker columns it reads, the structure of the inbound mapping document and the `$FUN_` token names it carries, which `ResultStructure` parts the templates fill for the write back to Shopify, and the process row each template becomes on import. Extends `dlake-txdownloaderpro/`. |
| [`dlake-normalsync/`](dlake-normalsync/SKILL.md) | Choose which ERP tables **Normal Sync** — the on-prem change-tracking agent for SQL Server 2008 R2+ — clones into the gateway database's `dbo` clone tables. The available-tables dropdown, the two-call add, per-table sync toggles and row filters, the shared-catalogue semantics, and the prerequisites this surface cannot set (so finishing it does not mean the customer syncs). Plus **resync** — queue a full or per-table re-pull for the agent's next run — and **clone coverage**, rows actually in each clone against the count recorded at last sync (a green sync run is not evidence rows moved). |
| [`dlake-crmpro/`](dlake-crmpro/SKILL.md) | Set up **and operate CRMPro** — the source→CRM (forward) sync agent that pushes ERP data into the supported CRM and e-commerce platforms. Flag-driven: CRUD the configuration and run-history/error tables, read and edit the field mapping, and know which of its tables are reachable as lake views and which deliberately are not. |
| [`dlake-crmpro-hubspot/`](dlake-crmpro-hubspot/SKILL.md) | The **HubSpot** specifics for a CRMPro forward sync: the configuration values the HubSpot engine dispatches on, the DLO view contract for it — the three view kinds and the `SavedTimeStamp` cursor rule that decides which rows a view returns — the seed/upsert pair for CRM-owned versus ERP-owned fields, the `CRM_FieldList` rows an object needs before it pushes anything, and what to check when a run completes without pushing any records. Extends `dlake-crmpro/`. |
| [`dlake-crmpro-salesforce/`](dlake-crmpro-salesforce/SKILL.md) | The **Salesforce** specifics for a CRMPro forward sync: the configuration values the Salesforce engine dispatches on — standard objects alongside the managed package's custom objects, the namespace prefix/postfix pair, and an external-id field as the match key — the view contract the shipped templates use (a single-colon repository key, the identity column named as the external id, no `SFDCID` output column), the id-chaining ladder that makes `Sync_Order` a dependency order, the reverse-lookup and create/update process pairs, and what to check when a run completes without pushing any records. Extends `dlake-crmpro/`; carries one `erps/` child page per source ERP. |
| [`dlake-crmpro-shopify/`](dlake-crmpro-shopify/SKILL.md) | The **Shopify** specifics for a CRMPro forward sync: the upper-case object tokens, the display name that reads as an operation, the insert-only create legs and their `::` repository key, and the two update legs — which are not cursor-driven at all but compare the ERP value against a mirrored Shopify value and push only the difference. Covers why a created product never updates, and what an empty mirror table does to a run. Extends `dlake-crmpro/`; carries one `erps/` child page per source ERP. |
| [`dlake-crmpro-zohocrm/`](dlake-crmpro-zohocrm/SKILL.md) | The **Zoho CRM** specifics for a CRMPro forward sync, read from the shipped templates: the objects those templates push to, the repository key they build, the view and `TimeStamp_Prefix` conventions, and which template groups every source ships versus only some. Extends `dlake-crmpro/`; carries one `erps/` child page per source ERP. |
| [`dlake-crmpro-dynamicscrm/`](dlake-crmpro-dynamicscrm/SKILL.md) | The **Dynamics CRM** specifics for a CRMPro forward sync, read from the shipped templates: the objects, the repository key, the view and `TimeStamp_Prefix` conventions, and what to check when a run pushes nothing. Extends `dlake-crmpro/`; carries one `erps/` child page per source ERP. |
| [`dlake-crmpro-mdc/`](dlake-crmpro-mdc/SKILL.md) | The **MDC** specifics for a CRMPro forward sync, read from the shipped templates: the objects, the repository key, the view and `TimeStamp_Prefix` conventions, and what to check when a run pushes nothing. Extends `dlake-crmpro/`; carries one `erps/` child page per source ERP. |
| [`dlake-crmpro-magento/`](dlake-crmpro-magento/SKILL.md) | The **Magento** specifics for a CRMPro forward sync, read from the shipped templates: the objects, the repository key, the view and `TimeStamp_Prefix` conventions, and what to check when a run pushes nothing. Extends `dlake-crmpro/`; carries one `erps/` child page per source ERP. |
| [`dlake-odbcsync/`](dlake-odbcsync/SKILL.md) | Configure **ODBC Sync** — the agent for a source that is NOT Microsoft SQL Server, which stages data through an S3 bucket into an intermediary database Normal Sync then consumes. The bucket registry, the IAM IP allow-list, the agent's `BridgeClient.exe.config` (and the encrypted-echo trap), and the two verification reads. |
| [`dlake-apisync/`](dlake-apisync/SKILL.md) | Set up **Generic API Sync** — the product for a source that is an API rather than a database. Enable it (schema provision + flag), describe the endpoints the agent calls (the two-mode save trap), read the shared per-ERP template catalogue, and read a hosted customer's real ERP columns. |
| [`dlake-syncagent/`](dlake-syncagent/SKILL.md) | Install and configure the **Commercient Sync Agent** — the on-premises Windows agent on the customer's own ERP server — with `CommercientSyncAgentCLI.exe`, the scriptable peer of the desktop app. The install/configure/test sequence, the per-product configuration fields, the exit codes to branch on, what it writes to the registry and Task Scheduler, and how it self-updates. The ON-PREMISES half of a setup; the other skills cover the platform half. |

Use `dlake/` for a tenant you already have and `dlake-integration-setup/` for one you are
creating. The rest cover the **sync agents**, in pipeline order: `dlake-normalsync/` extracts
the customer's ERP tables into the gateway database's clone tables (with `dlake-odbcsync/`
configuring the ODBC agent that stacks underneath it for non-SQL-Server sources, and
`dlake-apisync/` the product for API sources), `dlake-crmpro/` pushes those
clone tables source→CRM, and `dlake-txdownloaderpro/` moves changes back CRM→source. Most
integrations run Normal Sync plus CRMPro; add TxDownloaderPro when changes made in the CRM must
travel back. Normal Sync, CRM Pro and TxDownloaderPro are three distinct products — if a table is
missing from the CRM, `dlake-normalsync/` tells you whether it is an extract problem and
`dlake-crmpro/` whether it is a push problem. The destination sub-skills — `dlake-crmpro-hubspot/`,
`dlake-crmpro-salesforce/`, `dlake-crmpro-shopify/`, `dlake-crmpro-zohocrm/`,
`dlake-crmpro-dynamicscrm/`, `dlake-crmpro-mdc/` and `dlake-crmpro-magento/` — carry the
per-destination values, which genuinely differ. The writeback leg has the same shape:
`dlake-txdownloaderpro-salesforce/`, `-hubspot/`, `-dynamicscrm/`, `-zohocrm/` and `-shopify/` carry
the per-destination values for TxDownloaderPro.

### The `erps/` child pages

A destination skill says what is true of that CRM whatever the source ERP is. What the *shipped
templates* set up depends on the source, so each destination skill carries one child page per source
ERP the catalogue ships templates for:

```
dlake-crmpro-hubspot/
├── SKILL.md                 # the HubSpot conventions, plus the ERP table
└── erps/
    ├── sage100.md           # what the SAGE 100 → HubSpot templates set up
    ├── syspro7.md
    └── …
```

Every destination `SKILL.md` carries an **ERP table** listing its children with a line on what each
one's templates deliver, and the instruction for picking a row: identify the tenant's source ERP —
`dlake register erps` lists the catalogue's names and codes, `dlake admin crmpro_templates` shows
what a tenant can actually import — then read that row's page. Where no row matches, the destination
skill alone applies.

A child is addressed by its path: `dlake skills show dlake-crmpro-hubspot/erps/sage100`.
`dlake skills install` writes the children beside their parent's `SKILL.md`, so an agent that loads
the destination skill has them on disk already, and `dlake skills list` still lists skills — the
children are files of a skill, not skills of their own.

Those all configure the **platform** side. `dlake-syncagent/` is the other half: the Windows agent
that runs on the customer's own ERP server and actually reads the source. Nothing syncs until it is
installed and configured there, so reach for it once the platform side is set up — and when
something "isn't syncing", use it to decide whether the problem is on the machine (agent installed?
task present? connection testing?) or on the platform (tables selected? connector configured?).

## Install

A skill is just a folder. Copy it into the directory your harness scans for skills:

| Harness | Skills directory |
|---------|------------------|
| **Claude Code / Cowork** | `.claude/skills/<skill>/` (per-project) or `~/.claude/skills/<skill>/` (global) |
| **OpenAI Codex** | `~/.codex/skills/<skill>/` or your project's skills directory |
| **OpenCode** | `.opencode/skill/<skill>/` |
| **Other harnesses** | wherever the harness discovers skills — keep the `<skill>/SKILL.md` layout intact |

The CLI installs and refreshes all of them in one step, and it is the copy that stays current:

```bash
npm install -g @commercient/dlake@latest   # the skills ship inside the CLI; updates are frequent
dlake skills install                        # writes every skill where your agent looks, refreshing existing copies
```

Check `dlake --version` against `npm view @commercient/dlake version` before relying on a skill; a
newer CLI means newer skill text. Copying from a clone works too, but a copied file does not
refresh itself:

```bash
# from a clone of this repo — install any or all
cp -r skills/dlake                   ~/.claude/skills/   # operate an existing tenant
cp -r skills/dlake-integration-setup ~/.claude/skills/   # set up a new integration
cp -r skills/dlake-normalsync        ~/.claude/skills/   # which ERP tables get cloned
cp -r skills/dlake-odbcsync          ~/.claude/skills/   # the non-SQL-Server source agent
cp -r skills/dlake-apisync           ~/.claude/skills/   # the API-source sync product
cp -r skills/dlake-crmpro            ~/.claude/skills/   # the source→CRM sync agent
cp -r skills/dlake-crmpro-hubspot    ~/.claude/skills/   # HubSpot specifics for CRMPro
cp -r skills/dlake-crmpro-salesforce ~/.claude/skills/   # Salesforce specifics for CRMPro
cp -r skills/dlake-crmpro-shopify    ~/.claude/skills/   # Shopify specifics for CRMPro
cp -r skills/dlake-crmpro-zohocrm    ~/.claude/skills/   # Zoho CRM specifics for CRMPro
cp -r skills/dlake-crmpro-dynamicscrm ~/.claude/skills/   # Dynamics CRM specifics for CRMPro
cp -r skills/dlake-crmpro-mdc        ~/.claude/skills/   # MDC specifics for CRMPro
cp -r skills/dlake-crmpro-magento    ~/.claude/skills/   # Magento specifics for CRMPro
cp -r skills/dlake-txdownloaderpro   ~/.claude/skills/   # the CRM→source sync agent
cp -r skills/dlake-txdownloaderpro-salesforce ~/.claude/skills/   # Salesforce specifics for TxDownloaderPro
cp -r skills/dlake-txdownloaderpro-hubspot    ~/.claude/skills/   # HubSpot specifics for TxDownloaderPro
cp -r skills/dlake-txdownloaderpro-dynamicscrm ~/.claude/skills/   # Dynamics CRM specifics for TxDownloaderPro
cp -r skills/dlake-txdownloaderpro-zohocrm    ~/.claude/skills/   # Zoho CRM specifics for TxDownloaderPro
cp -r skills/dlake-txdownloaderpro-shopify    ~/.claude/skills/   # Shopify specifics for TxDownloaderPro
cp -r skills/dlake-syncagent         ~/.claude/skills/   # the on-premises agent on the ERP server
```

Confirm your harness's exact path against its own docs — skill-loading conventions still vary between
tools and change over time.

## How it works

Each skill is a `SKILL.md` with YAML frontmatter (`name` + `description`) followed by instructions.
The **`description` is the trigger**: your agent reads it and decides whether the skill is relevant to
the task at hand — so it fires on "build a backend on the data lake" or "why is my `dlk_` key 403-ing?"
without you naming the skill. Most skills are one file; where the detail depends on something the
skill cannot know up front, it is split into child files the `SKILL.md` names and points at, so they
load only when relevant (progressive disclosure) — which is what the destination skills' `erps/`
children are.

```
dlake/
└── SKILL.md                 # trigger + build sequence + rules + Data API HTTP contract

dlake-crmpro-hubspot/
├── SKILL.md                 # the HubSpot conventions + the ERP table that points at the children
└── erps/
    └── sage100.md           # what the SAGE 100 → HubSpot templates set up
```

## See also

- **CLI reference:** `dlake guide cli` · **live API guide:** `dlake guide api`
- Install the CLI: `npm install -g @commercient/dlake`
- Questions / issues: [support@commercient.com](mailto:support@commercient.com)
