---
name: dlake-syncagent
description: >-
  Install and configure the **Commercient Sync Agent** — the on-premises Windows agent that runs on
  the customer's own ERP server — using `CommercientSyncAgentCLI.exe`, the command-line counterpart to
  the desktop application (command availability varies by agent build; check `--help` on the machine). Covers signing in, listing the account's licensed products, installing one,
  configuring it per product type (NormalSync, ODBC/FastODBC, TxDownloaderPro, IOTSync, QuickBooks),
  testing the connection, and reading the agent's own health. Use it when the task is "the customer
  downloaded the agent, now what", "install NormalSync on their server", "the agent isn't running",
  or "configure this unattended". This is the ON-PREMISES half of a setup: the `dlake` skills
  configure the platform side, this one configures the machine that talks to the ERP.
---

# Installing and configuring the Commercient Sync Agent

## 1. When you need this

Platform-side setup — tenant, connectors, table selection — happens through `dlake` and the
registration wizard. None of it moves data on its own. **A Windows agent has to run on the
customer's own server**, next to their ERP, and that agent is what actually reads the source.

This skill covers that machine. You need it at the point where the wizard says *download and run the
Sync Agent installer on the server your ERP runs on*, and for everything afterwards: installing a
product, pointing it at the right database, proving it can connect, and checking it is still healthy.

Two interfaces exist and they are peers, not alternatives:

- **The desktop application** — the installer the customer downloads and runs.
- **`CommercientSyncAgentCLI.exe`** — the console counterpart. Same backend, same local install.

They read and write the same registry entries, scheduled tasks and product configuration, so
**anything you set up with one is immediately visible to and manageable from the other**. Use the CLI
for scripting, remote administration, unattended installs and provisioning pipelines; use the desktop
app when someone is sitting at the machine.

## 2. What the machine needs

| Requirement | Detail |
|---|---|
| Operating system | Windows. The agent is a .NET Framework application and is **x86 only**. |
| Privileges | **Administrator.** Non-negotiable — the agent manages registry keys under `HKLM` and creates Windows scheduled tasks. |
| Account | A Commercient account with **at least one product licence**. |
| Placement | The server the ERP runs on, or one that can reach the ERP's database. |

If a command fails with exit code `9`, the account is not permitted to install — either it lacks the
licence or it is an admin-only account. That is an account question, not a machine question.

## 3. Signing in

Every command except `--help` and `--version` authenticates first. Credentials are the customer's
**Commercient account** — the same username and password they use for the portal.

```
CommercientSyncAgentCLI.exe -u <username> -p <password> <command>
```

Both are optional: omit `-u` and you are prompted, omit `-p` and you are prompted with hidden input.

> **Prefer the prompt to the flag whenever a human is present.** A password passed as `-p` on the
> command line is visible in shell history and in the process table for the lifetime of the command.
> In an unattended pipeline, pass it from an environment variable rather than a literal.

**The password must satisfy the portal's password rules**, because this is the portal credential:
8–32 characters, made only of letters, digits and `! # $ % & * ? @ _`, with at least one uppercase,
one lowercase, one digit and one of those specials. If the customer registered through
`dlake register start --generate-password`, the password they saved already conforms.

Start with `login` — it is the cheapest way to confirm the credentials, the network path and the
licence in one call:

```
CommercientSyncAgentCLI.exe -u you@company.com login
→ Login successful. Welcome, you@company.com. 3 product(s) available.
```

## 4. The sequence

Products go through the same four steps in this order. Do not skip `test` — it is the only step that
proves the configuration is usable before a sync depends on it.

```
products    →  install    →  configure   →  test  →  run-test
(what am I    (download +   (point it at   (can it  (does a real
 licensed     create its     the source)    connect)  sync work)
 for?)        sync task)
```

