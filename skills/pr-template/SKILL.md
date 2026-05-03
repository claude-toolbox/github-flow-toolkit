---
name: pr-template
description: 'Create or modify GitHub PR templates via interactive questionnaire. Use when user asks to create, add, modify, or customize PR templates. Covers feature, bugfix, refactor, docs, chore and other PR types. Supports both single-file and multi-template directory modes.'
license: MIT
allowed-tools: Bash, Read, Write, Edit, AskUserQuestion
---

# PR Template Generator

Generate GitHub PR templates through an interactive questionnaire. Produces templates that follow GitHub conventions and match the project's existing style.

## Workflow

### 1. Detect Current State

```bash
# Check if templates already exist
ls .github/pull_request_template.md .github/PULL_REQUEST_TEMPLATE.md .github/PULL_REQUEST_TEMPLATE/ 2>/dev/null
```

- If templates exist, ask whether to modify existing or create new
- If no templates exist, proceed to questionnaire

### 2. Questionnaire

Use AskUserQuestion to gather requirements. Ask in this order:

**Q1 — Template Mode**
- Single file (`.github/pull_request_template.md`) — small projects, one template for all PRs
- Directory (`.github/PULL_REQUEST_TEMPLATE/`) — large projects, multiple templates by type

**Q2 — Template Types** (multiSelect)
- Feature — new functionality
- Bug Fix — fix a bug or regression
- Refactor — code restructuring without behavior change
- Documentation — docs-only changes
- Chore — maintenance, dependencies, CI config

**Q3 — Common Checklist Items** (multiSelect)
- CI checks pass
- Typecheck passes
- Lint passes
- Build passes (when relevant code changes)
- Manual verification performed
- PR title follows Conventional Commits
- Changes align with architecture docs
- Copilot comments addressed

**Q4 — Custom Sections** (optional)
- Ask if user wants project-specific sections (e.g., "Migration Notes", "Breaking Changes", "Screenshots")

### 3. Generate Templates

For each selected type, generate a template following this structure:

#### Single File Mode

Write to `.github/pull_request_template.md`:

```markdown
## Summary

<!-- Brief description of changes -->

## Type of Change

- [ ] Feature
- [ ] Bug fix
- [ ] Refactoring
- [ ] Documentation
- [ ] Other: ___

## Changes

<!-- Key changes organized by scope -->

## Checklist

<!-- Selected common items -->

<!-- Custom sections if any -->
```

#### Directory Mode

Write to `.github/PULL_REQUEST_TEMPLATE/<type>.md`:

```markdown
---
name: <Display Name>
about: <Description>
---

## Summary

<!-- 1-2 sentences -->

<!-- Type-specific sections (e.g., Root Cause for bugfix) -->

<!-- Selected common checklist items -->

<!-- Custom sections if any -->
```

Each template type has type-specific sections:

**Feature:**
- Summary
- Motivation
- Changes (by scope)
- Type of Change checkbox
- Checklist

**Bug Fix:**
- Summary
- Root Cause
- Fix
- Related Issue
- Regression Risk
- Verification (Before/After)
- Checklist

**Refactor:**
- Summary
- Motivation
- What Changed
- What Didn't Change (behavior preserved)
- Checklist

**Documentation:**
- Summary
- What Was Added/Changed/Removed
- Checklist

**Chore:**
- Summary
- Changes
- Checklist

### 4. Present and Confirm

Show the generated template(s) to the user for review before writing files. Allow them to request changes.

### 5. Write Files

After user confirms, write the template file(s). If the directory doesn't exist, create it:

```bash
mkdir -p .github/PULL_REQUEST_TEMPLATE
```

## Template Conventions

- Use HTML comments (`<!-- -->`) for placeholder guidance
- Keep sections concise — templates should guide, not overwhelm
- Checklist items use `- [ ]` format
- For directory mode, include YAML frontmatter with `name` and `about` fields
- Match existing project style if templates already exist
