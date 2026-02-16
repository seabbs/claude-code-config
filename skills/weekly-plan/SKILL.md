---
name: weekly-plan
description: Review the past week's activity and suggest a prioritised plan for the coming week
---

# Weekly Plan

Review what happened last week and suggest priorities for the coming week.

## Accounts

Read `bot_account`, `owner_account` from the org's CLAUDE.md `## Automation config` table.

## Helper script

A pre-built script at `~/.claude/scripts/weekly-plan.sh` gathers all planning context in one pass.
Run it first to avoid spending tokens on individual `gh` and `git` calls.

```bash
~/.claude/scripts/weekly-plan.sh > /tmp/weekly-plan.json
```

If the script is missing or not executable, flag this to the user and stop.

The script outputs a JSON object with:
- `today`, `week_start`: date range
- `daily_logs`: list of available daily log dates from `~/code/claude-log/`
- `open_prs`: all open PRs by the owner and bot accounts across orgs
- `assigned_issues`: all issues assigned to the owner across orgs
- `recent_commits`: commit counts per repo for the past week
- `prs_merged_this_week`, `issues_closed_this_week`: totals

## Phase 1: Enrich context

1. Read any daily log files listed in `daily_logs` for narrative context
2. Check grant repos for deadline-related files or comments
3. Check issues with milestone dates

## Phase 2: Categorise

Group work into:
- **Blocked**: PRs waiting on review, CI failures needing upstream fixes
- **In progress**: open PRs, active worktrees, partially done issues
- **Ready to do**: assigned issues, bot task requests, release preparation
- **Maintenance**: stale worktrees to prune, dependency updates, CI fixes

## Phase 3: Prioritise

Score each item:
- Blocking other work (+3)
- Quick win, under 30 minutes (+2)
- External deadline (+3)
- Requested by someone else (+1)
- Maintenance / nice to have (+0)

## Phase 4: Present plan

Write to `~/code/claude-log/weekly-plan-YYYY-MM-DD.md`:

```markdown
# Weekly plan: YYYY-MM-DD

## Last week summary
- [2-3 sentences on what was accomplished]
- PRs merged: N
- Issues closed: N
- New PRs opened: N

## Blockers
- [ ] item (reason blocked)

## This week's priorities

### Must do
1. item (reason)

### Should do
1. item (reason)

### Could do
1. item (reason)

## Maintenance backlog
- item
```

Also present the plan in the terminal.

## Auto-Exit When Standalone

**IMPORTANT**: If this command is being run as a standalone request, automatically exit after completing all phases successfully.
