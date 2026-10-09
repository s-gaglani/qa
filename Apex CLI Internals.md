# Apex CLI Internals

**Version investigated:** v0.0.1  
**Platform:** Windows (PowerShell)  

---

## 1. Full Command Tree

```
apex [--config <path>] [-v|--verbose] [--version] [-h|--help]
├── ENTITIES
│   ├── agents [agent]
│   │   ├── install [name...] [--all] [--source <repo>] [-w|--workspace]
│   │   ├── installed
│   │   ├── list
│   │   ├── search <query>
│   │   ├── uninstall <name...> [--all] [--source <repo>] [-w|--workspace]
│   │   └── update [name] [--all] [--source <repo>] [-w|--workspace]
│   │
│   ├── hooks [hook]
│   │   ├── install [name...] [--all] [--source <repo>] [-w|--workspace]
│   │   ├── installed
│   │   ├── list
│   │   ├── search <query>
│   │   ├── uninstall <name...> [--all] [--source <repo>] [-w|--workspace]
│   │   └── update [name] [--all] [--source <repo>] [-w|--workspace]
│   │
│   ├── mcps [mcp]
│   │   ├── install [name...] [--all] [--source <repo>] [-w|--workspace]
│   │   ├── installed
│   │   ├── list
│   │   ├── search <query>
│   │   ├── status
│   │   ├── uninstall <name...> [--all] [--source <repo>] [-w|--workspace]
│   │   └── update [name] [--all] [--source <repo>] [-w|--workspace]
│   │
│   ├── profiles [profile, workflows, workflow, wf]
│   │   ├── install [name...] [--source <repo>] [-w|--workspace]
│   │   ├── installed
│   │   ├── list
│   │   ├── search <query>
│   │   ├── swap <name>
│   │   ├── uninstall <name...> [--all] [--source <repo>] [-w|--workspace]
│   │   └── update [name] [--all] [--source <repo>] [-w|--workspace]
│   │
│   ├── skills [skill]
│   │   ├── install [name...] [--all] [--source <repo>] [-w|--workspace]
│   │   ├── installed
│   │   ├── list
│   │   ├── search <query>
│   │   ├── uninstall <name...> [--all] [--source <repo>] [-w|--workspace]
│   │   └── update [name] [--all] [--source <repo>] [-w|--workspace]
│   │
│   └── steering [steer]
│       ├── install [name...] [--all] [--source <repo>] [-w|--workspace]
│       ├── installed
│       ├── list
│       ├── search <query>
│       ├── uninstall <name...> [--all] [--source <repo>] [-w|--workspace]
│       └── update [name] [--all] [--source <repo>] [-w|--workspace]
│
├── INVENTORY
│   ├── doctor [--fix] [-w|--workspace]
│   ├── installed [-w|--workspace]
│   ├── list [--source <repo>]
│   └── status [-w|--workspace]
│
├── ACTIONS
│   ├── install [-w|--workspace]                   # install ALL from ALL sources
│   ├── uninstall [-w|--workspace] [-y|--yes]
│   ├── update [--source <repo>] [-w|--workspace]
│   └── upgrade                                    # self-update apex binary
│
├── ENVIRONMENT
│   ├── config
│   │   ├── get <key>
│   │   ├── list
│   │   └── set <key> <value>
│   │
│   ├── setup [-y|--yes]
│   │   ├── certs [--mozilla]
│   │   ├── kiro
│   │   ├── kiro-login
│   │   ├── kirocrew [--jira-url <url>]
│   │   └── pixi
│   │
│   ├── sources [source]
│   │   ├── add <gitlab-path> [--ref <branch>] [--all]
│   │   ├── discover
│   │   ├── list
│   │   ├── remove <name>
│   │   ├── scan [-f|--force]
│   │   └── status
│   │
│   └── vault
│       ├── add <url> [-u|--username <user>] [-l|--label <label>] [--method browser|device|manual|skip] [--select] [--no-login]
│       ├── edit <url>
│       ├── exec -- <command> [args...]           # alias for `apex exec`
│       ├── get <url> [-f|--field password|username|url] [--raw]
│       ├── list
│       ├── lock
│       ├── login <provider> [--method browser|device]   # providers: gitlab, msgraph, atlassian, a2rm
│       ├── remove <url>
│       ├── reset
│       ├── start
│       ├── status
│       ├── stop
│       └── unlock
│
├── RUNTIME
│   ├── exec [-m|--map <mappings>] -- <command> [args...]
│   └── info
│
└── ADDITIONAL
    ├── completion <shell>
    └── help [command]
```