```
CommercientSyncAgentCLI.exe -u you@company.com -p "$PW" products
CommercientSyncAgentCLI.exe -u you@company.com -p "$PW" install   --product NormalSync
CommercientSyncAgentCLI.exe -u you@company.com -p "$PW" configure --product NormalSync
CommercientSyncAgentCLI.exe -u you@company.com -p "$PW" test      --product NormalSync
CommercientSyncAgentCLI.exe -u you@company.com -p "$PW" run-test  --product NormalSync
```

> **Check what your agent build accepts before scripting against it.** The commands and flags in this
> skill — the verbs above and `-p` — are supported by some agent builds and not others; newer builds
> may prompt for the password and present the products as a numbered menu instead. Try a command as
> written: if the build reports an unknown argument, or asks for the password rather than taking
> `-p`, drive that machine interactively and use this skill as the map of what each step does rather
> than as a script. `CommercientSyncAgentCLI.exe --help` on the machine in front of you is the
> authority on which form it takes.

Use the product name **exactly as `products` prints it** — that listing is the authority, and it also
tells you what is already installed, so it is the right first call on an unfamiliar machine.

`test` opens and closes a connection using the saved configuration. `run-test` actually runs a sync.
`test` failing is a configuration problem; `test` passing and `run-test` failing is a data or
permissions problem at the source.

## 5. Configuring, per product type

`configure` prompts for anything you do not pass, defaulting to the current value. So it is a guided
setup with no options, and fully unattended with every option — the same command either way.

**NormalSync, NormalSyncPro, NormalSyncSyspro, NormalSyncTraverse**

`--sql-hostname` · `--sql-username` · `--sql-password` · `--database` · `--schema` (defaults to `dbo`)

> **The SQL login you give NormalSyncPro must be `db_owner` on the ERP database.** Not
> `db_datareader` — the agent does not merely read the source. It creates and alters clone tables,
> table types and stored procedures in that database, and enables change tracking. A read-only login
> passes `test` (which only opens a connection) and then fails or silently does nothing during the
> sync, which is the expensive way to discover this.

**ODBC, FastODBC**

`--dsn` · `--odbc-username` · `--odbc-password` · `--table-list` (comma-separated) · `--client-db-type`

**TxDownloaderPro, IOTSync**

These have no fixed option list — their fields are defined server-side and vary by account. Set each
one with a repeated flag:

```
--set <FLAG_NAME>=<value> --set <FLAG_NAME>=<value>
```

**QuickBooksSyncAgent**

`--company-path` (the `.qbw` file) · `--exe-path` · `--batch-size` · `--history-months`

**ODBCQuickBookConfigure**

`--ignore-modals` · `--app-path` · `--process-name` · `--qb-username` · `--modals-to-close`

Not every product supports every command. Configuration works for all of them; the rest varies:

| Product type | `configure` | `test` | `run-test` | `docs` | `cleanup` |
|---|:---:|:---:|:---:|:---:|:---:|
| NormalSync family | ✅ | ✅ | ✅ | ✅ | ✅ |
| ODBC, FastODBC | ✅ | ✅ | — | ✅ | — |
| TxDownloaderPro | ✅ | ✅ | — | — | — |
| IOTSync | ✅ | — | — | — | — |
| ODBCQuickBookConfigure | ✅ | — | — | — | — |
| QuickBooksSyncAgent | ✅ | — | — | — | — |

## 6. Checking health

```
CommercientSyncAgentCLI.exe -u you@company.com -p "$PW" status
```

`status` is the one call worth scripting on a schedule. It reports the account, every product and
whether it is installed, **and the state of the agent's own scheduled tasks** — the auto-update task
and the optional Commercient Receiver. A product that is installed and configured still will not sync
if its task was never created or has been removed, and `status` is where that shows.

