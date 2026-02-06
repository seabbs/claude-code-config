---
name: create-note
description: Import a markdown file into Obsidian library and format it as a note
---

Import a markdown file into your Obsidian library and format it as a note.

## Usage
`/create-note <source_markdown_path> [<target_name>]`

## Process
1. Read the source markdown file
2. Determine target filename (uses source name if not specified)
3. Create a copy in the Obsidian unpublished folder
4. Run format-note to add tags, set up frontmatter, and link from daily note

## File Naming
- Spaces replaced with hyphens, special characters removed, lowercase
- Date prefix added if duplicate exists

## Vault Location
Default: `/Users/lshsa2/Library/CloudStorage/GoogleDrive-s.e.abbott12@gmail.com/My Drive/cloud/apps/obsidian`
