---
name: org-orchestrate
description: Set up and run full org automation including repo cloning, script generation, cron scheduling, and skill execution
argument-hint: [org-name|setup|cron|run]
---

# Org Orchestration

One skill to set up, schedule, and run the full suite of org automation.
Handles repo cloning, script generation, cron jobs, and coordinated skill execution.

## Subcommands

- `/org-orchestrate setup <org>` — full first-time setup for an org
- `/org-orchestrate cron` — install or update cron jobs
- `/org-orchestrate run <org>` — run all org skills in the right order
- `/org-orchestrate status` — check what's set up and what's missing
- No argument — interactive: check status and ask what to do

## Phase 1: Status check

### Scripts
Run `/setup-scripts` logic: verify all scripts in `~/.claude/scripts/` exist and are executable.
If any are missing, regenerate them.

### Repos
For each org in `~/code/`, read the CLAUDE.md and check:
1. Are all listed repos cloned locally?
2. Are there repos on GitHub not listed in CLAUDE.md?
3. Are remotes up to date?

```bash
# List GitHub repos for an org
gh repo list <org> --limit 100 --json name,description,isArchived
```

### Cron
Check current crontab for existing automation entries:
```bash
crontab -l 2>/dev/null
```

### Permissions
Ask the user:
- Which tool to use: `claude` or `happy`?
- Whether to use `--dangerously-skip-permissions` for unattended runs
- If not, which permission prompts to pre-approve

## Phase 2: Setup (first time)

### 2a: Scripts
Generate all helper scripts via `/setup-scripts`.

### 2b: Interactive repo selection

For each org, fetch the full list of GitHub repos and present them grouped by status:

```
=== epinowcast ===

Already cloned (9):
  [x] baselinenowcast, epidist, epinowcast, primarycensored, ...

Available to clone (3):
  [ ] actions — Reusable GitHub Actions
  [ ] baselinenowcast-us-paper — Evaluating baseline nowcasting
  [ ] coerceDT — Ingest and check user input

Excluded (from CLAUDE.md):
  [-] old-archive-repo — Excluded 2025-01-15

Include all available? [Y/n/select]
```

For the "select" option, let the user pick individual repos.

Persist decisions in the org's CLAUDE.md:
- Cloned repos go in `## Local repos`
- Explicitly excluded repos go in `## Excluded repos` with date

```markdown
## Excluded repos

| Repo | Reason | Date |
|---|---|---|
| old-archive-repo | Archived, no active work | 2025-01-15 |
| fork-of-something | Not maintained by seabbs | 2025-02-14 |
```

### 2c: Clone selected repos
For repos selected but not yet cloned:
```bash
gh repo clone <org>/<repo> ~/code/<org>/<repo>
```

### 2d: Set up Taskfile.yml
For repos without a Taskfile.yml, offer to copy the template from `~/.claude/templates/Taskfile.yml`.
Auto-detect the project type (R package, Julia package, Quarto doc) and set the variables.

### 2e: Cron jobs
Install cron entries.
Use `launchd` plist on macOS for restart robustness.

#### Recommended schedule

All times are overnight to avoid work clashes.

| Job | Schedule | Script/Command |
|---|---|---|
| Bot task poll | `*/5 * * * *` | `cron-bot-tasks.sh` |
| Nightly automation | `0 1 * * *` | `cron-schedule.sh` |
| Daily summary | `0 8 * * *` (8am) | `claude --print --prompt "/daily-summary"` |
| Weekly plan | `0 9 * * 1` (Mon 9am) | `claude --print --prompt "/weekly-plan"` |

The bot task poll runs every 5 minutes but only triggers Claude if there are new unprocessed requests (checked via emoji dedup).

#### macOS launchd (restart-robust)

Cron jobs on macOS do not survive reboots without `launchd`.
Create plist files in `~/Library/LaunchAgents/`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "...">
<plist version="1.0">
<dict>
  <key>Label</key>
  <string>com.seabbs.bot-tasks-poll</string>
  <key>ProgramArguments</key>
  <array>
    <string>/Users/lshsa2/.claude/scripts/cron-bot-tasks.sh</string>
  </array>
  <key>StartInterval</key>
  <integer>300</integer>
  <key>StandardOutPath</key>
  <string>/Users/lshsa2/.claude/logs/bot-tasks-poll.log</string>
  <key>StandardErrorPath</key>
  <string>/Users/lshsa2/.claude/logs/bot-tasks-poll.err</string>
  <key>RunAtLoad</key>
  <true/>
  <key>EnvironmentVariables</key>
  <dict>
    <key>PATH</key>
    <string>/usr/local/bin:/usr/bin:/bin:/opt/homebrew/bin</string>
    <key>HOME</key>
    <string>/Users/lshsa2</string>
    <key>CODE_DIR</key>
    <string>/Users/lshsa2/code</string>
  </dict>
</dict>
</plist>
```

Load with: `launchctl load ~/Library/LaunchAgents/com.seabbs.bot-tasks-poll.plist`
Unload with: `launchctl unload ~/Library/LaunchAgents/com.seabbs.bot-tasks-poll.plist`

Create similar plists for daily-summary and weekly-plan using `StartCalendarInterval`.

### 2f: Initialise org CLAUDE.md files
For orgs without a CLAUDE.md, run `/working-on` to create one.

## Phase 3: Run all org skills (ordered)

When running a full pass, execute in this order to avoid wasted work:

1. **Git pull all** — update main on all repos
   - `git-pull-all.sh`
2. **Maintenance first** — prune worktrees and unstick PRs so later skills don't trip on stale state
   - `/org-maintenance <org>`
3. **CI health** — fix CI so tests can pass for later PRs
   - `/org-ci-health <org>`
4. **Standards** — propagate config improvements (.github first, then repos)
   - `/org-standards <org>`
5. **Dependencies** — check cross-repo compat
   - `/org-deps <org> [other-orgs...]`
6. **Issues tidy** — add clarity to issues before trying to resolve them
   - `/org-issues-tidy <org>`
7. **Issues do** — resolve easy ones
   - `/org-issues-do <org>`
8. **Releases** — check what's ready to ship
   - `/org-releases <org>`
9. **Repo watch** — check for active repos not cloned locally
   - `/repo-watch <org>`
10. **Bot tasks** — process any pending requests
    - `/bot-tasks`
11. **Daily summary** — log what was done
    - `/daily-summary`

For multiple orgs, run steps 2-9 per org, then steps 10-11 once.

Use a team for parallel execution where skills are independent (e.g. different orgs).

## Phase 4: Verify

After setup or a run:
- Check all scripts exist and are executable
- Check cron/launchd entries are loaded
- Check log files for recent errors
- Report status table

| Component | Status | Last run | Notes |
|---|---|---|---|

## Auto-Exit When Standalone

**IMPORTANT**: If this command is being run as a standalone request, automatically exit after completing all phases successfully.
