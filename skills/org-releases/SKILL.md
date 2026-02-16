---
name: org-releases
description: Check which packages in an org are overdue for release and draft release PRs
argument-hint: [org-name]
---

# Check and Prepare Package Releases

Scan packages in an org for unreleased changes and prepare release PRs.
Run from `~/code/{org}/` or pass the org name as an argument.

## Helper script

A pre-built script at `~/.claude/scripts/org-releases.sh` collects release status for all packages in one pass.
Run it first to avoid spending tokens on individual `gh` and `git` calls.

```bash
~/.claude/scripts/org-releases.sh <org-name> > /tmp/org-releases.json
```

If the script is missing or not executable, flag this to the user and stop.

The script outputs a JSON array with per-package entries containing:
- `repo`, `type`, `gh_org`, `version`
- `latest_tag`, `release_date`, `commits_since_release`
- `has_unreleased_news`, `main_ci`

Only packages (repos with DESCRIPTION or Project.toml) are included.

## Phase 1: Classify

From the script output, classify each package:
- **Ready to release**: version bumped, NEWS updated, CI passing
- **Needs preparation**: unreleased commits but version/NEWS not updated
- **Up to date**: no commits since last release
- **No releases yet**: package has never been released

## Phase 2: Report

Present a table:

| Repo | Last release | Commits since | NEWS updated | Version bumped | Status |
|---|---|---|---|---|---|

## Phase 3: Prepare releases (with confirmation)

For repos needing preparation, ask the user which to proceed with.
For each confirmed repo:

1. Create a worktree branch `release/v{version}`
2. Bump version in DESCRIPTION/Project.toml if not already done
3. Update NEWS.md with entries from commit messages since last release
4. Run `R CMD check` (R) or tests (Julia)
5. Commit, push, and create a PR

For repos ready to release, offer to create the GitHub release directly.

## Phase 4: Team mode

If more than 2 repos need preparation, spawn a team.

## Auto-Exit When Standalone

**IMPORTANT**: If this command is being run as a standalone request, automatically exit after completing all phases successfully.
