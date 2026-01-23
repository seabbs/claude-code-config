Create a well-structured commit using the specialized commit-engineer agent.

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

2. **Launch commit-engineer agent** to:
   - Review staged and unstaged changes
   - Create appropriate commit messages following conventional format
   - Handle staging of files (excluding temporary/generated files)
   - Push changes to the appropriate branch
   - Never push directly to main

3. **Add co-author trailer** (default mode only):
   - Append `Co-authored-by: Sam Abbott <contact@samabbott.co.uk>` to commit message

The agent will:
- Analyze changes to determine commit type (feat, fix, docs, etc.)
- Write clear, descriptive commit messages
- Follow project conventions from CLAUDE.md
- Ensure clean git history

## Auto-Exit When Standalone
**IMPORTANT**: If this command is being run as a standalone request, automatically exit after completing all phases successfully.
