
# Git structure 

[git structure]

![[Pasted image 20240404172118.png]]

**Local Repository:**
- Located on your computer.
- Created by `git init` or `git clone`.
- Used for personal work: editing, committing, branching.
- Works offline.
- Needs to push to share changes with others.

**Remote Repository:**
- Hosted on a server (e.g., GitHub, GitLab).
- Accessed via URL.
- Central hub for collaboration and sharing.
- Requires internet access.
- You pull from and push to this repository.

**Interaction:**
- **Push:** Send changes from local to remote.
- **Pull:** Update local repository with changes from remote.
- **Clone:** Copy a remote repository to create a new local repository.

---
**<span style="color:#ffc000">Working directory</span>**:

The working directory in Git is where you make changes to your project files. It's your local copy of the files in the Git repository.

<span style="color:#d68a8a">For example</span>, if you have a project with a file `example.txt`, when you edit this file in your working directory, these changes are only in your local environment. They are not yet saved in the Git repository. To save these changes in Git, you would add (`git add example.txt`) and then commit (`git commit`) them.

---
**<span style="color:#ffc000">The staging area</span>**:

The staging area in Git is where you prepare changes made in your working directory for a commit. It's a middle ground between your working directory and the commit history.

<span style="color:#d68a8a">For example</span>, if you edit a file called `example.txt`, you use `git add example.txt` to move those changes to the staging area. Then, you commit them to your repository's history with `git commit`. This process allows you to selectively commit changes.

---


# Git Commands

## git clone
[git clone]

`git clone` is used to copy a remote repository to your local machine. Here's a quick example:

```bash
git clone https://github.com/exampleuser/example-repo.git
```

This command creates a local copy of the repository found at `https://github.com/exampleuser/example-repo.git`. It includes all the files, history, and branches of that repository.

---
## git init
[git init]

 `git init` is a command used to initialize a new Git repository. It creates a new `.git` directory in your project folder, which houses all the necessary repository files and metadata for version control.

Here's a quick example:

1. **Open your terminal or command prompt.**

2. **Navigate to your project directory.**
   ```sh
   cd path/to/your/project
   ```

3. **Run the `git init` command.**
   ```sh
   git init
   ```
   This command creates a new Git repository in the current directory.

After running `git init`, you can start using other Git commands to track your project files and their changes.

---

## git add
[git add]

`git add` is a command in Git used to add changes in the working directory to the staging area, preparing them to be included in the next commit. It tells Git you want to include updates to a particular file or files in your next commit.

Here's a basic example:

1. **Editing a File:**
   Suppose you have edited a file named `example.txt` in your working directory.

2. **Adding the File:**
   To stage this file, you would use:
   ```bash
   git add example.txt
   ```
   This command adds `example.txt` to the staging area.

3. **Adding Multiple Files:**
   If you want to add multiple files, you can specify them all:
   ```bash
   git add file1.txt file2.txt
   ```

4. **Adding All Changes:**
   To add all the changes in your directory, you can use:
   ```bash
   git add .
   ```
   This will stage all modified and new files.

`git add` doesn't affect the repository yet; no changes are actually recorded until you run `git commit`.

---

## git commit 
[git commit]

`git commit` is a Git command used to save staged changes to the repository's history. After staging changes with `git add`, you use `git commit` to actually record those changes. A commit is like a snapshot of your repository at a specific point in time.

Example:
1. Stage your changes with `git add`:
   ```bash
   git add example.txt
   ```

2. Commit the changes with a message describing what you've done:
   ```bash
   git commit -m "Update example.txt"
   ```

Each commit has a unique ID and includes metadata like the author's name and timestamp. This allows you to track changes and revert to previous versions if needed.

---

## git push
[git push]

`git push` is a command used in Git to upload your local repository content to a remote repository. After committing your changes locally with `git commit`, you use `git push` to share them with others.

Here's a basic example:

