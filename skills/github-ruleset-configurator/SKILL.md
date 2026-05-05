---
name: github-ruleset-configurator
description: >
  Interactive questionnaire to configure and create GitHub repository rulesets via `gh api`.
  Use this skill whenever the user wants to set up branch protection, tag protection, push rules,
  or any kind of repository ruleset on GitHub — even if they say "branch protection rules",
  "merge rules", "PR requirements", "protect main branch", or "set up code review rules".
  Also use it to list, update, or delete existing rulesets.
context: fork
agent: github-flow
---

# GitHub Ruleset Configurator

Create GitHub repository rulesets through a fast, conversational flow. The goal is minimal questions — infer as much as possible from context, offer presets for common scenarios, and only ask for truly ambiguous choices.

## Prerequisites

- `gh` CLI installed and authenticated
- Target repo exists on GitHub

## Workflow

### Step 1 — Collect intent (1 question max)

Detect the repo automatically if in a git directory: `gh repo view --json nameWithOwner,defaultBranchRef`.

Then ask ONE question that captures everything:

```
用一句话描述你想保护什么，或者选一个预设模板：
```

**Offer these presets alongside the free-text input:**

| Preset | What it does |
|--------|-------------|
| `GitHub Flow 基础保护` | main 分支：必须 PR、禁止 force push、禁止删除 |
| `严格分支保护` | 上述 + 2 人 review、CI 必须通过、线性历史、签名 |
| `开源项目保护` | main 分支：PR + CI 通过 + conventional commits |
| `Tag 发布保护` | 保护 release tag 不被删除或覆盖 |

If the user picks a preset, skip to Step 3. If they describe in natural language, infer the config and proceed.

### Step 2 — Infer from context (no questions)

From the user's description, infer:
- **Target**: branch (default), tag, or push
- **Which refs**: `~DEFAULT_BRANCH` for main, `refs/heads/releases/**` for release branches, etc.
- **Rules to include**: map their intent to rules
- **Enforcement**: `active` by default, `disabled` if they say "先不启用" or "测试"

**Inference examples:**
- "保护 main" → branch, `~DEFAULT_BRANCH`, deletion + non_fast_forward + pull_request
- "禁止 force push" → non_fast_forward
- "必须 PR 才能合并" → pull_request (required_approving_review_count: 0)
- "要 2 人 review" → pull_request (required_approving_review_count: 2)
- "CI 必须过" → required_status_checks
- "conventional commits" → commit_message_pattern with regex
- "Copilot review" → copilot_code_review
- "保护 release tag" → tag target, deletion + non_fast_forward

### Step 3 — Confirm and create (1 question max)

Show the complete JSON payload in a code block, then ask:

```
确认创建这个 ruleset？（可以说"改 XXX"来调整）
```

If the user wants changes, modify inline and show again. Only proceed when they confirm.

### Step 4 — Create

```bash
echo '<json>' > /tmp/ruleset-<name>.json
gh api repos/{owner}/{repo}/rulesets --method POST --input /tmp/ruleset-<name>.json
```

Show the ruleset ID and URL. Remind them to activate if enforcement is `disabled`.

### Step 5 — CI & Security scanning (optional, offer after ruleset creation)

After creating the ruleset, ask:

```
要不要顺便配置 CI 安全扫描？可以自动检测敏感文件（密钥、可执行文件等）并阻止合并。
```

If the user wants it:

**Order matters** — if the ruleset already requires `required_status_checks`, you can't push the workflow directly to main. Two approaches:

**Approach A (recommended)**: Deploy workflow first, then create/update ruleset
1. Push workflow file directly to main (before ruleset blocks it)
2. Create/update ruleset with `required_status_checks`

**Approach B**: If ruleset already exists, deploy workflow via PR
1. Create a feature branch: `gh api repos/{owner}/{repo}/git/refs --method POST -f ref="refs/heads/ci/security-scan" -f sha="{main_sha}"`
2. Push workflow to the branch: `gh api repos/{owner}/{repo}/contents/.github/workflows/security-scan.yml --method PUT -f branch="ci/security-scan" ...`
3. Create PR: `gh pr create --repo {owner}/{repo} --head ci/security-scan --base main ...`
4. Remind user: first merge may need admin bypass since CI hasn't run yet

