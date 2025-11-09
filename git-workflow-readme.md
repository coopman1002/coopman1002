

# Git Workflow for Safe Changes and GitHub Pushes

Author: Mike Coopman  
Last Updated: 2025-11-08  

This README explains how to:

- Keep an **original baseline** of your playbooks
- Make changes safely in **branches**
- Use **tags** as permanent bookmarks
- Initialize a repo
- Commit and push changes to GitHub

The examples assume:

- You are in the folder:  
  `C:\Users\Mike\OneDrive\Documents\Ansible\stig\rhel-hardending-upgrade`
- Your GitHub repo is: `git@github.com:coopman1002/Ansible.git`
- Your main branch is called `main`

> You can adapt names/paths as needed.

---

## 1. Check if the folder is a Git repo

From the project folder:

```bash
git status
```

If you see something like:

```text
On branch main
Your branch is up to date with 'origin/main'.
```

then this folder is already a Git repo.  
If you see an error like `not a git repository`, see section **4. Initialize a new repo**.

---

## 2. Create a baseline tag (freeze the "original" state)

Once your repo is in a good state (original playbooks the way you like them), tag it:

```bash
git status         # should say: nothing to commit, working tree clean
git tag -a baseline-rhel-stig-v1 -m "Baseline RHEL 8 STIG hardening playbook"
git push origin baseline-rhel-stig-v1
```

What this does:

- `baseline-rhel-stig-v1` becomes a **permanent bookmark** to this exact commit.
- You can always pull files back from this tag later if you mess something up.

You can see your tags with:

```bash
git tag
```

---

## 3. Create a branch for experiments / changes

Instead of editing files on `main`, create a new branch:

```bash
git checkout -b rhel-stig-experiments
```

Now:

- You are on branch `rhel-stig-experiments`.
- Any changes you make and commit here DO NOT change the files on `main`.

Basic branch commands:

```bash
git branch             # list branches; * shows your current branch
git checkout main      # switch back to main
git checkout rhel-stig-experiments   # switch to your experiment branch
```

---

## 4. Initialize a new repo (only if needed)

If you ever start a new project folder that is not yet a Git repo:

```bash
cd path/to/your/new/project

git init                    # create .git in this folder
git add .                   # stage all files
git commit -m "Initial commit"
git branch -M main          # ensure branch is called main
git remote add origin git@github.com:USERNAME/REPO.git
git push -u origin main     # first push
```

For existing repos you **do not** run `git init` again.

---

## 5. Standard workflow for making changes

From your experiment branch (or whichever branch you are working on):

```bash
# 1. See what changed
git status

# 2. Stage new/modified files
git add .

# 3. Commit them with a message
git commit -m "Describe what you changed here"

# 4. Push to GitHub
git push origin rhel-stig-experiments
```

If this is the first time you push this branch:

```bash
git push -u origin rhel-stig-experiments
```

The `-u` sets the default upstream so later you can just run:

```bash
git push
git pull
```

on that branch.

---

## 6. RESTORE original files from the baseline tag

If you mess up a file and want to bring back the original version from the baseline:

```bash
# Make sure you are on the branch where you want to fix the file,
# e.g., rhel-stig-experiments
git checkout rhel-stig-experiments

# Restore a single file from baseline tag:
git checkout baseline-rhel-stig-v1 -- roles/rhel8STIG/tasks/main.yml
```

You can also restore a whole directory:

```bash
git checkout baseline-rhel-stig-v1 -- roles/rhel8STIG/
```

Then commit the restored file(s):

```bash
git add .
git commit -m "Restore original rhel8STIG files from baseline tag"
git push
```

---

## 7. Common Git commands (quick reference)

### Status & history

```bash
git status            # See changed files and current branch
git log               # View commit history
git log --oneline     # Short, one-line-per-commit history
```

### Staging and committing

```bash
git add filename.yml          # Stage one file
git add .                     # Stage all changes in this folder
git commit -m "Message"       # Commit staged changes
```

### Branches

```bash
git branch                    # List branches
git checkout main             # Switch to main
git checkout -b new-branch    # Create and switch to new-branch
git branch -d branch-name     # Delete a fully merged branch
git branch -D branch-name     # Force delete a branch
```

### Pushing & pulling

```bash
git push                      # Push current branch to its upstream
git push origin main          # Push main branch explicitly
git pull                      # Pull latest changes for current branch
git pull origin main          # Pull from main explicitly
```

### Remotes

```bash
git remote -v                          # Show remotes
git remote add origin URL              # Add a remote
git remote set-url origin NEW_URL      # Change the remote URL
```

For example, to switch your remote to SSH:

```bash
git remote set-url origin git@github.com:coopman1002/Ansible.git
```

Or to HTTPS:

```bash
git remote set-url origin https://github.com/coopman1002/Ansible.git
```

---

## 8. Optional: Manual backup copy

If you ever want a belt-and-suspenders backup outside Git, you can copy the entire folder once:

PowerShell example:

```powershell
Copy-Item -Recurse `
  "C:\Users\Mike\OneDrive\Documents\Ansible\stig\rhel-hardending-upgrade" `
  "C:\Users\Mike\OneDrive\Documents\Ansible\stig\rhel-hardending-upgrade_backup"
```

This is not required when using Git correctly, but can give peace of mind.

---

## 9. Typical daily pattern for this project

1. Open VS Code in the project folder.  
2. Make sure you are on the experiment branch:

   ```bash
   git branch
   git checkout rhel-stig-experiments
   ```

3. Edit playbooks / handlers / defaults.  
4. Save files.  
5. Run:

   ```bash
   git status
   git add .
   git commit -m "Update handlers for new STIG rules"
   git push
   ```

6. If you ever need to get back to the untouched state, use:

   ```bash
   git checkout main                          # original branch
   git checkout baseline-rhel-stig-v1 -- ...  # pull originals if needed
   ```

With this workflow, you can **experiment freely** while always having a clean, original baseline to return to.


---

## 10. Branch Workflow – Merging Test to Main

Follow this workflow to merge updates from your testing branch into `main`.

### 1️⃣ Check Your Current Branch
```bash
git status
```
If not on `test`, switch to it:
```bash
git checkout test
```

### 2️⃣ Update the Test Branch
```bash
git pull origin test
```

### 3️⃣ Switch to Main
```bash
git checkout main
git pull origin main
```

### 4️⃣ Merge Test into Main
```bash
git merge test
```

- If there are **no conflicts**, you’ll see a success message.
- If there **are conflicts**, run:
  ```bash
  git status
  ```
  Then manually fix files, add them, and commit:
  ```bash
  git add .
  git commit
  ```

### 5️⃣ Push the Updated Main Branch
```bash
git push origin main
```

### 6️⃣ (Optional) Clean Up
After confirming your changes:
```bash
git branch -d test
git push origin --delete test
```

### ✅ Quick Reference Table

| Step | Command | Purpose |
|------|----------|----------|
| 1 | `git checkout test` | Switch to test branch |
| 2 | `git pull origin test` | Update local test branch |
| 3 | `git checkout main` | Switch to main branch |
| 4 | `git merge test` | Merge changes into main |
| 5 | `git push origin main` | Push to remote main |
| 6 | `git branch -d test` | Delete test branch locally |
