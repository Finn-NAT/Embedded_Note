# How to Create a New Branch with Specific Commits

This guide explains how to create a new branch and cherry-pick specific commits from an existing branch.

## Scenario

You want to create a new branch and take 2 commits from an existing branch (e.g., `source-branch`).

**Example commit history:**
```
source-branch  97e5787  Add feature X
source-branch  ca92f50  Update configuration
source-branch  20e6fc8  Fix bug in module Y
source-branch  13f6b55  Refactor code structure
source-branch  6948c99  Add unit tests
source-branch  02907e7  Update dependencies
source-branch  c1af16e  Initial implementation
```

**Note:** "The first 2 commits" can be ambiguous - it might mean the 2 earliest commits or the 2 latest commits. This guide covers both scenarios.

---

## Step 1: Create a New Branch

Start by creating a new branch from your base branch (e.g., `master`):

```bash
# Fetch all remote changes
git fetch --all

# Switch to the base branch (e.g., master)
git checkout master

# Pull latest changes
git pull

# Create and switch to a new branch
git checkout -b <new_branch_name>
```

**Example:**
```bash
git checkout -b feature/my-new-branch
```

---

## Step 2: Cherry-Pick Commits

There are two approaches depending on which commits you want:

### Option A: Taking the 2 Earliest Commits

If you want the 2 oldest commits from `source-branch`:

#### 1. View commit history in reverse order (oldest first):
```bash
git log --reverse --oneline source-branch
```

#### 2. Identify the 2 earliest commits

Based on the example history above, the 2 earliest commits would likely be:
- `c1af16e`
- `02907e7`

#### 3. Cherry-pick those commits:
```bash
git cherry-pick c1af16e 02907e7
```

Or pick them one by one:
```bash
git cherry-pick c1af16e
git cherry-pick 02907e7
```

---

### Option B: Taking the 2 Latest Commits

If you want the 2 most recent commits from `source-branch`:

#### Cherry-pick the last 2 commits using range syntax:
```bash
git cherry-pick source-branch~2..source-branch
```

**Explanation:**
- `source-branch~2` = 2 commits before the tip
- `..source-branch` = up to and including the tip
- This picks the last 2 commits

**Alternative method:**
```bash
# View the last 2 commit hashes
git log --oneline -2 source-branch

# Then cherry-pick them explicitly
git cherry-pick <second_last_commit> <last_commit>
```

---

## Handling Merge Conflicts

If you encounter conflicts during cherry-picking:

### 1. Resolve the conflict:
- Open the conflicted files
- Fix the conflicts manually
- Save the files

### 2. Continue the cherry-pick:
```bash
# Stage the resolved files
git add -A

# Continue the cherry-pick process
git cherry-pick --continue
```

### 3. Or abort if needed:
```bash
# Abort the entire cherry-pick operation
git cherry-pick --abort
```

---

## Complete Example Workflow

### Example: Taking 2 Earliest Commits

```bash
# Step 1: Prepare the new branch
git fetch --all
git checkout master
git pull
git checkout -b feature/new-feature

# Step 2: View history to identify commits
git log --reverse --oneline source-branch

# Step 3: Cherry-pick the earliest commits
git cherry-pick c1af16e 02907e7

# Step 4: Push the new branch
git push -u origin feature/new-feature
```

### Example: Taking 2 Latest Commits

```bash
# Step 1: Prepare the new branch
git fetch --all
git checkout master
git pull
git checkout -b feature/latest-changes

# Step 2: Cherry-pick the last 2 commits
git cherry-pick source-branch~2..source-branch

# Step 3: Push the new branch
git push -u origin feature/latest-changes
```

---

## Tips

1. **Verify commits before cherry-picking:**
   ```bash
   git log --oneline source-branch
   ```

2. **Check what will be cherry-picked:**
   ```bash
   git log --oneline source-branch~2..source-branch
   ```

3. **Cherry-pick a single commit:**
   ```bash
   git cherry-pick <commit-hash>
   ```

4. **Cherry-pick multiple specific commits (not consecutive):**
   ```bash
   git cherry-pick <commit-1> <commit-3> <commit-5>
   ```

5. **Cherry-pick without committing (for review):**
   ```bash
   git cherry-pick -n <commit-hash>
   # Review changes, then:
   git commit
   ```

---

## Summary

- Use `git cherry-pick <hash1> <hash2>` for specific commits
- Use `git cherry-pick <branch>~N..<branch>` for the last N commits
- Use `git log --reverse --oneline` to see earliest commits first
- Always handle conflicts with `git add` + `git cherry-pick --continue`
- Use `git cherry-pick --abort` to cancel the operation