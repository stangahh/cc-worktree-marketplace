---
name: git-branching
description: Creates Git branches following naming conventions (feature/PROJ-123-desc, fix/PROJ-123-desc, refactor/PROJ-123-desc). This skill should be used when creating branches for assigned work, starting work on tickets/issues, or establishing branch naming patterns for feature, fix, refactor, hotfix, docs, or chore branches.
---

# Git Branch Creation

Automatically create properly named Git branches following project conventions.

## Branch Naming Conventions

### Format Patterns

- **Features**: `feature/PROJ-123-short-description`
  - Example: `feature/AUTH-101-user-login`
- **Bug Fixes**: `fix/PROJ-123-short-description`
  - Example: `fix/AUTH-105-session-timeout`
- **Refactoring**: `refactor/PROJ-123-short-description`
  - Example: `refactor/AUTH-108-simplify-api`
- **Hotfixes**: `hotfix/PROJ-123-short-description`
  - Example: `hotfix/PROD-999-critical-security`
- **Documentation**: `docs/short-description`
  - Example: `docs/setup-guide`
- **Chores**: `chore/short-description`
  - Example: `chore/update-deps`

### Naming Rules

- Use **lowercase only**
- Separate words with **hyphens** (kebab-case)
- Keep descriptions **concise but meaningful** (3-5 words max)
- Always include ticket number when applicable
- Limit total length to 50 characters

## Instructions

When invoked with $ARGUMENTS:

1. **Parse the Input**
   - Extract ticket number (PROJ-123, JIRA-456, etc.)
   - Identify work type (feature, fix, refactor, hotfix, docs, chore)
   - Extract description keywords

2. **Determine Branch Type**
   - If unclear, ask: "Is this a feature, fix, or refactor?"
   - Default to `feature/` if adding new functionality
   - Use `fix/` for bug fixes
   - Use `refactor/` for code improvements without behavior changes

3. **Create Branch Name**
   - Format: `TYPE/TICKET-description`
   - Convert description to lowercase with hyphens
   - Validate length (50 characters max)

4. **Verify Current State**

   ```bash
   git status
   git branch --show-current
   ```

5. **Create and Switch to Branch**

   ```bash
   git checkout -b TYPE/TICKET-description
   ```

6. **Confirm Creation**
   ```bash
   git branch --show-current
   ```

## Examples

**Input**: "PROJ-123 add user authentication"

```bash
git checkout -b feature/PROJ-123-user-authentication
```

**Input**: "fix bug JIRA-456 login redirect"

```bash
git checkout -b fix/JIRA-456-login-redirect
```

**Input**: "refactor AUTH-789 simplify token validation"

```bash
git checkout -b refactor/AUTH-789-simplify-token-validation
```

## Error Handling

- **Branch already exists**: Inform and offer to switch or create with suffix
- **Not on main/master**: Warn and ask whether to switch first
- **Uncommitted changes**: Suggest stashing or committing first