---

## 2. Sources: Registration, Cloning, Caching, and Updates

### How Sources Work

Sources are GitLab repositories that contain skills, agents, MCPs, steering, hooks, and profiles. They are NOT cloned as full git repos — instead, apex downloads **tarball archives** for each source and extracts entity metadata.

### Source Registration

```bash
apex sources add <gitlab-path>                  # add repo at default branch
apex sources add <gitlab-path> --ref <branch>   # add repo at specific branch/tag
apex sources add --all                          # add all discoverable repos (tagged apex-skills)
apex sources discover                           # list repos available to add
```

Sources are stored in the config file:

**Config file:** `%APPDATA%\apex\config.yaml` (Windows) or `~/.config/apex/config.yaml` (Linux/macOS)

```yaml
sources:
  - repo: myorg/skills-repo
    ref: main
  - repo: myteam/agent-capabilities
    ref: master
```

### Cache Location

| Path | Content |
|------|---------|
| `%LOCALAPPDATA%\apex\index.json` | Master index of all entities from all sources |
| `%LOCALAPPDATA%\apex\archives\` | Downloaded source tarballs (URL-encoded names) |

**Example archives:**
```
myorg%2Fskills-repo@main.tar.gz
myteam%2Fagent-capabilities@master.tar.gz
```

### Version Tracking (No Lockfile)

There is **no lockfile**. Version tracking uses git SHAs stored in `index.json`:

| Field | Purpose |
|-------|---------|
| `source_shas` | Current HEAD SHA per source (what's installed) |
| `scanned_shas` | SHA at last scan (what's in the index) |
| `updated` | Timestamp of last index update |
| `sources_checked` | Timestamp of last source freshness check |

```json
{
  "source_shas": {
    "myorg/skills-repo@main": "ad5624219698591e192808ad7b5ee72120e0962e",
    "myteam/agent-capabilities@master": "ccd4724fbd0f1040aea9a4837b01c9e617625c07"
  },
  "scanned_shas": {
    "myorg/skills-repo@main": "f8cbf8ec3eb3bb740544ffd5a421b53c5960975a",
    "myteam/agent-capabilities@master": "bb1c68d55aceecf77f35d7f7131a2abfe1d0df74"
  }
}
```

### Branch Update Detection

When `apex sources scan` runs:

1. Fetches current HEAD SHA from GitLab API for each source
2. Compares to `scanned_shas` in index
3. If different: downloads new tarball, extracts entity metadata, updates index
4. If `-f|--force`: ignores cached SHAs, rescans everything

Config controls auto-scan behavior:

| Config Key | Default | Meaning |
|------------|---------|---------|
| `source.update.check` | `true` | Check if sources have new commits |
| `source.update.scan` | `true` | Auto-rescan when changes detected |
| `source.update.auto` | `false` | Auto-update installed entities (UNKNOWN if functional) |

### Freshness Detection

`apex status` compares installed entity content against source:

- **Up to date**: content hash matches source
- **Outdated (content changed)**: local file differs from source
- **Orphan**: installed but source no longer contains it (renamed/removed)

---

## 3. Setup Wizard Steps

Run `apex setup` for interactive wizard, or individual subcommands:

### Step 1: Setup Certificates (`apex setup certs`)

**Purpose:** Install corporate CA certificates for TLS trust.

**Actions:**
1. Downloads CA bundle (Mozilla bundle via `--mozilla`, or corporate certs)
2. Writes certificates to `~/.certs/` directory:
   - `corp.pem` — corporate CA chain
   - `cacert.pem` — full CA bundle
   - Individual cert files as needed
3. Creates helper scripts in `~/.local/bin/`:
   - `ca-certs` (bash) — outputs `~/.certs/corp.pem`
   - `ca-certs.cmd` (Windows) — same
   - `ca-certs.ps1` (PowerShell) — same

**Environment variables set by `apex exec`:**
- `SSL_CERT_FILE` → `~/.certs/corp.pem`
- `REQUESTS_CA_BUNDLE` → `~/.certs/corp.pem`
- `NODE_EXTRA_CA_CERTS` → `~/.certs/corp.pem`
- `PIP_CERT` → `~/.certs/corp.pem`

### Step 2: Setup Pixi (`apex setup pixi`)

**Purpose:** Install pixi package manager with corporate registry mirrors.

**Actions:**
1. Downloads pixi from configured mirror (`registries.pixirelease` config)
2. Installs to `~/.local/bin/pixi` (or appropriate location)
3. Configures pixi to use corporate mirrors for conda packages

**Minimum version required:** 0.70.0

### Step 3: Setup Kiro CLI (`apex setup kiro`)

**Purpose:** Install the Kiro CLI for terminal-based AI interactions.

**Actions:**
1. Downloads and installs kiro-cli binary
2. UNKNOWN: specific installation location and mechanism

### Step 4: Setup Kiro Login (`apex setup kiro-login`)

**Purpose:** Authenticate to Kiro via SSO device flow.

**Actions:**
1. Initiates SSO device code flow
2. Opens browser for authentication
3. Stores credentials (location UNKNOWN — likely Kiro's own config)

### Step 5: Setup KiroCrew (`apex setup kirocrew`)

**Purpose:** Full KiroCrew bootstrap for collaborative features.

**Actions:**
1. Installs `kirocrew` CLI (via pipx)
2. Installs/verifies `glab` (GitLab CLI) and authenticates it
3. Writes `~/.kiro/crew/config.json`:
   - `dashboard.gitlab_hosts` — GitLab host configuration
   - `dashboard.jira_auth` — Jira email from vault
4. Writes `~/.kiro/crew/.env`:
   - `JIRA_API_TOKEN` — from vault
5. Runs: `kirocrew secrets import --apply`

**Requires vault entries for:** Atlassian URL, GitLab URL

### Step 6: Scan Sources (`apex sources scan`)

**Purpose:** Index all configured sources to discover available entities.

**Actions:**
1. For each source in config:
   - Fetch HEAD SHA from GitLab
   - Download tarball archive (if SHA changed or `-f`)
   - Extract and parse entity definitions
2. Build/update `%LOCALAPPDATA%\apex\index.json`
3. Update `scanned_shas` and timestamps

### Step 7: Install All (`apex install`)

**Purpose:** Install all available skills, agents, MCPs from all sources.

**Actions:**
1. Reads index.json for all available entities
2. Installs each entity type to its target location
3. Creates MCP launcher scripts

**Note:** This is opt-in and NOT pre-selected in the wizard — most users should use profiles instead.

---

## 4. Credential Injection via `apex exec`

### How It Works

`apex exec` scans environment for `apexvault:` prefixed variables and resolves them:

```
MYDB_URL="apexvault:oracle://host:1521/service"
         ├── prefix: apexvault:
         └── actual URL: oracle://host:1521/service

