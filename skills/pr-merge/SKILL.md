---
name: pr-merge
description: 'Merge a GitHub PR with strategy selection. Use when user asks to merge a PR, mentions "/pr-merge", or says "merge PR". Supports: (1) squash/merge/rebase strategy, (2) auto-merge when CI pending, (3) interactive strategy suggestion'
license: MIT
allowed-tools: Bash
---

# PR Merge

## Overview

Merge a GitHub PR using `gh pr merge`. Supports squash, merge commit, and rebase strategies.

## Workflow

### 1. Identify PR

```bash
# If user provides PR number, use it directly
# Otherwise, check current branch
gh pr list --state open --json number,title,headRefName
```

### 2. Check PR Status

```bash
gh pr view <number> --json statusCheckRollup,mergeable,reviewDecision
```

### 3. Determine Merge Strategy

- If user explicitly specifies `squash`, `merge`, or `rebase` in their message, use that directly
- Otherwise, analyze the PR and suggest:
  - **squash** (default suggestion) — single feature PR, clean history
  - **merge commit** — PR has multiple independent commits each with meaning
  - **rebase** — need linear history while preserving individual commits
- Wait for user confirmation before proceeding

### 4. Execute Merge

```bash
# When CI is passing
gh pr merge <number> --squash --delete-branch
gh pr merge <number> --merge --delete-branch
gh pr merge <number> --rebase --delete-branch

# When CI is still pending
gh pr merge <number> --squash --auto --delete-branch
gh pr merge <number> --merge --auto --delete-branch
gh pr merge <number> --rebase --auto --delete-branch
```

### 5. Post-merge Cleanup

If the source branch is the current local branch, switch back to main:

```bash
git checkout main
git pull
```

## Safety Rules

- Never pass `--subject` or `--body` unless user explicitly asks for a custom commit message
- Always append `--delete-branch` to clean up remote branch
- If merge conflict exists, pause and ask user
- If user is on the branch being merged, switch to main after merge
