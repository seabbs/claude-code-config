---
name: repo-summary
description: Analyse a GitHub repository combining activity metrics with code development insights
---

Provide an analysis of a GitHub repository combining activity metrics with code development insights.

## Usage
`/repo-summary [<owner/repo>]` - If no repo specified, uses current git repository.

## Output Sections

### Repository Overview
- Project description, technology stack, age, size, star/fork counts

### Development Activity (Git Analysis)
- Commit frequency, code churn, file change hotspots, author contributions, branch patterns

### GitHub Activity
- Issue and PR metrics, community engagement, review turnaround, release patterns

### Code Evolution
- Lines of code trends, refactoring periods, test coverage trends, documentation updates

### Development Insights
- Velocity trends, bus factor, time to merge, release frequency, active development areas

## Implementation
1. Run repo-activity to gather GitHub metrics
2. Use git log analysis for development patterns
3. Analyse commit messages for semantic patterns
4. Identify code hotspots using git statistics
5. Generate unified markdown report to `<repo>-summary-<date>.md`

## Auto-Exit When Standalone
**IMPORTANT**: If this command is being run as a standalone request, automatically exit after completing all phases successfully.
