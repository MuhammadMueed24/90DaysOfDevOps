# Day 23 — Git Branching, Pushing, and Collaboration

## Task 1: Understanding Branches

### 1. What is a branch in Git?

A branch in Git is a **lightweight, movable pointer to a specific commit**. When you create a branch, Git simply creates a new pointer — it doesn't copy your entire codebase. Every repository starts with a default branch (usually called `main` or `master`). As you make new commits on a branch, that pointer automatically moves forward to the latest commit.

Think of a branch as a parallel timeline for your code. You can diverge from the main line of development, do work in isolation, and later merge those changes back.

```
main:      A --- B --- C
                        \
feature-1:               D --- E
```

### 2. Why do we use branches instead of committing everything to `main`?

Committing everything directly to `main` is risky and messy. Branches solve several real problems:

| Problem without branches | Solution with branches |
|---|---|
| Half-finished features break the main codebase | Features live in isolation until they're ready |
| Multiple developers step on each other's changes | Each developer works on their own branch |
| Hard to experiment without fear of breaking things | Create a throwaway branch, delete it if it fails |
| Rolling back a bad change is painful | Merge only when the branch is stable and reviewed |
| Code review is impossible on a shared `main` | Open a Pull Request from a feature branch |

In professional workflows (like **GitHub Flow** or **Git Flow**), `main` is treated as sacred — it should always be deployable. All new work happens on branches and is merged only after review and testing.

### 3. What is `HEAD` in Git?

`HEAD` is a **special pointer that tells Git where you currently are** — i.e., which branch (or commit) you have checked out right now.

- Normally, `HEAD` points to a **branch name** (e.g., `HEAD → main`), and that branch points to the latest commit. This is called an **attached HEAD**.
- If you check out a specific commit directly (e.g., `git checkout abc1234`), `HEAD` points to that commit instead of a branch. This is called a **detached HEAD** state — changes you make won't belong to any branch unless you create one.

```bash
# See where HEAD is pointing
cat .git/HEAD
# Output: ref: refs/heads/main

# In detached HEAD state:
# Output: abc1234567890abcdef...
```

You can always see HEAD's current position with `git log --oneline` — it will show `(HEAD -> branch-name)` next to the current commit.

### 4. What happens to your files when you switch branches?

When you switch branches, Git:

1. **Looks at the commit the target branch points to**
2. **Updates your working directory** to match the state of files at that commit
3. **Moves HEAD** to point to the new branch

In practice:
- Files that exist on the new branch but not your current branch → **appear**
- Files that exist on your current branch but not the new branch → **disappear**
- Files that differ between branches → **their contents change**

Your files are **not deleted** — Git stores everything in its object database. Switching branches just changes which snapshot your working directory reflects.

> ⚠️ **Important**: Git will refuse to switch branches if you have **uncommitted changes** that would be overwritten. You must either commit, stash (`git stash`), or discard those changes first.

---

## Task 2: Branching Commands — Hands-On

### 1. List all branches

```bash
git branch
# Lists local branches; current branch marked with *

git branch -a
# Lists ALL branches (local + remote)

git branch -v
# Lists branches with the latest commit on each
```

**Output example:**
```
* main
  feature-1
  feature-2
```

### 2. Create a new branch called `feature-1`

```bash
git branch feature-1
```

This creates the branch at your current commit but **does not switch to it**. HEAD still points to `main`.

### 3. Switch to `feature-1`

```bash
git checkout feature-1
# OR (modern syntax)
git switch feature-1
```

Output: `Switched to branch 'feature-1'`

### 4. Create a new branch and switch to it in one command — `feature-2`

```bash
# Traditional way
git checkout -b feature-2

# Modern way (Git 2.23+)
git switch -c feature-2
```

The `-b` (checkout) or `-c` (switch) flag means "create". This is equivalent to `git branch feature-2` + `git switch feature-2` in one step.

### 5. `git switch` vs `git checkout` — What's the difference?

