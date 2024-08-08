
>[!What is "git rebase"?]
>
>- **Rebasing**: Reapply commits on top of another base tip. Instead of merging branches together, rebasing effectively moves the entire branch to start at a new base commit.
>- **Linear History**: "git rebase" can create a linear project history, making it easier to understand and review changes.

### Basic Concepts

1. **Base Commit**: The commit where a branch was originally created.
2. **New Base Commit**: The commit where you want to rebase your branch onto.
3. **Upstream**: The branch onto which you are rebasing your changes.
4. **Feature Branch**: The branch that you are rebasing.

### Basic Usage

1. **Rebasing the Current Branch onto Another Branch**:
   ```sh
   git rebase branch_name
   ```
   This replays the commits from the current branch onto `branch_name`.

2. **Rebasing to a Specific Commit**:
   ```sh
   git rebase commit_sha
   ```
   This replays the commits from the current branch onto the specified commit.

### Practical Examples

1. **Rebasing a Feature Branch onto Master**:
   ```sh
   git checkout feature-branch
   git rebase master
   ```

2. **Interactive Rebase**:
   ```sh
   git rebase -i HEAD~n
   ```
   This allows you to interactively rebase the last `n` commits. You can edit, reorder, squash, or drop commits during the interactive rebase.

### Interactive Rebase Options

When you run an interactive rebase, a text editor opens with a list of commits. Each commit line starts with a command that you can modify:

- **pick**: Use the commit as is.
- **reword**: Use the commit but edit the commit message.
- **edit**: Use the commit but stop for amending.
- **squash (s)**: Combine this commit with the previous one.
- **fixup (f)**: Like squash, but discard this commit’s message.
- **drop (d)**: Remove the commit.

### Advanced Options

- **Preserve Merges**:
  ```sh
  git rebase -p
  ```
  This preserves merges when rebasing a branch.

- **Continue a Rebase**:
  ```sh
  git rebase --continue
  ```
  After resolving conflicts, use this to continue the rebase process.

- **Abort a Rebase**:
  ```sh
  git rebase --abort
  ```
  This aborts the rebase process and returns to the state before rebase began.

- **Skip a Commit**:
  ```sh
  git rebase --skip
  ```
  Skips the commit that caused the conflict and continues with the rebase process.

### Common Use Cases

1. **Cleaning Up Commit History**:
   - Squashing multiple commits into one.
   - Rewording commit messages.
   - Reordering commits for logical grouping.

2. **Integrating Changes**:
   - Bringing changes from the main branch into a feature branch without creating a merge commit.

3. **Resolving Divergent Branch Histories**:
   - Rebasing to align branch histories for cleaner integration.


