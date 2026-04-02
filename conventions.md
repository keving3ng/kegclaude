# Coding Conventions

Kevin does not actively review generated code. Write code that is self-evidently correct:
explicit over clever, obvious over concise, predictable over flexible.

---

## TypeScript

- Always use strict mode
- No `any` — use `unknown` + type narrowing, or define the proper type
- Prefer `interface` for object shapes; use `type` for unions, intersections, and aliases
- No non-null assertions (`!`) — use optional chaining or type guards
- Explicit return types on all exported functions
- No `@ts-ignore` or `@ts-expect-error` — fix the underlying type issue

## Imports

- Use tsconfig path aliases (`@components/`, `@lib/`, `@hooks/`, `@utils/`, etc.) over relative paths wherever the alias exists
- Import directly from the source file — no barrel re-exports (`index.ts`) that obscure origins
- Group imports: external packages → internal aliases → relative (if unavoidable)

## Functions & Components

- Small, single-responsibility functions — if a function is doing two things, split it
- Functional React components only — no class components
- Names explain intent, not implementation: `getUserById` not `fetchData`, `isEligible` not `check`
- No magic numbers or strings — extract to named constants at the top of the file or a constants module
- Early returns over nested conditionals

## Error Handling

- Use try/catch for async — never let errors silently disappear
- Surface errors at the boundary where the user or caller can act on them
- Type thrown errors where possible; at minimum log with context before re-throwing

## Agent-Friendly Patterns

These rules exist so code can be understood and extended without reading the full file:

- **Explicit state** — mutations and side effects should be obvious from the call site
- **No clever tricks** — if it requires a comment to explain, rewrite it
- **One concept per file** — a file that imports from itself or has unrelated exports should be split
- **Flat over nested** — max 2 levels of nesting before extracting a function
- **Consistent patterns** — if the codebase does X one way, do it that way; don't introduce a new pattern without a strong reason
