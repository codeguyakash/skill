---
name: coding-standards
description: Best coding practices for TypeScript/JavaScript, React, Node.js. Use whenever writing or reviewing code: naming, structure, types, functions, comments, imports, async, performance.
---

# Coding Standards

## General
- **Readable over clever.** Write for the next person reading it.
- **Single responsibility.** One function does one thing; one module has one reason to change.
- **Small units.** Functions under ~40 lines, files under ~300, parameters 3 or fewer (use an options object beyond that).
- **Early returns** over deep nesting. Max nesting depth 3.
- **DRY after 3 repeats**, not before. Wrong abstractions cost more than duplication.
- **No dead code.** No commented-out code, unused imports, unused variables, or stale TODOs.
- **No magic values.** Extract named constants / enums.
- Comments explain *why*, never *what*. Good names remove the need for most comments.

## Naming
| Thing | Convention | Example |
|---|---|---|
| Variables, functions | camelCase, verb for functions | `getUserById` |
| Booleans | `is/has/can/should` prefix | `isLoading` |
| Classes, types, components | PascalCase | `UserCard` |
| Constants | UPPER_SNAKE_CASE | `MAX_RETRIES` |
| Files | kebab-case (components may be PascalCase) | `user-service.ts` |
| Event handlers | `handle*` in component, `on*` for props | `handleSubmit`, `onSubmit` |

## TypeScript
- `strict: true`, plus `noUncheckedIndexedAccess`, `noImplicitOverride`.
- **No `any`.** Use `unknown` and narrow. No non-null `!` without a comment.
- Prefer `type` for unions/utility types, `interface` for object contracts that may extend.
- Derive types from schemas (`z.infer<typeof Schema>`) so runtime and compile-time never drift.
- Use discriminated unions for state: `{ status: 'loading' } | { status: 'error'; error: AppError } | { status: 'ok'; data: T }`.
- Explicit return types on exported functions.
- `as const` and `satisfies` over type assertions.

## Imports & structure
- Order: node/builtin → external → internal alias → relative → styles. Enforced by ESLint.
- Use path aliases (`@/`), not `../../../`.
- Feature-based folders: `features/auth/{components,hooks,api,types,tests}`.
- No circular dependencies. No barrel files that cause bundle bloat.

## Async
- `async/await` over `.then` chains. Always `await` or explicitly handle every promise (`no-floating-promises`).
- Parallelize independent work: `Promise.all` / `Promise.allSettled`.
- Timeouts + cancellation (`AbortController`) on network calls.
- Never `await` inside a loop unless order matters.

## React / Next.js
- Function components + hooks only. One component per file.
- Keep components presentational; move logic to hooks (`useX`) and services.
- Derive state instead of syncing it. No `useEffect` for things computable during render.
- Complete dependency arrays; never silence `react-hooks/exhaustive-deps`.
- Stable `key` (id, never index for dynamic lists).
- `memo/useMemo/useCallback` only after measuring a problem.
- Server Components by default in Next.js; `"use client"` only where needed.
- Lazy-load heavy routes/components (`next/dynamic`, `React.lazy`).

## Node.js / API
- Layers: route → controller → service → repository. No DB calls in controllers.
- Validate input at the edge; return consistent response shapes:
```ts
type ApiResponse<T> =
  | { success: true; data: T }
  | { success: false; error: { code: string; message: string; details?: unknown } };
```
- Correct HTTP status codes (400, 401, 403, 404, 409, 422, 429, 500).
- Pagination on every list endpoint; indexes on queried fields.
- Config from env, validated at boot (fail fast if missing).
- Structured logging (pino/winston), never `console.log`.
- Graceful shutdown: handle `SIGTERM`, close DB/server.

## Git
- Conventional Commits: `feat:`, `fix:`, `refactor:`, `test:`, `docs:`, `chore:`.
- Branches: `feat/<slug>`, `fix/<slug>`. Small PRs, one concern each.
