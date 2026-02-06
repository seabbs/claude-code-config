# tmux Workflow Guide

Expert guidance for tmux-based development workflow with neovim, happy (Claude Code wrapper), and R/Julia REPLs.

## Workflow Overview

The user's setup uses:
- One tmux **session per project** (e.g., `epinowcast`, `EpiAware`)
- Multiple **windows per feature/worktree** within each session
- Standard **pane layout** per window: nvim (left) | happy (top-right) | REPL (bottom-right)

## Key Commands

### Navigation

| Keys | Action |
|------|--------|
| `Ctrl-b w` | Visual window picker (tree view of all windows) |
| `Ctrl-b s` | Session picker |
| `Alt-1` to `Alt-9` | Jump to window by number (no prefix needed) |
| `Alt-h/j/k/l` | Switch panes (no prefix needed) |
| `Ctrl-b h/j/k/l` | Switch panes (with prefix) |
| `Ctrl-b n` / `Ctrl-b p` | Next/previous window |
| `Ctrl-b (` / `)` | Previous/next session |

### Pane Management

| Keys | Action |
|------|--------|
| `Ctrl-b \|` | Split vertically |
| `Ctrl-b -` | Split horizontally |
| `Ctrl-b z` | Zoom/unzoom current pane |
| `Ctrl-b b` | Break pane to new window |
| `Ctrl-b D` | Create standard dev layout (nvim \| happy / repl) |
| `Ctrl-b H/J/K/L` | Resize panes |

### Copy Mode

| Keys | Action |
|------|--------|
| `Ctrl-b [` | Enter copy mode |
| `v` | Start selection (in copy mode) |
| `y` or `Enter` | Copy to clipboard |
| `q` | Exit copy mode |

## Shell Functions

### `proj <project-name>`
Start or attach to a project session.

```bash
proj epinowcast    # Starts session with nvim, happy, R
proj EpiAware      # Starts session with nvim, happy, Julia
```

### `feat <branch-name> [base-branch]`
Create a new feature worktree and window within the current session.

```bash
feat add-new-model        # Creates worktree from main
feat bugfix-123 develop   # Creates worktree from develop
```

### `feat-done <branch-name>`
Clean up a feature worktree and close its window.

```bash
feat-done add-new-model   # Removes worktree and closes window
```

### `feat-list`
List all worktrees in the current repository.

## tmuxinator

Projects are started with tmuxinator using a generic template:

```bash
tmuxinator start project <name>
```

The template auto-detects:
- R projects (DESCRIPTION file) -> starts R/radian
- Julia projects (Project.toml) -> starts `julia --project=.`

## Neovim Integration

### Auto-reload
Files changed by Claude Code are automatically reloaded in nvim. A notification appears when this happens.

### Sending Code to REPL

The REPL pane receives code from nvim via iron.nvim:

| Keys | Action |
|------|--------|
| `<leader>sc` | Send current line or visual selection |
| `<leader>sf` | Send entire file |
| `<leader>su` | Send from file start to cursor |
| `<leader>sp` | Send paragraph |
| `<leader>sm` | Send function |
| `<leader>sb` | Send code block (Quarto/Rmd) |
| `<leader>sa` | Send all code blocks (Quarto/Rmd) |

### REPL Management

| Keys | Action |
|------|--------|
| `<leader>rs` | Toggle REPL (auto-detects language) |
| `<leader>rr` | Restart REPL |
| `<leader>rf` | Focus REPL pane |
| `<leader>rh` | Hide REPL |
| `<leader>rj` | Start Julia REPL |
| `<leader>rR` | Start R REPL |
| `<leader>rp` | Start Python REPL |

## Typical Workflow

1. **Start project**: `proj myproject`
2. **Work on main branch** in the first window
3. **Start feature**: `feat new-feature` (creates worktree + window)
4. **Switch between features**: `Ctrl-b w` or `Alt-<number>`
5. **In each window**:
   - Edit in nvim (left pane)
   - Use happy for Claude Code assistance (top-right)
   - Test in REPL (bottom-right)
   - Send code from nvim to REPL with `<leader>sc`
6. **Complete feature**: commit, push, PR
7. **Clean up**: `feat-done new-feature`

## Session Naming

Each happy pane is started with `--resume '<project>-<branch>'`:
- Main window: `happy --resume 'epinowcast-main'`
- Feature window: `happy --resume 'epinowcast-new-feature'`

This allows resuming conversations per feature. Pane titles show `happy:<branch>` in the border.

## Troubleshooting

### Files not reloading in nvim
- Ensure `focus-events` is enabled in tmux (already set in config)
- Run `:checktime` manually in nvim

### REPL not receiving code
- Check iron.nvim is targeting the correct pane
- Restart REPL with `<leader>rr`

### Wrong REPL type started
- Use language-specific commands: `<leader>rj` for Julia, `<leader>rR` for R

### Session already exists error
- Use `tmux attach -t <session>` or `proj <name>` which handles this
