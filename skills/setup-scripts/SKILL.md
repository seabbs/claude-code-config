---
name: setup-scripts
description: Generate or regenerate all helper scripts used by org automation skills
---

# Setup Helper Scripts

Generate all shell scripts in `~/.claude/scripts/` used by the org automation skills.
Run this after a fresh install, or to regenerate scripts if they go missing.

## Script inventory

All scripts live in `~/.claude/scripts/` and must be executable.
They share a common library `_lib.sh` with functions for parsing CLAUDE.md files.

| Script | Used by | Purpose |
|---|---|---|
| `_lib.sh` | All scripts | Shared: `get_gh_org`, `parse_repo_names`, `parse_repo_names_with_type` |
| `org-ci-health.sh` | `/org-ci-health` | CI run status + action versions per repo |
| `org-deps.sh` | `/org-deps` | DESCRIPTION/Project.toml deps + release tags |
| `org-releases.sh` | `/org-releases` | Release tags, commit counts, NEWS status |
| `org-maintenance.sh` | `/org-maintenance` | Non-main worktrees + open PRs with status |
| `daily-summary.sh` | `/daily-summary` | Bot PRs, issues, commits, blockers for a date |
| `weekly-plan.sh` | `/weekly-plan` | Daily logs, open PRs, assigned issues, commit counts |
| `org-standards.sh` | `/org-standards` | Config files, CI workflows, badges per repo |
| `org-issues-scan.sh` | `/org-issues-tidy`, `/org-issues-do` | Open issues with metadata and bot comment status |
| `bot-tasks.sh` | `/bot-tasks` | Unprocessed bot notifications with comment context |
| `cron-bot-tasks.sh` | cron | Poll for new bot requests, trigger if needed |

## Procedure

1. Create `~/.claude/scripts/` if it does not exist
2. For each script in the inventory above, check if it exists and is executable
3. If missing, read the current version from this skill's embedded templates below and write it
4. If it exists, check its version comment against the template — offer to update if outdated
5. Run `chmod +x` on all scripts
6. Smoke test each script (run with `--help` or a quick org name) and report pass/fail

## Templates

The templates for each script are maintained in the script files themselves.
This skill should read the existing scripts at `~/.claude/scripts/*.sh` to verify they exist.

If any are missing, regenerate them by:
1. Reading `~/.claude/scripts/_lib.sh` for the shared library pattern
2. Following the same pattern (source `_lib.sh`, use `get_gh_org`, `parse_repo_names`)
3. Outputting JSON to stdout
4. Using `set -euo pipefail` and bash 3.2 compatibility (no `mapfile`, no associative arrays)

### Bash 3.2 compatibility rules

macOS ships bash 3.2. All scripts MUST:
- Not use `mapfile` — use `while IFS= read -r` loops instead
- Not use associative arrays (`declare -A`) — use parallel indexed arrays
- Not use `|&` — use `2>&1 |`
- Not use `[[ =~ ]]` with literal `)` in patterns — use `sed` for extraction
- Use `$(( ))` for arithmetic, not `let`
- Process substitution `< <()` works on macOS bash 3.2 but test if unsure

## Validation

After generating, run each script and verify:
- Exit code 0
- Output is valid JSON (pipe through `jq .`)
- No bash syntax errors

Report results as a table:

| Script | Exists | Executable | Valid JSON | Status |
|---|---|---|---|---|

## Auto-Exit When Standalone

**IMPORTANT**: If this command is being run as a standalone request, automatically exit after completing all phases successfully.
