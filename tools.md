# CLI Tools

These tools are installed and should be preferred over their legacy equivalents.

## Install

```bash
brew install ast-grep difftastic shellcheck sd scc yq hyperfine watchexec git-delta fd
```

Already installed: `jq`, `gh`
Skipped: `comby` (deprecated, no OCaml 5 support)

---

## Code Search & Refactoring

- **ast-grep** (`sg`) — structural code search/replace using AST patterns; prefer over regex for any multi-file code refactors
  - `sg -p 'console.log($$$ARGS)' -l js` — find all console.log calls
  - `sg -p '$OBJ && $OBJ()' --rewrite '$OBJ?.()' -l ts` — refactor to optional chaining
- **sd** — prefer over `sed` for all substitutions; uses PCRE regex, no escaping required
  - `sd 'old_name' 'new_name' **/*.ts`

## File System

- **fd** — prefer over `find`; faster, gitignore-aware, saner defaults
  - `fd -e ts ComponentName` — find TypeScript files matching name
  - `fd --type f --changed-within 1d` — files modified in last day

## Data & Config

- **jq** — JSON query and transform (system install)
- **yq** — YAML/TOML/XML query and transform, same syntax as jq
  - `yq '.services.web.image' docker-compose.yml`
  - `yq -i '.version = "2.0"' config.yaml` — in-place edit

## Code Analysis

- **shellcheck** — static analysis for shell scripts; always run on generated `.sh` files before presenting them
  - `shellcheck script.sh`
- **scc** — codebase overview: language breakdown, LOC, cyclomatic complexity
  - `scc .` — full summary
  - `scc --by-file .` — per-file breakdown

## Git & Diffs

- **delta** — syntax-highlighted git diff pager; configured as git pager (primarily human-facing)
- **difftastic** (`difft`) — structural diff by AST rather than lines; use when line-based diff is noisy
  - `difft file1.ts file2.ts`

## Benchmarking & Automation

- **hyperfine** — statistical benchmarking; use when comparing command or build performance
  - `hyperfine 'cmd_a' 'cmd_b'`
  - `hyperfine --export-markdown results.md 'cmd'`
- **watchexec** — file watcher for persistent dev loops (primarily human-facing)
  - `watchexec -e ts 'npm test'`
