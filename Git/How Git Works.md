

### 1. Git Basics
Git is a distributed version control system. It helps you track changes to your files, collaborate with others, and maintain a history of your work. Git operates primarily in three states:

1. **Working Directory**: The files you see and work with in your file system.
2. **Staging Area (Index)**: A staging area where changes are prepared before committing.
3. **Repository (.git directory)**: The database where commits and historical changes are stored.

### 2. `git add` and `git commit`

**`git add`**:
- When you add a file using `git add`, Git takes a snapshot of the changes you've made to the file and stores it in the staging area (also known as the index).

```bash
git add <file>
```

**`git commit`**:
- When you commit, Git takes the staged changes and records them in the repository.
- The commit consists of a snapshot of the changes along with metadata (author, date, message).
- The commit also stores a reference to the parent commit(s), forming a chain (or DAG) of commits.

```bash
git commit -m "Commit message"
```

### 3. Git Objects and Hashes
Git stores data in a few fundamental object types in its internal database:

1. **Blob (Binary Large Object)**: Represents the contents of a file.
2. **Tree**: Represents a directory and contains pointers to blobs and other trees.
3. **Commit**: Contains metadata and a pointer to a tree object, representing the state of the project at a point in time.

Every object in Git is identified by a SHA-1 hash, a 40-character hexadecimal string, which ensures the integrity and uniqueness of each object. For example, a commit might have a hash like `3a6e8d7a4a0eae1236bdf2a63c9a7d4f8aabc123`.

### 4. `git rebase`

**Rebase**:
- Rebasing involves moving or combining a series of commits to a new base commit.
- Instead of merging, which creates a new merge commit, rebase rewrites the commit history.

**Process**:
1. **Detach Commits**: Git temporarily detaches the commits in your current branch that are not in the target branch.
2. **Reapply Commits**: Git replays each of these commits onto the target branch one by one.
3. **Resolve Conflicts**: If there are conflicts, Git pauses and asks you to resolve them.
4. **Finalize**: Once all commits are reapplied, Git points the current branch to the new commit history.

```bash
git rebase <branch>
```

Example:

```bash
git rebase main
```

#### Before Rebase:

```c
A---B---C feature
     \
      D---E---F main
```

#### After Rebase (rebase feature onto main):

```c
            A'--B'--C' feature
           /
D---E---F main
```

### 5. `git reset`

**Reset**:
- Reset is used to undo changes by moving the HEAD and possibly the index and working directory to a different commit.

**Types of Reset**:
1. **Soft Reset (`--soft`)**: Moves the HEAD to a different commit, but leaves the index and working directory unchanged. Staged changes remain staged.

```bash
git reset --soft <commit>
```

2. **Mixed Reset (`--mixed`)**: Moves the HEAD to a different commit and resets the index, but leaves the working directory unchanged. Changes are unstaged.

```bash
git reset --mixed <commit>
```

3. **Hard Reset (`--hard`)**: Moves the HEAD to a different commit and resets the index and working directory. All changes are discarded.

```bash
git reset --hard <commit>
```

Example:

```bash
git reset --hard HEAD~1
```

#### Before Reset (on commit F):

```c
A---B---C---D---E---F main
```

#### After `git reset --hard D`:

```c
A---B---C---D main
```

### 6. `git cherry-pick`

**Cherry-pick**:
- Cherry-picking is the process of picking a commit from a branch and applying it onto another branch.

```bash
git cherry-pick <commit>
```

Example:

```bash
git cherry-pick 3a6e8d7a4a0eae1236bdf2a63c9a7d4f8aabc123
```

### 7. `git merge`

**Merge**:
- Merging combines the changes from two branches into one.

```bash
git merge <branch>
```

Example:

```bash
git merge feature-branch
```

#### Fast-Forward Merge:

``` c
A---B---C feature
     \
      D---E---F main
```

After `git merge feature`:

```c
A---B---C---D---E---F main/feature
```

#### Recursive Merge:

```c
A---B---C feature
     \
      D---E---F main
```

After `git merge feature` (with merge commit M):

```c
A---B---C------M main
     \       /
      D---E---F feature
```

### 8. Other Useful Commands

**`git stash`**:
- Stashes your uncommitted changes, allowing you to work on a clean slate and reapply the changes later.

```bash
git stash
git stash apply
```

**`git tag`**:
- Tags are used to mark specific points in history as being important.

```bash
git tag <tagname> <commit>
```

Example:

```bash
git tag v1.0.0 3a6e8d7a4a0eae1236bdf2a63c9a7d4f8aabc123
```

**`git log`**:
- Shows the commit history.

```bash
git log
```

