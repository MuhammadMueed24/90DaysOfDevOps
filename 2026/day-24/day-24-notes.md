# Day 24 – Advanced Git: Merge, Rebase, Stash & Cherry Pick

> **90DaysOfDevOps | DevOpsKaJosh | TrainWithShubham**

---

## Task 1: Git Merge — Hands-On

### Commands Practiced

```bash
# Setup
git checkout main
git checkout -b feature-login

# Add commits to feature-login
echo "login function" >> login.py
git add . && git commit -m "Add login function"

echo "login validation" >> login.py
git add . && git commit -m "Add login validation"

# Switch back and merge
git checkout main
git merge feature-login
```

```bash
# For feature-signup with diverged history
git checkout -b feature-signup
echo "signup form" >> signup.py
git add . && git commit -m "Add signup form"

git checkout main
echo "README update" >> README.md
git add . && git commit -m "Update README on main"

git merge feature-signup
# → This triggers a merge commit
```

```bash
# Intentional conflict setup
git checkout -b feature-conflict
echo "Hello from feature" > shared.txt
git add . && git commit -m "Feature version of shared.txt"

git checkout main
echo "Hello from main" > shared.txt
git add . && git commit -m "Main version of shared.txt"

git merge feature-conflict
# → CONFLICT (content): Merge conflict in shared.txt
```

---

### Observations & Answers

#### What is a fast-forward merge?

A **fast-forward merge** happens when the branch being merged is a direct linear descendant of the current branch — meaning `main` hasn't moved at all since `feature-login` was created.

Git simply moves the `main` pointer forward to the tip of `feature-login`. **No new commit is created.** The history stays perfectly linear.

```
Before:
  main → A → B
                \
  feature-login → C → D

After (fast-forward):
  main → A → B → C → D   ← main pointer just moved forward
```

#### When does Git create a merge commit instead?

Git creates a **merge commit** when both branches have diverged — i.e., `main` has new commits since the feature branch was created. Git cannot simply move a pointer; it must reconcile two histories.

```
Before:
  main → A → B → E (new commit on main)
                \
  feature-signup → C → D

After (merge commit):
  main → A → B → E → M (merge commit with 2 parents: E and D)
                \       ↗
                 C → D
```

The merge commit `M` has **two parents** and represents the reconciliation point.

#### What is a merge conflict?

A **merge conflict** occurs when the **same line(s) in the same file** have been changed differently in both branches. Git doesn't know which version to keep, so it pauses the merge and asks you to resolve it manually.

The conflicted file looks like this:

```
<<<<<<< HEAD
Hello from main
=======
Hello from feature
>>>>>>> feature-conflict
```

**Resolution steps:**
1. Edit the file to keep the correct content
2. `git add shared.txt`
3. `git commit` to complete the merge

---

## Task 2: Git Rebase — Hands-On

### Commands Practiced

```bash
# Setup
git checkout main
git checkout -b feature-dashboard

echo "dashboard widget 1" >> dashboard.py
git add . && git commit -m "Add dashboard widget 1"

echo "dashboard widget 2" >> dashboard.py
git add . && git commit -m "Add dashboard widget 2"

echo "dashboard chart" >> dashboard.py
git add . && git commit -m "Add dashboard chart"

# Move main ahead
git checkout main
echo "auth middleware" >> middleware.py
git add . && git commit -m "Add auth middleware to main"

# Rebase feature-dashboard onto main
git checkout feature-dashboard
git rebase main

# Visualize
git log --oneline --graph --all
```

---

### Observations & Answers

#### What does rebase actually do to your commits?

Rebase **replays** your branch's commits on top of the new base. Each original commit is re-applied one by one onto the tip of `main`, creating **brand new commits** (new SHAs, same changes). The original commits are discarded.

```
Before rebase:
  main → A → B → E
                  \
  feature-dash → C' → D' → F'   ← new commits replayed on top of E

After rebase:
  main → A → B → E → C' → D' → F'
```

#### How is the history different from a merge?

| Merge | Rebase |
|---|---|
| Preserves true history | Creates a linear, "clean" history |
| Creates a merge commit | No merge commit |
| Shows when branches diverged | Hides the branching point |
| Safer for shared branches | Rewrites commit SHAs |