apex exec -- my-command
```

Injected variables:
- `MYDB_URL` → `oracle://host:1521/service` (prefix stripped)
- `MYDB_USER` → username from vault
- `MYDB_PASS` → password from vault

### Variable Remapping

Use `-m` to remap variable names:

```bash
apex exec -m "GITLAB_HOST=GL_URL,GITLAB_TOKEN=GL_PASS" -- command
```

MCP configs use this pattern:
```yaml
env:
  GL_URL: apexvault:https://gitlab.example.com
mappings:
  GITLAB_HOST: GL_URL
  GITLAB_TOKEN: GL_PASS
```

### Registry Environment Variables Injected

`apex exec` always injects (no vault unlock needed):

| Variable | Purpose | Value from config key |
|----------|---------|----------------------|
| `UV_DEFAULT_INDEX`, `PIP_INDEX_URL` | Python package index | `registries.pypi` |
| `npm_config_registry`, `NPM_CONFIG_REGISTRY`, `YARN_NPM_REGISTRY_SERVER` | npm registry | `registries.npm` |
| `GOPROXY` | Go module proxy | `registries.goproxy` |
| `GOPRIVATE` | Private Go modules | `registries.goprivate` |
| `GOSUMDB` | Go checksum database | `registries.gosumdb` |
| `SSL_CERT_FILE`, `REQUESTS_CA_BUNDLE`, `NODE_EXTRA_CA_CERTS`, `PIP_CERT` | TLS certs | `~/.certs/corp.pem` |

### Vault Storage by Platform

| Platform | Storage Backend | Daemon Required |
|----------|-----------------|-----------------|
| **Windows** | Windows Credential Manager | No |
| **macOS** | System Keychain | No |
| **Linux/Containers** | Encrypted daemon (`apex vault start/stop/lock/unlock`) | Yes |

**Vault status shown by `apex info`:**
```
vault store  Windows Credential Manager
vault sock   Windows Credential Manager
```

### Vault Entry Types

