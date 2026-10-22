# Agentic Kit

Drop-in agents, skills, commands, and configs for Claude Code (works with other agent tools via `AGENTS.md`).

## Layout
```
agentic-kit/
├── CLAUDE.md / AGENTS.md        # global rules + routing + definition of done
├── agents/                      # specialised subagents
│   ├── bug-fixer.md
│   ├── feature-builder.md
│   ├── test-engineer.md
│   ├── security-auditor.md
│   ├── code-reviewer.md
│   └── ui-engineer.md
├── skills/                      # reusable rule sets, one folder each
│   ├── scope-discipline/SKILL.md
│   ├── coding-standards/SKILL.md
│   ├── error-handling/SKILL.md
│   ├── event-feedback/SKILL.md
│   ├── linting-formatting/SKILL.md
│   ├── testing/SKILL.md
│   ├── security/SKILL.md
│   └── ui-design/SKILL.md
├── commands/                    # slash commands
│   ├── fix-bug.md  build-feature.md  audit.md  test.md  redesign.md
└── configs/                     # copy into your project root
    ├── .prettierrc  .prettierignore  .editorconfig
    ├── eslint.config.js  tsconfig.strict.json
    ├── lint-staged.config.js  .env.example
```

## Install

**Global (all projects)**
```bash
mkdir -p ~/.claude/{agents,commands,skills}
cp agents/*.md   ~/.claude/agents/
cp commands/*.md ~/.claude/commands/
cp -r skills/*   ~/.claude/skills/
cp CLAUDE.md ~/.claude/CLAUDE.md
```

**Per project**
```bash
mkdir -p .claude/{agents,commands,skills}
cp agents/*.md   .claude/agents/
cp commands/*.md .claude/commands/
cp -r skills/*   .claude/skills/
cp CLAUDE.md AGENTS.md .
cp configs/.prettierrc configs/.prettierignore configs/.editorconfig \
   configs/eslint.config.js configs/lint-staged.config.js configs/.env.example .
```

**Lint tooling**
```bash
npm i -D prettier eslint typescript-eslint eslint-config-prettier eslint-plugin-react \
  eslint-plugin-react-hooks eslint-plugin-jsx-a11y eslint-plugin-import \
  eslint-plugin-security husky lint-staged
npx husky init && echo "npx lint-staged" > .husky/pre-commit
```
Then add the scripts from `skills/linting-formatting/SKILL.md`.

## Usage
```
/fix-bug  login returns 500 when email has uppercase letters
/build-feature  password reset via email OTP
/test  src/features/auth
/audit
/redesign  switch to the new neutral color palette
```
Or just describe the task; agents are auto-selected from their `description`.

## Customize
- Change the stack assumptions at the top of `CLAUDE.md`.
- Edit `skills/ui-design` for your own branding rules.
- Skills are loaded on demand, so add new ones as `skills/<name>/SKILL.md` with `name` + `description` frontmatter.
