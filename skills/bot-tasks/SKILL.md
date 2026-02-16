---
name: bot-tasks
description: Check bot notifications for tasks requested by the repo owner, then execute them using a team
---

# Process Bot Task Requests

Check the bot account's GitHub notifications for issues and PRs where the owner has asked the bot to do something, then execute those tasks.

Read the bot and owner accounts from the org's CLAUDE.md `## Automation config` table (`bot_account` and `owner_account`).

## Helper script

A pre-built script at `~/.claude/scripts/bot-tasks.sh` collects unprocessed task requests in one pass.
Run it first to avoid spending tokens on notification parsing.

```bash
~/.claude/scripts/bot-tasks.sh > /tmp/bot-tasks.json
```

If the script is missing or not executable, run `/setup-scripts` to regenerate it.

The script outputs a JSON object with:
- `notifications`: raw notification list
- `tasks`: array of unprocessed task requests with `thread_id`, `repo`, `type`, `number`, `title`, `url`, `comment_id`, `request` (the comment body)

Tasks are identified by:
- Direct mentions of the bot account in comments by the owner
- Comments by the owner on bot-authored issues/PRs containing task language
- Filtered to exclude comments already reacted to with 👀 (eyes emoji)

## Emoji deduplication

When a task request is processed:
1. React to the comment with 👀 (eyes) to mark it as seen
2. This prevents re-processing on future runs

```bash
gh api -X POST "repos/{repo}/issues/comments/{comment_id}/reactions" -f content=eyes
```

## Cron integration

A cron trigger script at `~/.claude/scripts/cron-bot-tasks.sh` polls every 5 minutes.
It only triggers Claude Code if there are new unprocessed requests.
Set up via: `crontab -e` and add:
```
*/5 * * * * ~/.claude/scripts/cron-bot-tasks.sh >> ~/.claude/logs/cron-bot-tasks.log 2>&1
```

## Phase 1: Parse script output

Read the `tasks` array from the JSON.
Skip if empty.

## Phase 2: Triage

Present a numbered list of tasks found:
- Issue/PR link
- What the owner asked for (quote the relevant comment)
- Repo
- Estimated complexity (simple/medium/complex)

Ask the user to confirm which tasks to execute, or proceed with all if invoked with `--all`.

## Phase 3: Execute as a team

Spawn a team with one teammate per task (up to 5 concurrent).
Each teammate:

1. Clones or navigates to the repo
2. Creates a worktree branch if code changes are needed
3. Implements the requested change using /pr patterns
4. Runs tests and linting
5. Commits, pushes, and creates a PR (or pushes to existing PR branch)
6. Replies to the original issue/comment confirming what was done
7. Reacts to the original comment with 👀 to mark as processed

For non-code tasks (labelling, closing, commenting):
- Execute directly without a worktree

## Phase 4: Clean up

1. Mark processed notifications as read: `gh api -X PATCH notifications/threads/{id}`
2. Report results to the team lead

## Phase 5: Summary

Report:
- Tasks completed (with PR/comment links)
- Tasks skipped (with reason)
- Tasks that failed (with error details)

## Auto-Exit When Standalone

**IMPORTANT**: If this command is being run as a standalone request, automatically exit after completing all phases successfully.
