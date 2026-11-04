# Git Workflow - Solo Developer

## Main Principle

I work alone, but I still use feature branches for major features and fixes.

Workflow:

Feature/Fix
→ Branch
→ Commit
→ Push
→ Pull Request
→ Merge
→ Delete Branch

---

# Check Current Branch

```bash
git branch --show-current
```

or

```bash
git status
```

---

# List All Local Branches

```bash
git branch
```

Example:

```text
* main
  feature/memory
  feature/rag
```

* = current branch

---

# Create New Branch

```bash
git checkout -b feature/memory
```

Meaning:

- Create branch
- Switch to branch

Equivalent to:

```bash
git branch feature/memory
git checkout feature/memory
```

---

# Switch To Existing Branch

```bash
git checkout feature/memory
```

Use this only if the branch already exists.

---

# Daily Work

After making changes:

```bash
git add .
git commit -m "Add memory entity"
```

Push:

```bash
git push origin feature/memory
```

---

# Open Pull Request

GitHub:

```text
feature/memory
      ↓
     main
```

Create Pull Request and review changes.

---

# Merge Pull Request

After merging to main:

```bash
git checkout main
git pull
```

---

# Delete Branch After Merge

Local:

```bash
git branch -d feature/memory
```

Remote:

```bash
git push origin --delete feature/memory
```

---

# Important Rule

After a branch is merged:

✅ Delete it

❌ Do not continue developing on it

Create a new branch instead.

Example:

```text
feature/memory
    ↓ Merge
    ↓ Delete

feature/rag
```

---

# Bug Fix Workflow

If a bug is discovered after merge:

DO NOT reuse old branch.

Create a new one:

```bash
git checkout main
git pull

git checkout -b fix/memory-duplicate-storage
```

Examples:

```text
fix/token-counter
fix/memory-retrieval
fix/task-status
```

---

# Branch Naming

Features:

```text
feature/memory-system
feature/rag
feature/task-manager
feature/github-integration
```

Bug Fixes:

```text
fix/memory-retrieval
fix/token-counter
fix/chat-history
```

---

# Commit Message Examples

Good:

```text
Add memory repository
Implement memory extraction
Fix duplicate memory storage
Refactor task service
Add GitHub integration
```

Avoid:

```text
test
final
update
aaa
123
work
```

---

# Recommended Workflow For My AI Agent

```text
main
    │
    ├── feature/memory-system
    ├── feature/rag
    ├── feature/task-manager
    ├── feature/github-integration
    └── fix/*
```

Each branch:

Create
→ Work
→ Commit
→ Push
→ PR
→ Merge
→ Delete

---

# Simple Rule To Remember

New Feature = New Branch

Merged Branch = Delete Branch

New Bug Fix = New Branch

Never keep coding on an old merged branch.

## Very Quick Short Summary:

# Git Workflow (Solo Developer)

## New Feature

```bash
git checkout main
git pull

git checkout -b feature/feature-name
```

Work:

```bash
git add .
git commit -m "Description"
git push origin feature/feature-name
```

Create PR → Merge

Delete branch:

```bash
git branch -d feature/feature-name
git push origin --delete feature/feature-name
```

---

## Bug Fix After Merge

```bash
git checkout main
git pull

git checkout -b fix/bug-name
```

Don't reuse old merged branches.

---

## Useful Commands

Current branch:

```bash
git branch --show-current
```

All local branches:

```bash
git branch
```

Switch to existing branch:

```bash
git checkout branch-name
```

Create new branch:

```bash
git checkout -b branch-name
```

---

## Rules

✅ New Feature = New Branch

✅ New Bug Fix = New Branch

✅ Merge → Delete Branch

✅ Clear commit messages

❌ Don't continue coding on a merged branch

❌ Don't use commits like:
```
test
update
final
aaa
```

---

## Simple Flow

```text
Feature
  ↓
Branch
  ↓
Commit
  ↓
Push
  ↓
Pull Request
  ↓
Merge
  ↓
Delete Branch
```
