# github-flow-toolkit

<!-- README-I18N:START -->

[English](./README.md) | **简体中文**

<!-- README-I18N:END -->

Claude Code 的 GitHub Flow 插件 — 提交、PR、代码审查、分支保护和发布。

## 前置依赖

- [GitHub CLI (`gh`)](https://cli.github.com/) — 所有 GitHub 操作均通过 `gh` 执行。使用前请先安装并完成认证（`gh auth login`）。

## 安装

```
/plugin marketplace add claude-toolbox/marketplace
/plugin install github-flow-toolkit@marketplace
```

## 使用方式

直接用自然语言描述需求：

- "commit" — 约定式提交，自动检测类型/作用域
- "create PR" — 按模板规范创建 PR
- "merge PR" — 选择策略合并（squash/merge/rebase）
- "review" — SOLID、安全、质量和死代码审查
- "pr template" — 通过问卷生成 PR 模板
- "set up branch protection" — 配置 GitHub rulesets
- "create release tag" — 创建并推送发布标签

或使用斜杠命令：`/github-flow-toolkit:commit`、`/github-flow-toolkit:pr`、`/github-flow-toolkit:merge`、`/github-flow-toolkit:review`、`/github-flow-toolkit:ruleset`、`/github-flow-toolkit:pr-template`、`/github-flow-toolkit:tag`

## Skills 来源

部分 skills 通过 CI 每日从外部仓库同步：

| Skill | 来源 |
|-------|------|
| git-commit | [github/awesome-copilot](https://github.com/github/awesome-copilot) |
| gh-cli | [github/awesome-copilot](https://github.com/github/awesome-copilot) |
| pr-creator | [google-gemini/gemini-cli](https://github.com/google-gemini/gemini-cli) |
| code-review-expert | [sanyuan0704/code-review-expert](https://github.com/sanyuan0704/code-review-expert) |
| github-actions-docs | [xixu-me/skills](https://github.com/xixu-me/skills) |

## 许可证

[MIT](LICENSE)
