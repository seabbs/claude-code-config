---
name: test
description: Run tests iteratively fixing code or tests until all pass, then commit
argument-hint: [commit|pr|all]
---

Run tests iteratively fixing code or tests until all pass, then commit.

Arguments: $ARGUMENTS (optional scope)
  - commit: Test only staged files
  - pr: Test all files changed in the current PR
  - all: Run full test suite
  - If no argument provided:
    - If in a PR branch: defaults to 'pr'
    - Otherwise: defaults to 'commit'

## Phase 1: Scope and Test Discovery
1. Determine scope based on $ARGUMENTS
2. Discover test framework (pytest, testthat, jest, Pkg.test, etc.)
3. Identify test file patterns (test_*.py, *.test.js, test-*.R)
4. Check for untested new features and flag them

## Phase 2: Create Missing Tests
5. For new features without tests:
   - Follow existing test patterns in the codebase
   - Write tests covering happy path, edge cases, error handling
   - For statistical models, test parameter recovery from simulated data

## Phase 3: Iterative Test Fixing
6. Run relevant tests based on scope
7. For each failure, determine if it's a code issue or test issue and fix accordingly
8. Re-run after each fix, verify no other tests broke

## Phase 4: Final Verification
9. Run full test suite for changed files one more time
10. Check test coverage if tools available

## Phase 5: Commit Changes
11. Stage all files and generate commit message:
    - `test: add tests for [feature]` or `fix: resolve test failures in [component]`

## Important
- Use judgement on whether to fix code or tests
- Don't blindly make tests pass, ensure fixes are meaningful
- For R: Use expect_identical over expect_equal where appropriate
- Stop if encountering flaky tests or environment issues

## Auto-Exit When Standalone
**IMPORTANT**: If this command is being run as a standalone request, automatically exit after completing all phases successfully.
