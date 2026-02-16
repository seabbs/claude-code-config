---
name: code-cleanup
description: Audit and clean up ~/code directory structure, archiving stale repos, fixing folder names, and removing dead worktrees
---

# Code Directory Cleanup

Audit and tidy the `~/code/` directory.
Run from the home directory (`~/`).

## Phase 1: Inventory

For every subdirectory in `~/code/` (including inside org folders), collect:

1. **Git remote** (`git remote get-url origin`, or "no-git")
2. **GitHub org** (parsed from remote URL)
3. **Repo type** (R package = has `DESCRIPTION`, Julia package = has `Project.toml`, paper/grant = has `.qmd`/`.tex`/`submission/`, website = has `_quarto.yml` without package files, org-config = `.github` repos, other)
4. **Last commit date** (`git log -1 --format="%ci"`)
5. **Folder name vs remote name** mismatch
6. **Worktrees** (`git worktree list` in each repo, flag any worktrees whose branch no longer exists on the remote)

## Phase 2: Flag Problems

Report a table for each category:

### Folder name mismatches
Local name differs from remote repo name (excluding `.git` suffix).
For `.github` repos, convention is `{org}-.github`.

### Stale repos (no commits in 6+ months)
Include last commit date and whether it's owned by seabbs orgs or external.

### Dead worktrees
Worktrees whose tracking branch no longer exists on the remote, or worktrees with no uncommitted changes that haven't been touched in 30+ days.

### Repos not in their org folder
Any repo whose remote belongs to a known org (epinowcast, epiforecasts, EpiAware, nfidd, seabbs) but isn't inside the corresponding `~/code/{org}/` folder.

### External repos (not owned by seabbs orgs)
Repos from other orgs/users cloned locally.

### No-git directories
Directories with no git remote.

## Phase 3: Propose Actions

For each flagged item, propose one of:
- **rename**: fix folder name to match remote
- **move**: move into correct org folder
- **archive**: move to `~/code/archive/`
- **delete**: remove entirely (only for empty dirs or confirmed duplicates)
- **clean-worktree**: remove dead worktree

## Phase 4: Execute (with confirmation)

Present the full action list and **ask for confirmation** before executing.
Group actions by type (renames, moves, archives, deletes, worktree cleanup).

When moving repos into org folders, create the org folder if it doesn't exist.
When archiving, move to `~/code/archive/`.

After execution, update `~/CLAUDE.md` with the current repo inventory.

## Phase 5: Summary

Print a final summary showing:
- Total repos by org
- Total archived
- Total cleaned worktrees
- Any items skipped

## Known Org Mapping

These GitHub orgs map to `~/code/{org}/`:
- epinowcast
- epiforecasts
- EpiAware
- nfidd
- seabbs (personal repos)

External repos go in `~/code/external/`.
Repos with no git go in `~/code/archive/` unless actively used.
