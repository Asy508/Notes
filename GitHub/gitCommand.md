# GitHub / Git Commands

Basic Git commands for creating, updating, and managing GitHub repositories.

---

# 1. Check Git Version

```bash
git --version
```

Example:

```text
git version 2.50.1
```

---

# 2. Configure Git

Set username:

```bash
git config --global user.name "Your Name"
```

Set email:

```bash
git config --global user.email "your@email.com"
```

Check configuration:

```bash
git config --global --list
```

---

# 3. Create a New Local Repository

Go to the project directory:

```bash
cd my-project
```

Initialize Git:

```bash
git init
```

Check status:

```bash
git status
```

---

# 4. Connect Local Repository to GitHub

Add GitHub remote:

```bash
git remote add origin https://github.com/USERNAME/REPOSITORY.git
```

Check remote:

```bash
git remote -v
```

Example:

```text
origin  https://github.com/Asy508/server-notes.git (fetch)
origin  https://github.com/Asy508/server-notes.git (push)
```

---

# 5. Add Files

Add one file:

```bash
git add README.md
```

Add multiple files:

```bash
git add file1.md file2.md
```

Add everything:

```bash
git add .
```

Check what will be committed:

```bash
git status
```

---

# 6. Commit

Create a commit:

```bash
git commit -m "Add Cloudflare Tunnel notes"
```

Example:

```bash
git commit -m "Add GitHub command notes"
```

---

# 7. Push to GitHub

First push:

```bash
git push -u origin main
```

After that:

```bash
git push
```

The `-u` sets the upstream branch, so future pushes can simply use:

```bash
git push
```

---

# 8. Pull Changes from GitHub

Get changes from GitHub:

```bash
git pull
```

Or specify the branch:

```bash
git pull origin main
```

---

# 9. Clone a GitHub Repository

Clone:

```bash
git clone https://github.com/USERNAME/REPOSITORY.git
```

Example:

```bash
git clone https://github.com/Asy508/server-notes.git
```

Then:

```bash
cd server-notes
```

---

# 10. Check Repository Status

```bash
git status
```

Example:

```text
On branch main

Changes not staged for commit:
  modified: README.md

Untracked files:
  Cloudflare/cloudflared-tunnel.md
```

---

# 11. View Commit History

Basic:

```bash
git log
```

Compact:

```bash
git log --oneline
```

Example:

```text
a82f91c Add Cloudflare Tunnel notes
f73c421 Add PM2 notes
c18a302 Initial commit
```

---

# 12. View Changes

Check unstaged changes:

```bash
git diff
```

Check staged changes:

```bash
git diff --cached
```

---

# 13. Create a Branch

Create a branch:

```bash
git branch feature-test
```

Switch to it:

```bash
git checkout feature-test
```

Or use the newer command:

```bash
git switch feature-test
```

Create and switch at the same time:

```bash
git switch -c feature-test
```

---

# 14. List Branches

```bash
git branch
```

Example:

```text
* main
  feature-test
```

The `*` shows the current branch.

---

# 15. Merge a Branch

Switch to main:

```bash
git switch main
```

Merge:

```bash
git merge feature-test
```

Then push:

```bash
git push
```

---

# 16. Delete a Local Branch

```bash
git branch -d feature-test
```

If Git refuses because the branch has not been merged:

```bash
git branch -D feature-test
```

Use `-D` carefully because it can delete unmerged work.

---

# 17. Undo `git add`

If you accidentally added a file:

```bash
git restore --staged filename
```

Example:

```bash
git restore --staged config.yml
```

The file remains on your computer but is removed from the staging area.

---

# 18. Discard Local Changes

Discard changes to a file:

```bash
git restore filename
```

Example:

```bash
git restore README.md
```

Discard all unstaged changes:

```bash
git restore .
```

**Be careful:** this removes your uncommitted changes.

---

# 19. Reset the Last Commit

Keep the changes but remove the commit:

```bash
git reset --soft HEAD~1
```

Remove the commit and unstage the changes:

```bash
git reset HEAD~1
```

Hard reset:

```bash
git reset --hard HEAD~1
```

**Be careful with `--hard`.**

---

# 20. View Remote Repository

```bash
git remote -v
```

Change the remote URL:

```bash
git remote set-url origin https://github.com/USERNAME/REPOSITORY.git
```

Remove remote:

```bash
git remote remove origin
```

---

# 21. GitHub Push Rejected