```bash
apex vault list
# Output:
  https://graph.microsoft.com   msgraph-refresh    (Microsoft Graph, OAuth)
  https://gitlab.example.com    gitlab-refresh     (GitLab, OAuth)
  https://jira.example.com      user@example.com   (Atlassian)
```

OAuth entries (username ends in `-refresh`) store refresh tokens; `apex vault get` returns a fresh access token by exchanging the refresh token.

### OAuth Providers

```bash
apex vault login gitlab      # GitLab OAuth
apex vault login msgraph     # Microsoft Graph
apex vault login atlassian   # Atlassian (Confluence/Jira)
apex vault login a2rm        # Application landscape registry
```

### VAULT Section in Sidebar

UNKNOWN — The help text and CLI don't explicitly describe an IDE sidebar feature. This may refer to:
- The Kiro IDE extension's vault status display
- Or simply `apex vault status` / `apex vault list` commands

---

## 5. Install Mechanics

### Copy vs Symlink

**Skills, Agents, Steering, Hooks:** Files are **COPIED** (not symlinked).

Evidence: `Get-Item` shows no LinkType on installed skill directories:
```powershell
Mode   LinkType Target FullName
d-----          {}     C:\Users\..\.kiro\skills\my-skill
```

**MCPs:** Source files copied to `~/.local/share/apex/mcps/<name>/`, launcher scripts generated in `~/.local/bin/<name>-mcp.cmd`.

### Install Targets (User vs Workspace Scope)

| Entity | User scope (default) | Workspace scope (`-w`) |
|--------|---------------------|------------------------|
| Skills | `~/.kiro/skills/<name>/` | `.kiro/skills/<name>/` |
| Agents | `~/.kiro/agents/<name>.json` | `.kiro/agents/<name>.json` |
| Steering | `~/.kiro/steering/<name>.md` | `.kiro/steering/<name>.md` |
| Hooks | `~/.kiro/hooks/` | `.kiro/hooks/` |
| MCPs | `~/.local/share/apex/mcps/<name>/` + `~/.local/bin/<name>-mcp.cmd` | `.kiro/settings/mcp.json` entry |
| Profiles | Metadata in `index.json` | N/A |

### Conflict Handling

UNKNOWN — The help text doesn't document explicit conflict handling. Observed behavior suggests:
- Install overwrites existing files
- `doctor --fix` can repair broken installations

### Uninstall

```bash
apex skills uninstall <name>       # remove specific skill
apex skills uninstall --all        # remove all skills
apex uninstall                     # remove ALL entities
apex uninstall -y                  # skip confirmation
```

### Status Counts (Green Tick / 10/10)

`apex status` computes:

1. **Freshness**: Compares installed file content hashes against source index
   - ✓ = content matches
   - ✗ = content differs

2. **Orphans**: Installed entities not present in any source (renamed or deleted upstream)

3. **Profile completeness**: 
   - "partial" = some dependencies installed
   - "complete" (assumed) = all dependencies installed

Example output:
```
Freshness:
  ✓ 29 entities up to date
  ✗ 19 outdated:
      skill  my-skill  (content changed)
      ...
Orphans:
  5 entities not in any source:
      skill  old-renamed-skill
      ...
Profile:
  my-profile  partial (10 skills, 5 agents, 3 mcps)
```

---

## 6. MCP Server Launch and Configuration

### MCP Package Structure

Each MCP lives in `~/.local/share/apex/mcps/<name>/`:

```
my-gitlab-mcp/
├── .gitignore
├── mcp.yaml           # MCP definition
├── mcp_server.py      # Server implementation
├── pixi.lock          # Locked dependencies
└── pixi.toml          # Pixi project config
```

### MCP Definition (`mcp.yaml`)

```yaml
name: my-gitlab-mcp
description: GitLab API — projects, merge requests, pipelines, issues
run: pixi run mcp-server
env:
  GL_URL: apexvault:https://gitlab.example.com
mappings:
  GITLAB_HOST: GL_URL
  GITLAB_TOKEN: GL_PASS
timeout: 120000
```

### Launcher Script Generation

Apex generates `.cmd` (Windows) or shell scripts in `~/.local/bin/<name>-mcp.cmd`:

