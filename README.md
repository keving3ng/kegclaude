# kegclaude

Kevin's Claude Code harness — personal AI workspace config, scripts, and documentation.

All the live config lives in `~/.claude/`. This repo documents what's set up, why, and how to recreate it.

---

## Status Line

Custom status line shown at bottom-left of the Claude Code input box.

**Script:** `~/.claude/statusline.sh`
**Config:** `~/.claude/settings.json` → `statusLine.command`

### Output format

```
sonnet-4-6 | main | [███░░░] 50% | 12.3k/1.2k/45.6k/8.9k | $0.05 | 5h:13% | wk:20%
```

| Segment | Source | Notes |
|---|---|---|
| Model name | `model.id` | Strips `claude-` prefix |
| Git branch | `workspace.current_dir` + `git symbolic-ref` | Hidden when not a git repo |
| Agent/style | `agent.name` or `output_style.name` | Hidden when value is "default" |
| Context bar | `context_window.used_percentage` | 6-block `█░` bar + percentage |
| Tokens | `total_input_tokens` / `total_output_tokens` / `total_cache_read_tokens` / `total_cache_write_tokens` | Format: `in/out/cache-read/cache-write` |
| Cost estimate | `total_input_tokens` + `total_output_tokens` | Rough estimate at Sonnet pricing ($3/$15/M) |
| 5h rate limit | `rate_limits.five_hour.used_percentage` | Claude.ai subscribers only |
| Weekly rate limit | `rate_limits.seven_day.used_percentage` | Claude.ai subscribers only |

### What's not available

The interactive permission mode (auto/plan/bypassPermissions) is not in the status line JSON — it's a UI-layer state that CC doesn't expose to the command. Closest proxy is `output_style.name`.

---

## Hooks (`~/.claude/settings.json`)

### Stop hook — sound + terminal color

On every Stop event (Claude finishes responding):

1. **Sound:** plays `Glass.aiff` via `afplay`
2. **Terminal tab color:** turns orange (`rgb(255, 128, 0)`) — signals Claude is done/waiting

```json
{ "type": "command", "command": "afplay /System/Library/Sounds/Glass.aiff" },
{ "type": "command", "command": "printf \"\\033]6;1;bg;red;brightness;255\\a\\033]6;1;bg;green;brightness;128\\a\\033]6;1;bg;blue;brightness;0\\a\" > /dev/tty" }
```

### UserPromptSubmit hook — color reset

When you submit a prompt, the terminal tab color resets to default — signals Claude is thinking.

```json
{ "type": "command", "command": "printf \"\\033]6;1;bg;*;default\\a\" > /dev/tty" }
```

### PreToolUse (Bash) — safety gate + audit log

Two hooks fire before every Bash command:

**1. Destructive command guard** — blocks dangerous patterns and forces manual confirmation:
- `rm -rf`
- `git push --force`
- `DROP TABLE`
- `git reset --hard`
- `git clean -f`
- `git branch -D`

Returns a `permissionDecision: "deny"` with a message to run manually if intended.

**2. Bash audit log** — appends every command to `~/.claude/bash-history.log` with a timestamp. Runs async so it never blocks.

### PreCompact hook — context preservation prompt

Before auto-compact fires, injects a system message reminding Claude to preserve:
- Current task state
- Open questions
- File paths in progress
- Key decisions

---

## Enabled Plugins

| Plugin | Purpose |
|---|---|
| `discord` | Discord bot integration — reply in channels from Claude |
| `frontend-design` | High-quality UI component generation |
| `superpowers` | Skill system (brainstorming, TDD, planning, debugging, etc.) |
| `code-review` | PR code review agent |
| `github` | GitHub issue/PR tools |
| `typescript-lsp` | TypeScript language server tools |
| `code-simplifier` | Simplify and clean up changed code |
| `feature-dev` | Feature development with architecture focus |
| `claude-md-management` | Audit and improve CLAUDE.md files |
| `skill-creator` | Create and iterate on custom skills |
| `security-guidance` | Security review guidance |
| `commit-commands` | Git commit/push/PR workflow skills |
| `claude-code-setup` | Automation recommendations |

---

## Custom Skills (`~/.claude/skills/`)

### `deploy-to-jonas`

Trigger: setting up a new repo/service to deploy to Jonas (homelab Unraid server).

Covers 5 deploy channels:
- **A** — Custom Docker image via GitHub Actions CI → Zot registry → SSH deploy
- **B** — npm library to Verdaccio private registry (`@keg/*` scope)
- **C** — Third-party container via Watchtower auto-updates
- **D** — Public Docker image via CI-controlled redeploy
- **E** — Generate a new channel prompt for novel cases

