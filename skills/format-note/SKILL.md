---
name: format-note
description: Format an Obsidian note with tags and link it from today's daily note
---

Format an existing Obsidian note with appropriate tags and link it from today's daily note.

## Process
1. Read the existing note from the unpublished folder
2. Analyse content to determine appropriate tags (topic, type, domain)
3. Update/add frontmatter with tags, creation date, modified date, status
4. Locate today's daily note in the periodic notes folder
5. Add a link to the formatted note: `- [[Note Title]] - Brief description #type`

## Vault Location
Default: `/Users/lshsa2/obsidian-vault`

## Expected Structure
```
obsidian/
├── periodic/
│   └── YYYY-MM-DD.md (daily notes)
├── unpublished/
│   └── your-notes-here.md
└── published/
```

## Auto-Exit When Standalone
**IMPORTANT**: If this command is being run as a standalone request, automatically exit after completing all phases successfully.
