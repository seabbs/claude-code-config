---
name: org-issues-tidy
description: Add clarity and structure to open issues across an org's repos without repeating previous work
argument-hint: [org-name]
---

# Tidy Issues Across an Org

Review open issues in an org's repos and add concise suggestions for clarity.
Run from `~/code/{org}/` or pass the org name as an argument.
Pass a specific repo name to limit scope (e.g. `/org-issues-tidy epinowcast/baselinenowcast`).

## Accounts

Read `bot_account`, `owner_account` from the org's CLAUDE.md `## Automation config` table.

## Helper script

A pre-built script at `~/.claude/scripts/org-issues-scan.sh` collects all open issues with metadata in one pass.
Run it first to avoid spending tokens on individual `gh` calls.

```bash
~/.claude/scripts/org-issues-scan.sh <org-name> [repo-name] > /tmp/org-issues.json
```

If the script is missing or not executable, run `/setup-scripts` to regenerate it.

The script outputs a JSON array of issues with:
- `repo`, `gh_org`, `number`, `title`, `url`, `labels`, `createdAt`, `updatedAt`
- `author_association` (OWNER, MEMBER, COLLABORATOR, CONTRIBUTOR, NONE)
- `bot_commented` (boolean — whether the bot account has already commented)
- `has_linked_pr` (boolean)
- `is_assigned` (boolean)

## What to do

For each open issue where `bot_commented` is false, check if it would benefit from:
- A clearer title
- A minimal reproducible example
- Breaking into smaller sub-issues
- Labelling suggestions
- A brief summary of the discussion so far (for long threads)

## What NOT to do

- Do not repeat work already done (skip issues where `bot_commented` is true)
- Do not comment on issues that already have clear acceptance criteria
- Do not add noise to active discussions
- Do not suggest changes to issues opened in the last 24 hours (give the author time)
- Keep comments under 100 words

## Phase 1: Filter from script output

From the JSON, filter to issues where:
- `bot_commented` is false
- Issue is older than 24 hours
- Score below 6 on the tidiness scale

## Phase 2: Triage

Score each issue for "tidiness" (0-10):
- Clear title (+2)
- Reproducible example or clear steps (+2)
- Labels assigned (+2)
- Acceptance criteria stated (+2)
- Recent activity within 7 days (+2)

## Phase 3: Comment

For each issue needing attention, post a single concise comment via `gh issue comment`.
Format:

```
A few suggestions to help move this forward:

- [specific, actionable suggestion]
- [specific, actionable suggestion]

This was posted by a bot. Please ping the repo owner for any questions.
```

## Phase 4: Team mode

If more than 5 issues need comments, spawn a team.
Each teammate handles a batch of issues for one repo.

## Phase 5: Summary

Report:
- Issues reviewed (count by repo)
- Comments posted (with links)
- Issues skipped (with reason)

## Auto-Exit When Standalone

**IMPORTANT**: If this command is being run as a standalone request, automatically exit after completing all phases successfully.
