---
name: github-dashboard
description: GitHub dashboard update with notifications, PR status, issue triage, and action items
---

Give me a GitHub dashboard update.

## Phase 1: Notifications Analysis
1. Fetch notifications: `gh api notifications`
2. Group by repository and type, prioritise by: direct mentions, review requests, CI failures, issue updates

## Phase 2: PR Status Check
3. Check open PRs: `gh pr list --author @me --state open`
4. For each: review status, CI status, merge conflicts, time since update
5. List PRs needing action

## Phase 3: Issue Triage
6. Issues assigned to me: `gh issue list --assignee @me`
7. New issues in maintained repos needing triage
8. Flag overdue or blocked issues

## Phase 4: Summary Report
9. Action items, review requests, CI failures, upcoming deadlines, quick stats
10. Suggest priority order for the day

## Auto-Exit When Standalone
**IMPORTANT**: If this command is being run as a standalone request, automatically exit after completing all phases successfully.
