---
name: update-deps
description: Review and update project dependencies safely with incremental testing
---

Review and update project dependencies safely.

## Phase 1: Dependency Audit
1. List all outdated dependencies
2. Check for security vulnerabilities
3. Identify breaking changes in major updates

## Phase 2: Update Strategy
4. Categorise: security patches (immediate), minor updates (safe), major updates (needs testing)
5. For major updates: read changelog, identify breaking changes, find migration guides

## Phase 3: Incremental Updates
6. Update in order: security patches, dev dependencies, minor production, major (one at a time)
7. After each group: run full test suite, check deprecation warnings

## Phase 4: Documentation
8. Update lock files, README if setup changed, migration notes

Create a PR for each logical group with clear description and testing performed.

## Auto-Exit When Standalone
**IMPORTANT**: If this command is being run as a standalone request, automatically exit after completing all phases successfully.
