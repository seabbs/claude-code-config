---
name: lint
description: Lint, format, and optionally commit changes
argument-hint: [commit|pr|all]
---

Lint, format, and optionally commit changes.

Arguments: $ARGUMENTS (optional scope)
  - commit: Lint only staged files for commit
  - pr: Lint all files changed in the current PR
  - all: Lint the entire codebase
  - If no argument provided:
    - If in a PR branch: defaults to 'pr'
    - Otherwise: defaults to 'commit'

## Phase 1: Scope Determination
1. Determine scope based on $ARGUMENTS (commit/pr/all/default)

## Phase 2: Linting and Fixing
2. Identify and run available linting tools for detected languages
3. Check against project standards (80 char limit, no trailing whitespace, no spurious blank lines)
4. Apply auto-formatting where possible
5. Report issues that cannot be auto-fixed

## Phase 3: Verification (if issues were fixed)
6. If fixes were applied, run tests to ensure nothing broke
7. Re-stage any fixed files

## Phase 4: Commit Creation (only for "commit" scope)
8. If scope is "commit" and all checks pass:
   - Create commit with format: `style(scope): description`

## Phase 5: Final Report
9. Summary of issues found, fixed, and any remaining issues

If any blocking issues are found, list them clearly and stop before committing.

## Auto-Exit When Standalone
**IMPORTANT**: If this command is being run as a standalone request, automatically exit after completing all phases successfully.