| | `git checkout` | `git switch` |
|---|---|---|
| **Introduced** | Git 1.x (very old) | Git 2.23 (2019) |
| **Purpose** | Multi-purpose (branches AND files) | Branches only |
| **Restore files** | `git checkout -- file.txt` | Use `git restore` instead |
| **Create branch** | `git checkout -b branch` | `git switch -c branch` |
| **Ambiguity** | Can be confusing (same command, different purposes) | Clear, single-purpose |
| **Safety** | Fewer safeguards | Warns more clearly about detached HEAD, etc. |

**Bottom line**: `git switch` is the modern, focused replacement for branch-switching. Use `git switch` for branches and `git restore` for file-level operations. `git checkout` still works and is widely seen in older docs/tutorials.

### 6. Make a commit on `feature-1` that doesn't exist on `main`

```bash
# Switch to feature-1
git switch feature-1

# Make a change
echo "This is a new feature being developed" >> feature-notes.txt

# Stage and commit
git add feature-notes.txt
git commit -m "feat: add feature notes file (feature-1 only)"
```

Now `feature-1` has a commit that `main` does not.

### 7. Switch back to `main` — verify the commit is NOT there

```bash
git switch main

# Verify the file from feature-1 is gone
ls
# feature-notes.txt is NOT visible here

# Verify commit history doesn't include the feature-1 commit
git log --oneline
# Only shows commits up to where feature-1 was branched from
```

This confirms branches are truly isolated. The `feature-notes.txt` file physically disappears from your working directory when you're on `main`.

### 8. Delete a branch you no longer need

```bash
# Safe delete — only works if branch is fully merged
git branch -d feature-2

# Force delete — works even if unmerged (use with caution!)
git branch -D feature-2

# Delete a remote branch
git push origin --delete feature-2
```

> Use `-d` (lowercase) by default. Git will warn you if the branch has unmerged work. Only use `-D` when you're sure you want to discard that work.

---

## Task 3: Push to GitHub

### Steps performed:

```bash
# 1. Create new repo on GitHub (empty, no README)
#    → Done via GitHub UI at github.com/new

# 2. Connect local repo to GitHub remote
git remote add origin https://github.com/YOUR_USERNAME/devops-git-practice.git

# Verify the remote was added
git remote -v

# 3. Push main branch
git push -u origin main
# -u sets the upstream tracking, so future `git push` works without specifying remote/branch

# 4. Push feature-1 branch
git push -u origin feature-1

# 5. Verify on GitHub → both branches visible in the branch dropdown ✅
```

### What is the difference between `origin` and `upstream`?

| Term | Meaning |
|---|---|
| **`origin`** | The default name Git gives to the remote you cloned from — or the remote you added first. For most people, this is **your own fork or repository** on GitHub. |
| **`upstream`** | A conventional name for the **original source repository** that your fork was created from. It's not set automatically — you add it manually. |

**Example scenario — open source contribution:**

```
Original repo:   github.com/kubernetes/kubernetes    ← this is upstream
Your fork:       github.com/yourname/kubernetes      ← this is origin
Your local:      /home/you/kubernetes                ← local machine
```

```bash
# After forking and cloning:
git remote -v
# origin    https://github.com/yourname/kubernetes.git (fetch)
# origin    https://github.com/yourname/kubernetes.git (push)

# Add upstream manually:
git remote add upstream https://github.com/kubernetes/kubernetes.git

git remote -v
# origin    https://github.com/yourname/kubernetes.git (fetch)
# origin    https://github.com/yourname/kubernetes.git (push)
# upstream  https://github.com/kubernetes/kubernetes.git (fetch)
# upstream  https://github.com/kubernetes/kubernetes.git (push)
```

**Rule of thumb**: `origin` = where you push YOUR changes. `upstream` = where you pull the LATEST official changes from.

---

## Task 4: Pull from GitHub

### Steps performed:

