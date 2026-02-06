---
name: add-bot
description: Add seabbs-bot as a collaborator with push permissions to a repository
---

Add seabbs-bot as a collaborator with push permissions to a repository.

## Arguments
- Just repo name (assumes seabbs owner): `my-repo`
- Full owner/repo: `seabbs/my-repo`
- No argument: uses current repository from `git remote get-url origin`

## Process
1. Save current gh user: `gh api user --jq '.login'`
2. Switch to seabbs account: `gh auth switch --user seabbs`
3. Parse repository argument
4. Add collaborator: `gh api repos/{owner}/{repo}/collaborators/seabbs-bot -X PUT -f permission=push`
5. Verify access: `gh api repos/{owner}/{repo}/collaborators/seabbs-bot`
6. Switch back to original user: `gh auth switch --user {original_user}`