One thing neither `status` nor a clean `run-test` can tell you: **whether rows actually moved**. Normal
Sync runs incrementally against change tracking, so a run over an unchanged source — or one whose
initial load never completed — reports success while syncing nothing. To see rows-in-clones against
what the source held, and to queue a re-pull when tables are missing or short, use the platform side:
`dlake normalsync resync-status` and `dlake normalsync resync` (see the **dlake-normalsync** skill —
this skill deliberately does not restate those semantics).

## 7. Exit codes — check these, don't parse output

On builds that take the commands above, every one sets a meaningful exit code, and automation should
branch on the code rather than the text. Where a build presents the interactive menu instead, read the
codes below as the meaning of each outcome rather than as something to script against.

| Code | Meaning | Usually means |
|---|---|---|
| `0` | Success | |
| `1` | General error | |
| `2` | Invalid arguments | A typo in a flag, or a required one missing |
| `3` | Authentication failed | Wrong credentials, or a password the portal rejects |
| `4` | Product not found | Name does not match `products` output |
| `5` | Product already installed | Safe to skip to `configure` |
| `6` | Product not installed | Run `install` first |
| `7` | Configuration error | A field is missing or invalid |
| `8` | Connection/sync test failed | Credentials or reachability at the **source**, not at Commercient |
| `9` | Permission error | Account not licensed to install, or is admin-only |
| `10` | Operation failed | |

Codes `5` and `6` are the useful ones for idempotent scripts: treat `5` on install as success, and `6`
anywhere else as "install has not happened yet".

## 8. What it changes on the machine

Useful when something looks wrong and you need to see actual state rather than what a command reports.

| Where | What |
|---|---|
| `HKLM\SOFTWARE\CommercientSyncAgent\` | The agent's configuration and installed-product paths. Values are Base64-encoded, so they are unreadable at a glance but **not secret** — treat anything here as readable by any administrator on the box. |
| Windows Task Scheduler | The maintenance auto-update task, one sync task per installed product, and optionally a Receiver task. |
| `C:\ProgramData\Commercient\` | Where `uninstall` backs a product up before removing it, and where the updater stages downloads. |

**The agent updates itself.** A maintenance task runs every four hours as SYSTEM and applies updates
without asking. Do not pin or hand-patch an agent installation expecting it to stay put; if a version
matters for a diagnosis, record it at the time.

## 9. Removing a product

Two commands, and the difference matters:

```
CommercientSyncAgentCLI.exe ... uninstall --product NormalSync -y   # remove it, keeping a backup
CommercientSyncAgentCLI.exe ... cleanup   --product NormalSync -y   # remove what uninstall leaves behind
```

`uninstall` removes the product and its task, backing up to `C:\ProgramData\Commercient\`. `cleanup`
is the further step for a product being retired for good. Use `uninstall` alone if there is any
chance of reinstating it — and note that `cleanup` is only supported for the NormalSync family.

## 10. Where this fits

The agent is one half of a working integration. The other half is configured through `dlake`:

- **[`dlake-integration-setup`](../dlake-integration-setup/SKILL.md)** — stand the tenant up and drive
  the registration wizard. Its final step is where the customer gets the agent installer; **this skill
  is what happens next**.
- **[`dlake-normalsync`](../dlake-normalsync/SKILL.md)** — choose which ERP tables Normal Sync clones.
  Installing the NormalSync product here does not select any tables; that is done platform-side.
- **[`dlake-odbcsync`](../dlake-odbcsync/SKILL.md)** — the bucket, IP allow-list and `BridgeClient`
  config for an ODBC customer. **Both agents run on an ODBC customer**, so expect to install the ODBC
  product here *and* configure Normal Sync platform-side.
- **[`dlake-txdownloaderpro`](../dlake-txdownloaderpro/SKILL.md)** — the CRM→source writeback agent's
  platform-side configuration and field mapping.

A useful rule when something "isn't syncing": decide first whether the failure is on the **machine**
(agent installed? task present? `test` passing?) or on the **platform** (tables selected? connector
configured?). This skill answers the first; the `dlake` skills answer the second.