```batch
@echo off
REM Generated by apex -- do not edit
REM Source: myorg/skills (ref: main, path: .kiro/mcps/my-gitlab-mcp)
REM Local:  C:\Users\..\.local\share\apex\mcps\my-gitlab-mcp
set GL_URL=apexvault:https://gitlab.example.com
set APEX_MCP_NAME=my-gitlab-mcp
cd /d C:\Users\..\.local\share\apex\mcps\my-gitlab-mcp
apex exec -m "GITLAB_HOST=GL_URL,GITLAB_TOKEN=GL_PASS" -- pixi run mcp-server
```

### Transport

All observed MCPs use **stdio transport** (not HTTP/SSE):
- Server runs via `pixi run mcp-server` 
- Communicates via stdin/stdout JSON-RPC
- IDE launches the process and connects

HTTP transport is also supported (seen in `mcp.json` with `"type": "http"` or `"url": "..."`).

### Auth Flow

1. Launcher script sets `apexvault:` env var
2. `apex exec` resolves against vault
3. Injects credential env vars into server process
4. Server uses credentials for API calls

### MCP Registration in Kiro

MCPs are registered in `~/.kiro/settings/mcp.json`:

```json
{
  "mcpServers": {
    "my-gitlab-mcp": {
      "command": "my-gitlab-mcp",
      "disabled": false,
      "timeout": 120000
    }
  }
}
```

The `command` is the launcher script name (found in PATH via `~/.local/bin`).

---

## 7. Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                                   USER                                          │
│                                                                                 │
│   ┌──────────────────┐    ┌──────────────────┐    ┌──────────────────┐         │
│   │   Kiro IDE       │    │   Terminal       │    │   MCP Clients    │         │
│   │   (Extension)    │    │   (PowerShell)   │    │   (AI Agents)    │         │
│   └────────┬─────────┘    └────────┬─────────┘    └────────┬─────────┘         │
│            │                       │                       │                    │
└────────────┼───────────────────────┼───────────────────────┼────────────────────┘
             │                       │                       │
             ▼                       ▼                       ▼
┌─────────────────────────────────────────────────────────────────────────────────┐
│                              APEX CLI (apex.exe)                                │
│                              ~/.local/bin/apex.exe                              │
│                                                                                 │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐        │
│  │   setup      │  │   install    │  │   exec       │  │   vault      │        │
│  │   certs      │  │   skills     │  │   -m maps    │  │   add/get    │        │
│  │   pixi       │  │   agents     │  │   -- cmd     │  │   login      │        │
│  │   kiro       │  │   mcps       │  │              │  │   unlock     │        │
│  └──────────────┘  │   steering   │  └──────┬───────┘  └──────┬───────┘        │
│                    │   hooks      │         │                 │                 │
│                    │   profiles   │         │                 │                 │
│                    └──────┬───────┘         │                 │                 │
│                           │                 │                 │                 │
└───────────────────────────┼─────────────────┼─────────────────┼─────────────────┘
                            │                 │                 │
        ┌───────────────────┼─────────────────┼─────────────────┼───────────────┐
        │                   ▼                 ▼                 ▼               │
        │  ┌─────────────────────────────────────────────────────────────────┐  │
        │  │                     LOCAL FILE SYSTEM                           │  │
        │  │                                                                 │  │
        │  │  CONFIG                    CACHE                   DATA         │  │
        │  │  %APPDATA%\apex\           %LOCALAPPDATA%\apex\    ~/.local/    │  │
        │  │  └─ config.yaml            ├─ index.json           ├─ bin/      │  │
        │  │     ├─ gitlab.host         ├─ archives/            │  ├─ apex   │  │
        │  │     ├─ sources[]           │  └─ *.tar.gz          │  └─ *-mcp  │  │
        │  │     ├─ registries.*        ├─ sessions.enc         └─ share/    │  │
        │  │     └─ kiro.*              └─ telemetry.*              └─ apex/ │  │
        │  │                                                          └─mcps/│  │
        │  │  KIRO                      CERTS                                │  │
        │  │  ~/.kiro/                  ~/.certs/                            │  │
        │  │  ├─ skills/                ├─ corp.pem                          │  │
        │  │  ├─ agents/                ├─ cacert.pem                        │  │
        │  │  ├─ steering/              └─ *.pem                             │  │
        │  │  ├─ hooks/                                                      │  │
        │  │  └─ settings/mcp.json                                           │  │
        │  │                                                                 │  │
        │  └─────────────────────────────────────────────────────────────────┘  │
        │                                                                       │
        │  ┌─────────────────────────────────────────────────────────────────┐  │
        │  │                     CREDENTIAL STORE                            │  │
        │  │                                                                 │  │
        │  │  Windows: Windows Credential Manager                            │  │
        │  │  macOS:   System Keychain                                       │  │
        │  │  Linux:   Encrypted daemon (vault start/stop/lock/unlock)       │  │
        │  │                                                                 │  │
        │  │  Entries: URL → (username, password/token, label, OAuth type)   │  │
        │  └─────────────────────────────────────────────────────────────────┘  │
        │                                                                       │
        └───────────────────────────────────────────────────────────────────────┘
                            │
                            │ HTTPS (GitLab API)
                            ▼
