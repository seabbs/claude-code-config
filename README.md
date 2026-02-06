# Claude Code Configuration

Personal Claude Code configuration for Sam Abbott (@seabbs).

## Architecture

This configuration uses a **skills-first** approach to minimise context window usage.

- **Skills** (`skills/`): Only load a one-line `description` field into the system prompt. Full content loads on invocation. This is where most functionality lives.
- **Commands** (`commands/`): Short (<20 line) slash commands for quick tasks. Work the same as skills but without supporting files.
- **CLAUDE.md**: Global preferences, coding standards, and proactive skill dispatch rules.

Previously, agents (`agents/`) were used. Both agents and skills load their `description` field into the system prompt. The context savings come from writing concise descriptions and from the fact that agent entries also include tool lists and examples, which added up. With concise one-line descriptions, 33 skills use ~500 tokens vs ~4-6k tokens for 19 agents. Skills also offer features agents lack: supporting files, argument substitution, invocation control, and auto-loading based on context.

## Contents

```
~/.claude/
  CLAUDE.md              # Global instructions
  commands/              # 9 short slash commands
  skills/                # 33 skills (one-liner in system prompt)
```

## Skills (33)

### Development

| Skill | Description |
|-------|-------------|
| `/commit` | Create a well-structured commit with conventional format |
| `/lint` | Lint, format, and optionally commit changes |
| `/test` | Run tests, fix failures iteratively, then commit |
| `/review` | Code review with linting and priority-ranked findings |
| `/docs` | Check and fix documentation gaps |
| `/pr` | End-to-end PR workflow from a GitHub issue |
| `/improve-coverage` | Identify test coverage gaps and generate tests |
| `/update-deps` | Review and update dependencies with incremental testing |

### Language-specific

| Skill | Description |
|-------|-------------|
| `/r-development` | R package development (devtools, testthat, roxygen2) |
| `/julia-development` | Julia package development (SciML standards) |
| `/stan-development` | Stan probabilistic programming (cmdstanr integration) |
| `/taskfile-automation` | Task (taskfile.dev) automation for project workflows |
| `/project-organization` | Project scaffolding and structure patterns |

### Research

| Skill | Description |
|-------|-------------|
| `/academic-writing-standards` | Academic writing standards for peer-reviewed papers |
| `/analyzing-research-papers` | Systematic paper analysis and summarisation |
| `/paper-summary` | Summarise an academic paper from URL, DOI, or file |
| `/literature-search` | Search bibliography files and Paperpile |
| `/preprint-search` | Search arXiv, medRxiv, bioRxiv for new preprints |
| `/grant-application-setup` | Set up grant application repositories |
| `/grant-compliance-checking` | Check work against grant requirements |
| `/humanizer` | Remove signs of AI-generated writing from text |

### GitHub

| Skill | Description |
|-------|-------------|
| `/github-dashboard` | Notifications, PR status, issue triage |
| `/issue-summary` | Summarise a GitHub issue conversation |
| `/issue-reply` | Post a helpful reply to a GitHub issue |
| `/repo-summary` | Repository overview with activity metrics |
| `/repo-activity` | Recent repository activity analysis |
| `/scan-issues` | Find issues suitable for automated resolution |
| `/add-bot` | Add seabbs-bot as repo collaborator |

### Notes

| Skill | Description |
|-------|-------------|
| `/create-note` | Import markdown into Obsidian |
| `/format-note` | Format an Obsidian note with tags and daily link |
| `/minutes` | Create meeting minutes from a transcript |

### Other

| Skill | Description |
|-------|-------------|
| `/uk-news` | UK news summary from BBC and Guardian |
| `/tmux-workflow` | Tmux session management |

## Commands (9)

Short commands that stay under 20 lines. These are lightweight enough that their system prompt footprint is minimal.

| Command | Description |
|---------|-------------|
| `/academic-revise` | Revise academic text from reviewer comments |
| `/check-requirements` | Verify work against original requirements |
| `/refactor` | Refactor code to meet project standards |
| `/stats-implement` | Implement statistical model from a specification |
| `/stats-review` | Review statistical analysis against the plan |
| `/worktree` | Manage git worktrees |
| `/performance-review` | Analyse agent/command usage in the conversation |
| `/mark-pr-agent` | Add draft notice to agent-generated PRs |
| `/read-up` | Read documentation entries noted in context |

## Why skills over agents?

Both skills and agents load their `description` field into the system prompt. The context savings are a result of writing concise descriptions, not an inherent mechanism difference. You could write concise agent descriptions or verbose skill descriptions.

In practice, agent entries include tool lists and example blocks alongside the description, making them heavier even with similar description text. Skills appear as a compact list of one-liners.

| Aspect | Agents | Skills |
|--------|--------|--------|
| System prompt format | Description + tools + examples per entry | One-liner list |
| Typical tokens per entry | ~200-300 | ~15-20 |
| Auto-load based on context | No (requires explicit delegation) | Yes |
| Supporting files | No | Yes (templates, scripts, examples) |
| Argument substitution | No | Yes (`$ARGUMENTS`, `$0`, `$1`) |
| Invocation control | Always available | `disable-model-invocation`, `user-invocable` |
| Dynamic context | No | Yes (`!`command`` syntax) |

The main win is the auto-loading and compact listing, plus better features for defining reusable workflows.

## Usage

Skills and commands are invoked with `/name`:

```
/commit
/test pr
/review commit
/pr 123
/literature-search Bayesian nowcasting
/stats-implement hierarchical model from section 3.2
```

CLAUDE.md includes proactive dispatch rules so Claude will suggest relevant skills during workflows (e.g. `/lint` and `/test` before committing, `/review` when asked to check code).
