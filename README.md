# github-flow-toolkit

<!-- README-I18N:START -->

**English** | [简体中文](./README.zh.md)

<!-- README-I18N:END -->

Claude Code plugin for GitHub Flow — commits, PRs, code review, branch protection, and releases.

## Prerequisites

- [GitHub CLI (`gh`)](https://cli.github.com/) — all GitHub operations are performed through `gh`. Install and authenticate (`gh auth login`) before use.

## Installation

```
/plugin marketplace add claude-toolbox/marketplace
/plugin install github-flow-toolkit@marketplace
```

## Usage

Just say what you want in natural language:

- "commit" — conventional commit with auto-detected type/scope
- "create PR" — PR with template compliance
- "merge PR" — merge with strategy selection (squash/merge/rebase)
- "review" — SOLID, security, quality, and dead code analysis
- "pr template" — generate PR templates via questionnaire
- "set up branch protection" — configure GitHub rulesets
- "create release tag" — tag and push

Or use slash commands: `/github-flow-toolkit:commit`, `/github-flow-toolkit:pr`, `/github-flow-toolkit:merge`, `/github-flow-toolkit:review`, `/github-flow-toolkit:ruleset`, `/github-flow-toolkit:pr-template`, `/github-flow-toolkit:tag`

## Skills Sources

Some skills are synced daily from external repos via CI:

| Skill | Source |
|-------|--------|
| git-commit | [github/awesome-copilot](https://github.com/github/awesome-copilot) |
| gh-cli | [github/awesome-copilot](https://github.com/github/awesome-copilot) |
| pr-creator | [google-gemini/gemini-cli](https://github.com/google-gemini/gemini-cli) |
| code-review-expert | [sanyuan0704/code-review-expert](https://github.com/sanyuan0704/code-review-expert) |
| github-actions-docs | [xixu-me/skills](https://github.com/xixu-me/skills) |

## License

[MIT](LICENSE)
