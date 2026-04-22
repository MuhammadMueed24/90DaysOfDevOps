<img width="755" height="178" alt="Screenshot from 2026-04-22 23-28-31" src="https://github.com/user-attachments/assets/1dcd8b79-4579-4430-a7f4-21c9d1846031" />

# Day 22 Notes – Understand the Git Workflow

## 1. What is the difference between git add and git commit?

`git add` moves file changes to the staging area so they are ready to be saved.
`git commit` permanently saves those staged changes as a snapshot in Git history.

---

## 2. What does the staging area do? Why doesn't Git just commit directly?

The staging area lets you choose which changes to include in the next commit.
This gives better control and helps keep commits clean and organized instead of saving every change immediately.

---

## 3. What information does git log show you?

`git log` shows commit history such as:
- Commit ID (hash)
- Author name
- Date and time
- Commit message

---

## 4. What is the .git/ folder and what happens if you delete it?

The `.git/` folder stores all repository data like commits, branches, settings, and history.
If you delete it, the folder is no longer a Git repository and all local Git history is lost.

---

## 5. What is the difference between a working directory, staging area, and repository?

- **Working Directory:** Your current project files where you make changes.
- **Staging Area:** Temporary place where selected changes wait for commit.
- **Repository:** Permanent Git database containing commits and history.
~                                                                                                                                                                     
~                                                                     
