---
name: daily-summary
description: Summarise today's Claude Code activity across all repos into a dated log file
---

# Daily Activity Summary

Generate a summary of what the bot account did today and write it to a dated log file.
Read the bot account name from the org's CLAUDE.md `## Automation config` table (`bot_account`).

## Output location

Write to `~/code/claude-log/YYYY-MM-DD.md` (create the directory if needed).

## Helper script

A pre-built script at `~/.claude/scripts/daily-summary.sh` collects all bot activity for a given date in one pass.
Run it first to avoid spending tokens on individual `gh` and `git` calls.

```bash
~/.claude/scripts/daily-summary.sh [YYYY-MM-DD] > /tmp/daily-summary.json
```

Defaults to today if no date is given.

If the script is missing or not executable, flag this to the user and stop.

The script outputs a JSON object with:
- `date`: the date being summarised
- `prs`: array of PRs created/updated by the bot account (repo, number, title, state, url)
- `issues_commented`: array of issues where bot commented (title, url, state)
- `commits`: array of local commits by the bot account (repo, hash, message)
- `blockers`: array of open PRs with failing CI or changes requested

## Format the output

Using the JSON data, write a markdown file to `~/code/claude-log/YYYY-MM-DD.md`:

```markdown
# Claude Code activity: YYYY-MM-DD

## PRs

| Repo | PR | Status |
|---|---|---|
| org/repo | [#N Title](url) | open/merged/closed |

## Issues commented

| Repo | Issue | Action |
|---|---|---|
| org/repo | [#N Title](url) | commented/labelled |

## Commits

### org/repo
- abc1234 commit message
- def5678 commit message

## Blockers

- [ ] org/repo#N: reason it's blocked

## Summary

[2-3 sentence overview of what was accomplished and what needs attention]
```

## Auto-Exit When Standalone

**IMPORTANT**: If this command is being run as a standalone request, automatically exit after completing all phases successfully.
