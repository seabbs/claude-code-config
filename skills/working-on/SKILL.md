---
name: working-on
description: Create or update a CLAUDE.md project inventory for the current directory. Works at any level of the code directory hierarchy.
argument-hint: [update]
---

# Project Inventory

Create or update a `CLAUDE.md` file in the current working directory that describes the projects found there.
This gives future Claude Code sessions immediate context about what lives in this directory.

## Behaviour by directory level

### Code root (e.g. `~/code/`)

List the org folders with a one-line description of each.
Point to each org's `CLAUDE.md` for details.
Include any top-level config (common tasks, cross-project work patterns).

### Org folder (e.g. `~/code/epinowcast/`)

List all repos in this org with type and description.
Detect type from files present:
- `DESCRIPTION` → R package
- `Project.toml` → Julia package
- `.tex` or `.qmd` manuscript files → paper
- `submission/` or `application/` dir → grant
- `_quarto.yml` without package files → website or slides
- Otherwise infer from contents

Get descriptions from `DESCRIPTION` Title field, `Project.toml` name, or first meaningful line of `README.md`.

### Individual repo

If run inside a repo, defer to `/init` behaviour (document build commands, architecture, etc.).
Do not duplicate what this skill does.

## When the CLAUDE.md already exists

If the argument is "update" or the file already exists:
1. Scan the directory for current subdirectories
2. Compare with the existing inventory
3. Report what changed (new repos, removed repos, description updates)
4. Update only the inventory section, preserving any other content the user has added

## When no CLAUDE.md exists

Ask the user:
1. What is this directory? (org folder, project collection, something else)
2. Are the subdirectories organised by any pattern?
3. What types of projects are here?

Then scan and generate the inventory.

## Output format

Use the same structure as existing org-level CLAUDE.md files.
Keep it concise: org name, one-line summary, then a table of repos.

## Auto-Exit When Standalone

**IMPORTANT**: If this command is being run as a standalone request, automatically exit after completing all phases successfully.
