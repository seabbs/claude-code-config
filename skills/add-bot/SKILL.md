---
name: add-bot
description: Add the bot account as a collaborator with push permissions to a repository
---

Add the bot account as a collaborator with push permissions to a repository.

Read `bot_account`, `owner_account` from the org's or project's CLAUDE.md `## Automation config` table.

## Arguments
- Just repo name (assumes owner from config): `my-repo`
- Full owner/repo: `owner/my-repo`
- No argument: uses current repository from `git remote get-url origin`

## Process
1. Save current gh user: `gh api user --jq '.login'`
2. Switch to owner account: `gh auth switch --user <owner_account>`
3. Parse repository argument
4. Add collaborator: `gh api repos/{owner}/{repo}/collaborators/<bot_account> -X PUT -f permission=push`
5. Verify access: `gh api repos/{owner}/{repo}/collaborators/<bot_account>`
6. Switch back to original user: `gh auth switch --user {original_user}`
