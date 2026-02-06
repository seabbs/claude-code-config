---
name: commit
description: Create a well-structured commit with conventional format and proper git identity
---

Create a well-structured commit.

## Arguments

- `--as-me`: Commit as seabbs (Sam Abbott) instead of seabbs-bot
- `--bot-only`: Commit as seabbs-bot without co-author

## Git Identity

| Mode | Git Author | Co-author |
|------|------------|-----------|
| (default) | seabbs-bot (signin@samabbott.co.uk) | Sam Abbott (contact@samabbott.co.uk) |
| `--as-me` | Sam Abbott (contact@samabbott.co.uk) | none |
| `--bot-only` | seabbs-bot (signin@samabbott.co.uk) | none |

## Process

1. **Set git identity** based on arguments:
   - Default or `--bot-only`: `git config user.name "seabbs-bot"` and `git config user.email "signin@samabbott.co.uk"`
   - `--as-me`: `git config user.name "Sam Abbott"` and `git config user.email "contact@samabbott.co.uk"`

2. **Review and commit changes:**
   - Review staged and unstaged changes
   - Determine commit type (feat, fix, docs, style, refactor, perf, test, build, ci, chore)
   - Stage relevant files (exclude temp/generated: *.html, *.pdf, build/, *.log, .DS_Store)
   - Write concise commit message in conventional format: `type(scope): description`
   - Never push directly to main

3. **Add co-author trailer** (default mode only):
   - Append `Co-authored-by: Sam Abbott <contact@samabbott.co.uk>` to commit message

## Auto-Exit When Standalone
**IMPORTANT**: If this command is being run as a standalone request, automatically exit after completing all phases successfully.
