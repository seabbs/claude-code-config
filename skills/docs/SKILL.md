---
name: docs
description: Check that all code has appropriate documentation and fix gaps
argument-hint: [commit|pr|all]
---

Check that all code has appropriate documentation.

Arguments: $ARGUMENTS (optional scope)
  - commit: Check only staged files
  - pr: Check all files changed in the current PR
  - all: Check the entire codebase
  - If no argument provided:
    - If in a PR branch: defaults to 'pr'
    - Otherwise: defaults to 'commit'

## Phase 1: Scope Determination
1. Determine scope based on $ARGUMENTS

## Phase 2: Documentation Analysis
2. Identify functions/classes lacking documentation
3. Check quality of existing documentation
4. Extract project documentation standards from CLAUDE.md

## Phase 3: Documentation Updates
3. For identified gaps:
   - Generate documentation following project conventions
   - Apply language-specific formats (roxygen2, docstrings, JSDoc)
   - Use `@inheritParams` in R to avoid duplication

4. **For R projects:** Run `devtools::document()` to update man/ files

## Phase 4: Quality Verification
5. Verify documentation format compliance and no broken references

## Phase 5: Report Results
6. Summary of files checked, documentation added/updated, and remaining issues

## Auto-Exit When Standalone
**IMPORTANT**: If this command is being run as a standalone request, automatically exit after completing all phases successfully.