Example:

```text
! [rejected] main -> main (fetch first)

error: failed to push some refs
```

This usually means the GitHub repository contains commits that your local repository does not have.

First try:

```bash
git pull --rebase origin main
```

Then:

```bash
git push
```

---

# 22. Git Push Conflict

If Git reports conflicts:

```bash
git status
```

Open the conflicted files.

You may see:

```text
<<<<<<< HEAD
Local version
=======
GitHub version
>>>>>>> origin/main
```

Edit the file and keep the correct content.

Then:

```bash
git add .
```

Continue the rebase:

```bash
git rebase --continue
```

Finally:

```bash
git push
```

---

# 23. Force Push

Sometimes you may intentionally want the local branch to replace the remote branch:

```bash
git push --force
```

A safer version is:

```bash
git push --force-with-lease
```

**Do not use force push unless you understand what will be overwritten.**

---

# 24. Create a Tag

Tags are useful for marking versions/releases.

Create a tag:

```bash
git tag v1.0.0
```

List tags:

```bash
git tag
```

Push a tag:

```bash
git push origin v1.0.0
```

Push all tags:

```bash
git push origin --tags
```

Example:

```bash
git tag v1.1.0
git push origin v1.1.0
```

---

# 25. Delete a Tag

Delete local tag:

```bash
git tag -d v1.0.0
```

Delete remote tag:

```bash
git push origin --delete v1.0.0
```

---

# 26. `.gitignore`

Create:

```text
.gitignore
```

Example:

```gitignore
# Environment
.env

# Node.js
node_modules/

# Python
__pycache__/
*.pyc

# Logs
*.log

# Cloudflare credentials
.cloudflared/
*.pem

# Temporary files
tmp/
temp/
```

Then:

```bash
git add .gitignore
git commit -m "Add gitignore"
git push
```

---

# 27. Example: Create Server Notes Repository

Create a folder:

```bash
mkdir server-notes
cd server-notes
```

Initialize Git:

```bash
git init
```

Create folders:

```bash
mkdir WSL
mkdir Cloudflare
mkdir Linux
mkdir MQTT
mkdir Git
```

Create a README:

```bash
echo "# Server Notes" > README.md
```

Check:

```bash
git status
```

Add everything:

```bash
git add .
```

Commit:

```bash
git commit -m "Initial server notes"
```

Connect GitHub:

```bash
git remote add origin https://github.com/USERNAME/server-notes.git
```

Push:

```bash
git push -u origin main
```

---

# 28. Example: Add a New Note

Suppose we create:

```text
Cloudflare/cloudflared-tunnel.md
```

Check:

```bash
git status
```

Add:

```bash
git add Cloudflare/cloudflared-tunnel.md
```

Commit:

```bash
git commit -m "Add Cloudflare Tunnel notes"
```

Push:

```bash
git push
```

---

# 29. Typical Daily Workflow

Most of the time, the workflow is simply:

```bash
git pull
```

Make changes.

Then:

```bash
git status
```

Add:

```bash
git add .
```

Commit:

```bash
git commit -m "Update server notes"
```

Push:

```bash
git push
```

So the basic cycle is:

```text
Pull
  ↓
Edit files
  ↓
git status
  ↓
git add
  ↓
git commit
  ↓
git push
```

---

# 30. Useful Command Cheat Sheet

| Command | Purpose |
|---|---|
| `git init` | Create local repository |
| `git clone URL` | Download repository |
| `git status` | Check repository status |
| `git add .` | Stage all changes |
| `git commit -m "message"` | Create commit |
| `git push` | Upload commits |
| `git pull` | Download and merge changes |
| `git log --oneline` | View commit history |
| `git diff` | View changes |
| `git branch` | List branches |
| `git switch branch` | Switch branch |
| `git switch -c branch` | Create and switch branch |
| `git merge branch` | Merge branch |
| `git tag v1.0.0` | Create version tag |
| `git remote -v` | Show remote repository |
| `git restore file` | Discard file changes |
| `git restore --staged file` | Unstage file |
| `git reset HEAD~1` | Undo last commit |
| `git stash` | Temporarily store changes |

---

# Simple Rule

For normal daily work:

```bash
git pull
```

Edit your files.

```bash
git add .
git commit -m "Describe what changed"
git push
```

For example:

```bash
git pull

# Edit Markdown files

git add .
git commit -m "Add PM2 and Cloudflare notes"
git push
```
