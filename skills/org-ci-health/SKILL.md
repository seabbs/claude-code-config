---
name: org-ci-health
description: Quick scan of CI status across all repos in an org, flagging failures and stale workflows
argument-hint: [org-name]
---

# CI Health Check

Scan CI status across repos in an org.
Run from `~/code/{org}/` or pass the org name as an argument.

## Helper script

A pre-built script at `~/.claude/scripts/org-ci-health.sh` collects all CI data in one pass.
Run it first to avoid spending tokens on individual `gh` calls.

```bash
~/.claude/scripts/org-ci-health.sh <org-name> > /tmp/org-ci-health.json
```

If the script is missing or not executable, flag this to the user and stop.

The script outputs a JSON array with per-repo entries containing:
- `repo`, `gh_org`, `has_workflows`
- `recent_runs` (last 5 CI runs on main with status/conclusion)
- `actions_used` (list of `uses:` action references from workflows)

## Phase 1: Analyse script output

Parse the JSON and flag:
- **Failing**: latest main CI conclusion is not "success"
- **Stale**: no CI run in 30+ days
- **No CI**: `has_workflows` is false

## Phase 2: Check workflow versions

From `actions_used`, flag:
- Actions using deprecated versions (e.g. `actions/checkout@v3` when `v4` exists)
- Actions pinned to branches instead of tags
- Outdated `actions/setup-r` or `julia-actions/setup-julia`

## Phase 3: Check for common issues

- Workflows using deprecated `set-output` or `save-state` commands
- Missing `permissions:` blocks
- Workflows that run on `push` to all branches (should be limited)
- R packages missing `R-CMD-check` workflow
- Julia packages missing CI workflow

## Phase 4: Report

Present a dashboard:

| Repo | Main CI | Last run | Actions up to date | Issues |
|---|---|---|---|---|

Then list specific issues grouped by repo.

## Phase 5: Fix (with confirmation)

For fixable issues (action version bumps, workflow updates):
1. Create a worktree branch
2. Apply the fix
3. Create a PR

Use a team if more than 3 repos need fixes.

## Auto-Exit When Standalone

**IMPORTANT**: If this command is being run as a standalone request, automatically exit after completing all phases successfully.
