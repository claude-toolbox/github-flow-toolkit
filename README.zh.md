# github-flow-toolkit

<!-- README-I18N:START -->

[English](./README.md) | **简体中文**

<!-- README-I18N:END -->

一个覆盖完整 GitHub Flow 生命周期的 Claude Code 插件：约定式提交、PR 创建、代码审查、分支保护和发布标签。

只需用自然语言说 "commit"、"create PR" 或 "review"，插件会自动完成其余工作。

## 功能特性

- **约定式提交** — 从 diff 自动检测类型/作用域，生成语义化提交信息
- **PR 生命周期** — 按模板规范创建 PR，支持策略选择合并（squash/merge/rebase）
- **代码审查** — 审查待提交的变更，检测 SOLID 违规、安全风险、代码质量问题和死代码，生成 P0-P3 严重等级报告
- **分支保护** — 通过交互式预设配置 GitHub rulesets（基础、严格、开源、标签保护）
- **PR 模板** — 交互式问卷生成 GitHub PR 模板，支持单文件和多模板目录模式，包含按类型分区（Feature、Bug Fix、Refactor、Documentation、Chore）
- **发布标签** — 创建并推送发布标签
- **GitHub Actions 文档** — 基于官方文档提供准确回答
- **GitHub CLI 参考** — 涵盖所有 GitHub 操作的 gh CLI 综合参考
- **自然语言路由** — 无需记忆命令，直接描述需求即可

## 命令

| 命令 | 说明 |
|------|------|
| `/github-flow-toolkit:init` | 将插件规则安装到 `.claude/rules/` |
| `/github-flow-toolkit:commit` | 使用约定式提交信息提交 |
| `/github-flow-toolkit:pr` | 创建 Pull Request |
| `/github-flow-toolkit:merge` | 选择策略合并 PR |
| `/github-flow-toolkit:review` | 审查待提交变更（SOLID、安全、质量、死代码） |
| `/github-flow-toolkit:ruleset` | 配置分支/标签保护 |
| `/github-flow-toolkit:pr-template` | 通过交互式问卷生成 PR 模板 |
| `/github-flow-toolkit:tag` | 创建发布标签 |

也可以直接用自然语言说 "commit"、"create PR"、"review"、"pr template" 等。

## 安装

先添加 marketplace，然后安装插件：

```
/plugin marketplace add claude-toolbox/marketplace
/plugin install github-flow-toolkit@marketplace
```

## 项目结构

```
.github/workflows/
  sync-skills.yml    # 每日同步上游 skills 的 CI

.claude-plugin/
  plugin.json            # 插件清单

commands/
  commit.md              # /github-flow-toolkit:commit
  pr.md                  # /github-flow-toolkit:pr
  merge.md               # /github-flow-toolkit:merge
  review.md              # /github-flow-toolkit:review
  ruleset.md             # /github-flow-toolkit:ruleset
  init.md                # /github-flow-toolkit:init — 安装规则
  pr-template.md         # /github-flow-toolkit:pr-template
  tag.md                 # /github-flow-toolkit:tag

rules-templates/
  language.md            # 输出语言跟随 Claude Code 设置
  workflow.md            # 自然语言 → skill 路由
  pr.md                  # PR 创建/合并策略
  commit.md              # 提交策略（amend、拆分、循环）
  branch.md              # backup 分支保护

skills/
  git-commit/            # 约定式提交
  pr-creator/            # 按模板规范创建 PR
  pr-template/           # PR 模板生成器
  pr-merge/              # PR 策略选择合并
  code-review-expert/    # SOLID + 安全 + 质量审查
  github-actions-docs/   # GitHub Actions 文档
  github-ruleset-configurator/  # 分支/标签保护 rulesets
  gh-cli/                   # GitHub CLI 综合参考
```

## Skills 来源

部分 skills 来自 [skills.sh](https://skills.sh/)，通过每日 CI 自动同步：

| Skill | 来源 |
|-------|------|
| git-commit | [github/awesome-copilot](https://github.com/github/awesome-copilot) |
| gh-cli | [github/awesome-copilot](https://github.com/github/awesome-copilot) |
| pr-creator | [google-gemini/gemini-cli](https://github.com/google-gemini/gemini-cli) |
| code-review-expert | [sanyuan0704/code-review-expert](https://github.com/sanyuan0704/code-review-expert) |
| github-actions-docs | [xixu-me/skills](https://github.com/xixu-me/skills) |

CI 工作流（`sync-skills.yml`）每天运行，从上游仓库获取最新的 SKILL.md 文件并自动提交变更。

## 规则

安装插件后，运行 `/github-flow-toolkit:init` 将始终激活的规则安装到 `.claude/rules/`。命令会自动检测插件是安装在用户级别（`~/.claude/rules/`）还是项目级别（`.claude/rules/`）。

包含的规则：

- **language** — 输出语言跟随 Claude Code 设置（默认英文）
- **workflow** — 将自然语言路由到对应的 skill
- **pr** — 强制使用 pr-creator/pr-merge（不允许直接使用 `gh pr` 命令）
- **commit** — 同任务 amend、按类型拆分、循环直到干净
- **branch** — 保护包含 `backup` 的分支

## 许可证

[MIT](LICENSE)
