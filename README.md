# Getting started with the Kana CLI

The **Kana CLI** is the same tool whether you run **`kana`** (Homebrew) or **`gh kana`** (GitHub CLI extension). Examples below use **`kana`**; substitute **`gh kana`** everywhere if you installed the extension.

---

## Install or upgrade

### Homebrew (macOS / Linux)

Add the tap once, then install or upgrade the **`kana`** cask:

```bash
brew tap kana-ai/cli
brew update
brew install --cask kana-ai/cli/kana
```

Upgrade when a new release is out:

```bash
brew update
brew upgrade --cask kana-ai/cli/kana
```

### GitHub CLI extension

Install or upgrade the extension from **[kana-ai/gh-kana](https://github.com/kana-ai/gh-kana)** (releases match the same CLI version as Homebrew):

```bash
gh extension install kana-ai/gh-kana
```

If it is already installed, upgrade to the latest release:

```bash
gh extension upgrade kana-ai/gh-kana
```

Pin a specific version (optional):

```bash
gh extension install kana-ai/gh-kana --pin v0.1.0
```

---

## Verify the install

```bash
kana version
# or
gh kana version
```

---

## Prerequisites on your machine

Before running commands, the CLI checks that **`docker`** and **`jq`** are available on your `PATH`. If something is missing, it can offer to install (for example via Homebrew on macOS). You can answer **no** and install yourself.

- **Optional override:** set **`KANA_SKIP_DEPS_CHECK=1`** to skip this check (automation or minimal environments only).

After you **create** an app or module, the CLI downloads a dev workspace the same way as the web “Clone & open” flow. That step needs **`unzip`** and a working network (it runs a public clone script). In **interactive** mode (no `--yes`), you may be asked whether to open the project in **Cursor**, **Claude Code**, or **skip**.

---

## Typical first-time flow

### 1. Sign in

```bash
kana auth
```

Uses browser OAuth (scopes **`kana:read`** and **`kana:write`**). Tokens are stored under **`~/.kana/`** (see **Configuration files** in [commands.md](commands.md#configuration-files)).

### 2. Choose a workspace (customer)

```bash
kana customer
```

If you belong to more than one workspace, pick the one you want. The choice is saved for later commands.

### 3. Create an app or module

**App (main Kana app):**

```bash
kana app create "My app name"
```

**Module (module app):**

```bash
kana module create "My module name"
```

You will confirm creation; for **apps**, you can optionally enter a **build prompt** for code generation (leave empty to skip). With **`--yes`**, confirmation (and the optional app prompt, unless you pass **`--prompt`** / **`--no-prompt`**) is skipped.

After the resource exists in Kana, the CLI **clones the dev workspace** into **`~/.kana/repos/<name>/`**, sets that folder as the **current** app or module, and **runs initialization** (same idea as the web flow).

### 4. Work locally

Use the repo under **`~/.kana/repos/…`**. From anywhere, with the **current** app or module set, you can run bundled scripts:

```bash
kana init
kana healthcheck    # alias: kana health-check
kana publish
kana upgrade
kana test           # optional: kana test clear | kana test reset
```

These run **`./script/…`** inside your local clone. You need a successful clone and a **current** target (set by **create** or **use**). See [commands.md](commands.md) for the full command list.

### 5. Switch between apps or modules

```bash
kana app use
kana module use
```

With no name, the CLI lists **every** app or module in the workspace and whether you already have a **local checkout**. If you pick (or name) one that is **not** checked out, it **asks** whether to download the dev workspace (same clone as create). Use **`--yes`** to clone without prompting (and to skip the optional Cursor / Claude step after clone).

You can pass a name: **`kana app use "My app name"`**. When several resources share a name, use **scope flags** — see [Scope flags](commands.md#scope-flags) in [commands.md](commands.md).

---

## Remove local or remote work

- **Local only** (stop containers, delete **`~/.kana/repos/…`**, clear saved current target when it matches): **`kana delete`** or **`kana app delete`** / **`kana module delete`** without **`--remote`**.
- **Also delete in Kana:** add **`--remote`** (and usually **`--yes`** in scripts).

Details: [Delete](commands.md#delete) in [commands.md](commands.md).

---

## Command reference

Every command and flag: **[commands.md](commands.md)**.

For scripted usage, prefer **`--yes`**, explicit names or **`--group`** / **`--app-id`**, and **`--customer-id`** when needed — summarized in [commands.md](commands.md).
