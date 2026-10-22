# Project Rules (CLAUDE.md / AGENTS.md)

Read this first, every session. Detailed rules live in `skills/*/SKILL.md`. Load only the skill relevant to the task.

## Stack (default assumptions)
React, Next.js, Node.js, TypeScript (strict), MongoDB, PostgreSQL, Docker, GitHub Actions. Verify with `package.json` before assuming.

## Non-negotiables
1. **Scope discipline** — do only what was asked. No drive-by refactors. See `skills/scope-discipline`.
2. **Plan before edit** — for any task touching 3+ files, write a 5-line plan first.
3. **Verify before "done"** — run lint, typecheck, tests, build. Show the output. Never claim success without running them.
4. **No silent failures** — every error is handled, logged, and surfaced. See `skills/error-handling`.
5. **No secrets in code** — env vars only. See `skills/security`.
6. **Match existing conventions** — read 2-3 nearby files before writing new code.
7. **Small diffs** — one concern per change. Easy to review, easy to revert.

## Agent routing
| Task | Agent |
|---|---|
| Something is broken | `bug-fixer` |
| New functionality | `feature-builder` |
| Write / fix tests | `test-engineer` |
| Vulnerabilities, secrets, deps | `security-auditor` |
| Review a diff before merge | `code-reviewer` |
| Anything visual | `ui-engineer` |

## Definition of Done
- [ ] Requirement met and manually verified
- [ ] `npm run lint` — 0 errors, 0 warnings
- [ ] `npm run typecheck` — clean
- [ ] `npm test` — green, new code covered
- [ ] `npm run build` — succeeds
- [ ] No `console.log`, no commented-out code, no `any`, no TODO without ticket
- [ ] Error paths and loading/empty states handled
- [ ] Changelog entry added (user-facing changes)

## Final report format (every task)
```
Changed: <files, one line each>
Why: <1-2 lines>
Verified: <commands run + result>
Risks / follow-ups: <or "none">
```
