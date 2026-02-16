---
name: org-deps
description: Audit cross-repo dependencies within an org and flag outdated or breaking changes
argument-hint: [org-name]
---

# Cross-Repo Dependency Audit

Check how packages in an org depend on each other and flag version mismatches.
Run from `~/code/{org}/` or pass the org name as an argument.
Can also run across multiple orgs (e.g. `/org-deps epinowcast epiforecasts`).

## Helper script

A pre-built script at `~/.claude/scripts/org-deps.sh` collects all dependency info in one pass.
Run it first to avoid spending tokens on file parsing.

```bash
~/.claude/scripts/org-deps.sh <org-name> [org-name2 ...] > /tmp/org-deps.json
```

If the script is missing or not executable, flag this to the user and stop.

The script outputs a JSON array with per-repo entries containing:
- `repo`, `org`, `gh_org`, `type`, `version`, `latest_release_tag`
- `dependencies` object with R fields (`depends`, `imports`, `suggests`, `remotes`) or Julia fields (`julia_deps`, `julia_compat`)

## Phase 1: Build dependency graph

From the script output, build a graph of which local packages depend on which other local packages.

## Phase 2: Check for issues

### Version mismatches
- Package A requires `primarycensored >= 1.0` but local copy is on `0.9.5`
- `Remotes` pointing to branches that no longer exist
- `[compat]` bounds that exclude the latest release

### Breaking changes
For each dependency edge, check if the upstream package has commits since the version pinned downstream that include:
- Breaking changes (search NEWS.md for "breaking", "removed", "deprecated")
- API changes (renamed exports, changed function signatures)

### Stale pins
Downstream packages pinning old versions when newer ones are available on CRAN or in the org.

## Phase 3: Report

Present:
1. Dependency graph (text format showing who depends on whom)
2. Table of issues found with severity (breaking/outdated/info)
3. Suggested fixes

## Phase 4: Fix (with confirmation)

For each confirmed fix:
1. Create a worktree branch
2. Update version constraints
3. Run tests to verify compatibility
4. Create a PR

## Auto-Exit When Standalone

**IMPORTANT**: If this command is being run as a standalone request, automatically exit after completing all phases successfully.
