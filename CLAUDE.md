# kegclaude — Claude Working Environment

This repo is the source of truth for Kevin's Claude Code environment. Any change to `~/.claude/` should be documented here.

## Purpose

Catalog every meaningful change to the Claude environment so it can be exactly recreated on any machine. README.md is the human-readable reference; this file governs how Claude should behave when working in this repo.

## Workflow

Whenever we make a change to any Claude environment file (`~/.claude/settings.json`, `~/.claude/statusline.sh`, skills, hooks, etc.):

1. **Update README.md** to reflect the change — keep the status line format, segment table, hooks, plugins, skills, and settings sections accurate.
2. **Commit and push** the changes to `main`.

After any session that modifies the environment, prompt: _"Should I update the README and push?"_ — or just do it if the user says to proceed.

## What belongs in README.md

- Status line: current output format (with example), all segments and their JSON source fields
- Hooks: each hook, what event triggers it, what it does
- Plugins: full list with purpose
- Skills: name, trigger condition, what it does
- Global settings: key/value table
- File reference: all relevant `~/.claude/` paths

## What does NOT belong here

- Ephemeral session state or task lists
- Project-specific memory (that lives in `~/.claude/projects/*/memory/`)
- Secrets or credentials