Key patterns enforced:
- `docker stop/rm/run` (never `restart` — doesn't pick up new image)
- Both `daemon.json` AND `buildkitd-config-inline` needed for insecure Zot registry
- Never commit `.npmrc` (contains Tailscale IP injected from secrets)
- All custom images: `LABEL org.opencontainers.image.vendor="keg"`, no Watchtower label
- `printf` not heredoc when writing files over SSH

### `request-partiful-api-change`

Trigger: working in `vball-tracker` and a needed change belongs in `keving3ng/partiful-api`.

Flow: create GitHub issue tagged `@claude` → bot opens PR → CI smoke test → auto-merge.

Don't use for vball-tracker-specific logic.

---

## Global Context (`~/.claude/CLAUDE.md`)

A global `CLAUDE.md` is loaded by Claude Code in every session, regardless of project. It imports `tools.md` via `@tools.md`.

**How propagation works:**

```
~/.claude/CLAUDE.md  →  @tools.md  →  ~/.claude/tools.md (symlink)  →  kegclaude/tools.md
```

Edit `kegclaude/tools.md` and every future session everywhere picks it up automatically — no per-project setup needed.

Per-project CLAUDE.md files should only contain project-specific overrides (e.g. preferred lint script, project conventions).

---

## CLI Tools (`tools.md`)

Documented in `tools.md` (source of truth). Symlinked to `~/.claude/tools.md` and imported globally.

**Install:**
```bash
brew install ast-grep difftastic shellcheck sd scc yq hyperfine watchexec git-delta fd
```

| Tool | Replaces / Purpose |
|---|---|
| `ast-grep` (`sg`) | Structural code search/replace by AST pattern |
| `sd` | `sed` — PCRE regex, no escaping |
| `fd` | `find` — gitignore-aware, saner syntax |
| `yq` | `jq` for YAML/TOML/XML |
| `shellcheck` | Static analysis for generated shell scripts |
| `scc` | Codebase overview (LOC, language breakdown) |
| `difftastic` | Structural diff by AST (human-facing) |
| `delta` | Syntax-highlighted git diff pager (human-facing) |
| `hyperfine` | Statistical benchmarking |
| `watchexec` | File watcher for dev loops |

Already installed: `jq`, `gh`. Skipped: `comby` (deprecated).

---

## Other Global Settings

| Setting | Value | Notes |
|---|---|---|
| `alwaysThinkingEnabled` | `true` | Extended thinking on by default |
| `voiceEnabled` | `true` | Hold-to-talk voice dictation |
| `skipDangerousModePermissionPrompt` | `true` | Already accepted the bypass prompt |
| `permissions.allow` | `["Bash(chmod:*)"]` | Allow chmod without prompting |

---

## Project Memories

Key facts Claude carries across sessions (stored in `~/.claude/projects/*/memory/`):

### Kevin's profile

- Runs private volleyball sessions in Ottawa, manages via Partiful
- Comfortable with TypeScript/Node/Docker, opinionated on architecture
- Self-hosted everything: Zot (Docker), Verdaccio (npm), Jonas (Unraid homelab)
- Mobile-first UI matters (checks guest lists on-site)
- Commits directly to `main`, no PRs on personal projects

### Jonas homelab infrastructure

- **Server:** Jonas, Unraid, amd64, Docker
- **Local IP:** LAN IP / jonas.local (stored in jonas-config/.env)
- **Remote access:** Tailscale
- **Zot registry:** `${TAILSCALE_IP}:5000` (plain HTTP)
- **Verdaccio npm:** `${TAILSCALE_IP}:4873` (`@keg/*` scope)
- **SSH:** root, credentials in `jonas-config/.env`
- **CI secrets:** `TAILSCALE_AUTH_KEY`, `TAILSCALE_IP`, `ZOT_PASSWORD`, `JONAS_SSH_PRIVATE_KEY`
- **Data persistence:** `/mnt/user/appdata/<service>/`

### vball-tracker

- Next.js 14 App Router, TypeScript, Tailwind + shadcn/ui, SQLite (better-sqlite3), Docker
- `@keg/partiful-api` from Verdaccio — Partiful + Firestore client
- All `fetch` in server-side code needs `cache: 'no-store'` (Next.js 14 caches POSTs → stale Firebase tokens → 401s)
- Port `3000`, data at `/mnt/user/appdata/vball-tracker`
- Full CI/CD pipeline live as of 2026-03-28

---

## File Reference

| Path | Purpose |
|---|---|
| `~/.claude/settings.json` | Global hooks, plugins, status line, permissions |
| `~/.claude/settings.local.json` | Local overrides (e.g. `Bash(chmod:*)`) |
| `~/.claude/statusline.sh` | Status line shell script |
| `~/.claude/bash-history.log` | Audit log of all Bash commands |
| `~/.claude/skills/deploy-to-jonas/` | Deploy to Jonas skill |
| `~/.claude/skills/request-partiful-api-change/` | Partiful API change request skill |
| `~/.claude/projects/*/memory/` | Per-project persistent memories |
| `~/.claude/CLAUDE.md` | Global instructions loaded in every session; imports `tools.md` |
| `~/.claude/tools.md` | Symlink → `kegclaude/tools.md` |
| `kegclaude/tools.md` | Source of truth for installed CLI tools and usage notes |
