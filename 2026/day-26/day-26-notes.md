# Day 26 – GitHub CLI: Manage GitHub from Your Terminal

> **90DaysOfDevOps | DevOpsKaJosh | TrainWithShubham**

---

## Task 1: Install and Authenticate

### Installation

```bash
# ── macOS (Homebrew) ──────────────────────────────────────────────────────────
brew install gh

# ── Ubuntu / Debian ───────────────────────────────────────────────────────────
(type -p wget >/dev/null || (sudo apt update && sudo apt install wget -y)) \
  && sudo mkdir -p -m 755 /etc/apt/keyrings \
  && wget -qO- https://cli.github.com/packages/githubcli-archive-keyring.gpg \
     | sudo tee /etc/apt/keyrings/githubcli-archive-keyring.gpg > /dev/null \
  && sudo chmod go+r /etc/apt/keyrings/githubcli-archive-keyring.gpg \
  && echo "deb [arch=$(dpkg --print-architecture) \
     signed-by=/etc/apt/keyrings/githubcli-archive-keyring.gpg] \
     https://cli.github.com/packages stable main" \
     | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null \
  && sudo apt update \
  && sudo apt install gh -y

# ── Windows (winget) ──────────────────────────────────────────────────────────
winget install --id GitHub.cli

# ── Windows (Scoop) ───────────────────────────────────────────────────────────
scoop install gh

# Verify installation
gh --version
# gh version 2.x.x (...)
```

### Authentication

```bash
# Interactive login — walks you through browser or token flow
gh auth login

# Prompts:
# ? What account do you want to log into?  GitHub.com
# ? What is your preferred protocol?       HTTPS
# ? How would you like to authenticate?    Login with a web browser
# ! First copy your one-time code: XXXX-XXXX
# Press Enter to open github.com in your browser...
# ✓ Authentication complete.

# Verify active account and token scope
gh auth status
# github.com
#   ✓ Logged in to github.com account yourname (keyring)
#   - Active account: true
#   - Git operations protocol: https
#   - Token: gho_****
#   - Token scopes: 'gist', 'read:org', 'repo', 'workflow'

# Log out
gh auth logout
```

### What authentication methods does `gh` support?

`gh` supports two authentication methods:

**1. Web Browser (OAuth)**
The most common method. Run `gh auth login`, select "Login with a web browser", copy the one-time code shown in the terminal, paste it on github.com, and authorize the app. A token is stored securely in your system's credential store (keychain on macOS, credential manager on Windows, or a plain config file on Linux).

**2. Personal Access Token (PAT)**
Select "Paste an authentication token" during `gh auth login`, then paste a token generated at `github.com/settings/tokens`. Useful in headless/CI environments where a browser isn't available.

**Environment Variable (non-interactive)**
```bash
# Set token for CI pipelines or scripts — no interactive login needed
export GH_TOKEN=ghp_yourPersonalAccessToken
gh auth status   # reads from env var
```

**SSH for Git operations**
`gh` can also be configured to use SSH for git push/pull even when authenticated via HTTPS:
```bash
gh auth login --git-protocol ssh
```

---

## Task 2: Working with Repositories

### Create a Repo from Terminal

```bash
# Interactive — gh asks for name, visibility, etc.
gh repo create

# Non-interactive — public repo with README, auto-cloned locally
gh repo create my-test-repo \
  --public \
  --description "Testing GitHub CLI" \
  --add-readme \
  --clone

# Private repo, no auto-clone
gh repo create my-private-project --private

# Create from an existing local directory
cd my-project/
gh repo create --source=. --public --push
```

### Clone a Repo via `gh`

```bash
# By owner/repo (no full URL needed)
gh repo clone torvalds/linux

# Clone one of YOUR repos by just its name
gh repo clone devops-git-practice

# Clone into a specific directory
gh repo clone owner/repo my-local-dir

# gh repo clone handles both HTTPS and SSH automatically
# based on your gh auth protocol setting
```

### View Repo Details

```bash
# View current repo info (must be inside a git repo)
gh repo view

# View a specific repo
gh repo view yourname/devops-git-practice

# Open in browser
gh repo view --web
gh repo view yourname/devops-git-practice --web

# Get JSON output (useful for scripting)
gh repo view yourname/devops-git-practice --json name,description,url,stargazerCount
```

### List Your Repos

