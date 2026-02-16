---
name: org-standards
description: Scan repos in an org for standards drift and propagate fixes via worktree PRs
argument-hint: [org-name]
---

# Propagate Standards Across Repos

Scan all repos in a GitHub org (or local org folder) for standards drift, then apply fixes.
Two-phase approach: update the `.github` shared repo first, then propagate to individual repos.
Run from `~/code/{org}/` or pass the org name as an argument.

## Accounts

Read `bot_account`, `owner_account` from the org's CLAUDE.md `## Automation config` table.

## Helper script

A pre-built script at `~/.claude/scripts/org-standards.sh` collects config file presence across all repos in one pass.
Run it first to avoid spending tokens checking files individually.

```bash
~/.claude/scripts/org-standards.sh <org-name> > /tmp/org-standards.json
```

If the script is missing or not executable, run `/setup-scripts` to regenerate it.

The script outputs a JSON object with:
- `org`, `gh_org`
- `dot_github`: shared `.github` repo info (shared_workflows, shared_templates, has_taskfile)
- `declined`: previously declined suggestions (from CLAUDE.md `## Declined standards`)
- `open_standards_prs`: open PRs on the .github repo from bot
- `repos`: array of per-repo entries with config booleans, ci_files, uses_shared_workflows, taskfile, badge_count

## Declined suggestions tracking

The org's CLAUDE.md has a `## Declined standards` section listing suggestions that were previously rejected.
**Never re-suggest** anything listed there.
Format for declined entries:

```markdown
## Declined standards

- `pre-commit-config`: Not using pre-commit in this org (2025-01-15)
- `pkgdown/baselinenowcast`: Custom docs setup preferred (2025-02-01)
```

When a suggestion is declined (by 👎 on the PR, or explicit rejection):
1. Add it to `## Declined standards` with the date and reason
2. Close the PR if one exists

## What to check

Pick the most complete repo in the org as the reference, then compare others against it.

### Code quality
- Linting config (.lintr, .JuliaFormatter.toml)
- Pre-commit config (.pre-commit-config.yaml)
- Formatting consistency

### Project config
- CI workflow patterns (.github/workflows/)
- CITATION.cff presence and format
- CLAUDE.md presence
- LICENSE consistency
- .gitignore patterns
- Taskfile.yml presence

### Documentation
- README structure and badges
- NEWS.md / CHANGELOG format
- pkgdown / Documenter config

## Phase 1: Analyse script output

From the JSON, identify:
1. What shared configs exist in the `.github` repo
2. Which shared workflows repos are already using
3. What the most complete repo looks like as a reference
4. What has been declined before (skip those)
5. Whether there are open standards PRs still waiting for review

**If there are open standards PRs**: report their status and do not duplicate their changes.

## Phase 2: Update .github repo first

Before touching individual repos, ensure shared configs are up to date in the `.github` repo.
This means shared workflows, templates, and Taskfile.yml live centrally.

Things that belong in `.github`:
- Reusable CI workflows (`.github/workflows/`)
- Issue and PR templates
- CONTRIBUTING.md, CODE_OF_CONDUCT.md, STYLE_GUIDE.md
- Shared Taskfile.yml (if the org uses Task)

For each missing shared config:
1. Create a worktree branch in the `.github` repo
2. Add the shared config
3. Create a PR
4. **Stop here for this item** — do not propagate to repos until the PR is merged

## Phase 3: Report per-repo drift

Present a table of repos vs standards, showing what needs updating.
Group by priority:
- Missing entirely (e.g. no .pre-commit-config.yaml)
- Could use shared workflow (local workflow duplicates a shared one)
- Outdated (e.g. old CI workflow version)
- Minor drift (e.g. slightly different .gitignore)

**Exclude** anything in the declined list.
**Exclude** anything covered by an open, unmerged `.github` PR.

## Phase 4: Apply per-repo fixes (with confirmation)

Ask the user which fixes to apply.
For each fix:
1. Create a worktree branch in the target repo
2. Apply the change
3. Where possible, reference a shared workflow instead of duplicating config
4. Run linting if applicable
5. Commit and push
6. Create a PR

**For bot/owner-owned repos**: apply directly with a clear commit message.
**For repos owned by others**: open a PR making it clear the suggestions are optional.

## Phase 5: Team mode

If more than 3 repos need fixes, spawn a team.
Each teammate handles one repo's fixes as a worktree PR.
The lead coordinates and reports results.

## Phase 6: Handle responses on previous PRs

Check for reactions/comments on previously opened standards PRs:
- Merged or 👍: mark as accepted, update the repo's entry
- Closed or 👎: add to `## Declined standards` in CLAUDE.md with date and reason

```bash
# Check PR status
gh pr view <number> -R <org>/<repo> --json state,reviews
```

## Auto-Exit When Standalone

**IMPORTANT**: If this command is being run as a standalone request, automatically exit after completing all phases successfully.
