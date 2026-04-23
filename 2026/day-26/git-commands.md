# Git & GitHub CLI Reference — devops-git-practice

> Complete reference covering Days 22–26 | #90DaysOfDevOps | #DevOpsKaJosh | #TrainWithShubham

---

## 1. Setup & Config

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
git config --global init.defaultBranch main
git config --global core.editor "code --wait"
git config --global pull.rebase false        # merge on pull (default)
git config --global pull.rebase true         # rebase on pull
git config --global credential.helper store
git config --global color.ui auto
git config --list                            # view all settings
```

---

## 2. Basic Workflow

```bash
git init
git clone https://github.com/user/repo.git
git clone https://github.com/user/repo.git my-folder
git status
git add filename.txt
git add .
git add -p filename.txt           # stage hunks interactively
git commit -m "message"
git commit -am "message"          # stage tracked + commit
git commit --amend -m "fixed"     # rewrite last commit
git log
git log --oneline
git log --oneline --graph --all
git diff                          # unstaged changes
git diff --staged                 # staged changes
git show abc1234                  # show a specific commit
git rm filename.txt
git rm --cached filename.txt      # untrack but keep on disk
```

---

## 3. Branching

```bash
git branch                        # list local branches
git branch -a                     # list local + remote
git branch feature-login          # create branch
git checkout feature-login        # switch branch
git switch feature-login          # switch (modern)
git checkout -b feature-login     # create + switch
git switch -c feature-login       # create + switch (modern)
git branch -m new-name            # rename current branch
git branch -d feature-login       # delete (safe)
git branch -D feature-login       # force delete
git branch --merged               # branches merged into current
git branch --no-merged            # branches not yet merged
```

---

## 4. Remote Operations

```bash
git remote add origin https://github.com/user/repo.git
git remote add upstream https://github.com/original/repo.git
git remote -v                     # view remotes
git fetch origin                  # fetch without merging
git fetch --all
git pull origin main
git pull --rebase origin main
git push origin feature-login
git push -u origin feature-login  # set upstream tracking
git push origin --delete feature-login
git push --force-with-lease       # safer force push
# Fork sync
git fetch upstream && git merge upstream/main && git push origin main
```

---

## 5. Merging & Rebasing

```bash
git merge feature-login
git merge --no-ff feature-login   # force merge commit
git merge --squash feature-login  # collapse into staged changes
git merge --abort
git checkout --ours file.txt      # resolve: keep current
git checkout --theirs file.txt    # resolve: keep incoming
git rebase main                   # rebase current onto main
git rebase -i HEAD~3              # interactive rebase
git rebase --abort
git rebase --continue
git rebase --skip
git log main..feature --oneline   # commits unique to feature
```

---

## 6. Stash & Cherry-Pick

```bash
git stash
git stash push -m "WIP: description"
git stash push --include-untracked
git stash list
git stash pop                     # apply + remove from list
git stash apply                   # apply, keep in list
git stash apply stash@{2}
git stash show -p stash@{0}
git stash drop stash@{1}
git stash clear
git cherry-pick abc1234
git cherry-pick abc123..def456    # range
git cherry-pick --no-commit abc1234
git cherry-pick --abort
git cherry-pick --continue
```

---

## 7. Reset & Revert

```bash
# Reset — rewrites history (local only)
git reset --soft HEAD~1           # commit removed, changes STAGED
git reset --mixed HEAD~1          # commit removed, changes UNSTAGED (default)
git reset --hard HEAD~1           # commit removed, changes DELETED
git reset --hard abc1234          # reset to specific commit
git restore --staged filename.txt # unstage a file

# Revert — safe undo (creates new commit, safe for shared branches)
git revert HEAD
git revert abc1234
git revert --no-edit abc1234
git revert --no-commit abc1234

# Reflog — emergency recovery (your safety net)
git reflog                        # see ALL head movements
git checkout -b recovery f3a1b2c  # recover a lost commit
```

---

## 8. History Visualization

```bash
git log --oneline --graph --all --decorate
git log --pretty=format:"%h %an %ad %s" --date=short
git log --grep="fix" --oneline
git log --follow -p filename.txt
git blame filename.txt
git log --left-right --graph --oneline main...feature
```

---

## 9. GitHub CLI (gh) — Install & Auth

```bash
# Install
brew install gh                         # macOS
sudo apt install gh                     # Ubuntu/Debian (after adding repo)
winget install GitHub.cli               # Windows

# Authenticate
gh auth login                           # interactive (browser or PAT)
gh auth status                          # verify active account
gh auth logout
export GH_TOKEN=ghp_yourToken           # non-interactive (CI/scripts)