```bash
# List your repos (default: most recently updated)
gh repo list

# List with limit
gh repo list --limit 20

# Filter by visibility
gh repo list --public
gh repo list --private

# List repos for another user or org
gh repo list kubernetes --limit 10

# JSON output for scripting
gh repo list --json name,url,isPrivate,pushedAt
```

### Delete a Repo

```bash
# Delete (asks for confirmation)
gh repo delete yourname/my-test-repo

# Skip confirmation prompt (careful!)
gh repo delete yourname/my-test-repo --confirm
```

---

## Task 3: Issues

### Create an Issue

```bash
# Interactive — gh opens your editor
gh issue create

# Non-interactive with title and body
gh issue create \
  --title "Fix login form validation bug" \
  --body "The login form doesn't validate empty password fields. Steps to reproduce: 1. Open /login 2. Submit with empty password" \
  --label "bug"

# With multiple labels, assignee, and milestone
gh issue create \
  --title "Add dark mode support" \
  --body "Users have requested dark mode. Implement a CSS variable-based theme toggle." \
  --label "enhancement,frontend" \
  --assignee "@me" \
  --milestone "v2.0"

# In a specific repo (from outside the directory)
gh issue create \
  --repo yourname/devops-git-practice \
  --title "Update README with Day 26 notes" \
  --body "Add gh CLI documentation to the repo README."
```

### List Issues

```bash
# List open issues
gh issue list

# List closed issues
gh issue list --state closed

# List all (open + closed)
gh issue list --state all

# Filter by label
gh issue list --label "bug"

# Filter by assignee
gh issue list --assignee "@me"

# JSON output
gh issue list --json number,title,state,labels,createdAt
```

### View a Specific Issue

```bash
# View issue #5
gh issue view 5

# Open issue in browser
gh issue view 5 --web

# Get machine-readable output
gh issue view 5 --json title,body,state,comments
```

### Close an Issue

```bash
# Close issue #5
gh issue close 5

# Close with a comment
gh issue close 5 --comment "Fixed in PR #12 — deploying now."

# Reopen a closed issue
gh issue reopen 5
```

### How could you use `gh issue` in a script or automation?

`gh issue` becomes powerful when combined with shell scripting and `--json` output:

```bash
# Count all open bugs — alert if threshold exceeded
BUG_COUNT=$(gh issue list --label "bug" --state open --json number | jq '. | length')
if [ "$BUG_COUNT" -gt 10 ]; then
  echo "⚠️  $BUG_COUNT open bugs — escalate to team lead"
fi

# Auto-create a release checklist issue
gh issue create \
  --title "Release v$(date +%Y.%m.%d) Checklist" \
  --body "- [ ] Run smoke tests\n- [ ] Update CHANGELOG\n- [ ] Tag release\n- [ ] Deploy to staging" \
  --label "release"

# Close all issues matching a label after a release
gh issue list --label "v1.0" --state open --json number \
  | jq -r '.[].number' \
  | xargs -I{} gh issue close {} --comment "Resolved in v1.0 release."

# Export all issues to CSV for a report
gh issue list --state all --limit 200 \
  --json number,title,state,createdAt,closedAt \
  | jq -r '.[] | [.number, .title, .state, .createdAt] | @csv' > issues.csv
```

Real-world uses: sprint automation, release checklists, SLA monitoring, auto-triage bots, syncing issues between repos.

---

## Task 4: Pull Requests

### Create a PR Entirely from Terminal

```bash
# Step 1: Create and switch to a new branch
git checkout -b feature/add-cli-docs

# Step 2: Make changes and commit
echo "# GitHub CLI Notes" >> cli-notes.md
git add cli-notes.md
git commit -m "Add GitHub CLI documentation"

# Step 3: Push the branch
git push -u origin feature/add-cli-docs

# Step 4: Create the PR — interactive
gh pr create

# Step 4 (alternative): Non-interactive with all fields
gh pr create \
  --title "Add GitHub CLI documentation" \
  --body "This PR adds comprehensive notes on using the GitHub CLI for repo, issue, and PR management." \
  --base main \
  --head feature/add-cli-docs \
  --label "documentation" \
  --assignee "@me"

# Auto-fill title and body from commit messages
gh pr create --fill

# Create as a draft PR
gh pr create --draft --fill
```

### List Open PRs

```bash
# List open PRs in current repo
gh pr list

# List all PRs (open + closed + merged)
gh pr list --state all

# Filter by author
gh pr list --author yourname

# Filter by base branch
gh pr list --base main

# JSON for scripting
gh pr list --json number,title,headRefName,state,author,reviews
```

