### Git Merge

The `git merge` command is used to integrate changes from one branch into another. It is one of the key tools for collaboration in Git, allowing multiple developers to work on different features or fixes and then combine their efforts.

### Basic Concepts

- **Branches**: In Git, branches are pointers to snapshots of your changes. The default branch is usually called `main` or `master`.
- **Merge**: Combining the changes from one branch into another branch.

### Types of Merges

1. **Fast-Forward Merge**:
   - Occurs when the branch being merged has no additional commits compared to the branch you're merging into.
   - Simply moves the branch pointer forward.

2. **Three-Way Merge**:
   - Occurs when the branches have diverged.
   - Uses a common ancestor commit to create a new merge commit that combines the changes from both branches.

### Basic Merge Command

To merge changes from one branch (e.g., `feature-branch`) into the current branch:

```bash
git merge feature-branch
```

### Handling Merge Conflicts

Sometimes changes from different branches conflict. In such cases, Git will stop and allow you to resolve the conflicts manually.

1. **Identify Conflicts**:
   - Git marks the conflicts in the affected files.
   - Conflict markers look like this:
```diff
     <<<<<<< HEAD
     Your changes.
     =======
     Changes from the other branch.
     >>>>>>> feature-branch
```

2. **Resolve Conflicts**:
   - Edit the conflicting files to resolve conflicts.
   - Remove the conflict markers.
   - Add the resolved files to the staging area:
     ```bash
     git add <file>
     ```

3. **Complete the Merge**:
   - After resolving all conflicts, complete the merge with:
     ```bash
     git commit
     ```

### Useful Merge Options

1. **Fast-Forward Merge**:
   - By default, Git will use a fast-forward merge if possible.
   - To explicitly disable fast-forward and create a merge commit, use:
     ```bash
     git merge --no-ff feature-branch
     ```

2. **Squash Merge**:
   - Combines all the commits from the feature branch into a single commit on the target branch.
   - Useful for keeping the history clean.
   ```bash
   git merge --squash feature-branch
   ```

3. **Ours and Theirs**:
   - To favor changes from your branch in conflicts, use:
     ```bash
     git merge -X ours feature-branch
     ```
   - To favor changes from the other branch in conflicts, use:
     ```bash
     git merge -X theirs feature-branch
     ```

4. **Abort Merge**:
   - If a merge goes wrong, you can abort it and return to the pre-merge state:
     ```bash
     git merge --abort
     ```

### Advanced Merging Techniques

1. **Rebase Before Merging**:
   - Rebasing can create a cleaner project history by moving the entire feature branch to begin on the tip of the main branch.
   - First, rebase the feature branch:
     ```bash
     git checkout feature-branch
     git rebase main
     ```
   - Then, merge:
     ```bash
     git checkout main
     git merge feature-branch
     ```

2. **Conflict Markers**:
   - During a conflict, Git will insert markers in the affected files:
```diff
     <<<<<<< HEAD
     Content from the base branch.
     =======
     Content from the feature branch.
     >>>>>>> feature-branch
```

### Merge Strategies

1. **Recursive**:
   - Default merge strategy used by Git.
   - Handles most merge scenarios, including resolving conflicts by performing a three-way merge.

2. **Ours**:
   - Ignores all changes from the other branch, keeping your branch as the final state.
   ```bash
   git merge -s ours feature-branch
   ```

3. **Octopus**:
   - Used for merging more than two branches simultaneously.
   ```bash
   git merge branch1 branch2 branch3
   ```

### Example Merge Workflow

1. **Create and Switch to a New Branch**:
   ```bash
   git checkout -b feature-branch
   ```

2. **Make Some Changes and Commit**:
   ```bash
   git add .
   git commit -m "Implement new feature"
   ```

3. **Switch Back to Main Branch**:
   ```bash
   git checkout main
   ```

4. **Merge the Feature Branch into Main**:
   ```bash
   git merge feature-branch
   ```

5. **Resolve Any Conflicts** (if any):
   - Edit the conflicting files to resolve conflicts.
   - Stage the resolved files:
     ```bash
     git add <resolved-file>
     ```
   - Commit the merge:
     ```bash
     git commit
     ```

### Conclusion

`git merge` is a powerful command that allows you to combine work from different branches in a Git repository. Understanding how to handle merges and resolve conflicts is essential for collaborating effectively with others. By mastering different merge strategies and options, you can maintain a clean and manageable project history.