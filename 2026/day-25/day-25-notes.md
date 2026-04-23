# Day 25 – Git Reset vs Revert & Branching Strategies

> **90DaysOfDevOps | DevOpsKaJosh | TrainWithShubham**

---

## Task 1: Git Reset — Hands-On

### Setup: 3 Commits to Practice On

```bash
echo "line 1" >> app.py && git add . && git commit -m "Commit A: initial app"
echo "line 2" >> app.py && git add . && git commit -m "Commit B: add feature"
echo "line 3" >> app.py && git add . && git commit -m "Commit C: add logging"

git log --oneline
# c3f1a2b Commit C: add logging      ← HEAD
# b2e0f9a Commit B: add feature
# a1d8c7e Commit A: initial app
```

---

### `git reset --soft HEAD~1`

```bash
git reset --soft HEAD~1

git status
# Changes to be committed:
#   modified: app.py     ← Commit C's changes are STAGED

git log --oneline
# b2e0f9a Commit B: add feature   ← HEAD moved back, C is gone from log
# a1d8c7e Commit A: initial app
```

**What happened:** The commit was removed from history, but the changes from Commit C are still **staged** (in the index). You can re-commit immediately with `git commit -m "new message"`.

---

### `git reset --mixed HEAD~1` (default behavior)

```bash
# Re-commit C first
git commit -m "Commit C: add logging"

# Now reset with --mixed
git reset --mixed HEAD~1

git status
# Changes not staged for commit:
#   modified: app.py     ← Commit C's changes are UNSTAGED (working tree)

git log --oneline
# b2e0f9a Commit B: add feature   ← HEAD moved back
# a1d8c7e Commit A: initial app
```

**What happened:** The commit was removed, and changes moved back to the **working directory** — unstaged. You need to `git add` before recommitting. This is the default when you just type `git reset HEAD~1`.

---

### `git reset --hard HEAD~1`

```bash
# Re-commit C first
git add . && git commit -m "Commit C: add logging"

# Now reset with --hard
git reset --hard HEAD~1

git status
# nothing to commit, working tree clean   ← Changes are GONE

git log --oneline
# b2e0f9a Commit B: add feature   ← HEAD moved back
# a1d8c7e Commit A: initial app
```

**What happened:** The commit was removed AND the changes were **permanently deleted** from the working directory. Commit C never existed, from Git's perspective.

> ⚠️ Emergency recovery: `git reflog` can still find the lost commit hash for a short window.

---

### Answers

#### Difference between `--soft`, `--mixed`, and `--hard`

| Mode | Commit removed? | Index (staging) | Working Directory |
|---|---|---|---|
| `--soft` | ✅ Yes | ✅ Changes kept staged | ✅ Changes kept |
| `--mixed` | ✅ Yes | ❌ Changes unstaged | ✅ Changes kept |
| `--hard` | ✅ Yes | ❌ Changes discarded | ❌ Changes DELETED |

**Memory trick:** Think of how far back the "wrecking ball" swings:
- `--soft` → only moves the HEAD pointer
- `--mixed` → moves HEAD + clears the index
- `--hard` → moves HEAD + clears index + nukes working directory

#### Which is destructive and why?

