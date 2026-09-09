---
name: dlake-syncagent
description: >-
  Install and configure the **Commercient Sync Agent** — the on-premises Windows agent that runs on
  the customer's own ERP server — using `CommercientSyncAgentCLI.exe`, the command-line counterpart to
  the desktop application. Covers signing in, listing the account's licensed products, installing
  one, configuring it per product type (NormalSync, ODBC/FastODBC, TxDownloaderPro, IOTSync,
  QuickBooks), reading a saved configuration back, testing the connection, and reading the agent's
  own health. Also covers the PLATFORM side of the same decision — `dlake registration products`,
  which says which products that agent should run. Use it when the task is "the customer downloaded
  the agent, now what", "install NormalSync on their server", "the agent isn't running", or
  "configure this unattended". This is the ON-PREMISES half of a setup: the `dlake` skills configure
  the platform side, this one configures the machine that talks to the ERP.
---

# Installing and configuring the Commercient Sync Agent

> **Keep this skill current.** `dlake` ships updates often and this text is embedded in the CLI
> you have installed. Before relying on it, compare `dlake --version` with
> `npm view @commercient/dlake version`; if they differ, run `npm install -g @commercient/dlake@latest`
> and then `dlake skills install`, which overwrites the installed skill files with the current text.

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

Products go through the same steps in this order. Do not skip `test` — it is the only step that
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
CommercientSyncAgentCLI.exe -u you@company.com -p "$PW" config    --product NormalSync
CommercientSyncAgentCLI.exe -u you@company.com -p "$PW" test      --product NormalSync
CommercientSyncAgentCLI.exe -u you@company.com -p "$PW" run-test  --product NormalSync
```

`config` (singular) READS a product's saved configuration back and never modifies it; passwords and
secrets are not printed. `configure` is the one that writes. Reach for `config` first on a machine
someone else set up — it answers "what is this pointed at" without risking a change.

Use the product name **exactly as `products` prints it** — that listing is the authority, and it also
tells you what is already installed, so it is the right first call on an unfamiliar machine.

`test` opens and closes a connection using the saved configuration. `run-test` triggers the
product's real scheduled sync task and monitors it to completion, reporting per-table progress.
`test` failing is a configuration problem; `test` passing and `run-test` failing is a data or
permissions problem at the source.

`CommercientSyncAgentCLI.exe --help`, and `<command> --help` for any one command, is the authority
on the machine in front of you. Check it before scripting against a build you have not used.

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

Not every product supports every command. `configure` and `config` work for all of the types below;
the rest varies:

| Product type | `configure` / `config` | `test` | `run-test` | `docs` | `cleanup` |
|---|:---:|:---:|:---:|:---:|:---:|
| NormalSync family | ✅ | ✅ | ✅ | ✅ | ✅ |
| ODBC, FastODBC | ✅ | ✅ | — | ✅ | — |
| TxDownloaderPro | ✅ | ✅ | — | — | — |
| IOTSync | ✅ | — | — | — | — |
| ODBCQuickBookConfigure | ✅ | — | — | — | — |
| QuickBooksSyncAgent | ✅ | — | — | — | — |

A command a product does not support fails with a message saying so, not silently.

## 6. Checking health

```
CommercientSyncAgentCLI.exe -u you@company.com -p "$PW" status
```

`status` is the one call worth scripting on a schedule. It reports the account, every product and
whether it is installed, **and the state of the agent's own scheduled tasks** — the auto-update task
and the Commercient Receiver. A product that is installed and configured still will not sync if its
task was never created or has been removed, and `status` is where that shows.

**`Commercient Receiver: Not Created` is a diagnostic, not a detail.** The Receiver
(`CommercientReceiver.exe`, run by a scheduled task named `Commercient Receiver Task - <ApplicationName>`)
is a **WebSocket CLIENT**: it stays connected to the Commercient service and starts a product's sync
when a run command arrives from that side. With it created, a sync can be triggered **on demand**
instead of waiting for the product's own scheduled task. With it `Not Created`, on-demand runs cannot
reach that server at all and the product runs only on its schedule — so "I asked for a run and
nothing happened" is answered here first.

The task can only be created once `CommercientReceiver.exe` is present under the install path's
`CommercientReceiver` folder; the agent's updater is what puts it there. A `Not Created` on a machine
that has never updated is therefore expected, and the fix is to let the updater run rather than to
create a task by hand.

One thing neither `status` nor a clean `run-test` can tell you: **whether rows actually moved**. Normal
Sync runs incrementally against change tracking, so a run over an unchanged source — or one whose
initial load never completed — reports success while syncing nothing. To see rows-in-clones against
what the source held, and to queue a re-pull when tables are missing or short, use the platform side:
`dlake normalsync resync-status` and `dlake normalsync resync` (see the **dlake-normalsync** skill —
this skill deliberately does not restate those semantics).

## 7. Exit codes — check these, don't parse output

Every command sets a meaningful exit code. Automation should branch on the code rather than on the
text, which is written for a person.

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
| `HKLM\SOFTWARE\CommercientSyncAgent\` | The agent's install path and per-product configuration, shared with the desktop app and the updater. Values are Base64-encoded, so they are unreadable at a glance but **not secret** — treat anything here as readable by any administrator on the box. |
| Windows Task Scheduler | `Commercient Update Task` (the auto-updater), one sync task per installed product, and `Commercient Receiver Task - <ApplicationName>` (the WebSocket client that accepts on-demand run commands). |

**The agent updates itself.** The update task runs with highest privileges, starting daily and
repeating every four hours, and applies updates without asking. Do not pin or hand-patch an agent
installation expecting it to stay put; if a version matters for a diagnosis, record it at the time.

## 9. Removing a product

Two commands, and the difference is not "more thorough" versus "less" — they act on different
machines' worth of state:

```
CommercientSyncAgentCLI.exe ... uninstall --product NormalSync -y   # this machine
CommercientSyncAgentCLI.exe ... cleanup   --product NormalSync -y   # the ERP DATABASE. Destructive.
```

`uninstall` removes the product's scheduled task and its installed files. Local, reversible by
reinstalling and reconfiguring.

`cleanup` is **destructive and irreversible, and it does not touch the local install at all**: it
connects to the product's configured SQL Server database and DROPS Change Tracking along with the
`Log_*` / `LogSF_*` tables and stored procedures. That is the customer's ERP database. Run it only
for a product being retired for good, and only with the customer's agreement. It is supported for
the NormalSync family only.

Both prompt for confirmation; `-y` (`--yes`) skips the prompt and is required for unattended use —
which is exactly the flag to think twice about on `cleanup`.

## 9b. The platform half — `dlake registration products`

An agent on the customer's server is only half the arrangement. The other half is a list in the
customer's own gateway database saying **which products that agent should run** — and the wizard
never sets it. Phase 1 is `CommercientCRMPro` (11); Phase 2 is `CommercientTxDownloaderPro` (22).

This is what "the agent is installed but nothing happens" usually turns out to be. Check it from the
platform side, with a tenant API key whose user holds the Admin role:

```bash
# What is registered, and what names `add` will accept
dlake registration products list --profile <tenant>

