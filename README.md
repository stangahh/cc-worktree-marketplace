# cc-worktree-marketplace

Task management using git worktrees for parallel development with automated workspace isolation.

## What's Included

- **worktree-manager** [Skill] - Create isolated worktrees for tasks with automatic Jira ticket integration
- **git-branching** [Skill] - Standardized branch naming conventions
- **Atlassian MCP** [MCP] - Automatic ticket retrieval from Jira

## Installation

```bash
# Add the marketplace
claude plugin marketplace add stangahh/cc-worktree-marketplace

# Install the plugin
claude plugin install worktree-manager@cc-worktree-marketplace
```

**Requirements:** Git >=2.5.0; for worktree support
