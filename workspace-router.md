# Workspace Router (`~/code`)

Use this guide when a task starts from the `~/code` workspace root and the user has not named a repository. The goal is to route the request to the right project with minimal interruption.

## Route a request

1. Read `wiki/index.md` to find likely project pages and `wiki/projects/<project>.md` for purpose, dependencies, and related worktrees. Treat this vault as a discovery map, not proof of current implementation state.
2. Inspect the likely repository's root instructions (`AGENTS.md`, `CLAUDE.md`), README, and relevant files before editing. Those project instructions govern implementation details. Follow any more specific instructions in nested directories.
3. Use the user's nouns, feature area, runtime, or deployment target to choose the canonical repository. Check for duplicate or superseded repos and worktrees; the wiki may identify the canonical one.
4. If one repository is a strong match, proceed there and briefly state the route in your progress update. Do not ask the user to pick a repo when the request and local evidence make the target clear.
5. If the request clearly spans repositories, inspect and work across each relevant repo, respecting each repo's instructions. Check shared dependencies and deployment configuration when a change crosses those boundaries.
6. If multiple targets remain plausible and choosing incorrectly would cause materially different or risky changes, inspect enough to identify the ambiguity, then ask one focused question before editing. For low-risk investigation, report the likely target and continue read-only discovery.

## Workspace map

- Project and topic index: `wiki/index.md`
- Project context: `wiki/projects/`
- Shared infrastructure context: `wiki/topics/infrastructure.md`
- Durable notes and capture workflow: `wiki/START-HERE.md`
- Likely canonical repositories include `keg-watch`, `vball-tracker`, `jonas-config`, `jem`, `matchamap`, `kwant`, `vivisualizer`, `stuj`, `claudespace`, `overpour`, and `kgeng.dev`. This list is a routing hint, not an exhaustive inventory or activity claim.
- `vivisualizer-qa-collection` and `vivisualizer-qa-ui-20260413` are worktrees related to `vivisualizer`; treat them as task-specific checkouts, not separate products.
- `matchamap2` is an older, superseded project; the current `matchamap` repo is the default unless the user explicitly asks for the old implementation.
- `partiful-api` supports `vball-tracker`; route Partiful sync work to the library, app, or both based on where the requested behavior lives.
- `jonas-config` contains shared server deployment configuration. A request to change a deployed service may involve both its application repo and `jonas-config`; verify before modifying deployment files.
- Other folders may be experiments, archives, practice repos, or uncatalogued work. Inspect them before assuming their role.

## Scope and safety

- Work only in the repository or repositories relevant to the request. Do not sweep up unrelated workspace changes.
- Never move, rename, or consolidate project folders as a routing convenience.
- Do not assume a repo is safe to overwrite or that its current branch/worktree is disposable. Inspect Git status before changes and preserve existing edits.
- Keep secrets out of notes and responses. Treat shared server operations and production deployments as higher-impact; inspect the runbook and project instructions before acting.
- Update the wiki only when the task changes durable project facts or the user asks for documentation. Do not mirror issue/task state there; Linear is the tracker when configured.
- If a request does not concern code or an existing project, handle it from the workspace only when that is clearly appropriate; otherwise ask for the intended location.
