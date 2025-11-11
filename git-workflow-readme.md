# Git Workflow Guide (Generic Version)

This guide outlines a practical Git workflow for managing any project or configuration repository. It is designed to ensure **reproducibility**, **auditability**, and **team collaboration**. The workflow follows best practices commonly used in DevOps, compliance, and configuration management environments.

---

## 📘 Overview
- **Main Branch:** `main`
- **Remote:** `origin` (GitHub or other remote)
- **Workflow:** Initialize → Branch → Commit → PR/Merge → Tag → Clean up

---

## 🧩 0. One-Time Setup (Per Machine)
```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
git config --global init.defaultBranch main
```

> **Tip:** If using SSH for GitHub, verify your connection:
> ```bash
> ssh -T git@github.com
> ```

If you receive an authentication error, follow [GitHub SSH Setup Guide](https://docs.github.com/en/authentication/connecting-to-github-with-ssh).

---

## 🚀 1. Initialize and Connect to Remote Repo
```bash
cd /path/to/project
git init
git add .
git commit -m "Initial import of project baseline"
```

On GitHub (or other platform):
1. Create a new repository.
2. Copy the remote URL (SSH recommended).

Connect and push:
```bash
git remote add origin git@github.com:username/repo.git
git push -u origin main
```

> **Tip:** Run `git status --short` before pushing to confirm a clean repo.

---

## 🌿 2. Branching for Changes
Always branch off `main` to isolate development or testing.
```bash
git checkout main
git pull
git checkout -b feature-update-logging
```
Make edits → Stage → Commit:
```bash
git add .
git commit -m "Add improved logging to configuration management role"
git push -u origin feature-update-logging
```

Open a Pull Request (PR) to merge `feature-update-logging` → `main` on GitHub.

> **Naming Conventions:**
> - `feature-*` → For new functionality.
> - `bugfix-*` → For fixes.
> - `hardening-*` → For security/compliance updates.

---

## 🔄 3. Syncing Your Branch
If updates occur on `main` during your work:
```bash
git checkout main
git pull
git checkout feature-update-logging
git merge main
```

Resolve conflicts if prompted, then:
```bash
git add .
git commit
git push
```
> **Pitfall:** Avoid `git rebase` unless you fully understand its effect on shared history.

---

## 🧭 4. Merging Back to Main
**Option A (Recommended):** via GitHub PR
- Review & approve PR.
- Merge it into `main`.

Then locally:
```bash
git checkout main
git pull
```

**Option B:** via CLI
```bash
git checkout main
git pull
git merge feature-update-logging
git push
```

---

## 🏷️ 5. Tagging Stable Baselines
Tags mark **auditable points** in history — ideal for baselines, releases, or compliance milestones.
```bash
git checkout main
git pull
git tag -a baseline-v1.0 -m "Baseline v1.0: initial configuration hardening"
git push origin baseline-v1.0
```

### When to Tag
- After merging a tested PR
- At a known-good configuration state
- Before production rollout or compliance submission

> **Tip:** Inspect a tagged version anytime:
> ```bash
> git checkout baseline-v1.0
> ```

---

## 🧹 6. Cleaning Up Branches
After merging:
```bash
git branch -d feature-update-logging
git push origin --delete feature-update-logging
```
> **Recommendation:** Protect `main` via GitHub branch rules to prevent direct pushes.

---

## ⚙️ 7. Cheat Sheet
```bash
# Setup
git config --global user.name "Your Name"
git config --global user.email "you@example.com"

# Init Repo
git init
git add .
git commit -m "Initial commit"
git remote add origin git@github.com:username/repo.git
git push -u origin main

# New Branch
git checkout main
git pull
git checkout -b feature-branch
git add .
git commit -m "Describe change"
git push -u origin feature-branch

# Sync Branch
git checkout main
git pull
git checkout feature-branch
git merge main
git push

# Merge to Main
git checkout main
git pull
git merge feature-branch
git push

# Tag Baseline
git tag -a v1.0.0 -m "Version 1.0.0 release"
git push origin v1.0.0

# Cleanup
git branch -d feature-branch
git push origin --delete feature-branch
```

---

## 🤝 8. Collaboration & Best Practices
- **Pull Requests:** Use PRs for peer review before merging.
- **Commit Messages:** Use meaningful, traceable messages (e.g., referencing a ticket or control ID).
- **CI/CD Integration:** Automate tests (e.g., ansible-lint, YAML validation) via GitHub Actions.
- **Auditing:** Reference control IDs or issue numbers in commits and tags for traceability.
- **Branch Protection:** Enforce reviews and disallow force pushes on `main`.

---

## 🧠 9. Common Issues & Tips
<details>
<summary>Authentication errors when pushing</summary>
Ensure SSH keys are added to GitHub or switch to HTTPS authentication.
</details>

<details>
<summary>Merge conflicts</summary>
Run `git status` to identify conflicts, edit the conflicting files, mark them resolved:
```bash
git add conflicted_file.yml
git commit
```
</details>

<details>
<summary>Undo last commit (safe rollback)</summary>
```bash
git reset --soft HEAD~1
```
This unstages the last commit but keeps changes.
</details>

---

## 🔍 Retroactive Tagging
If you already merged and want to tag the previous baseline:
```bash
git log --oneline
```
Identify the commit before the merge, then tag it:
```bash
git tag -a baseline-rel-stig-v1 -m "Baseline before ansible-rhel-hardening-test merge" <commit-id>
git push origin baseline-rel-stig-v1
```
Or simply:
```bash
git tag -a baseline-rel-stig-v1 -m "Baseline before merge" HEAD~1
git push origin baseline-rel-stig-v1
```

---


## 🗺️ 10. Visual Workflow (ASCII)
```
     +-----------------+
     |   Initialize    |
     +--------+--------+
              |
              v
      +-------+--------+
      |   Create Branch |
      +-------+--------+
              |
              v
     +--------+--------+
     |   Develop & Commit |
     +--------+--------+
              |
              v
     +--------+--------+
     | Pull Request / Merge |
     +--------+--------+
              |
              v
     +--------+--------+
     |     Tag Baseline     |
     +--------+--------+
              |
              v
     +--------+--------+
     |  Clean Up Branches  |
     +-----------------+
```

---

**Author Template:** Generic Git Workflow (Reusable)