`--hard` is **destructive** because it permanently deletes uncommitted changes from your working directory. There's no Recycle Bin — the content is gone from Git's tracked state. (You may recover it briefly via `git reflog` if the data is still in Git's object store, but that window closes.)

#### When would you use each one?

| Mode | Use Case |
|---|---|
| `--soft` | Undo a commit but keep your work staged — useful to reword a commit message or split a commit |
| `--mixed` | Undo a commit and unstage changes — useful to reorganize what goes into the next commit |
| `--hard` | Completely throw away a commit and its changes — useful to discard a bad experiment entirely |

#### Should you ever use `git reset` on pushed commits?

**No — almost never.** If you reset commits that others have pulled, their history diverges from yours. When they try to push or merge, Git sees conflicts because the commits you deleted still exist on their machines.

If you must undo a pushed commit, use `git revert` instead — it's designed for exactly this situation.

---

## Task 2: Git Revert — Hands-On

### Setup

```bash
echo "feature X" >> app.py && git add . && git commit -m "Commit X: feature X"
echo "feature Y" >> app.py && git add . && git commit -m "Commit Y: feature Y"
echo "feature Z" >> app.py && git add . && git commit -m "Commit Z: feature Z"

git log --oneline
# z9a8b7c Commit Z: feature Z   ← HEAD
# y6d5e4f Commit Y: feature Y   ← want to revert THIS
# x3c2b1a Commit X: feature X
```

### Reverting the Middle Commit (Y)

```bash
git revert y6d5e4f

# Git opens your editor for the revert commit message, then:
git log --oneline
# r1a2b3c Revert "Commit Y: feature Y"   ← NEW commit that undoes Y
# z9a8b7c Commit Z: feature Z
# y6d5e4f Commit Y: feature Y            ← still exists in history!
# x3c2b1a Commit X: feature X
```

**Commit Y is still in history.** Revert adds a new commit that applies the inverse diff of Y — it doesn't erase anything.

---

### Answers

#### How is `git revert` different from `git reset`?

- `git reset` **removes** commits from history (rewrites history)
- `git revert` **adds a new commit** that undoes the effect of a previous commit (preserves history)

With revert, the original commit is still visible in `git log`. With reset, it's gone (from the regular log — `reflog` still has it briefly).

#### Why is revert safer for shared branches?

Because `git revert` only **adds** to history — it never rewrites it. Everyone on the team can pull the new revert commit normally. No one's history is broken, no force-push is needed, no divergence occurs.

Reset changes the past; revert changes the future.

#### When would you use revert vs reset?

| Use `git revert` when... | Use `git reset` when... |
|---|---|
| The commit is already pushed/shared | The commit is local only |
| You want a clear audit trail of the undo | You want to cleanly erase the mistake |
| You need to undo a specific middle commit | You want to go back multiple commits cleanly |
| Working on `main` or a shared branch | Working on your personal feature branch |

---

## Task 3: Reset vs Revert — Comparison

| | `git reset` | `git revert` |
|---|---|---|
| **What it does** | Moves HEAD backward, optionally discarding changes | Creates a new commit that undoes a previous commit's changes |
| **Removes commit from history?** | ✅ Yes — history is rewritten | ❌ No — original commit stays, new "undo" commit is added |
| **Safe for shared/pushed branches?** | ❌ No — breaks teammates' history | ✅ Yes — safe for everyone |
| **When to use** | Local cleanup before pushing; discarding private experiments | Undoing pushed commits; reverting specific changes in shared code |
| **Can undo middle commits?** | Not cleanly (would remove everything after it too) | ✅ Yes — can target any specific commit |
| **Risk level** | 🔴 High (`--hard` destroys work) | 🟢 Low (non-destructive) |
| **Emergency recovery** | `git reflog` (short window) | Not needed — nothing was lost |

---

## Task 4: Branching Strategies

---

### 1. GitFlow

#### How It Works

GitFlow uses **two permanent branches** (`main` and `develop`) plus three types of short-lived branches.

```
main      ──────●─────────────────────●──────────●──── (production releases only)
                │                     │          │
                │     hotfix-v1.1 ────┘          │
                │                                │
develop   ──●───●───●───●───●───●───────────────●──── (integration branch)
               │           │         │
feature/login ─┘    feature/signup ──┘
                              │
                        release/1.0 ─── (QA, final fixes) ─── merged to main + develop
```

**Branch types:**
- `main` — Only contains production-ready, tagged releases
- `develop` — Integration branch; all features merge here
- `feature/*` — Branched from `develop`, merged back to `develop`
- `release/*` — Branched from `develop` when ready for QA; merged to `main` AND `develop`
- `hotfix/*` — Branched from `main` for emergency patches; merged to `main` AND `develop`

#### When / Where It's Used
Large teams with **scheduled, versioned releases** — enterprise software, mobile apps with app store review cycles, SaaS products with quarterly releases.

#### Pros & Cons

| Pros | Cons |
|---|---|
| Very structured — clear purpose for each branch type | Heavy overhead — lots of branches to manage |
| Great for parallel development of many features | `develop` can become a long-lived integration bottleneck |
| Explicit release process with QA gate | Slow — features wait in `develop` before reaching production |
| Good audit trail for versioned software | Complex for small teams or fast-moving projects |

---

### 2. GitHub Flow

#### How It Works

Extremely simple — there's only `main` and feature branches. Everything merges directly to `main` via Pull Requests.

```
main   ──●──────────────────────●───────────●────────── (always deployable)
          \                    /             \          /
           feature/login ─────               feature/search
                 (PR → review → merge → deploy)
```

**Rules:**
1. `main` is always deployable
2. New work → new branch from `main`
3. Open a Pull Request early for discussion
4. Merge to `main` only after review + CI passes
5. Deploy immediately after merge

#### When / Where It's Used
Small-to-medium teams doing **continuous deployment**. Web applications, SaaS startups, open-source projects (React, Vue, many GitHub repos follow this).

#### Pros & Cons

| Pros | Cons |
|---|---|
| Simple — almost no overhead | No explicit staging/release mechanism |
| Fast — features ship as soon as they're merged | Requires strong CI/CD and feature flags for large teams |
| Easy to understand and onboard | Can be chaotic if `main` breaks frequently |
| `main` always reflects production | Less suited for versioned/packaged software |

---

### 3. Trunk-Based Development (TBD)

#### How It Works

Everyone commits to `main` (the "trunk") directly or via **very short-lived branches** (< 1–2 days). No long-lived feature branches. Feature flags control what's visible in production.

```
main   ──●──●──●──●──●──●──●──●──●──●──●──────── (everyone commits here)
              \   /     \  /
               ──           ──
          (short branch,  (short branch,
           1-2 days max)   merged fast)
```

**Key practices:**
- Branches live for hours to 2 days maximum
- Feature flags hide incomplete features from users
- Continuous Integration runs on every commit
- Small, frequent commits rather than large merges

#### When / Where It's Used
High-performing engineering orgs with strong CI/CD: **Google, Facebook, Netflix**. Teams that deploy multiple times per day. This is what the Google DevOps research (DORA) identifies in elite performers.

#### Pros & Cons

| Pros | Cons |
|---|---|
| Fastest integration — no big-bang merges | Requires mature CI/CD pipeline |
| Eliminates merge conflicts from long-lived branches | Requires discipline with feature flags |
| Forces small, releasable increments | Can feel chaotic without strong team culture |
| Closest to true continuous delivery | Not ideal for open-source (external contributors need branches) |

---

### Strategy Selection Answers

#### Which strategy for a startup shipping fast?

**GitHub Flow** — simple, low overhead, ships fast. A single `main` branch with feature branches and PRs is all you need. No release branches, no `develop` integration layer. Deploy on merge.

If the team is very experienced with CI/CD, **Trunk-Based Development** is even better — it forces small commits and eliminates merge pain entirely.

#### Which strategy for a large team with scheduled releases?

**GitFlow** — the structured branch model with `develop`, `release/*`, and `hotfix/*` branches is built exactly for this. It gives large teams parallel workstreams, a clear QA gate before production, and the ability to patch production without disturbing in-progress work.

#### Which does a popular open-source project use?

**Kubernetes** uses a **branch-per-release model** close to GitFlow: `main` for active development, plus long-lived `release-1.x` branches for each minor version that receive only cherry-picked bug fixes and security patches.

**React** uses a variation of **GitHub Flow** — PRs go to `main`, and releases are tagged from there. The `experimental` branch is the only long-lived non-main branch.

**Linux kernel** uses **Trunk-Based Development** at its core — Linus pulls from subsystem maintainers into `mainline`; the merge window is 2 weeks, then only fixes go in until the release.

---

## Summary

```
RESET  → Time machine (rewrites history) — local only
REVERT → Undo button (adds to history)   — safe for shared

GitFlow          → Scheduled releases, large teams, enterprise
GitHub Flow      → Continuous delivery, startups, web apps
Trunk-Based Dev  → Elite CI/CD, multiple deploys per day
```

---

*Day 25 complete. Knowing when NOT to force-push is as important as knowing how. 🔐*

`#90DaysOfDevOps` `#DevOpsKaJosh` `#TrainWithShubham`
