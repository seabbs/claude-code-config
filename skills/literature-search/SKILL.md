---
name: literature-search
description: Search local bibliography files and Paperpile for relevant papers
argument-hint: [topic]
---

Search for relevant papers on: $ARGUMENTS

## Process

1. Extract key search terms, concepts, and themes from the query
2. Search .bib files in the current project first
3. Search across ~/code for .bib files (references.bib, library.bib, papers.bib, etc.)
4. For each relevant entry extract: citation key, title, authors, year, journal, abstract, DOI, exact file and line number
5. If deeper analysis needed, create summaries of top papers

## Output

- Categorised list of relevant papers with brief summaries
- Exact file locations (`/path/to/file.bib:line_number`)
- Key themes and methodologies identified
- Gaps in local collection
