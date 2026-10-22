---
description: Security + quality audit of current changes or a path
argument-hint: [path, or empty for git diff]
---
Run `security-auditor` and `code-reviewer` in parallel on: ${ARGUMENTS:-the current git diff}

Merge both reports into one list sorted by severity. End with a single PASS / FAIL verdict.
