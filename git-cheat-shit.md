# **Git & GitHub Power Workflow — From Local to Cloud**

Master Git as a local-first version control system that mirrors your project history to GitHub. Below is a complete, efficient command-line flow for professionals who want full control over their repositories and automation-ready workflows.

---

### 🔧 Commands

```bash
cd "C:/path/to/project"
git init
git remote add origin git@github.com:YourUsername/YourRepo.git
git add .
git commit -m "Initial commit"
git branch -M main
git push -u origin main
git status
git log --oneline --graph --decorate --all
git diff
git checkout -b feature/branch
git merge feature/branch
git rebase origin/main
git fetch --all --prune
git tag -a v1.0 -m "Release 1.0"
git reset HEAD~1
git commit --amend
git gc --prune=now --aggressive
ssh -T git@github.com
```

---

### 🧠 Explanation

**Initialization** – `git init` creates a local repository; `.git` stores version history.
**Remote setup** – `git remote add origin` links your local repo to GitHub via SSH or HTTPS.
**Staging & Commit** – `git add .` prepares changes, `git commit` records snapshots.
**Branch Management** – `git branch -M main` standardizes branch naming; `checkout -b` creates feature branches.
**Pushing & Tracking** – `git push -u origin main` uploads commits and sets tracking for future pushes.
**Inspection** – `status`, `log`, and `diff` give insight into repo state and history.
**Merging & Rebasing** – `merge` integrates branches; `rebase` rewrites commit history cleanly.
**Maintenance** – `fetch --all --prune` updates remote refs; `gc` optimizes storage.
**Versioning** – `tag` marks release points.
**Undo & Amend** – `reset` reverts commits, `commit --amend` edits the latest one.
**SSH Check** – `ssh -T git@github.com` validates authentication.

---

### ⚙️ Professional Practice

Commit logically, branch cleanly, rebase often, and tag releases. Treat Git as infrastructure: automate repo creation, pushing, and CI/CD hooks for a fully autonomous pipeline. Git mastery isn’t about memorizing commands — it’s about **precision, structure, and flow**.




ssh access fix:
Excellent — you’re nearly done.
You’ve already added your **public SSH key** to GitHub, so now your system just needs to *recognize and use* that key when talking to GitHub.

Below is the **complete guide** for connecting and pushing via SSH from your Windows 11 terminal (Git Bash).
You’ll be set up like a pro — permanent, secure, and smooth.

---

## ⚙️ STEP-BY-STEP SETUP (Once Per Machine)

### **1. Confirm your key is stored locally**

You should have two files under:

```
C:\Users\<YourUser>\.ssh\
```

* `id_rsa` → your **private key** (keep secret)
* `id_rsa.pub` → your **public key** (the one you added to GitHub)

If your key has a different name (like `id_ed25519` or `my_custom_key`), just adjust the file name accordingly.

---

### **2. Start the SSH agent**

In Git Bash, run:

```bash
eval "$(ssh-agent -s)"
```

You should see something like:

```
Agent pid 1234
```

This runs the background process that holds your key securely.

---

### **3. Add your private key to the agent**

```bash
ssh-add ~/.ssh/id_rsa
```

If you used a custom filename, specify it:

```bash
ssh-add ~/.ssh/my_custom_key
```

To verify:

```bash
ssh-add -l
```

This should list your key’s fingerprint (e.g., SHA256:xxxxxxx).

---

### **4. Test the connection**

```bash
ssh -T git@github.com
```

If everything’s correct, you’ll see:

```
Hi <your_username>! You've successfully authenticated, but GitHub does not provide shell access.
```

That means SSH authentication is now working — you’re fully connected.

If you see “permission denied” or “no identities found,” recheck the filename or rerun `ssh-add`.


## ✅ Summary

| Step | Command                                                  | Purpose               |
| ---- | -------------------------------------------------------- | --------------------- |
| 1    | `eval "$(ssh-agent -s)"`                                 | Start SSH key manager |
| 2    | `ssh-add ~/.ssh/id_rsa`                                  | Load your private key |
| 3    | `ssh -T git@github.com`                                  | Test authentication   |
| 4    | `git remote set-url origin git@github.com:User/Repo.git` | Use SSH remote        |
| 5    | `git push -u origin main`                                | Push via SSH          |

