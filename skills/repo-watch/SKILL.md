---
name: repo-watch
description: Detect active repos by the owner not cloned locally and request confirmation before acting
argument-hint: [org-name]
---

# Repo Watch

## Accounts

Read `bot_account`, `owner_account` from the org's CLAUDE.md `## Automation config` table.

## Purpose

Detect GitHub repos where the owner has active work (issues, PRs, reviews) that are not cloned locally.
For each discovered repo, open an issue asking for confirmation before cloning.

## Helper script

```bash
~/.claude/scripts/repo-watch.sh [org-name ...] > /tmp/repo-watch.json
```

If the script is missing, run `/setup-scripts` to regenerate it.

The script outputs a JSON object with `missing_repos` array, each containing:
- `org`, `gh_org`, `repo`, `last_push`, `open_issues_assigned`

## Phase 1: Run script and review

Parse the JSON output.
Skip repos that are already listed in the org's CLAUDE.md under `## Excluded repos`.

## Phase 2: Confirm with owner

For each missing repo, **do not clone automatically**.
Instead, create a GitHub issue on the repo asking for confirmation:

```bash
gh issue create -R {gh_org}/{repo} \
  --title "Add to local dev environment?" \
  --body "The bot detected that the owner has active work on this repo but it's not in the local dev setup.

Should I clone this repo and add it to the org automation?

- React with 👍 to confirm
- React with 👎 to exclude (will be added to the exclusion list)

This was opened by a bot. Please ping the owner for any questions."
```

## Phase 3: Check for responses

On subsequent runs, check for reactions on previously opened issues:
- 👍 — clone the repo and add to CLAUDE.md
- 👎 — add to `## Excluded repos` in CLAUDE.md and close the issue

```bash
# Use the owner_account from Automation config
gh api "repos/{gh_org}/{repo}/issues/{number}/reactions" \
  --jq '[.[] | select(.user.login == "<owner_account>")] | .[0].content'
```

## Phase 4: Clone confirmed repos

For repos confirmed with 👍:
1. Clone: `gh repo clone {gh_org}/{repo} $CODE_DIR/{org}/{repo}`
2. Add to the org's CLAUDE.md local repos table
3. Close the issue with a comment

## Phase 5: Update exclusions

For repos rejected with 👎:
1. Add to `## Excluded repos` section in the org's CLAUDE.md
2. Close the issue with a comment

## Auto-Exit When Standalone

**IMPORTANT**: If this command is being run as a standalone request, automatically exit after completing all phases successfully.