# Register one, by id or by its exact name. Idempotent — re-running changes nothing.
dlake registration products add 11 --profile <tenant>
dlake registration products add CommercientTxDownloaderPro --profile <tenant>

# Register it AND ask for the agent to be installed, in one call
dlake registration products add 22 --profile <tenant> --request-install

# Ask for the agent without registering anything
dlake registration products request-install --profile <tenant>

# Remove a registration. Soft — the row stays, marked removed.
dlake registration products remove 11 --profile <tenant>
```

Three rules worth knowing before you use it:

- **Product names are lookup keys, and several are misspelled in the estate on purpose.** The
  installer matches them literally, so a name that differs by one character creates a SECOND row for
  the same product and the installer then tries to install it twice. `products list` prints the
  accepted names; use them verbatim and never "correct" one. An unknown name is refused, and the
  refusal carries the accepted list — pass that on rather than guessing.
- **`--request-install` is a REQUEST, not an install.** It arms the switch the installer reads;
  somebody still has to run the installer on the customer's ERP server. Nothing in this skill's
  first nine sections happens by itself.
- **A registered product with no processes does nothing.** Registering says what to run; the Phase 1
  and Phase 2 configuration (`dlake-crmpro`, `dlake-txdownloaderpro`) says what it runs ON.

`installedDate` in that listing is a local wall-clock reading from the customer's own server, not
UTC — the installer writes it with no offset recorded anywhere. Do not convert it.

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
