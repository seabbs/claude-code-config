I need you to create a pull request based on issue #$ARGUMENTS. Follow these steps systematically:

## Phase 0: Setup and Worktree Decision
1. **Ask the user:** "Do you want to use a git worktree for this issue? (y/n)"
   - If yes: Launch worktree-manager agent to create worktree for issue-$ARGUMENTS
   - If no: Create a new branch in the current repository: `git checkout -b issue-$ARGUMENTS-[brief-description]`

## Phase 1: Issue Analysis and Planning
2. **First, analyze the issue (REQUIRED FIRST STEP):**
   - github-issue-analyzer: Analyze issue #$ARGUMENTS for comprehensive understanding and requirements

3. **Then launch supporting agents in parallel using the issue context:**
   - codebase-navigator: Identify relevant code areas for the issue (using github-issue-analyzer output)
   - context-mining-researcher: Extract project-specific coding standards and patterns from CLAUDE.md

4. **Launch feature-planning-orchestrator** with the gathered context to create a comprehensive implementation plan

5. **Create planning document** `pr_plan_issue_$ARGUMENTS.md` with the orchestrator's output including:
   - Issue summary and acceptance criteria
   - Implementation approach with specific steps
   - Files to modify/create
   - Testing strategy
   - Potential edge cases

6. **Iterate on the plan** - Review the plan and if refinements are needed:
   - **Re-launch feature-planning-orchestrator** with the current plan and feedback
   - Update `pr_plan_issue_$ARGUMENTS.md` with the refined plan
   - Repeat until the plan comprehensively addresses all requirements

## Phase 2: Implementation
7. **Execute implementation based on the plan:**
   - Primary agents for implementation:
     - **statistical-implementation-specialist**: For most code implementation
     - **test-debug-fixer**: For writing and fixing tests
   - Alternative agents for specific needs:
     - academic-revision-specialist: For scientific writing/documentation
     - general-purpose agent: Only for non-statistical utility code
   - For independent tasks: Launch agents in parallel (e.g., implementing different modules)
   - For dependent tasks: Launch agents sequentially
     - Implement feature → Write/run tests → Fix based on test results
     - Each iteration may require code-refactoring-specialist based on feedback
8. **Monitor progress and adapt agent deployment:**
   - Use parallel execution when tasks don't depend on each other
   - Use sequential execution when output from one agent informs the next

## Phase 3: Quality Assurance
9. **Launch agents in parallel for comprehensive review:**
   - code-linting-specialist: Check uncommitted changes for style and quality issues
   - documentation-updater: Update documentation to match code changes
   - code-review-expert: Perform thorough code review
   - statistical-analysis-reviewer: If statistical code was implemented
   - test-debug-fixer: Ensure all tests pass
   - requirements-compliance-checker: Verify implementation matches original issue requirements
   - academic-writing-reviewer: If academic documentation was modified
   - grant-spec-reviewer: If working on grant-funded project deliverables

## Phase 4: Final Verification and PR Creation
10. Fix any issues identified by the review agents
11. **Self-review with pr-review-analyzer:** Review the completed work as if it were someone else's PR
12. Verify all acceptance criteria from the plan are met
13. **Clean up planning documents:** `rm pr_plan_issue_$ARGUMENTS.md`
14. **Launch commit-pr-engineer agent** to:
    - Create well-structured commits for all changes
    - Push the branch
    - Create the pull request with title "Fix #$ARGUMENTS: [Clear description]"
    - Include comprehensive description of changes, testing, and issue resolution

Throughout this process, the agents will handle the detailed work while you coordinate their efforts and ensure all acceptance criteria are met.