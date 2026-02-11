---
name: worktree-manager
description: Task management using isolated git worktrees for parallel development. This skill should be used when starting new tasks, returning to in-progress work, switching between tasks, working on multiple tasks simultaneously, before executing implementation plans, or when workspace isolation is needed. Creates worktrees with safety verification.
---

# Worktree Manager - Task Assignment & Parallel Development

Automate task setup using git worktrees for parallel development workflows. Creates isolated environments for assigned tasks without disrupting current work.

## Core Use Case

**Problem**: Assigned a new task while having uncommitted work in progress.

**Solution**: Create isolated worktree for the new task while preserving current workspace.

Traditional approach (context loss):
```
Stash → Switch branches → Work → Switch back → Unstash
```

Worktree approach (context preserved):
```
Create worktree → Work in parallel → No context switching
```

## Instructions

When invoked with $ARGUMENTS:

### Phase 0: Check for Existing Worktree

**FIRST: Check if worktree already exists**

```bash
git worktree list
```

**If worktree exists:**
- Navigate to existing worktree: `cd <path>`
- Show current state:
  ```bash
  git status
  git log -1 --oneline
  ```
- Report status and skip to Phase 5

**If worktree does not exist:**
- Proceed to Phase 1

### Phase 1: Pre-Flight Checks

Before creating worktree:

1. **Check current state**
   ```bash
   git status
   git worktree list
   pwd
   ```

2. **Parse task assignment from $ARGUMENTS**
   - Extract ticket number (PROJ-123, JIRA-456, etc.)
   - Extract task type (feature, fix, refactor)
   - Extract brief description

3. **Verify prerequisites**
   - Ensure main branch is up-to-date
   - Check target location is available
   - Confirm sufficient disk space

### Phase 2: Create Worktree

Determine worktree location based on ticket from Phase 1:
- Use pattern: `../{repo-name}-{ticket-number}`
- Example: `../myapp-PROJ-123`

```bash
# Fetch latest
git fetch origin main

# Create worktree from main (user specifies path)
git worktree add <path> main

# Navigate to worktree
cd <path>

# Verify
git worktree list
```

### Phase 3: Branch Setup

Invoke the git-branching skill with the task information from Phase 1:
- Pass the ticket number (e.g., PROJ-123)
- Pass the task type (feature, fix, refactor)
- Pass the brief description

The git-branching skill will:
- Determine the appropriate branch type
- Format the branch name following conventions
- Create and switch to the new branch

Expected result: Branch created following pattern `TYPE/TICKET-description`
- Example: `feature/PROJ-123-add-login`

### Phase 4: Environment Setup

**Initialize dependencies if needed:**

```bash
# Node projects
[ -f "package.json" ] && npm install

# Python projects
[ -f "requirements.txt" ] && pip install -r requirements.txt
```

**Verify environment:**
```bash
npm test  # or appropriate test command
```

### Phase 5: Confirm Ready

**For new worktree:**
```
✅ Worktree created: <path>
✅ Branch: TYPE/TICKET-description
✅ Dependencies installed
✅ Ready for implementation

Next steps:
  1. Implement task in isolated workspace
  2. Run tests: npm test
  3. Commit: git commit -m "feat: description"
  4. Push: git push origin BRANCH_NAME
```

**For existing worktree:**
```
✅ Found existing worktree: <path>
✅ Branch: TYPE/TICKET-description
✅ Last commit: [message]
✅ Uncommitted changes: [count]
✅ Ready to continue
```

## Parallel Development Example

**Scenario**: Working on three tasks simultaneously

```bash
# Task 1: PROJ-123 - Add login
git worktree add ../myapp-PROJ-123 main
cd ../myapp-PROJ-123
git checkout -b feature/PROJ-123-add-login
npm install

# Task 2: PROJ-456 - Fix timeout
cd /original/repo
git worktree add ../myapp-PROJ-456 main
cd ../myapp-PROJ-456
git checkout -b fix/PROJ-456-timeout
npm install

# Task 3: PROJ-789 - Refactor auth
cd /original/repo
git worktree add ../myapp-PROJ-789 main
cd ../myapp-PROJ-789
git checkout -b refactor/PROJ-789-auth
npm install
```

**Result:**
```
/Users/dev/myapp/              (main) - coordination hub
/Users/dev/myapp-PROJ-123/     (feature/PROJ-123-add-login)
/Users/dev/myapp-PROJ-456/     (fix/PROJ-456-timeout)
/Users/dev/myapp-PROJ-789/     (refactor/PROJ-789-auth)

Benefits:
✅ Work on all three simultaneously
✅ No branch switching or stashing
✅ Separate IDE windows with full context
✅ Independent test runs
✅ Isolated dependencies
```

## Integration with Implementation Plans

Before executing plan-implementer agent:

1. Create isolated worktree for implementation
2. Set up proper branch
3. Verify environment ready
4. Pass worktree path to plan-implementer

This ensures implementation proceeds without affecting the main workspace.

## Cleanup After Completion

When PR is merged:

```bash
# Return to main repo
cd /path/to/main/repo

# Update main
git checkout main
git pull origin main

# Remove worktree
git worktree remove ../myapp-PROJ-123

# Delete local branch (if remote deleted)
git branch -d feature/PROJ-123-add-login

# Verify cleanup
git worktree list
```

**Batch cleanup:**
```bash
# Remove multiple worktrees
git worktree remove ../myapp-PROJ-123
git worktree remove ../myapp-PROJ-456

# Prune stale references
git worktree prune
```

## Error Handling

**Directory exists:**
```
⚠️  Directory exists: ../myapp-PROJ-123

Options:
1. Navigate to existing: cd ../myapp-PROJ-123
2. Remove and recreate: git worktree remove ../myapp-PROJ-123 --force
3. Use different name: ../myapp-PROJ-123-v2
```

**Branch already checked out:**
```
⚠️  Branch already checked out elsewhere

Action: Check git worktree list to find location
```

**Uncommitted changes in main:**
```
✅ No problem - worktrees isolate changes
WIP stays intact, safe to create worktree for new task
```

## Best Practices

**Naming:** Use short names (`myapp-PROJ-123` not `myapp-worktree-for-feature-PROJ-123`)

**Organization:** Keep worktrees in parent directory (`../`) for easy navigation

**IDE Setup:** Open each worktree in separate IDE window to maintain context

**Cleanup:** Remove worktrees promptly after PR merge to free disk space

**Team Coordination:** Share branch names, not local worktree paths

## Success Criteria

✅ Worktree created in specified location
✅ Branch follows naming conventions
✅ Dependencies installed if applicable
✅ Safety checks passed
✅ Original workspace untouched
✅ Ready for parallel work immediately
