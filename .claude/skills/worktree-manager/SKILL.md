---
name: worktree-manager
description: Task management using isolated git worktrees for parallel development. This skill should be used when starting new tasks, returning to in-progress work, switching between tasks, working on multiple tasks simultaneously, before executing implementation plans, or when workspace isolation is needed. Creates worktrees with safety verification.
---

# Worktree Manager - Task Assignment & Parallel Development

Automate task setup using git worktrees for parallel development workflows. Creates isolated environments for assigned tasks without disrupting current work.

## Instructions

When invoked with $ARGUMENTS:

### Phase 0: Check for Existing Worktree

```bash
git worktree list
```

**If worktree exists:**

- Navigate to existing worktree: `cd <path>`
- Show current state: `git status` and `git log -1 --oneline`
- Report status and skip to Phase 6

**If worktree does not exist:**

- Proceed to Phase 1

### Phase 1: Pre-Flight Checks

1. **Check current state:** `git status`, `git worktree list`, `pwd`
2. **Parse task from $ARGUMENTS:**
   - Extract ticket number (PROJ-123, JIRA-456, etc.)
   - Determine task type (feature, fix, refactor)
   - Extract brief description
3. **Verify:** main is up-to-date, target location available

### Phase 2: Retrieve Ticket Information

If $ARGUMENTS contains a ticket ID, attempt to retrieve from Atlassian MCP:

1. Use `getAccessibleAtlassianResources` to get cloudId
2. Use `getJiraIssue` to retrieve ticket details
3. Extract issue type → branch type (Bug→fix/, Story/Task→feature/)
4. Extract summary → branch description

Fall back to manual parsing from Phase 1 if unavailable.

### Phase 3: Create Worktree with Branch

Determine worktree path: `../{repo-name}-{ticket-number}-{short-description}`

- Short description should be 1-2 words from ticket summary or task title

Determine branch name using git-branching skill conventions:

- **If Phase 2 retrieved ticket data:** Use issue type and summary from Jira
- **Otherwise:** Use task type and description from manual parsing (Phase 1)

**Create worktree with new branch in one command:**

```bash
# Fetch latest (if remote exists)
git fetch origin main 2>/dev/null || true

# Create worktree with new branch from main
# Use -b flag to avoid "branch already used" error
git worktree add -b <branch-name> <path> main

# Navigate and verify
cd <path>
git worktree list
git branch --show-current
```

### Phase 4: Verify Branch Setup

Confirm branch was created using git-branching skill conventions.

### Phase 5: Environment Setup

Check for README.md in the worktree and follow any setup instructions. The worktree is a clean copy - perform project-specific environment setup according to documentation.

### Phase 6: Confirm Ready

**For new worktree:**

```
✅ Worktree created: <path>
✅ Branch: <branch-name>
✅ Ready for implementation

Next steps:
  1. Implement task in isolated workspace
  2. Commit iteratively
```

**For existing worktree:**

```
✅ Found existing worktree: <path>
✅ Branch: <branch-name>
✅ Last commit: [message]
✅ Ready to continue
```

## Example

```bash
# With Atlassian MCP: /worktree-manager PROJ-123
# Retrieves ticket (Bug: "Fix login validation") from Jira
git worktree add -b fix/PROJ-123-login-validation ../myapp-PROJ-123-login main
cd ../myapp-PROJ-123-login

# Manual workflow: /worktree-manager feature PROJ-456 add user dashboard
git worktree add -b feature/PROJ-456-add-user-dashboard ../myapp-PROJ-456-dashboard main
cd ../myapp-PROJ-456-dashboard

# Result:
# /Users/dev/myapp/                    (main) - coordination hub
# /Users/dev/myapp-PROJ-123-login/     - isolated workspace
```

## Error Handling

**Directory exists:** Check with Phase 0, navigate to existing or use different name

**Branch checked out elsewhere:** Use `git worktree list` to find location

**Uncommitted changes in main:** No problem - worktrees isolate changes, WIP stays intact