With merge, `git log --graph` shows a "rail track" shape. With rebase, it's a single straight line.

#### Why should you NEVER rebase commits that have been pushed and shared?

When you rebase, Git **rewrites commit history** — the same changes get new commit hashes. If someone else already pulled your original commits, their local history now diverges from yours. When they try to push or pull, Git sees two different versions of what should be the "same" commits, causing major confusion, duplicate commits, and potentially lost work.

> **Golden Rule:** Never rebase a public branch. Only rebase local, unpushed commits.

#### When would you use rebase vs merge?

| Use **Rebase** when... | Use **Merge** when... |
|---|---|
| Cleaning up local commits before a PR | Integrating a completed feature into `main` |
| Keeping feature branch up-to-date with `main` | You want to preserve the full branching history |
| You want a linear, readable history | Working on a shared/public branch |
| Preparing tidy commits for code review | Combining long-lived branches |

---

## Task 3: Squash Commit vs Merge Commit

### Commands Practiced

```bash
# feature-profile with many small commits
git checkout -b feature-profile

echo "fix1" >> profile.py && git add . && git commit -m "Fix typo in profile"
echo "fix2" >> profile.py && git add . && git commit -m "Fix formatting"
echo "fix3" >> profile.py && git add . && git commit -m "Remove unused import"
echo "feat1" >> profile.py && git add . && git commit -m "Add avatar upload"
echo "feat2" >> profile.py && git add . && git commit -m "Add bio field"

# Squash merge
git checkout main
git merge --squash feature-profile
git commit -m "Add profile feature (squashed)"

# Check log — only 1 new commit on main
git log --oneline -5
```

```bash
# feature-settings with regular merge
git checkout -b feature-settings
echo "settings1" >> settings.py && git add . && git commit -m "Add dark mode toggle"
echo "settings2" >> settings.py && git add . && git commit -m "Add notification settings"
echo "settings3" >> settings.py && git add . && git commit -m "Add privacy settings"

git checkout main
git merge feature-settings
# → Creates a merge commit, all 3 commits visible in history
```

---

### Observations & Answers

#### What does squash merging do?

`git merge --squash` takes **all commits from the feature branch** and compresses them into a single set of staged changes on your current branch. It does **not** automatically commit — you must run `git commit` manually to create the one final commit.

The feature branch's individual commits **never appear** in `main`'s history. Only one clean commit lands.

#### When would you use squash merge vs regular merge?

| Use **Squash** when... | Use **Regular Merge** when... |
|---|---|
| Feature branch has messy "WIP" commits | Each commit has meaningful, standalone value |
| You want `main` history to stay clean | You want full audit trail of the feature |
| Commits are "fix typo", "oops", "another fix" | Working with a team that values granular history |

#### What is the trade-off of squashing?

- ✅ **Pro:** Clean, readable `main` history — each entry is a meaningful feature
- ❌ **Con:** You **lose the granular commit history** of the feature branch forever (once the branch is deleted). Debugging with `git bisect` becomes harder since intermediate states are gone.

---

## Task 4: Git Stash — Hands-On

### Commands Practiced

```bash
# Make uncommitted changes
echo "work in progress..." >> app.py

# Try to switch branch (Git may warn or block)
git checkout main
# → error: Your local changes to the following files would be overwritten by checkout

# Stash the changes
git stash push -m "WIP: app.py changes"

# Switch branch freely, do work
git checkout main
echo "hotfix" >> hotfix.py
git add . && git commit -m "Apply quick hotfix"

# Return and restore stash
git checkout feature-dashboard
git stash pop
```

```bash
# Multiple stashes
git stash push -m "feature A changes"
git stash push -m "feature B changes"
git stash push -m "feature C changes"

# List all stashes
git stash list
# stash@{0}: On feature-dashboard: feature C changes
# stash@{1}: On feature-dashboard: feature B changes
# stash@{2}: On feature-dashboard: feature A changes

# Apply a specific stash (without dropping it)
git stash apply stash@{1}

# Apply and drop the top stash
git stash pop
```

