---
name: issue-reply
description: Post a helpful reply to a GitHub issue
---

Post a helpful reply to issue #$ARGUMENTS.

## Phase 1: Context Gathering
1. Read full issue: `gh issue view $ARGUMENTS --repo [REPO]`
2. Read all comments: `gh issue view $ARGUMENTS --comments --repo [REPO]`
3. Check labels, assignees, project status

## Phase 2: Reply Analysis
4. Determine reply type: answer, status update, clarification, information request, solution proposal

## Phase 3: Reply Drafting
5. Draft a reply that directly addresses the last comment, is concise and actionable, includes code examples if relevant

## Phase 4: Pre-flight Checks
6. Check accuracy, clarity, no unintended commitments, appropriate @mentions

## Phase 5: Posting
7. Post: `gh issue comment $ARGUMENTS --body "[CONTENT]" --repo [REPO]`
