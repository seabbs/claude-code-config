---
name: pr
description: Create a pull request based on a GitHub issue with analysis, implementation, QA, and PR creation
---

Create a pull request based on issue #$ARGUMENTS. Follow these steps systematically:

## Phase 0: Setup and Worktree Decision
1. **Ask the user:** "Do you want to use a git worktree for this issue? (y/n)"
   - If yes: Create worktree `./worktree-issue-$ARGUMENTS-[desc]` with branch `issue-$ARGUMENTS-[desc]`
   - If no: Create a new branch: `git checkout -b issue-$ARGUMENTS-[brief-description]`

## Phase 1: Issue Analysis and Complexity Assessment
2. **Analyse the issue:**
   - Launch a haiku agent to: fetch issue #$ARGUMENTS with `gh issue view`, summarise it, identify related code areas using Glob/Grep, and return structured findings

3. **Assess complexity** (SIMPLE / MEDIUM / COMPLEX):
   - SIMPLE: Single file, typos, docs-only, trivial test updates
   - MEDIUM: 2-5 files, bug fixes, standard test updates
   - COMPLEX: New features, architectural changes, statistical implementations

4. **Execute workflow based on tier:**

### FOR SIMPLE ISSUES:
- Quick codebase search for relevant code
- Implement changes directly
- Basic linting check only
- Skip to Phase 4

### FOR MEDIUM ISSUES:
- Launch parallel haiku agents to: (a) search codebase for relevant files, (b) search CLAUDE.md and project docs for relevant guidelines
- Create lightweight plan in memory
- Continue to Phase 2

### FOR COMPLEX ISSUES:
- Write issue analysis to `issue_analysis_$ARGUMENTS.md`
- Launch parallel haiku agents for codebase search and context mining
- Launch a sonnet agent to: create a detailed implementation plan with file modifications, code snippets, and testing strategy
- Write plan to `pr_plan_issue_$ARGUMENTS.md`
- Iterate on plan if needed
- Continue to Phase 2

## Phase 2: Implementation
5. Implement the changes:
   - For statistical/research code: use /stats-implement patterns
   - Write and run tests
   - Fix based on test results
   - For independent tasks, use parallel subagents

## Phase 3: Quality Assurance (complexity-based)

### SIMPLE: Basic lint check and run tests if applicable
### MEDIUM: Run lint, docs check, code review, tests, and requirements check
### COMPLEX: All of MEDIUM plus statistical review (if applicable) and academic writing review (if applicable)

## Phase 4: Final Verification and PR Creation
6. Fix any issues from quality checks
7. For MEDIUM/COMPLEX: Self-review the completed work
8. Verify all acceptance criteria are met
9. For COMPLEX: Clean up planning documents
10. Create well-structured commits, push the branch, and create PR:
    - Title: "Fix #$ARGUMENTS: [Clear description]"
    - Prepend: `**This is entirely from an agent so do not review until I have pinged for review as I will do a first pass**`
    - Include summary, changes, testing, and related issues