### View PR Details

```bash
# View PR #3
gh pr view 3

# View current branch's PR (if you're on the feature branch)
gh pr view

# Open in browser
gh pr view 3 --web

# Check PR status — CI checks, review status, merge readiness
gh pr status

# View diff of a PR
gh pr diff 3

# List checks (CI status) on a PR
gh pr checks 3
```

### Merge a PR from Terminal

```bash
# Merge PR #3 (interactive — asks for method)
gh pr merge 3

# Merge with specific method
gh pr merge 3 --merge          # regular merge commit
gh pr merge 3 --squash         # squash and merge
gh pr merge 3 --rebase         # rebase and merge

# Auto-delete branch after merge
gh pr merge 3 --squash --delete-branch

# Enable auto-merge (merges automatically when all checks pass)
gh pr merge 3 --auto --squash
```

### What merge methods does `gh pr merge` support?

`gh pr merge` supports three methods, mirroring the GitHub web UI:

| Method | Flag | What it does |
|---|---|---|
| **Merge Commit** | `--merge` | Creates a merge commit preserving full branch history. All commits appear individually in `main`. |
| **Squash and Merge** | `--squash` | Compresses all branch commits into one commit on `main`. Keeps history clean. |
| **Rebase and Merge** | `--rebase` | Replays commits linearly onto `main` without a merge commit. Clean, linear history. |

### How would you review someone else's PR using `gh`?

```bash
# Check out the PR branch locally to test it
gh pr checkout 7

# Read the diff in terminal
gh pr diff 7

# View all review comments
gh pr view 7 --comments

# Submit a review
gh pr review 7 --approve
gh pr review 7 --request-changes --body "The auth logic has an edge case when token is null."
gh pr review 7 --comment --body "Left some inline questions — overall looks good."

# Add a comment without a formal review
gh pr comment 7 --body "LGTM! Just minor nit on the variable naming."

# Check CI status before approving
gh pr checks 7
```

---

## Task 5: GitHub Actions & Workflows (Preview)

### List Workflow Runs

```bash
# List recent workflow runs in current repo
gh run list

# List for a specific repo
gh run list --repo kubernetes/kubernetes

# Filter by workflow name
gh run list --workflow "CI"

# Filter by branch
gh run list --branch main

# Limit output
gh run list --limit 10

# JSON output
gh run list --json databaseId,name,status,conclusion,createdAt
```

### View a Specific Workflow Run

```bash
# View run by ID (get ID from gh run list)
gh run view 1234567890

# Watch a run in real time (streams logs)
gh run watch 1234567890

# View logs of a completed run
gh run view 1234567890 --log

# View logs of only failed steps
gh run view 1234567890 --log-failed

# Re-run a failed workflow
gh run rerun 1234567890

# Re-run only failed jobs
gh run rerun 1234567890 --failed-only
```

### List and Trigger Workflows

```bash
# List all workflows in a repo
gh workflow list

# View a specific workflow definition
gh workflow view "CI"

# Manually trigger a workflow (if it has workflow_dispatch trigger)
gh workflow run "Deploy to Production" --ref main

# Trigger with inputs
gh workflow run "Deploy to Production" \
  --ref main \
  --field environment=staging \
  --field dry_run=true

# Disable / enable a workflow
gh workflow disable "Nightly Tests"
gh workflow enable "Nightly Tests"
```

### How could `gh run` and `gh workflow` be useful in a CI/CD pipeline?

**Monitoring & Alerting:**
```bash
# Check if the last run on main succeeded — alert if not
CONCLUSION=$(gh run list --branch main --limit 1 --json conclusion \
  | jq -r '.[0].conclusion')
if [ "$CONCLUSION" != "success" ]; then
  echo "❌ Last CI run FAILED — blocking deployment"
  exit 1
fi
```

**Chaining pipelines across repos:**
```bash
# After merging a library, trigger dependent service's build
gh workflow run "Integration Tests" \
  --repo yourname/my-service \
  --ref main \
  --field lib_version=v2.1.0
```

**Deployment gates:**
```bash
# Only deploy if all checks on a PR pass
gh pr checks $PR_NUMBER --watch   # waits until all checks complete
gh pr merge $PR_NUMBER --auto --squash
```

**Audit and reporting:**
```bash
# Get last 30 days of workflow run outcomes for a report
gh run list --limit 100 \
  --json name,status,conclusion,createdAt,headBranch \
  | jq '[.[] | select(.conclusion != null)]' > run-report.json
```

