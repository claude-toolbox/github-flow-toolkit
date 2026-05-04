## Commit Policy

1. **Same-task append** — amend unpushed commits; create new commit if already pushed.
2. **Split by type** — separate commits for `feat`/`fix`/`refactor` etc.
3. **Loop until clean** — repeat until `git status` is clean.

### Edge Cases

- Never commit empty changes.
- Never commit sensitive files (`.env`, `credentials.json`, `*.pem`).
- Pause and ask the user on merge conflicts.

### Validation

- If the project has a PreToolUse hook, validation runs automatically.
- Otherwise, manually run project-defined check scripts before committing (refer to CLAUDE.md or package.json).
