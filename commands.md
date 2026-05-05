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
| **`kana customer`** | Choose workspace → **`~/.kana/current-customer.json`** |
| **`kana init`** | Run **`./script/init`** in the local clone of the **current** app or module |
| **`kana healthcheck`** | Run **`./script/healthcheck`** (alias: **`kana health-check`**) |
| **`kana publish`** | Run **`./script/publish`** |
| **`kana upgrade`** | Run **`./script/upgrade`** |
| **`kana test`** | Run **`./script/test`** |
| **`kana test clear`** | **`./script/test clear`** |
| **`kana test reset`** | **`./script/test reset`** |
| **`kana app create`** `<name>` | Create app; sets **current app** |
| **`kana app use`** `[name]` | Set **current app**; pick from **all** apps if name omitted; **clones** dev workspace if there is no local checkout (see **`--yes`**) |
| **`kana app current`** | Print saved working app (friendly names) |
| **`kana app list`** | List main apps by sidebar section |
| **`kana app edit`** | Edit external auth / changeable agents / external MCP servers |
| **`kana module create`** `<name>` | Create module; sets **current module** |
| **`kana module list`** | List modules (module apps) |
| **`kana module use`** / **`current`** / **`edit`** | Same pattern as **`app`**, using **`current-module.json`** |
| **`kana delete`** | Remove **current** target’s local dev files; **`--remote`** also deletes in Kana |
| **`kana app delete`** / **`kana module delete`** | Same; name optional; **`--remote`** for server-side |

Repo scripts require a **local clone** and a **current** app or module. Paths resolve from **`~/.kana/current-app.json`** / **`current-module.json`** (**`localRepoPath`**). If you still have a legacy **`current-agent.json`** from an older CLI, it is read until you save a module target again.

---

## Auth and configuration

### `kana auth`

Opens the browser for OAuth. Stores tokens in **`~/.kana/auth.json`**.

### `kana version`

Prints the CLI version (release builds embed git metadata).

### `kana customer`

Interactive picker for workspace (customer). Needed before most API-backed flows when you have not fixed **`--customer-id`**.

---

## Apps

### `kana app create <name>`

Creates an app through the same APIs as the web flow.

**Flags:**

| Flag | Meaning |
|------|---------|
| **`--yes`** / **`-y`** | Skip confirmation; skip interactive optional build prompt unless **`--prompt`** / **`--no-prompt`** |
| **`--prompt "…"`** | Optional build / coding prompt |
| **`--no-prompt`** | Do not send a prompt |
| **`--customer-id N`** | Workspace customer for **`x-kana-cid`** without **`current-customer.json`** |

If multiple sidebar sections exist and nothing is saved yet, **`--yes`** alone may fail until you pass **`--group-name`** / **`--group`** or run once **without** **`--yes`** so the CLI can save a section hint (**`app-create-scope.json`**).

### `kana app use [name]`

Sets **`~/.kana/current-app.json`**. In Kana, each **orchestrator** row **is** the app (there are no separate “apps inside” another shell).

- **Pick target:** With **no** **`[name]`** and no disambiguating flags, shows **all** main apps in the workspace in **one** numbered list. Each line notes whether there is already a **local checkout** under **`~/.kana/repos/`** or **no local checkout**.
- **Name / ids:** Pass **`[name]`** or **`--app-name`**, or **`--group-name`** with **`--app-name`**, or **`--group`** with **`--app-id`** (see scope flags).
- **Clone:** If there is **no** local dev workspace for that app, the CLI asks whether to **download** (same **`clone_app.sh`** flow as create). Declining still saves the **current app** id without **`localRepoPath`**. **`--yes`** / **`-y`** skips the question and clones non-interactively (and skips the post-clone Cursor / Claude prompt, same as **`app create --yes`**).

**Flags:** scope flags (below), **`--yes`** / **`-y`**, **`--customer-id`**.

### `kana app current`

Prints the current app (friendly names for display).

### `kana app list`

Lists **main** apps (not modules), grouped by sidebar section. Each line shows whether the app is already **checked out** under **`~/.kana/repos/`** (or **`localRepoPath`**) or **no local checkout** yet.

### `kana app edit`

Edits external auth, changeable agents, and **external MCP servers** for the selected app’s orchestrator. With no flags, uses **current app** when still valid, otherwise interactive resolution.

**Flags:** scope flags, **`--yes`** (skip save confirmation), **`--customer-id`**.

---

## Modules

Commands mirror **`kana app …`** but target modules (module apps) and **`~/.kana/current-module.json`**.

### `kana module create <name>`

**Note:** optional interactive **build prompt** is **not** shown for modules; use **`--prompt`** if you want auto-code on create.

Other **`create`** flags match **`kana app create`**.

### `kana module use`, `kana module current`, `kana module list`, `kana module edit`

Same roles as the **`app`** equivalents (**`module use`** lists **all** modules and can clone when there is no checkout, like **`app use`**). **`module list`** shows the same per-row **checked out** / **no local checkout** hints as **`app list`**.

---

## Repo scripts (local clone)

These run executables under **`script/`** in your project root (**`~/.kana/repos/…`**).

| Command | Script |
|---------|--------|
| **`kana init`** | **`./script/init`** |
| **`kana healthcheck`** | **`./script/healthcheck`** |
| **`kana publish`** | **`./script/publish`** |
| **`kana upgrade`** | **`./script/upgrade`** |
| **`kana test`** | **`./script/test`** |
| **`kana test clear`** | **`./script/test clear`** |
| **`kana test reset`** | **`./script/test reset`** |

### `kana init`

Runs **`./script/init`** for the **current** app or module. When signed in, refreshes **`.auth_token`** at the project root from the API where applicable.

**Optional targeting before init:**

| Flag | Meaning |
|------|---------|
| **`--switch`** | Interactively pick app vs module, then pick resource (**same flow as `use`**) before **`./script/init`** |
| **`--app <name>`** | Set **current app** by display name, then run **`./script/init`** |
| **`--module <name>`** | Set **current module** by display name, then run **`./script/init`** |

**`--app`** and **`--module`** are mutually exclusive. **`--customer-id`** applies when switching target.

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

Use when multiple resources share a display name or when scripting by id.

| Flag | Purpose |
|------|---------|
| **`--group-name "Name"`** | Sidebar section name (with **`--app-name`** when needed) |
| **`--group N`** | Container (**widget_cont**) id |
| **`--app-id N`** | App or module id (with **`--group`**) |
| **`--app-name "…"`** | Display name with **`use`**, **`edit`**, **`delete`** |

---

## Configuration files

Typical paths under **`~/.kana/`**:

| File | Role |
|------|------|
| **`config.json`** | CLI preferences used for API access (defaults apply when absent) |
| **`auth.json`** | OAuth access + refresh tokens |
| **`current-customer.json`** | Selected customer **`id`** + **`name`** |
| **`app-create-scope.json`** | Last sidebar-section hint from interactive create/edit |
| **`current-app.json`** | Current **app** (ids + names + **`localRepoPath`**) |
| **`current-module.json`** | Current **module** |
| **`oauth-client.json`** | Registered OAuth client metadata |

Legacy **`current-agent.json`** may still be present from older CLI versions; the CLI reads it when **`current-module.json`** is missing.

Internal ids are stored for API calls; normal command output uses **friendly names**.
