---
name: scope-discipline
description: Keeps agents fast and focused. Use at the start of every task to prevent over-exploration, over-refactoring, and long runaway sessions.
---

# Scope Discipline

Long agent runs (1h+ for a small change) almost always come from the same causes. Prevent them.

## Before starting
1. Restate the task in one sentence and list the files you expect to touch.
2. Set a budget: small change = under 10 files, under 15 tool calls of exploration.
3. Find all relevant locations with ONE broad search (`grep -rn`, `git grep`), not file-by-file wandering.

## While working
- Edit in batches per file; avoid re-reading files you already have.
- Run the cheap check (typecheck on changed files, targeted test) per step; run the full suite once at the end.
- Do not refactor, rename, reformat, or "improve" code outside the task.
- Do not install or upgrade dependencies unless required.
- If you discover a larger problem, STOP, note it in the report, and continue with the original task.

## Stop conditions
Stop and ask when: requirements conflict, a destructive action is needed (delete data, force push, migrate prod), you have failed the same fix 3 times, or scope has doubled.

## Anti-loop rule
If the same error appears twice after a fix, do not try a third variation blindly. Re-read the error, re-check your assumption, and test the hypothesis in isolation.