**Workflow template** — read from `references/security-scan-workflow.yml` when needed. It includes:
- Gitleaks secret scanning
- Blocked file extensions (`.exe`, `.env`, `.pem`, `.key`, etc.)
- Blocked sensitive paths (`.ssh/`, `.aws/credentials`, etc.)
- File size limit (50MB)

**Important**: Push rulesets (`target: "push"`) only work on org-owned repos. For personal repos, use `required_status_checks` + CI workflow as the alternative.

## Preset configs

### GitHub Flow 基础保护
```json
{
  "name": "main-branch-protection",
  "target": "branch",
  "enforcement": "active",
  "conditions": { "ref_name": { "include": ["~DEFAULT_BRANCH"], "exclude": [] } },
  "rules": [
    { "type": "deletion" },
    { "type": "non_fast_forward" },
    { "type": "pull_request", "parameters": {
      "required_approving_review_count": 0,
      "dismiss_stale_reviews_on_push": false,
      "require_code_owner_review": false,
      "require_last_push_approval": false,
      "required_review_thread_resolution": false
    }}
  ]
}
```

### 严格分支保护
```json
{
  "name": "strict-branch-protection",
  "target": "branch",
  "enforcement": "active",
  "conditions": { "ref_name": { "include": ["~DEFAULT_BRANCH"], "exclude": [] } },
  "rules": [
    { "type": "deletion" },
    { "type": "non_fast_forward" },
    { "type": "required_linear_history" },
    { "type": "required_signatures" },
    { "type": "pull_request", "parameters": {
      "required_approving_review_count": 2,
      "dismiss_stale_reviews_on_push": true,
      "require_code_owner_review": true,
      "require_last_push_approval": true,
      "required_review_thread_resolution": true
    }},
    { "type": "required_status_checks", "parameters": {
      "required_status_checks": [{ "context": "security/scan" }],
      "strict_required_status_checks_policy": false,
      "do_not_enforce_on_create": true
    }}
  ]
}
```

### 开源项目保护
```json
{
  "name": "open-source-protection",
  "target": "branch",
  "enforcement": "active",
  "conditions": { "ref_name": { "include": ["~DEFAULT_BRANCH"], "exclude": [] } },
  "rules": [
    { "type": "deletion" },
    { "type": "non_fast_forward" },
    { "type": "pull_request", "parameters": {
      "required_approving_review_count": 1,
      "dismiss_stale_reviews_on_push": true,
      "require_code_owner_review": false,
      "require_last_push_approval": false,
      "required_review_thread_resolution": true
    }},
    { "type": "required_status_checks", "parameters": {
      "required_status_checks": [{ "context": "security/scan" }],
      "strict_required_status_checks_policy": false,
      "do_not_enforce_on_create": true
    }},
    { "type": "commit_message_pattern", "parameters": {
      "name": "conventional-commits",
      "operator": "regex",
      "pattern": "^(feat|fix|docs|style|refactor|perf|test|chore|build|ci|revert)(\\(.+\\))?: .+",
      "negate": false
    }}
  ]
}
```

### Tag 发布保护
```json
{
  "name": "release-tag-protection",
  "target": "tag",
  "enforcement": "active",
  "conditions": { "ref_name": { "include": ["~ALL"], "exclude": [] } },
  "rules": [
    { "type": "deletion" },
    { "type": "non_fast_forward" }
  ]
}
```

## Other operations

For **list/update/delete**, handle inline without extra questions:

- **List**: `gh api repos/{owner}/{repo}/rulesets --jq '.[] | {id, name, target, enforcement}'`
- **Update**: fetch current config, show what to change, ask for confirmation
- **Delete**: list rulesets, ask which one, confirm, delete

## Important notes

- Always show JSON before creating — never skip confirmation
- Push rulesets (`target: "push"`) only work on org-owned repos — warn users if they request this on a personal repo
- For status checks, `do_not_enforce_on_create: true` is usually the right default for new branches
- When the user asks for Copilot review, set it as non-blocking (don't add it to required_status_checks)
