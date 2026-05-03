# github-flow-toolkit

A Claude Code plugin that covers the full GitHub Flow lifecycle: conventional commits, PR creation, code review, branch protection, and release tagging.

Just say "commit", "create PR", or "review" in natural language — the plugin handles the rest.

## Features

- **Conventional Commits** — auto-detect type/scope from diff, generate semantic commit messages
- **PR Lifecycle** — create PRs with template compliance, merge with strategy selection (squash/merge/rebase)
- **Code Review** — SOLID violations, security scanning, code quality, dead code detection with P0-P3 severity reports
- **Branch Protection** — configure GitHub rulesets via interactive presets (basic, strict, open-source, tag protection)
- **PR Templates** — generate type-specific templates (Feature, Bug Fix, Refactor, Documentation, Chore)
- **Release Tagging** — create and push release tags
- **GitHub Actions Docs** — grounded answers from official documentation
- **Natural Language Routing** — no need to remember commands, just describe what you want

## Commands

| Command | Description |
|---------|-------------|
| `/github-flow-toolkit:commit` | Commit with conventional commit message |
| `/github-flow-toolkit:pr` | Create a pull request |
| `/github-flow-toolkit:merge` | Merge a PR with strategy selection |
| `/github-flow-toolkit:review` | Code review (SOLID, security, quality, dead code) |
| `/github-flow-toolkit:ruleset` | Configure branch/tag protection |
| `/github-flow-toolkit:tag` | Create a release tag |

Or just say "commit", "create PR", "review" etc. in natural language.

## Installation

```bash
claude plugin install github-flow-toolkit
```

Or clone and install locally:

```bash
git clone https://github.com/<your-username>/github-flow-toolkit.git
claude plugin install ./github-flow-toolkit
```

## Project Structure

```
.github/workflows/
  sync-npx-skills.yml    # Daily CI to sync upstream skills

.claude-plugin/
  plugin.json            # Plugin manifest

commands/
  commit.md              # /github-flow-toolkit:commit
  pr.md                  # /github-flow-toolkit:pr
  merge.md               # /github-flow-toolkit:merge
  review.md              # /github-flow-toolkit:review
  ruleset.md             # /github-flow-toolkit:ruleset
  tag.md                 # /github-flow-toolkit:tag

rules/
  workflow.mdc           # Natural language → skill routing
  pr.mdc                 # PR creation/merge policy
  commit.mdc             # Commit policy (amend, split, loop)
  branch.mdc             # Backup branch protection
  language.mdc           # Output language from Claude Code settings

skills/
  git-commit/            # Conventional Commits
  pr-creator/            # PR creation with template compliance
  pr-template/           # PR template generator
  pr-merge/              # PR merge with strategy selection
  code-review-expert/    # SOLID + security + quality review
  github-actions-docs/   # GitHub Actions documentation
  github-ruleset-configurator/  # Branch/tag protection rulesets

skills-sources.json      # Upstream source tracking for npx skills
```

## Skills Sources

Some skills are sourced from [skills.sh](https://skills.sh/) and kept in sync via daily CI:

| Skill | Source |
|-------|--------|
| git-commit | [github/awesome-copilot](https://github.com/github/awesome-copilot) |
| pr-creator | [google-gemini/gemini-cli](https://github.com/google-gemini/gemini-cli) |
| code-review-expert | [sanyuan0704/code-review-expert](https://github.com/sanyuan0704/code-review-expert) |
| github-actions-docs | [xixu-me/skills](https://github.com/xixu-me/skills) |

The CI workflow (`sync-npx-skills.yml`) runs daily, fetches the latest SKILL.md files from upstream repos, and commits changes automatically.

## Rules

The plugin includes 5 always-active rules:

- **workflow** — routes natural language to the appropriate skill
- **pr** — enforces using pr-creator/pr-merge (no direct `gh pr` commands)
- **commit** — same-task amend, type-based splitting, loop until clean
- **branch** — protects branches containing `backup`
- **language** — output follows Claude Code's language setting (defaults to English)

## License

[MIT](LICENSE)
