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
| **`kana publish`** | Publish the **current** state of the app or module as the live version (**`./script/publish`**) |
| **`kana upgrade`** | Upgrade the app or module to the latest Kana features and definitions (**`./script/upgrade`**) |
| **`kana test`** | Open the test environment (**`./script/test`**) |
| **`kana test clear`** | Clear the test environment database (**`./script/test clear`**) |
| **`kana test reset`** | Reset the test environment database to the live database state (**`./script/test reset`**) |
| **`kana app create`** `<name>` | Create app; sets **current app** |
| **`kana app use`** `[name]` | Set **current app**; pick from **all** apps if name omitted; **clones** if there is no local checkout; with a checkout, offers **Cursor** / **Claude** / **skip** unless **`--yes`** |
| **`kana app current`** | Print saved working app (friendly names) |
| **`kana app list`** | List main apps by sidebar section |
| **`kana app edit`** | Interactive: external auth, linked modules, external MCP servers |
| **`kana app extern-auth`** `list` \| `add` \| `remove` | Non-interactive external OAuth integrations (type ids) |
| **`kana app extern-module`** `list` \| `add` \| `remove` | Non-interactive linked modules (datasrc ids); alias: **`kana app module`** |
| **`kana app mcp`** `list` \| `add` \| `remove` | Non-interactive external MCP servers |
| **`kana module create`** `<name>` | Create module; sets **current module** |
| **`kana module list`** | List modules (module apps) |
| **`kana module use`** / **`current`** / **`edit`** / **`extern-auth`** / **`extern-module`** / **`mcp`** | Same pattern as **`app`**, using **`current-module.json`** (linked modules: alias **`module`**) |
| **`kana delete`** | Remove **current** target’s local dev files; **`--remote`** also deletes in Kana |
| **`kana app delete`** / **`kana module delete`** | Same; name optional; **`--remote`** for server-side |

Repo scripts require a **local clone** and a **current** app or module. Paths resolve from **`~/.kana/current-app.json`** / **`current-module.json`** (**`localRepoPath`**). Older CLI versions may have stored the module workflow under a different filename in **`~/.kana/`**; that file is still read for migration until **`current-module.json`** is written again.

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

- **Pick target:** With **no** **`[name]`** and no disambiguating flags, shows **all** main apps in the workspace in **one** numbered list. Each line notes whether there is already a **local checkout** under **`~/.kana/repos/`** or **no local checkout**.
- **Name / ids:** Pass **`[name]`** or **`--app-name`**, or **`--app-id`** when that id is unique in the workspace (see scope flags).
- **Clone:** If there is **no** local dev workspace for that app, the CLI asks whether to **download** (same **`clone_app.sh`** flow as create). Declining still saves the **current app** id without **`localRepoPath`**. **`--yes`** / **`-y`** skips the question and clones non-interactively (and skips editor open prompts, same as **`app create --yes`**). After clone, **Claude Code** is opened in a **new** system terminal window (not the current shell).
- **Editor prompt:** When you already have a **local checkout** for the app you picked, the CLI still offers **Cursor**, **Claude Code**, or **skip** (same as after a fresh clone), unless you passed **`--yes`** / **`-y`**.

**Flags:** scope flags (below), **`--yes`** / **`-y`**, **`--customer-id`**.

### `kana app current`

Prints the current app (friendly names for display).

### `kana app list`

Lists **main** apps (not modules), grouped by sidebar section. Each line shows whether the app is already **checked out** under **`~/.kana/repos/`** (or **`localRepoPath`**) or **no local checkout** yet.

### `kana app edit`

Edits **external auth**, **linked modules** (assignable pool / datasrc selection), and **external MCP servers** for the selected app’s orchestrator row. **External auth** and **linked modules** are written to the API as soon as you finish each interactive editor (no separate “save all” step). **MCP** changes save when you perform each add/edit/delete in that submenu. With no flags, uses **current app** when still valid, otherwise interactive resolution.

**Flags:** scope flags, **`--yes`** / **`-y`** (skip the confirmation prompt before those saves), **`--customer-id`**.

### `kana app extern-auth` and `kana app extern-module`

Non-interactive counterparts of the **`edit`** menu (same targeting flags). **`extern-auth add <typ>`** / **`remove <typ>`** use integration type integers from the server’s external-auth catalog (**`list`** shows enabled types). **`extern-module add <datasrcid>`** / **`remove <datasrcid>`** update linked modules; **`extern-module list`** prints linked datasrc ids and resolved names. The older name **`module`** is still accepted as an alias (e.g. **`kana app module list`**).

Under **`kana module …`**, the nested **`extern-module`** subcommand is **`kana module extern-module list`** (and **`add`** / **`remove`**) — same behavior as **`kana app extern-module …`**, scoped to module orchestrator rows (alias: **`kana module module …`**).

### `kana app mcp` / `kana module mcp`

Non-interactive MCP list/add/remove (see quick table).

---

## Modules

Commands mirror **`kana app …`** but target modules (module apps) and **`~/.kana/current-module.json`**.

### `kana module create <name>`

**Note:** optional interactive **build prompt** is **not** shown for modules; use **`--prompt`** or **`--prompt-file`** if you want auto-code on create.

Other **`create`** flags match **`kana app create`**.

### `kana module use`, `kana module current`, `kana module list`, `kana module edit`

Same roles as the **`app`** equivalents (**`module use`** lists **all** modules and can clone when there is no checkout, like **`app use`**; with a local checkout it offers **Cursor** / **Claude Code** / **skip** unless **`--yes`**). **`module list`** shows the same per-row **checked out** / **no local checkout** hints as **`app list`**.

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

Starts or restarts the development environment for the **current** app or module by running **`./script/init`**. When signed in, refreshes **`.auth_token`** at the project root from the API where applicable.

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