```bash
# 1. Edited README.md directly on GitHub using the pencil ✏️ icon
#    Added a line: "Updated directly on GitHub"
#    Committed via GitHub UI

# 2. Pull the change to local repo
git pull origin main
# OR simply:
git pull
# (works because -u tracking was set earlier)
```

### What is the difference between `git fetch` and `git pull`?

This is one of the most important distinctions in Git collaboration:

| | `git fetch` | `git pull` |
|---|---|---|
| **What it does** | Downloads changes from remote but does NOT touch your working directory or local branches | Downloads changes AND immediately merges them into your current branch |
| **Safety** | Safe — you can inspect before integrating | Can cause merge conflicts right away |
| **When to use** | When you want to see what changed before merging | When you trust the remote and want to update quickly |
| **Equivalent to** | `git fetch` alone | `git fetch` + `git merge` |
| **Remote tracking** | Updates `origin/main` etc. | Updates `origin/main` AND your local `main` |

```bash
# FETCH: Download and inspect first
git fetch origin
git log origin/main --oneline   # See what's new
git diff main origin/main       # Compare before merging
git merge origin/main           # Merge when ready

# PULL: Fetch + merge in one step
git pull origin main
```

**Best practice for teams**: Use `git fetch` + review + `git merge` (or `git rebase`) to stay in control. Use `git pull` for quick solo updates when you trust the remote state.

---

## Task 5: Clone vs Fork

### Steps performed:

```bash
# 1. Clone a public repository
git clone https://github.com/torvalds/linux.git
# (or any public repo — a smaller one is faster!)
git clone https://github.com/sindresorhus/awesome.git

# 2. Fork on GitHub → click "Fork" button on the repo page
#    Then clone YOUR fork:
git clone https://github.com/YOUR_USERNAME/awesome.git
cd awesome
git remote -v
# origin → your fork (you can push here)
```

### What is the difference between clone and fork?

| | **Clone** | **Fork** |
|---|---|---|
| **What it is** | A **Git operation** — copies a repo to your local machine | A **GitHub concept** — copies a repo to YOUR GitHub account |
| **Where it lives** | Your local machine | GitHub (remote) |
| **Who can push** | Only if you have write access to the source | You always have write access to your fork |
| **Relation to original** | No connection tracked by default | GitHub tracks the relationship (shows "forked from") |
| **Used for** | Working on repos you own or have access to | Contributing to projects you don't own |

### When would you clone vs fork?

**Clone when:**
- It's your own repository
- You're a collaborator with push access
- You just want a local copy to read/study (no intent to contribute)
- You're working within a company/team repo

**Fork when:**
- You want to contribute to an open-source project you don't own
- You want your own independent copy to experiment with freely
- You plan to submit a Pull Request to the original repo
- You want to customize a project for your own use without affecting the original

### After forking, how do you keep your fork in sync with the original repo?

```bash
# Step 1: Add the original repo as "upstream"
git remote add upstream https://github.com/ORIGINAL_OWNER/REPO.git

# Step 2: Fetch the latest changes from upstream
git fetch upstream

# Step 3: Switch to your main branch
git switch main

# Step 4: Merge upstream's main into your local main
git merge upstream/main

# Step 5: Push the updated main to your fork on GitHub
git push origin main
```

Or in one go with rebase (cleaner history):
```bash
git fetch upstream
git rebase upstream/main
git push origin main
```

**Tip**: Do this regularly (weekly, or before starting any new feature branch) to minimize merge conflicts later.

---

## Summary: Key Concepts from Day 23

```
Branch     → A pointer to a commit; enables parallel development
HEAD       → Where you are right now in the commit history
origin     → Your remote (your fork/repo on GitHub)
upstream   → The original source repo you forked from
git fetch  → Download changes, don't apply them yet
git pull   → Download + merge changes immediately
clone      → Local copy of a repo (Git operation)
fork       → Your own copy of a repo on GitHub (GitHub concept)
```
