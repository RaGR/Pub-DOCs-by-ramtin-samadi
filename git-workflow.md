### 1. Creating a New Branch for Your Task

To create a new branch for your task in Azure DevOps, follow these steps:

- **Using the Azure DevOps Web Interface:**
  1. Go to your repository in Azure DevOps.
  2. Click on the "Branches" tab.
  3. Find the branch you want to base your work on (usually `main` or another feature branch).
  4. Click the "New branch" button.
  5. Give your branch a descriptive name (e.g., `feature/your-task-name`).
  6. Start working on your task in this new branch.

- **Using Git Command Line:**
  ```bash
  git checkout -b feature/your-task-name origin/main
  ```
  This creates a new branch based on `main` and switches to it.

### 2. Difference Between Forking and Creating a Branch

- **Forking:**
  - Creating a fork makes a copy of the entire repository into your own account.
  - Useful when you don't have write access to the original repository.
  - Typically used for open-source contributions or when working independently.
  - Changes are contributed back via pull requests from your fork to the original repository.

- **Creating a Branch:**
  - Creating a branch keeps your work within the same repository.
  - You have full access to the repository (if you have permissions).
  - Useful for collaborative work within the same team.
  - Changes are merged back into the main branch via pull requests.

### 3. Adding Another Branch on Top of Your Own Branch

- **Creating a Sub-Branch:**
  1. Ensure you are on your main task branch:
     ```bash
     git checkout feature/your-task-name
     ```
  2. Create a new branch for additional work:
     ```bash
     git checkout -b feature/your-task-name-subtask
     ```
  3. Do your work and commit changes in this sub-branch.

- **Merging Back:**
  1. Switch back to your main task branch:
     ```bash
     git checkout feature/your-task-name
     ```
  2. Merge the sub-branch into your main task branch:
     ```bash
     git merge feature/your-task-name-subtask
     ```
  3. Resolve any conflicts if they arise.
  4. Delete the sub-branch:
     ```bash
     git branch -d feature/your-task-name-subtask
     ```

- **Submitting a Pull Request:**
  1. Once all your work is merged into `feature/your-task-name`, go to Azure DevOps.
  2. Create a pull request from `feature/your-task-name` to the target branch (usually `main`).
  3. Review your changes and submit the pull request for approval.

### 4. Staging Changes Without Committing

If you want to add changes to the staging area (index) without committing them, you can use:

```bash
git add .
```

This stages all changes, but doesn't commit them. You can then commit when you're ready:

```bash
git commit -m "Your commit message"
```

If you want to stage only specific files or parts of files, you can use:

```bash
git add <file-name>
```

Or use `git add -p` to stage changes patch by patch.

Remember, staging changes is a way to prepare a commit, but nothing is saved to the history until you commit.