1. **Commit your changes locally.**
   First, make sure you've committed all your changes:
   ```bash
   git commit -m "Your commit message"
   ```

2. **Push the changes to the remote repository.**
   Then, push these changes to the remote:
   ```bash
   git push origin main
   ```
   This command pushes the commits from your local `main` branch to the `main` branch in the remote repository (`origin`).

`origin` is the default name given to the remote repository from which you cloned, and `main` is the primary branch you're pushing to. You can replace these with the appropriate remote name and branch name for your project.

---

## git fetch
[git fetch]

`git fetch` is a command in Git used to download changes from a remote repository to your local repository, but it doesn't automatically merge these changes into your current working branch. This command is particularly useful for seeing what others have done without integrating those changes into your local branches immediately.

Here's how it works:

1. **Fetch Changes:**
   ```bash
   git fetch origin
   ```
   This command fetches any new work that has been pushed to the repository on the server (`origin` is a common shorthand for your remote repository) since your last fetch or clone. It updates your remote-tracking branches.

2. **Inspect Changes:**
   After fetching, you can inspect these changes with commands like `git log` or `git diff` before deciding to merge them into your branch.

3. **Merging Changes:**
   To merge the fetched changes into your current branch, you would then use:
   ```bash
   git merge origin/main
   ```
   Here, `origin/main` is the main branch of the remote. You can replace `main` with any branch you wish to merge.

`git fetch` is a safe way to review changes without affecting your local working environment.

---

## git pull
[git pull]

`git pull` is a Git command that combines two actions: `git fetch` followed by `git merge`. When you use `git pull`, Git first fetches updates from a remote repository (like new commits in a remote branch) and then immediately merges them into your current local branch.

Here's a basic example:

1. **Pulling Changes:**
   ```bash
   git pull origin main
   ```
   This command fetches changes from the `main` branch of the remote repository named `origin` and merges them into your current local branch.

2. **How It Works:**
   - If you're on your `main` branch locally, and you run this command, Git will fetch the latest commits from the `main` branch in the remote repository and merge any new changes into your local `main` branch.
   - It's equivalent to doing `git fetch origin main` followed by `git merge origin/main`.

`git pull` is a convenient way to update your local branch with the latest changes from a remote repository, but it's important to be aware that it can lead to merge conflicts if there are diverging changes. It's often used in collaborative settings where multiple people are working on the same codebase.

---
## git status
[git status]

`git status` shows the current status of your working directory and staging area. It tells you which changes are staged for the next commit, which are not, and which files aren't being tracked by Git.

In your shell, it looks like this:

```bash
$ git status
On branch main
Your branch is up to date with 'origin/main'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   example.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

This output indicates that `example.txt` has been modified but not yet staged for a commit.

---
## git diff
[git diff]

`git diff` is a Git command that shows the differences between files in your working directory and the staging area, or between the staging area and your last commit, or between any two commits.

Here's a short example in the shell:

1. You've modified a file named `example.txt`.
2. To see what's changed, run:
   ```bash
   $ git diff
   ```

```bash
$ git diff
diff --git a/example.txt b/example.txt
index 1c2d3e4..5f6g7h8 100644
--- a/example.txt
+++ b/example.txt
@@ -1,3 +1,4 @@
 This is an example file.
+This is a new line added to the file.
 This line did not change.
-This line will be removed.
```

This will display the changes in `example.txt` that are not yet staged. If you had already staged the changes (with `git add`), you'd use `git diff --staged` to see the differences between your staged changes and the last commit.

---

## git branch
[git branch]

`git branch` is a command in Git used to manage branches in your repository. Branches are an essential part of working with Git, allowing you to diverge from the main line of development and work on changes independently.

### Basic Usage of `git branch`:

1. **List All Branches:**
   - Just running `git branch` will list all local branches in your repository. The current branch will be marked with an asterisk and highlighted in a different color.
     ```bash
     $ git branch
       feature-x
     * main
       experiment-1
     ```
  
If you run `git branch -a` in your shell, the output lists both your local and remote branches.
 ```bash
