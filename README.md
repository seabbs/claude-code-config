# Claude Code Configuration

Personal Claude Code configuration for Sam Abbott (@seabbs).

## Contents

- **CLAUDE.md**: Global preferences and coding standards
- **agents/**: Specialised agents for different tasks
- **commands/**: Custom slash commands for common workflows
- **skills/**: Domain knowledge and best practices

## Skills Reference

Skills provide domain knowledge that Claude can apply when working on specific types of tasks.
They are activated contextually based on the work being done.

### Development Skills

| Skill | Description | When to Use |
|-------|-------------|-------------|
| **r-development** | R package development with devtools, testthat, roxygen2 | Working on R packages, writing R tests, documenting R functions |
| **julia-development** | Julia package development with SciML standards, Distributions.jl patterns | Developing Julia packages, writing Julia tests, performance optimisation |
| **stan-development** | Stan probabilistic programming with cmdstanr integration | Writing Stan models, integrating Stan with R, debugging inference |
| **taskfile-automation** | Task (taskfile.dev) automation for development workflows | Projects with Taskfile.yml, discovering available tasks |
| **project-organization** | Project scaffolding and structure patterns | Creating new projects, reviewing structure, adding config files |

### Research Skills

| Skill | Description | When to Use |
|-------|-------------|-------------|
| **academic-writing-standards** | Academic writing for peer-reviewed papers | Reviewing manuscripts, editing papers, scientific writing |
| **grant-application-setup** | Setting up grant application repositories | Creating new grant projects, organising proposals |
| **grant-compliance-checking** | Grant compliance, deliverables, funder requirements | Checking work against grant specs, preparing reports |
| **analyzing-research-papers** | Systematic paper analysis and summarisation | Reading papers, extracting key findings, literature review |

## Agents Reference

Agents are specialised subprocesses for complex multi-step tasks.

### Code Quality

| Agent | Purpose |
|-------|---------|
| **code-linting-specialist** | Check code quality, run linting, fix issues |
| **code-review-expert** | Comprehensive code review with context analysis |
| **code-refactoring-specialist** | Refactor code to meet standards |
| **test-debug-fixer** | Debug failing tests, fix test suites |

### Git/GitHub

| Agent | Purpose |
|-------|---------|
| **commit-engineer** | Create well-structured commits |
| **pr-engineer** | Create pull requests with detailed descriptions |
| **pr-review-analyzer** | Review PRs for quality and compliance |
| **github-issue-analyzer** | Analyse issues, find related code |
| **worktree-manager** | Manage git worktrees |

### Research/Analysis

| Agent | Purpose |
|-------|---------|
| **statistical-implementation-specialist** | Implement statistical models from specs |
| **statistical-analysis-reviewer** | Review statistical code against plans |
| **literature-finder** | Find relevant papers in bibliography |
| **academic-revision-specialist** | Revise text based on reviewer feedback |

### Planning/Navigation

| Agent | Purpose |
|-------|---------|
| **codebase-navigator** | Search codebase for relevant areas |
| **feature-planning-orchestrator** | Create implementation plans |
| **context-mining-researcher** | Extract context from project files |
| **requirements-compliance-checker** | Verify work meets requirements |

## Commands Reference

Slash commands for streamlined workflows.

### Development

| Command | Description |
|---------|-------------|
| `/test [scope]` | Run tests, fix failures, commit |
| `/commit` | Create well-structured commit |
| `/lint [scope]` | Lint, format, optionally commit |
| `/review [scope]` | Comprehensive code review |
| `/docs` | Check documentation |
| `/pr <issue>` | End-to-end PR workflow from issue |

### Research

| Command | Description |
|---------|-------------|
| `/paper-summary` | Summarise research paper |
| `/literature-search <topic>` | Search for relevant papers |

### GitHub

| Command | Description |
|---------|-------------|
| `/github-dashboard` | GitHub activity summary |
| `/issue-summary <num>` | Summarise issue conversation |
| `/issue-reply <num>` | Post helpful reply to issue |
| `/repo-summary` | Repository overview |
| `/repo-activity` | Recent repository activity |

### Other

| Command | Description |
|---------|-------------|
| `/uk-news` | UK news summary from BBC/Guardian |
| `/preprint-search <area>` | Search for new preprints |
| `/update-deps` | Review and update dependencies |
| `/improve-coverage <target>` | Improve test coverage |

## Usage

This configuration is automatically loaded by Claude Code when placed in `~/.claude`.

### Invoking Skills

Skills are activated automatically based on context, but can be invoked explicitly:

```
/r-development
/julia-development
/project-organization
```

### Invoking Commands

```
/commit
/test pr
/review commit
/pr 123
```

### Using Agents

Agents are typically invoked through commands or by Claude when appropriate.
They can also be requested directly:

```
"Use the test-debug-fixer agent to fix these failing tests"
"Launch the codebase-navigator to find authentication code"
```
