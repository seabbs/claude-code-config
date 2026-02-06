---
name: improve-coverage
description: Improve test coverage by identifying gaps and generating meaningful tests
argument-hint: [target-file-or-module]
---

Improve test coverage for $ARGUMENTS.

## Phase 1: Coverage Analysis
1. Generate coverage report
2. Identify untested functions, branches, error paths, edge cases

## Phase 2: Test Priority
3. Prioritise by: critical business logic, complex functions, error handling, public APIs

## Phase 3: Test Generation
4. Write tests covering edge cases, error conditions, and property-based tests where appropriate
5. Follow existing test patterns and conventions

## Phase 4: Test Quality
5. Ensure tests are independent, clearly named, test one thing, and run quickly

## Phase 5: Integration
6. Run full test suite, check coverage improvement, refactor for maintainability

Generate tests that catch bugs, not just increase numbers. Focus on behaviour, not implementation.

## Auto-Exit When Standalone
**IMPORTANT**: If this command is being run as a standalone request, automatically exit after completing all phases successfully.