---

### Observations & Answers

#### What is the difference between `git stash pop` and `git stash apply`?

| `git stash pop` | `git stash apply` |
|---|---|
| Restores the stash AND **removes** it from the stash list | Restores the stash but **keeps** it in the stash list |
| Use when you're done with that stash | Use when you might need to apply the same stash elsewhere |

#### When would you use stash in a real-world workflow?

1. **Urgent context switch** — You're mid-feature when a critical bug is reported. Stash your WIP, fix the bug, unstash and continue.
2. **Pulling latest changes** — Your working tree has changes that would conflict with `git pull`. Stash, pull, unstash.
3. **Trying an idea** — Stash current work to experiment on a clean state, without losing anything.
4. **Wrong branch** — You started working on the wrong branch. Stash, switch to the right branch, pop.

---

## Task 5: Cherry Picking

### Commands Practiced

```bash
# Setup feature-hotfix with 3 commits
git checkout -b feature-hotfix

echo "change A" >> hotfix.py
git add . && git commit -m "Hotfix: fix null pointer error"

echo "change B" >> hotfix.py
git add . && git commit -m "Hotfix: patch SQL injection vulnerability"   ← WANT THIS ONE

echo "change C" >> hotfix.py
git add . && git commit -m "Hotfix: experimental cache refactor"

# Find the commit hash
git log --oneline
# abc1234 Hotfix: experimental cache refactor
# def5678 Hotfix: patch SQL injection vulnerability   ← this one
# ghi9012 Hotfix: fix null pointer error

# Cherry-pick only the second commit onto main
git checkout main
git cherry-pick def5678

# Verify
git log --oneline -3
# 7fe3a21 Hotfix: patch SQL injection vulnerability   ← applied!
# (previous main commits...)
```

---

### Observations & Answers

#### What does cherry-pick do?

`git cherry-pick <commit-hash>` takes a **specific commit from any branch** and applies its changes as a **new commit** on your current branch. It copies the diff of that commit and replays it — the new commit gets a different hash but contains the same changes.

#### When would you use cherry-pick in a real project?

1. **Backporting a fix** — A bug is fixed on `main` but an older release branch (e.g., `release-1.2`) also needs the fix. Cherry-pick it there without merging all of `main`.
2. **Selective feature inclusion** — A feature branch has mixed commits; only one is ready for production right now.
3. **Recovering lost work** — A commit accidentally made on the wrong branch can be cherry-picked to the right one.
4. **Hotfixes** — Apply a critical patch to multiple release branches simultaneously.

#### What can go wrong with cherry-picking?

- **Duplicate commits:** If the cherry-picked branch is eventually merged, the same change appears twice in history (different hashes, same diff) — this is confusing and can cause conflicts.
- **Missing dependencies:** A cherry-picked commit may depend on code from earlier commits that aren't present on the target branch, causing build failures or bugs.
- **Diverging histories:** Over-reliance on cherry-pick instead of proper branching leads to tangled, hard-to-understand histories.
- **Conflict resolution burden:** If the target branch has diverged significantly, every cherry-pick may require manual conflict resolution.

---

## Summary: Key Mental Models

```
MERGE     → Join histories. Safe. Creates merge commits on diverged branches.
REBASE    → Replay commits. Linear history. Never on public/shared branches.
SQUASH    → Compress many commits into one. Great for PR hygiene.
STASH     → Temporary shelf for uncommitted work. Not a long-term solution.
CHERRY-PICK → Surgical copy of one specific commit. Great for hotfixes.
```

---

## Git Workflow Decision Tree

```
Need to integrate a feature branch into main?
├── Is history/audit important? → Regular Merge
├── Want clean linear history? → Rebase then Merge (or Squash Merge)
└── Have messy WIP commits?   → Squash Merge

Need a specific fix from another branch?
└── Cherry-pick

In the middle of work and need to switch?
└── Git Stash
```

---

*Day 24 complete. These are the Git skills that separate beginners from practitioners. 🚀*

`#90DaysOfDevOps` `#DevOpsKaJosh` `#TrainWithShubham`
