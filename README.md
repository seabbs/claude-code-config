# Claude Code Configuration

Personal Claude Code configuration for Sam Abbott (@seabbs).

## Contents

- **CLAUDE.md**: Global preferences and coding standards
- **agents/**: Specialised agents for different tasks
- **commands/**: Custom slash commands for common workflows

## Agents

Custom agents for various development and research tasks including:
- Academic writing and revision
- Code review and refactoring  
- Statistical analysis and implementation
- Literature search and paper analysis
- Documentation updates
- GitHub workflow management

## Commands

Slash commands for streamlined workflows including:
- **`/pr 123`**: End-to-end pull request workflow from issue analysis to PR creation
- Paper summarisation and analysis
- Repository activity tracking
- Literature searches
- Meeting minutes formatting
- Issue summarisation

### PR Command Demo

```bash
/pr 96
```

This launches a structured workflow that:
1. **Analyses issue #96** to understand requirements and scope
2. **Plans implementation** using multiple specialist agents in parallel
3. **Implements the feature** with statistical and testing specialists
4. **Reviews thoroughly** using code quality, documentation, and compliance checkers
5. **Creates the PR** with structured commits and detailed description

The command coordinates 15+ specialist agents to ensure high-quality, well-tested implementations that meet all requirements.

## Usage

This configuration is automatically loaded by Claude Code when placed in the `~/.claude` directory.