$ git branch -a
* main
  feature-1
  bugfix
  remotes/origin/main
  remotes/origin/feature-2

     ```
2. **Create a New Branch:**
   - To create a new branch, use `git branch` followed by the name of the new branch. For example:
     ```bash
     $ git branch new-feature
     ```
   - This creates a new branch called `new-feature` but doesn't switch to it.

3. **Delete a Branch:**
   - To delete a branch, use `git branch -d` (or `git branch -D` for a force delete):
     ```bash
     $ git branch -d old-branch
     ```

4. **Renaming a Branch:**
   - To rename a branch, use `git branch -m`:
     ```bash
     $ git branch -m old-name new-name
     ```

>[!info] 
>## <span style="color:#ffc000">Notes:</span>
>- <span style="color:#d68a8a">To switch to another branch, you use</span> `git checkout branchname` <span style="color:#d68a8a">or</span> `git switch branchname`<span style="color:#d68a8a"> in newer versions of Git</span>.
>- <span style="color:#d68a8a">Creating a branch doesn't change any files in your working directory. It just creates a new pointer to the current commit.</span>
>- <span style="color:#d68a8a">Deleting a branch removes that pointer. If there are changes in the branch not merged elsewhere, it's recommended to merge or stash them before deleting the branch.</span>
>
>`git branch` <span style="color:#d68a8a">is a fundamental part of Git's branch-based workflow, allowing developers to work on features, fixes, or experiments in isolation from the main codebase.</span> 


---

## git merge
[git merge]

`git merge` is a command in Git used to merge changes from one branch into another. It's commonly used to combine the changes from a feature branch back into the main branch or to update a feature branch with changes from the main branch.

Here's how it works:

1. **Switch to the Target Branch:**
   First, you need to be on the branch you want to merge changes into. For example, to merge into `main`, you'd switch to it:
   ```bash
   $ git checkout main
   ```

2. **Merge Another Branch:**
   Then, merge the feature branch (let's say `feature-1`) into the current branch (`main`):
   ```bash
   $ git merge feature-1
   ```

This will combine the changes from `feature-1` into `main`. If there are no conflicts, the merge will complete automatically. If there are conflicts, Git will ask you to resolve them before completing the merge.

>[!info]
Merges can be fast-forward (where the branch being merged into just advances to the head of the merged branch, if there are no divergent changes) or they can create a new commit in the history that ties together the histories of both branches.

---

## git log
[git log]

`git log` displays the commit history of the current branch in your Git repository. It shows details like commit ID, author, date, and message.

### Basic Usage

1. **Standard Log:**
   Shows detailed commit history.
   ```bash
   $ git log
   commit 3a6e14e... (HEAD -> main, origin/main, origin/HEAD)
   Author: Your Name <you@example.com>
   Date:   Mon Sep 14 16:50:34 2020 -0700

       Initial commit
   ```

2. **Oneline Log:**
   Shows a simplified one-line version per commit.
   ```bash
   $ git log --oneline
   3a6e14e (HEAD -> main, origin/main, origin/HEAD) Initial commit
   ```

3. **Graph Log:**
   Shows the commit history as a graph.
   ```bash
   $ git log --graph --oneline
   * 3a6e14e (HEAD -> main, origin/main, origin/HEAD) Initial commit
   ```

These examples provide a way to visualize and understand the history of your project. The `--oneline` and `--graph` options make the output more concise and readable, especially for projects with a long history of commits.

>[!info]
>### <span style="color:#9263b6">HEAD</span>
>In Git, `HEAD` is a reference to the latest commit in the currently checked-out branch. It essentially points to where you are in your Git history. When you make new commits, `HEAD` moves forward to the latest commit. If you switch branches, `HEAD` moves to the last commit of the new branch. A "detached HEAD" means you're on a specific commit rather than a branch.

---