---

## Task 6: Useful `gh` Tricks

### `gh api` — Raw GitHub API Calls

```bash
# GET request — fetch repo info
gh api repos/yourname/devops-git-practice

# GET with field selection via JQ
gh api repos/yourname/devops-git-practice \
  --jq '.stargazers_count, .forks_count, .open_issues_count'

# List org members
gh api orgs/kubernetes/members --jq '.[].login'

# POST request — create a label
gh api repos/yourname/devops-git-practice/labels \
  --method POST \
  --field name="day-26" \
  --field color="0075ca" \
  --field description="GitHub CLI tasks"

# PATCH request — update repo description
gh api repos/yourname/devops-git-practice \
  --method PATCH \
  --field description="90 Days of DevOps practice repo"

# Paginate through all results
gh api --paginate /repos/yourname/devops-git-practice/commits \
  --jq '.[].commit.message'
```

### `gh gist` — Manage GitHub Gists

```bash
# Create a public gist from a file
gh gist create my-script.sh --public --desc "Useful bash snippets"

# Create a private gist from multiple files
gh gist create file1.sh file2.py --desc "Config snippets"

# Create a gist from stdin
echo "alias k=kubectl" | gh gist create --filename aliases.sh

# List your gists
gh gist list

# View a gist
gh gist view <gist-id>

# Edit a gist
gh gist edit <gist-id>

# Delete a gist
gh gist delete <gist-id>
```

### `gh release` — Create and Manage Releases

```bash
# Create a new release with a tag
gh release create v1.0.0 \
  --title "Version 1.0.0 — Initial Release" \
  --notes "First stable release. See CHANGELOG for details."

# Create release and upload build artifacts
gh release create v1.0.0 \
  --title "v1.0.0" \
  --notes-file CHANGELOG.md \
  dist/app-linux-amd64 \
  dist/app-darwin-arm64

# Create a draft release (not published yet)
gh release create v1.1.0 --draft --title "v1.1.0 Draft"

# Create a pre-release
gh release create v2.0.0-beta1 --prerelease --title "v2.0.0 Beta 1"

# List releases
gh release list

# View a specific release
gh release view v1.0.0

# Download release assets
gh release download v1.0.0 --dir ./downloads

# Delete a release
gh release delete v1.0.0
```

### `gh alias` — Custom Command Shortcuts

```bash
# Create a simple alias
gh alias set prc 'pr create --fill'
gh prc   # same as: gh pr create --fill

# Alias for listing your assigned PRs
gh alias set mine 'pr list --assignee @me'
gh mine

# Alias for quickly viewing current PR status
gh alias set check 'pr checks'

# Alias using a shell command (prefix with !)
gh alias set recent '!gh repo list --limit 5 --json name,pushedAt | jq -r ".[] | \(.name) — \(.pushedAt)"'

# List all aliases
gh alias list

# Delete an alias
gh alias delete prc
```

### `gh search repos` — Search GitHub from Terminal

```bash
# Search by keyword
gh search repos "kubernetes operator"

# Filter by language
gh search repos "machine learning" --language python

# Filter by stars
gh search repos devops --stars ">1000"

# Search with multiple filters
gh search repos "helm charts" \
  --language yaml \
  --stars ">500" \
  --sort stars

# Search your own repos
gh search repos --owner @me devops

# JSON output for processing
gh search repos "terraform aws" \
  --limit 10 \
  --json name,description,stargazerCount,url \
  | jq '.[] | "\(.stargazerCount) ⭐  \(.name) — \(.description)"'
```

---

## Summary: Why `gh` Matters for DevOps

```
WITHOUT gh:
  terminal → browser → navigate → click → copy link → back to terminal
  (context lost, 2+ minutes per task)

WITH gh:
  terminal → gh command → done
  (stay in flow, seconds per task, scriptable)
```

**The real power of `gh` is scripting.** Every command supports `--json` output, making it composable with `jq`, `bash`, Python, and any automation tool. You can:
- Auto-create PRs from CI pipelines
- Gate deployments on PR approval status
- Generate weekly issue reports
- Trigger cross-repo workflows
- Manage releases as part of build scripts

This is what "DevOps automation" looks like in practice — not clicking buttons, but composing CLI tools into repeatable, auditable workflows.

---

*Day 26 complete. The terminal is now your GitHub. 🖥️*

`#90DaysOfDevOps` `#DevOpsKaJosh` `#TrainWithShubham`day
