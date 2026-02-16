---
name: org-maintenance
description: Clean up stale worktrees and help stuck PRs across an org's repos
argument-hint: [org-name]
---

# Org Maintenance

Clean up dead worktrees and unstick PRs across repos in an org.
Run from `~/code/{org}/` or pass the org name as an argument.

## Accounts

Read `bot_account`, `owner_account` from the org's CLAUDE.md `## Automation config` table.

## Helper script

A pre-built script at `~/.claude/scripts/org-maintenance.sh` collects worktree and PR data in one pass.
Run it first to avoid spending tokens on individual `git` and `gh` calls.

```bash
~/.claude/scripts/org-maintenance.sh <org-name> > /tmp/org-maintenance.json
```

If the script is missing or not executable, flag this to the user and stop.

The script outputs a JSON object with:
- `worktrees`: array of non-main worktrees per repo (path, branch, whether directory exists)
- `open_prs`: array of open PRs by the bot/owner accounts with CI status, review decision, merge info

## Phase 1: Worktree Audit

From the script output, flag worktrees that:
1. Point to missing directories (`exists: false`)
2. Have branches merged or deleted on the remote
3. Have no commits in 30+ days and no uncommitted changes

Present a summary table and prune with confirmation.

## Phase 2: Stuck PR Audit

From the script output, identify PRs that are stuck:
- CI status (failing checks)
- Review status (changes requested, unreviewed)
- Time since last update
- Merge conflicts

### What to fix

**For bot/owner PRs** (full access):
- Fix failing CI (linting errors, test failures, docs issues)
- Resolve merge conflicts
- Address review comments where the fix is mechanical
- Push fixes directly to the PR branch

**For PRs on other people's repos**:
- Open a new PR addressing linting, docs, or test coverage gaps only
- Make it clear in the PR description that suggestions are optional
- Do not touch architectural or behavioural changes

### What NOT to fix
- Design decisions or API changes
- Anything requiring domain knowledge beyond the code itself
- PRs with ongoing discussion (don't interrupt)

## Phase 3: Team mode

If more than 3 PRs need attention, spawn a team.
Each teammate handles one PR fix.
The lead tracks progress and reports results.

## Phase 4: Summary

Report:
- Worktrees pruned (count by repo)
- PRs fixed (with links)
- PRs skipped (with reason)

## Auto-Exit When Standalone

**IMPORTANT**: If this command is being run as a standalone request, automatically exit after completing all phases successfully.
