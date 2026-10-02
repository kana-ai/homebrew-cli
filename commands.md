# Kana CLI — command reference

End-user reference for **`kana`** and **`gh kana`**. Behavior is the same for both; examples use **`kana`**.

**New to the CLI?** See [README.md](README.md) (next to this file in the CLI source tree; on **[kana-ai/gh-kana](https://github.com/kana-ai/gh-kana)** the same content is published as **README.md** at the repository root).

---

## Running `kana` with no subcommand

Running **`kana`** or **`gh kana`** alone prints:

- Current **workspace (customer)**
- Current **app** or **module** (and **local clone path**, when saved)

Then it prints **help** (available subcommands).

---

## Quick lookup table

| Command | What it does |
|--------|----------------|
| **`kana auth`** | Browser OAuth2 sign-in (`kana:read`, `kana:write`) |
| **`kana version`** | CLI version and build metadata |
| **`kana customer`** `[name]` | Choose workspace → **`~/.kana/current-customer.json`** (interactive, or pass **`name`** to pick non-interactively) |
| **`kana config repos-dir`** `[path]` | Show or set the root directory for local clones (**`reposDir`** in **`~/.kana/config.json`**); env **`KANA_REPOS_DIR`** overrides when set |
| **`kana init`** | Start or restart the development environment for the **current** app or module (**`./script/init`**) |
| **`kana healthcheck`** | Check for sanity over the **current** app or module development state (**`./script/healthcheck`**; alias: **`kana health-check`**) |
| **`kana push`** `"<summary>"` | Commit the working tree, push your branch, build, and deploy to **your test environment** (**`./script/push "<summary>"`**). Distinct from **`kana publish`** below. |
| **`kana publish`** | Upgrade, then fast-forward **`main`** to your commit so it becomes the live (**production**) version (**`./script/publish`**; **`--continue`** skips the verify stop) |
| **`kana sync_workspace`** | Merge your branch from the server and then **`main`** into the workspace, refresh the template files, and push the result unless there is nothing new (**`./script/sync_workspace`**) |
| **`kana test`** | Open the test environment (**`./script/test`**) |
| **`kana test clear`** | Clear the test environment database (**`./script/test clear`**) |
| **`kana test reset`** | Reset the test environment database to the live database state (**`./script/test reset`**) |
| **`kana app create`** `<name>` | Create app; sets **current app** |
| **`kana app use`** `[name]` | Set **current app** (a main app or an extension); pick from **all** of them if name omitted; **clones** if there is no local checkout; with a checkout, offers **Codex** / **Claude CLI** / **VS Code** / **Cursor** / **skip** unless **`--yes`** |
| **`kana app current`** | Print saved working app (friendly names) |
| **`kana app list`** | List main apps and their extensions by sidebar section |
| **`kana app edit`** | Interactive: external auth, linked modules, MCP, managed DBs, features, billing, access, A2A |
| **`kana app extern-auth`** `list` \| `add` \| `remove` | Non-interactive external OAuth integrations (type ids) |
| **`kana app extern-module`** `list` \| `add` \| `remove` | Non-interactive linked modules (datasrc ids); alias: **`kana app module`** |
| **`kana app mcp`** `list` \| `add` \| `remove` | Non-interactive external MCP servers |
| **`kana app managed-db`** `list` \| `add` \| `remove` \| `wipe` | Managed DBs (sqlite3 / BigQuery); aliases: **`managed-dbs`**, **`db`** |
| **`kana app features`** `list` \| `enable` \| `disable` | Optional sym-skills feature toggles |
| **`kana app billing`** `get` \| `set` \| `portal` | Stripe Pricing Table IDs + Customer Portal |
| **`kana app access`** `get` \| `set` \| `users` | Access & Sharing (company / restricted / public) |
| **`kana app library`** `status` \| `promote` \| `unpromote` | Promote / re-promote / remove from the app library (libop) |
| **`kana app a2a`** `status` \| `broadcast` \| `agents …` | A2A broadcast and linked agents |
| **`kana app slack`** `status` \| `connect` \| `disconnect` | Slack workspace OAuth |
| **`kana app schedule`** `list` \| `add` \| `mod` \| `del` \| `run` \| `system …` | Prompt + system schedules |
| **`kana app connection`** `datasrc` \| `config` \| `refs-check` \| `keys` | Connection datasrcs / config (beyond extern-auth) |
| **`kana app extension`** `list` \| `create` \| `delete` | App extensions |
| **`kana app tile`** `set` \| `clear-icon` | Tile name / desc / bgcolor / icon |
| **`kana module create`** `<name>` | Create module; sets **current module** |
| **`kana module list`** | List modules (module apps) |
| **`kana module use`** / **`current`** / **`edit`** / settings commands | Same pattern as **`app`**, using **`current-module.json`** |
| **`kana delete`** | Remove **current** target’s local dev files; **`--remote`** also deletes in Kana |
| **`kana app delete`** / **`kana module delete`** | Same; name optional; **`--remote`** for server-side |

Repo scripts require a **local clone** (a git repository, checked out on a branch named after your email) and a **current** app or module. Paths resolve from **`~/.kana/current-app.json`** / **`current-module.json`** (**`localRepoPath`**). Older CLI versions may have stored the module workflow under a different filename in **`~/.kana/`**; that file is still read for migration until **`current-module.json`** is written again.

---

## Auth and configuration

### `kana auth`

Opens the browser for OAuth. Stores tokens in **`~/.kana/auth.json`**.

### `kana version`

Prints the CLI version (release builds embed git metadata).

### `kana customer [name]`

Chooses the workspace (customer) and saves **`~/.kana/current-customer.json`**. With **multiple** workspaces and **no** **`[name]`**, lists numbers and prompts. With **`[name]`**, selects by display name (exact case-insensitive match, else unique prefix or substring).

Needed before most API-backed flows when you have not fixed **`--customer-id`** on each command.

### `kana config`

Shows persisted CLI settings in **`~/.kana/config.json`**. The documented subcommand is **`repos-dir`** (see below). Other keys may exist for internal or advanced tooling and are not described in this reference.

### `kana config repos-dir`

Optional argument **`[path]`**. Controls where **`kana app create`**, **`kana app use`**, **`kana module create`**, **`kana module use`**, and related flows put **local dev workspaces** (cloned repos). **`current-app.json`** / **`current-module.json`** store **`localRepoPath`** under this tree.

- **No arguments:** prints **`reposDir`** from **`config.json`** (if any) and the **effective** directory the CLI uses (after env overrides).
- **With `[path]`:** saves an absolute path as **`reposDir`** in **`config.json`**.

**Precedence** for the effective clone root (highest wins):

1. **`KANA_REPOS_DIR`** when set in the environment  
2. **`reposDir`** in **`~/.kana/config.json`**  
3. Default: **`~/.kana/repos`** (or **`<KANA_STATE_DIR>/repos`** when using a custom state directory)

Changing **`reposDir`** does **not** move existing project folders; move them yourself or clone again after switching roots.

---

## Apps

### `kana app create <name>`

Creates an app through the same APIs as the web flow.

**Flags:**

| Flag | Meaning |
|------|---------|
| **`--yes`** / **`-y`** | Skip confirmation; skip interactive optional build prompt unless **`--prompt`**, **`--prompt-file`**, or **`--no-prompt`** |
| **`--prompt "…"`** | Optional build / coding prompt |
| **`--prompt-file <path>`** | Same as **`--prompt`**, but prompt text is read from the file (UTF-8); **`~`** in the path is expanded |
| **`--no-prompt`** | Do not send a prompt |
| **`--customer-id N`** | Workspace customer for **`x-kana-cid`** without **`current-customer.json`** |

### `kana app use [name]`

Sets **`~/.kana/current-app.json`**. In Kana, each **orchestrator** row **is** the app (there are no separate “apps inside” another shell).

- **Pick target:** With **no** **`[name]`** and no disambiguating flags, shows **all** main apps and their **extensions** in the workspace in **one** numbered list. Extension lines are marked **`[extension]`** and name the app they extend. Each line notes whether there is already a **local checkout** under **`~/.kana/repos/`** or **no local checkout**.
- **Name / ids:** Pass **`[name]`** or **`--app-name`** (an app or extension display name), or **`--app-id`** (the app or extension **widgetappid**) when that id is unique in the workspace (see scope flags). Selecting an extension clones that extension and makes it the current app, so **`init`**, **`push`**, and **`publish`** run against it.
- **Clone:** If there is **no** local dev workspace for that app, the CLI asks whether to **clone** it (same **`clone_app.sh`** flow as create: a git clone of the app's repository on a branch named after your email). Declining still saves the **current app** id without **`localRepoPath`**. **`--yes`** / **`-y`** skips the question and clones non-interactively (and skips editor open prompts, same as **`app create --yes`**). After clone, the editor prompt below is offered; a terminal agent you pick there (**Claude CLI**, or **Codex** when no Codex desktop app is installed) opens in a **new** system terminal window, not the current shell.
- **Editor prompt:** When you already have a **local checkout** for the app you picked, the CLI still offers **Codex**, **Claude CLI**, **VS Code**, **Cursor**, or **skip** (same as after a fresh clone), unless you passed **`--yes`** / **`-y`**.

**Flags:** scope flags (below), **`--yes`** / **`-y`**, **`--customer-id`**.

### `kana app current`

Prints the current app (friendly names for display).

### `kana app list`

Lists **main** apps (not modules) and each app's **extensions**, grouped by sidebar section. Extension lines are marked **`[extension]`**. Each line shows whether the app is already **checked out** under **`~/.kana/repos/`** (or **`localRepoPath`**) or **no local checkout** yet.

### `kana app edit`

Edits App Settings slices for the selected app’s orchestrator row: **external auth**, **linked modules**, **external MCP servers**, **managed DBs**, **features**, **billing**, **Access & Sharing**, and **A2A**. **External auth** and **linked modules** are written to the API as soon as you finish each interactive editor (no separate “save all” step). Other submenus save when you perform each change. With no flags, uses **current app** when still valid, otherwise interactive resolution.

**Flags:** scope flags, **`--yes`** / **`-y`** (skip the confirmation prompt before those saves), **`--customer-id`**.

### `kana app extern-auth` and `kana app extern-module`

Non-interactive counterparts of the **`edit`** menu (same targeting flags). **`extern-auth add <typ>`** / **`remove <typ>`** use integration type integers from the server’s external-auth catalog (**`list`** shows enabled types). **`extern-module add <datasrcid>`** / **`remove <datasrcid>`** update linked modules; **`extern-module list`** prints linked datasrc ids and resolved names. The older name **`module`** is still accepted as an alias (e.g. **`kana app module list`**).

Under **`kana module …`**, the nested **`extern-module`** subcommand is **`kana module extern-module list`** (and **`add`** / **`remove`**) — same behavior as **`kana app extern-module …`**, scoped to module orchestrator rows (alias: **`kana module module …`**).

### `kana app mcp` / `kana module mcp`

Non-interactive MCP list/add/remove (see quick table).

### `kana app managed-db` / `kana module managed-db`

Non-interactive managed databases (same APIs as App Settings → Managed DBs). Aliases: **`managed-dbs`**, **`db`**.

| Subcommand | Meaning |
|------------|---------|
| **`list`** | List managed DBs (`service_id`, name, engine, table count, default/removable) |
| **`add <sqlite3\|bigquery>`** | Add an extra managed DB (`sqlite` / `bq` accepted as aliases) |
| **`remove <service_id>`** | Delete a removable DB (default sqlite3 cannot be removed; extra sqlite must have zero tables) |
| **`wipe [service_id]`** | Wipe DB contents (DBs stay; schema returns from **`dbs.json`** on next run). Omit **`service_id`** to wipe all. **`--prod`** (default true) / **`--test-env`** select which copies |

Destructive **`remove`** / **`wipe`** confirm unless **`--yes`**.

### `kana app features` / `kana module features`

Optional sym-skills feature toggles (App Settings → Services & Features).

| Subcommand | Meaning |
|------------|---------|
| **`list`** | Available features with on/off state (library installs are locked) |
| **`enable <feature_id>`** | Enable one feature |
| **`disable <feature_id>`** | Disable one feature |

### `kana app billing` / `kana module billing`

Stripe Pricing Table IDs (App Settings → Billing). Setting requires the app developer.

| Subcommand | Meaning |
|------------|---------|
| **`get`** | Show live/test pricing table IDs and purchase flags (aliases: **`show`**, **`list`**) |
| **`set`** | **`--live`** / **`--test`** to set; **`--clear-live`** / **`--clear-test`** to clear |
| **`portal`** | Mint Stripe Customer Portal URL (**`--test`**, **`--open`**) |

### `kana app slack` / `kana module slack`

| Subcommand | Meaning |
|------------|---------|
| **`status`** | Workspace connection + personal link flags |
| **`connect`** | Print/open Slack OAuth URL; poll until connected (**`--open`**, **`--timeout`**) |
| **`disconnect`** | Disconnect workspace |

### `kana app schedule` / `kana module schedule`

Prompt schedules (`schedules_*`) and system schedules (`app_sched_*`).

| Subcommand | Meaning |
|------------|---------|
| **`list`** / **`add`** / **`mod`** / **`del`** / **`run`** | Prompt schedules CRUD + run-now |
| **`system list`** / **`enable`** / **`disable`** / **`run <name>`** | System schedules + process runner |

### `kana app connection` / `kana module connection`

Extras beyond **`extern-auth`** (toggle still uses **`extern-auth`**).

| Subcommand | Meaning |
|------------|---------|
| **`datasrc add\|del`** | Extra warehouse/Gmail datasrcs |
| **`refs-check`** | Whether source references a connection |
| **`config get\|set`** | App-level `configuration_required` values |
| **`keys`** | Workspace Keys configured status |

### `kana app extension` / `kana module extension`

| Subcommand | Meaning |
|------------|---------|
| **`list`** | Extensions of the target |
| **`create <name>`** | Create extension (**`--desc`**, **`--prompt`**) |
| **`delete <widgetcontid>`** | Delete extension |

### `kana app tile` / `kana module tile`

| Subcommand | Meaning |
|------------|---------|
| **`set`** | **`--bgcolor`**, **`--icon <file>`**, **`--name`**, **`--desc`** |
| **`clear-icon`** | Clear thumb icon |

### `kana app access` / `kana module access`

Access & Sharing (company / restricted / public).

| Subcommand | Meaning |
|------------|---------|
| **`get`** | Show level, users (restricted), wckey / auth typ (public), A2A broadcast, library privacy |
| **`users`** | List workspace users (`userid` + email) for Restricted **`--user`** |
| **`set`** | Partial update — see flags below |

**`access set` flags** (omit any flag to leave that field unchanged):

| Flag | Meaning |
|------|---------|
| **`--level company\|restricted\|public`** | Access level |
| **`--user <id\|email>`** | Restricted allow-list (repeatable; replaces the list). Leaving Restricted for another level clears the list |
| **`--wckey`** / **`--gen-wckey`** | Public share-link token (or generate a new one) |
| **`--auth-typ global\|user`** | Public auth mode |
| **`--user-typ auto\|external`** | When **`auth-typ=user`** |
| **`--name`** / **`--desc`** | Display name / tile description |
| **`--lib-private`** / **`--clear-lib-private`** | Library visibility (requires libop) |

Switching to **public** without a stored wckey auto-generates one.

### `kana app library` / `kana module library`

Promote or re-promote an app/module to the **app library**, or remove it (libop only — same as the web “Promote to library” / “Update library (Re-promote)” / “Remove from library” actions).

| Subcommand | Meaning |
|------------|---------|
| **`status`** | Whether the target is promoted (`public` / `private` / not promoted) and its **`libid`** |
| **`promote`** | First promote or re-promote (refresh the library copy after **`kana publish`**). Aliases: **`re-promote`**, **`repromote`** |
| **`unpromote`** | Remove from the library. Alias: **`remove`** |

**`library promote` flags:**

| Flag | Meaning |
|------|---------|
| **`--public`** / **`--private`** | Library visibility (first promote; also overrides on re-promote). Interactive menu if omitted and not **`--yes`** (defaults to public with **`--yes`**) |
| **`--allow-schema-clashes`** | On re-promote, proceed even when installed copies would see destructive DB schema changes |
| **`--yes`** / **`-y`** | Skip confirmation prompts (does **not** imply **`--allow-schema-clashes`**) |

Targeting flags match other settings commands (**`--app-name`**, **`--app-id`**, **`--customer-id`**, …).

### `kana app a2a` / `kana module a2a`

A2A broadcast and linked agents (App Settings → MCP & A2A).

| Subcommand | Meaning |
|------------|---------|
| **`status`** | Broadcast on/off + linked agents |
| **`broadcast on\|off`** | Toggle broadcasting this app as an A2A agent (off also drops inbound links) |
| **`agents list`** | Agents linked to this app |
| **`agents available`** | Workspace apps currently broadcasting |
| **`agents add\|remove <widgetcontid>`** | Link / unlink one agent |
| **`agents set [widgetcontid…]`** | Full replace of the linked set (no ids = clear) |
---

## Modules

Commands mirror **`kana app …`** but target modules (module apps) and **`~/.kana/current-module.json`**.

### `kana module create <name>`

**Note:** optional interactive **build prompt** is **not** shown for modules; use **`--prompt`** or **`--prompt-file`** if you want auto-code on create.

Other **`create`** flags match **`kana app create`**.

### `kana module use`, `kana module current`, `kana module list`, `kana module edit`

Same roles as the **`app`** equivalents (**`module use`** lists **all** modules and can clone when there is no checkout, like **`app use`**; with a local checkout it offers **Codex** / **Claude CLI** / **VS Code** / **Cursor** / **skip** unless **`--yes`**). **`module list`** shows the same per-row **checked out** / **no local checkout** hints as **`app list`**.

---

## Repo scripts (local clone)

These run executables under **`script/`** in your project root (**`~/.kana/repos/…`**).

| Command | Script |
|---------|--------|
| **`kana init`** | **`./script/init`** |
| **`kana healthcheck`** | **`./script/healthcheck`** |
| **`kana push`** `"<summary>"` | **`./script/push "<summary>"`** (commit, push your branch, build, deploy to your test environment) |
| **`kana publish`** | **`./script/publish`** (sync, then fast-forward **`main`** — production; distinct from **`kana push`**) |
| **`kana sync_workspace`** | **`./script/sync_workspace`** (merge your branch from the server, then **`main`**; refresh the template files; push) |
| **`kana test`** | **`./script/test`** |
| **`kana test clear`** | **`./script/test clear`** |
| **`kana test reset`** | **`./script/test reset`** |

### `kana init`

Starts or restarts the development environment for the **current** app or module by running **`./script/init`**. When signed in, refreshes **`.auth_token`** at the project root from the API where applicable.

**Optional targeting before init:**

| Flag | Meaning |
|------|---------|
| **`--switch`** | Interactively pick app vs module, then pick resource (**same flow as `use`**) before **`./script/init`** |
| **`--app <name>`** | Set **current app** by display name, then run **`./script/init`** |
| **`--module <name>`** | Set **current module** by display name, then run **`./script/init`** |

**`--app`** and **`--module`** are mutually exclusive. **`--customer-id`** applies when switching target.

### `kana push "<summary>"`

Runs **`./script/push "<summary>"`** in the local clone: commits the working tree (the summary is the commit message and labels the revision in the app's history), pushes your branch (named after your email) to the app's git repository, builds the app bundle, and deploys it to **your own** test environment. It never touches **`main`**. The summary is required by the script; without it the script prints its usage and exits **2**.

**Exit codes** (the script's own, passed through unchanged):

| Code | Meaning |
|------|---------|
| **0** | Pushed and deployed |
| **1** | Error (lint, build, upload, …) |
| **6** | Your branch on the server has newer commits from another workspace (for example a web-hosted vibe-dev session): run **`kana sync_workspace`** to merge them, then push again |

**`--force`** is still accepted for compatibility but ignored (the CLI says so): pushes are never forced.

### `kana publish`

Runs **`./script/publish`** in the local clone: first the workspace sync (merges your branch from the server, then **`main`**, into it and refreshes the template files) and a push of the result to your test environment; then it fast-forwards **`main`** to your commit and makes that revision live.

| Flag | Meaning |
|------|---------|
| **`--continue`** | Forwarded as **`./script/publish --continue`**: publish even when the sync merged new commits from **`main`** (skips the exit-4 stop below) |

**Exit codes** (the script's own, passed through unchanged):

| Code | Meaning |
|------|---------|
| **0** | Published |
| **1** | Error |
| **3** | Conflicts, either merging or putting your uncommitted changes back: the script's report lists the files and the exact `git` commands to finish, and says whether **`kana publish`** has to be run again |
| **4** | Merged changes from **`main`** and deployed them to your test environment; verify, then run **`kana publish`** again (it publishes as long as **`main`** has not changed again) |
| **5** | **`main`** changed since your sync; run **`kana publish`** again to merge the new commits |

### `kana sync_workspace`

Runs **`./script/sync_workspace`** in the local clone: merges your branch from the server (commits made in a web-hosted vibe-dev session or on another machine), then **`main`**, into the workspace, refreshes the template files, commits, and pushes the result to your test environment. The push is skipped when the server already has every commit and your test environment was built from it ("Nothing new to push").

**Exit codes** (the script's own, passed through unchanged):

| Code | Meaning |
|------|---------|
| **0** | Upgraded |
| **1** | Error |
| **3** | Conflicts, either merging or putting your uncommitted changes back: the script's report lists the files and the exact `git` commands to finish, and says whether **`kana sync_workspace`** has to be run again |

---

## Delete

### `kana delete`

Targets whatever is in **`current-app.json`** or **`current-module.json`** (the **current** app or module).

**Flags:**

| Flag | Meaning |
|------|---------|
| **`--remote`** | Also delete the app or module in Kana (like the web UI) |
| **`--yes`** / **`-y`** | Skip confirmation (typical in scripts) |

With **`--yes`**, you must either name the resource (positionals or scope flags) **or** have a matching **current** target saved.

**Local-only delete** using the saved current target does **not** need **`kana customer`** / API — it uses **`~/.kana/current-*.json`** and removes **`~/.kana/repos/…`**.

Stopping Docker is **best-effort**: if **`docker compose down`** fails, local folder removal (and **`--remote`**) may still proceed after a warning.

### `kana app delete` / `kana module delete`

Same behavior; lets you name an app or module. With **`--remote`**, if that was the **only** app in its sidebar section, the CLI may remove the empty container server-side.

---

## Scope flags

Use when multiple resources share a display name or when scripting by app/module row id.

| Flag | Purpose |
|------|---------|
| **`--app-id N`** | App or module row id (**`widgetappid`**) — must be unique across the workspace |
| **`--app-name "…"`** | Display name with **`use`**, **`edit`**, **`delete`** |

---

## Configuration files

Typical paths under **`~/.kana/`**:

| File | Role |
|------|------|
| **`config.json`** | Persisted CLI preferences; **`reposDir`** is the clone root (see **`kana config repos-dir`**) |
| **`auth.json`** | OAuth access + refresh tokens |
| **`current-customer.json`** | Selected customer **`id`** + **`name`** |
| **`app-create-scope.json`** | Last sidebar-section hint from interactive create/edit |
| **`current-app.json`** | Current **app** (ids + names + **`localRepoPath`**) |
| **`current-module.json`** | Current **module** |
| **`oauth-client.json`** | Registered OAuth client metadata |

Migration: an older module-workflow state file under **`~/.kana/`** may still exist; the CLI reads it when **`current-module.json`** is missing.

Internal ids are stored for API calls; normal command output uses **friendly names**.