┌─────────────────────────────────────────────────────────────────────────────────┐
│                              REMOTE SOURCES                                     │
│                                                                                 │
│  ┌──────────────────────────────────────────────────────────────────────────┐  │
│  │                     GitLab (gitlab.example.com)                          │  │
│  │                                                                          │  │
│  │  myorg/skills-repo@main           myteam/agent-capabilities@master       │  │
│  │  ├─ .kiro/skills/*                ├─ .kiro/skills/*                      │  │
│  │  ├─ .kiro/agents/*                ├─ .kiro/agents/*                      │  │
│  │  ├─ .kiro/mcps/*                  ├─ .kiro/hooks/*                       │  │
│  │  ├─ .kiro/steering/*              └─ .kiro/steering/*                    │  │
│  │  └─ .kiro/profiles/*                                                     │  │
│  │                                                                          │  │
│  │  API: /projects/:id/repository/archive.tar.gz?sha=<sha>                  │  │
│  │       /projects/:id/repository/commits?ref=<branch>&per_page=1           │  │
│  └──────────────────────────────────────────────────────────────────────────┘  │
│                                                                                 │
│  ┌──────────────────────────────────────────────────────────────────────────┐  │
│  │                     Nexus / Artifact Mirror                              │  │
│  │                                                                          │  │
│  │  /repository/python-proxy/           PyPI mirror                         │  │
│  │  /repository/npm-group/              npm mirror                          │  │
│  │  /repository/go-proxy/               Go module proxy                     │  │
│  │  /repository/conda-forge-proxy/      Conda mirror                        │  │
│  │  /repository/raw/.../apex            Apex CLI releases                   │  │
│  │  /repository/raw/.../pixi            Pixi releases                       │  │
│  └──────────────────────────────────────────────────────────────────────────┘  │
│                                                                                 │
└─────────────────────────────────────────────────────────────────────────────────┘


                         MCP LAUNCH FLOW
┌─────────────────────────────────────────────────────────────────────────────────┐
│                                                                                 │
│   ┌──────────┐     ┌──────────────────┐     ┌───────────────────────────────┐  │
│   │ Kiro IDE │────▶│ my-gitlab-mcp.cmd │────▶│ apex exec                    │  │
│   └──────────┘     │                  │     │  ├─ resolve apexvault: URLs   │  │
│        │           │ set GL_URL=      │     │  ├─ inject registry env vars  │  │
│        │           │   apexvault:...  │     │  ├─ inject TLS cert paths     │  │
│        │           │ cd /d mcps/...   │     │  └─ exec pixi run mcp-server  │  │
│        │           │ apex exec ...    │     │                               │  │
│        │           └──────────────────┘     └───────────────────────────────┘  │
│        │                                                    │                   │
│        │                                                    ▼                   │
│        │                                    ┌───────────────────────────────┐  │
│        │                                    │ pixi run mcp-server           │  │
│        │◀───────────────────────────────────│  (Python MCP server)          │  │
│    JSON-RPC                                 │  GITLAB_HOST=...              │  │
│    over stdio                               │  GITLAB_TOKEN=...             │  │
│                                             └───────────────────────────────┘  │
│                                                                                 │
└─────────────────────────────────────────────────────────────────────────────────┘
```

---

## UNKNOWN Items

Items that could not be determined from CLI help and inspection:

1. **`apex setup kiro`**: Exact installation mechanism and target location
2. **`apex setup kiro-login`**: Where SSO tokens are stored (Kiro's own config?)
3. **IDE extension internals**: How the VS Code extension interacts with apex CLI
4. **`source.update.auto`**: Whether auto-update of installed entities is functional
5. **Conflict handling**: Exact behavior when installing over existing files
6. **VAULT sidebar**: Whether this refers to an IDE feature or just CLI commands
7. **Profile "complete" status**: Whether this state exists (only "partial" observed)
8. **Symlink on Linux/macOS**: Whether those platforms use symlinks instead of copies
 