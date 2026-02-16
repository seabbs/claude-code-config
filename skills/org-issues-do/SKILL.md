---
name: org-issues-do
description: Scan org repos for easy issues opened by contributors and resolve a few via worktree PRs
argument-hint: [org-name] [count]
---

# Do Easy Issues Across an Org

Scan repos in an org for straightforward issues that can be resolved quickly, then do a few via worktree PRs.
Run from `~/code/{org}/` or pass the org name as an argument.
Optional second argument limits how many to do (default: 3).

## Helper script

A pre-built script at `~/.claude/scripts/org-issues-scan.sh` collects all open issues with metadata in one pass.
Run it first to avoid spending tokens on individual `gh` calls.

```bash
~/.claude/scripts/org-issues-scan.sh <org-name> > /tmp/org-issues.json
```

If the script is missing or not executable, run `/setup-scripts` to regenerate it.

The script outputs a JSON array of issues with:
- `repo`, `gh_org`, `number`, `title`, `url`, `labels`, `createdAt`, `updatedAt`
- `author_association` (OWNER, MEMBER, COLLABORATOR, CONTRIBUTOR, NONE)
- `bot_commented` (boolean)
- `has_linked_pr` (boolean)
- `is_assigned` (boolean)

## Issue eligibility

From the script output, only work on issues that meet ALL criteria:

1. **Opened by a contributor**: `author_association` is COLLABORATOR, MEMBER, or OWNER
2. **Or endorsed by a contributor**: has "good first issue" label, or a contributor commented positively
3. **Not assigned**: `is_assigned` is false
4. **No open PR**: `has_linked_pr` is false

## Suitability scoring (0-10)

Score each eligible issue:
- Clear acceptance criteria (+3)
- Limited scope / single file (+2)
- Test-related or documentation (+2)
- Clear refactoring pattern (+2)
- No external dependencies (+1)
- Needs design decisions (-3)
- Needs deep domain knowledge (-2)
- UI/UX work (-3)

Only attempt issues scoring 7+.

## Phase 1: Filter and score from script output

Parse the JSON, apply eligibility and scoring filters.
Rank by score descending.

## Phase 2: Report

Present the top candidates in a table:
- Issue number, title, repo, score, rationale
- Recommended approach (one line)

Ask the user to confirm which to proceed with.

## Phase 3: Execute

For each confirmed issue, use the /pr skill pattern:
1. Create a worktree branch in the target repo
2. Analyse the issue and implement the fix
3. Run tests and linting
4. Commit, push, and create a PR referencing the issue

## Phase 4: Team mode

If doing more than 2 issues, spawn a team.
Each teammate handles one issue as a worktree PR using /pr patterns.
The lead assigns issues and tracks progress.

## Phase 5: Summary

Report:
- Issues attempted (with PR links)
- Issues skipped (with reason)
- Any issues that turned out harder than expected

## Auto-Exit When Standalone

**IMPORTANT**: If this command is being run as a standalone request, automatically exit after completing all phases successfully.
