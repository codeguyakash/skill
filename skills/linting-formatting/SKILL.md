---
name: linting-formatting
description: Indentation, Prettier, ESLint, EditorConfig, TypeScript, and git hooks setup and rules. Use when setting up a project, fixing lint errors, or formatting code.
---

# Linting & Formatting

Formatting is automated, never debated. Agents must run the tools, not format by hand.

## Rules
- **Indentation: 2 spaces**, no tabs. LF line endings. UTF-8. Final newline.
- Prettier owns formatting; ESLint owns correctness. Do not duplicate style rules in ESLint (`eslint-config-prettier`).
- Zero warnings policy: `--max-warnings=0`.
- Never fix lint with `eslint-disable`. If unavoidable, scope to one line with a reason: `// eslint-disable-next-line rule -- reason`.
- Run on save in editor and on commit via hooks; CI is the final gate.

## Setup
```bash
npm i -D prettier eslint typescript-eslint eslint-config-prettier \
  eslint-plugin-react eslint-plugin-react-hooks eslint-plugin-jsx-a11y \
  eslint-plugin-import eslint-plugin-security husky lint-staged
npx husky init
```
Config templates are in `configs/` (`.prettierrc`, `eslint.config.js`, `.editorconfig`, `tsconfig.strict.json`, `lint-staged.config.js`).

## package.json scripts
```json
{
  "scripts": {
    "lint": "eslint . --max-warnings=0",
    "lint:fix": "eslint . --fix",
    "format": "prettier --write .",
    "format:check": "prettier --check .",
    "typecheck": "tsc --noEmit",
    "test": "vitest run",
    "validate": "npm run format:check && npm run lint && npm run typecheck && npm test && npm run build"
  }
}
```

## Hooks
`.husky/pre-commit`:
```
npx lint-staged
```
`.husky/commit-msg` (optional, Conventional Commits):
```
npx --no -- commitlint --edit "$1"
```

## CI (GitHub Actions)
```yaml
name: CI
on: [push, pull_request]
jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: 22, cache: npm }
      - run: npm ci
      - run: npm run validate
      - run: npm audit --omit=dev --audit-level=high
```

## Agent workflow
1. Before finishing any task: `npm run lint:fix && npm run format`, then `npm run validate`.
2. If a lint rule fails, fix the code, not the rule.
3. Only change lint config when asked.
