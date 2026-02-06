---
name: review
description: Perform a code review with linting, standards checking, and priority-ranked findings
argument-hint: [commit|pr|all]
---

Perform a code review.

Arguments: $ARGUMENTS (optional scope)
  - commit: Review only staged files for commit
  - pr [number]: Review all files changed in the current PR (or specific PR number)
  - all: Review the entire codebase
  - If no argument provided:
    - If in a PR branch: defaults to 'pr'
    - Otherwise: defaults to 'commit'

## Phase 1: Scope Determination
1. Determine scope based on $ARGUMENTS
2. If reviewing a PR, fetch PR details and linked issue with `gh pr view` and `gh issue view`

## Phase 2: Review
3. Run available linting tools for detected languages
4. Check against project standards from CLAUDE.md (80 char limit, whitespace, naming)
5. Review for: correctness, performance, security, maintainability, test coverage
6. If PR: check alignment with target issue requirements and CI/CD status with `gh pr checks`

## Phase 3: Synthesis
7. Organise findings by priority:
   - Critical issues (must fix)
   - Important improvements (should fix)
   - Suggestions (consider fixing)
   - Positive observations

For each issue, provide location, description, suggested fix, and rationale.
