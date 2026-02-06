---
argument-hint: [create|list|cleanup]
---
Manage git worktrees: $ARGUMENTS

Operations: create, list, cleanup

**Create:** `git worktree add ./worktree-issue-{N}-{desc} -b issue-{N}-{desc}`
- Always create within current repo as subdirectory
- Use absolute paths for all operations
- Validate creation and report absolute path

**List:** Show existing worktrees and their status

**Cleanup:** Remove worktree after PR merge
- Check for uncommitted changes first
- Remove worktree and clean up branch if needed
