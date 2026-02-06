---
name: scan-issues
description: Scan repositories and identify issues that Claude Code can efficiently handle
---

Scan all repositories in $ARGUMENTS and identify issues that Claude Code can efficiently handle.

## Phase 1: Repository Discovery
1. Find all git repositories in the folder recursively
2. Check for open issues: `gh issue list --repo [REPO] --state open`

## Phase 2: Issue Analysis
3. Analyse labels, complexity indicators, file paths, required domain knowledge

## Phase 3: Suitability Scoring (0-10)
Increase for: clear acceptance criteria (+3), limited scope (+2), test-related (+2), documentation (+2), clear refactoring patterns (+2), no external dependencies (+1)
Decrease for: design decisions (-3), deep domain knowledge (-2), complex debugging (-2), UI/UX work (-3)

## Phase 4: Categorisation
- Quick wins (8-10): One session
- Good candidates (6-7): Clear but more time
- Possible with help (4-5): Need human guidance
- Not suitable (<4): Better for humans

## Phase 5: Report
- Top 10 issues sorted by score with title, link, repo, rationale, complexity, approach
- Summary statistics by repository

## Auto-Exit When Standalone
**IMPORTANT**: If this command is being run as a standalone request, automatically exit after completing all phases successfully.