# Help
gh help
gh <command> --help
```

---

## 10. GitHub CLI — Repos

```bash
gh repo create my-repo --public --add-readme --clone
gh repo create --source=. --public --push   # from existing dir
gh repo clone owner/repo
gh repo clone owner/repo my-dir
gh repo view                                # current repo
gh repo view owner/repo
gh repo view --web                          # open in browser
gh repo list
gh repo list --limit 20 --public
gh repo list --json name,url,pushedAt
gh repo delete owner/repo --confirm
```

---

## 11. GitHub CLI — Issues

```bash
gh issue create
gh issue create --title "Bug" --body "Details" --label "bug"
gh issue create --label "bug,urgent" --assignee "@me" --milestone "v2.0"
gh issue list
gh issue list --state closed
gh issue list --label "bug"
gh issue list --assignee "@me"
gh issue list --json number,title,state,labels
gh issue view 5
gh issue view 5 --web
gh issue close 5
gh issue close 5 --comment "Fixed in PR #12"
gh issue reopen 5
```

---

## 12. GitHub CLI — Pull Requests

```bash
gh pr create                                      # interactive
gh pr create --fill                               # auto-fill from commits
gh pr create --title "Title" --body "Body" --base main
gh pr create --draft --fill
gh pr list
gh pr list --state all
gh pr list --author yourname
gh pr list --json number,title,state,author
gh pr view 3
gh pr view                                        # current branch's PR
gh pr view 3 --web
gh pr status                                      # review + CI status
gh pr diff 3
gh pr checks 3
gh pr checks --watch                              # wait for checks to finish
gh pr merge 3 --merge                             # merge commit
gh pr merge 3 --squash --delete-branch           # squash + cleanup
gh pr merge 3 --rebase
gh pr merge 3 --auto --squash                    # auto-merge when checks pass
gh pr review 7 --approve
gh pr review 7 --request-changes --body "Needs fix"
gh pr comment 7 --body "LGTM!"
gh pr checkout 7                                  # check out PR locally
```

---

## 13. GitHub CLI — Actions & Workflows

```bash
gh run list
gh run list --workflow "CI" --branch main --limit 10
gh run list --json databaseId,name,status,conclusion
gh run view 1234567890
gh run view 1234567890 --log
gh run view 1234567890 --log-failed
gh run watch 1234567890                           # stream live logs
gh run rerun 1234567890
gh run rerun 1234567890 --failed-only
gh workflow list
gh workflow view "CI"
gh workflow run "Deploy" --ref main
gh workflow run "Deploy" --ref main --field env=staging
gh workflow disable "Nightly Tests"
gh workflow enable "Nightly Tests"
```

---

## 14. GitHub CLI — Advanced Tools

```bash
# Raw API calls
gh api repos/owner/repo
gh api repos/owner/repo --jq '.stargazers_count'
gh api repos/owner/repo/labels --method POST \
  --field name="urgent" --field color="d93f0b"
gh api --paginate /repos/owner/repo/commits --jq '.[].commit.message'

# Gists
gh gist create file.sh --public --desc "My script"
echo "content" | gh gist create --filename notes.md
gh gist list
gh gist view <id>
gh gist edit <id>
gh gist delete <id>

# Releases
gh release create v1.0.0 --title "v1.0.0" --notes "Release notes"
gh release create v1.0.0 --notes-file CHANGELOG.md dist/app-linux
gh release create v2.0.0-beta --prerelease --draft
gh release list
gh release view v1.0.0
gh release download v1.0.0 --dir ./downloads
gh release delete v1.0.0

# Aliases
gh alias set prc 'pr create --fill'
gh alias set mine 'pr list --assignee @me'
gh alias list
gh alias delete prc

# Search
gh search repos "kubernetes operator" --language go --stars ">500"
gh search repos devops --owner @me
gh search repos "terraform" --sort stars --limit 10 --json name,stargazerCount
```

---

## 15. Reset vs Revert — Quick Reference

| | `git reset` | `git revert` |
|---|---|---|
| Removes commit from history? | ✅ Yes | ❌ No |
| Safe for pushed branches? | ❌ No | ✅ Yes |
| Targets middle commit cleanly? | ❌ | ✅ |
| Risk | 🔴 `--hard` destroys work | 🟢 Non-destructive |

## 16. Branching Strategy — Quick Reference

| Strategy | Best For | Key Branches |
|---|---|---|
| **GitFlow** | Large teams, scheduled releases | `main`, `develop`, `feature/*`, `release/*`, `hotfix/*` |
| **GitHub Flow** | Startups, continuous delivery | `main` + short feature branches |
| **Trunk-Based** | Elite CI/CD, multiple deploys/day | `main` only (< 2 day branches) |

---

*Last updated: Day 26 — Git & GitHub CLI complete reference | #90DaysOfDevOps*
