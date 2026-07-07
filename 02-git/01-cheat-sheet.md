# Git Cheatsheet

Quick reference for the Git commands I use most often.

---

## Repository

Repositories are where Git stores your project's history.
You'll usually create one for a new project or clone an existing one.

| Command | Hint |
|---------|------|
| `git init` | Initialize a new repository. |
| `git clone <url>` | Download an existing repository. |
| `git remote -v` | Show connected remote repositories. |
| `git remote add origin <url>` | Connect a local repository to GitHub. |
| `git remote set-url origin <url>` | Change the remote repository URL. |

---

## Status & Information

These commands help you understand the current state of your repository before making changes.

| Command | Hint |
|---------|------|
| `git status` | Show modified, staged, and untracked files. |
| `git log --oneline --graph --all` | View a compact commit history. |
| `git diff` | Show unstaged changes. |
| `git diff --staged` | Show staged changes waiting to be committed. |
| `git show <commit>` | Show details of a specific commit. |

---

## Staging

The staging area lets you choose exactly which changes will be included in your next commit.
This helps keep commits clean and focused.

| Command | Hint |
|---------|------|
| `git add <file>` | Stage one file. |
| `git add .` | Stage all current changes. |
| `git restore --staged <file>` | Remove a file from the staging area. |

---

## Commit

A commit is a snapshot of your staged changes.
Try to make each commit represent one logical change.

| Command | Hint |
|---------|------|
| `git commit -m "message"` | Create a new commit. |
| `git commit --amend` | Edit the last commit. |
| `git commit --amend --no-edit` | Add more changes to the last commit without changing its message. |

---

## Branches

Branches let you work on new features or fixes without affecting the main branch.

| Command | Hint |
|---------|------|
| `git branch` | List branches. |
| `git branch <name>` | Create a branch. |
| `git switch <branch>` | Switch branches. |
| `git switch -c <branch>` | Create and switch. |
| `git branch -d <branch>` | Delete a merged branch. |

---

## Stash

Stash temporarily saves your uncommitted work without creating a commit.
Useful when you need to switch branches or pull changes quickly.

| Command | Hint |
|---------|------|
| `git stash` | Save uncommitted changes. |
| `git stash pop` | Restore and remove the latest stash. |
| `git stash list` | Show all saved stashes. |
| `git stash drop` | Delete a stash. |

...
