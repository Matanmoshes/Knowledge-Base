### Task Set 1: HEAD, Remote, Revision, and Tag

#### Task: Setting Up and Managing a Remote Repository

1. **Initialize a New Repository:**
   - Create a new local Git repository and make three initial commits with meaningful changes.
   - Use `git show HEAD`, `git show HEAD~1`, and `git show HEAD~2` to display the commit details.

   **Solution:**
   ```sh
   mkdir my-project
   cd my-project
   git init
   ```
   *Explanation: Initializes a new Git repository in the `my-project` directory.*

   ```sh
   echo "Initial commit" > file1.txt
   git add file1.txt
   git commit -m "First commit: Add file1.txt"
   ```
   *Explanation: Creates a new file `file1.txt`, stages it, and commits it with a message.*

   ```sh
   echo "Second commit" > file2.txt
   git add file2.txt
   git commit -m "Second commit: Add file2.txt"
   ```
   *Explanation: Repeats the process for `file2.txt`.*

   ```sh
   echo "Third commit" > file3.txt
   git add file3.txt
   git commit -m "Third commit: Add file3.txt"
   ```
   *Explanation: Repeats the process for `file3.txt`.*

   ```sh
   git show HEAD
   git show HEAD~1
   git show HEAD~2
   ```
   *Explanation: Displays the details of the latest commit, the previous commit, and the second previous commit.*

   **Visualization:**
   ```mathematica
   A---B---C (main)
   ```

2. **Add and Push to a Remote Repository:**
   - Create a new repository on GitHub.
   - Add the remote repository using `git remote add origin <url>`.
   - Push the local commits to the remote repository.
   - Verify the push by checking the commit history on GitHub.

   **Solution:**
   ```sh
   git remote add origin https://github.com/username/my-project.git
   git branch -M main
   git push -u origin main
   ```
   *Explanation: Adds the GitHub repository as a remote named `origin`, renames the default branch to `main`, and pushes the local commits to the remote repository.*

   **Visualization:**
   ```mathematica
   A---B---C (main, origin/main)
   ```

3. **Tagging Commits:**
   - Create two tags in your local repository: one lightweight tag and one annotated tag.
   - Push the tags to the remote repository.
   - Verify the tags on GitHub and read about the differences between lightweight and annotated tags.

   **Solution:**
   ```sh
   git tag v1.0
   ```
   *Explanation: Creates a lightweight tag named `v1.0`.*

   ```sh
   git tag -a v1.1 -m "Version 1.1"
   ```
   *Explanation: Creates an annotated tag named `v1.1` with a message "Version 1.1".*

   ```sh
   git push origin v1.0
   git push origin v1.1
   ```
   *Explanation: Pushes both tags to the remote repository.*

   **Visualization:**
   ```mathematica
   A---B---C (main, origin/main)
        |     |
       v1.0  v1.1
   ```

### Task Set 2: gitignore, Log, Reset, and Restore

#### Task: Managing Ignored Files and Undoing Changes

1. **Create and Use .gitignore:**
   - Create a project with a few directories and files, including some log files and temporary files.
   - Create a `.gitignore` file to ignore all log files and temporary files.
   - Commit the changes and verify that the ignored files are not tracked.
   - Push the changes to the remote repository.

   **Solution:**
   ```sh
   mkdir my-project
   cd my-project
   echo "Log files" > log1.log
   echo "Temporary files" > temp1.tmp
   mkdir src
   echo "Source code" > src/code1.py
   ```
   *Explanation: Sets up a project with log files, temporary files, and a source code file.*

   ```sh
   echo "*.log" > .gitignore
   echo "*.tmp" >> .gitignore
   ```
   *Explanation: Creates a `.gitignore` file to ignore all `.log` and `.tmp` files.*

   ```sh
   git init
   git add .
   git commit -m "Initial commit with .gitignore"
   ```
   *Explanation: Initializes the Git repository, stages all files except those ignored, and commits them.*

   ```sh
   git remote add origin https://github.com/username/my-project.git
   git branch -M main
   git push -u origin main
   ```
   *Explanation: Adds the remote repository, sets the branch name to `main`, and pushes the commits.*

   **Visualization:**
   ```mathematica
   A (main, origin/main)
   ```

2. **Analyze Commit History:**
   - Make a series of five more commits to your project.
   - Use `git log --oneline`, `git log --graph`, and `git log -p` to explore the commit history.

   **Solution:**
   ```sh
   echo "Update 1" > src/code2.py
   git add .
   git commit -m "Update 1: Add code2.py"
   ```
   *Explanation: Creates `code2.py`, stages it, and commits it.*

   ```sh
   echo "Update 2" > src/code3.py
   git add .
   git commit -m "Update 2: Add code3.py"
   ```
   *Explanation: Repeats the process for `code3.py`.*

   ```sh
   echo "Update 3" > src/code4.py
   git add .
   git commit -m "Update 3: Add code4.py"
   ```
   *Explanation: Repeats the process for `code4.py`.*

   ```sh
   echo "Update 4" > src/code5.py
   git add .
   git commit -m "Update 4: Add code5.py"
   ```
   *Explanation: Repeats the process for `code5.py`.*

   ```sh
   echo "Update 5" > src/code6.py
   git add .
   git commit -m "Update 5: Add code6.py"
   ```
   *Explanation: Repeats the process for `code6.py`.*

   ```sh
   git log --oneline
   git log --graph
   git log -p
   ```
   *Explanation: Displays a condensed commit history, a graphical commit history, and detailed commit changes, respectively.*

   **Visualization:**
   ```mathematica
   A---B---C---D---E---F (main, origin/main)
   ```

3. **Undo Changes with Reset and Restore:**
   - Make a few additional commits to the project.
   - Use `git reset --soft HEAD~1` to reset the last commit but keep the changes staged.
   - Use `git reset --hard HEAD~1` to reset the last commit and discard the changes.
   - Modify a few files in your project and stage the changes.
   - Use `git restore --staged <file>` to unstage a specific file.
   - Use `git restore <file>` to discard changes in a specific file.

   **Solution:**
   ```sh
   echo "Additional commit 1" > src/code7.py
   git add .
   git commit -m "Additional commit 1: Add code7.py"
   ```
   *Explanation: Creates `code7.py`, stages it, and commits it.*

   ```sh
   echo "Additional commit 2" > src/code8.py
   git add .
   git commit -m "Additional commit 2: Add code8.py"
   ```
   *Explanation: Repeats the process for `code8.py`.*

   **Visualization:**
   ```mathematica
   A---B---C---D---E---F---G---H (main, origin/main)
   ```

   ```sh
   git reset --soft HEAD~1
   ```
   *Explanation: Resets the last commit but keeps the changes staged.*

   **Visualization:**
   ```mathematica
   A---B---C---D---E---F---G (main)
                        \
                         H (staged changes)
   ```

   ```sh
   git reset --hard HEAD~1
   ```
   *Explanation: Resets the last commit and discards the changes.*

   **Visualization:**
   ```mathematica
   A---B---C---D---E---F---G (main)
   ```

   ```sh
   echo "Modify code2" > src/code2.py
   echo "Modify code3" > src/code3.py
   git add src/code2.py src/code3.py
   ```
   *Explanation: Modifies `code2.py` and `code3.py`, then stages the changes.*

   **Visualization:**
   ```mathematica
   A---B---C---D---E---F---G (main)
                        \
                         I (staged changes)
   ```

   ```sh
   git restore --staged src/code2.py
   ```
   *Explanation: Unstages the changes in `code2.py`.*

   **Visualization:**
   ```mathematica
   A

---B---C---D---E---F---G (main)
                        \
                         I' (partially staged changes)
   ```

   ```sh
   git restore src/code3.py
   ```
   *Explanation: Discards the changes in `code3.py`.*

   **Visualization:**
   ```mathematica
   A---B---C---D---E---F---G (main)
                        \
                         I'' (staged changes for other files)
   ```

### Task Set 3: Revert, Stash, Switch, and Rebase

#### Task: Managing Changes and Branches

1. **Undo Changes with Revert:**
   - Make a few more commits in your project.
   - Use `git revert <commit_hash>` to create a commit that undoes the changes of a specific commit.
   - Push the changes to the remote repository.
   - Read about how `git revert` differs from `git reset`.

   **Solution:**
   ```sh
   echo "Revert commit 1" > src/code9.py
   git add .
   git commit -m "Revert commit 1: Add code9.py"
   ```
   *Explanation: Creates `code9.py`, stages it, and commits it.*

   ```sh
   echo "Revert commit 2" > src/code10.py
   git add .
   git commit -m "Revert commit 2: Add code10.py"
   ```
   *Explanation: Repeats the process for `code10.py`.*

   ```sh
   git revert <commit_hash>
   git push origin main
   ```
   *Explanation: Reverts a specific commit by its hash and pushes the changes to the remote repository.

   **Visualization:**
   ```mathematica
   A---B---C---D---E---F---G---H (main)
                             \
                              I (revert commit)
   ```

   **Explanation**:
   - `git revert` creates a new commit that undoes the changes of a previous commit, without altering the commit history, unlike `git reset` which changes the commit history.

2. **Stashing and Restoring Changes:**
   - Make some modifications in multiple files but don't commit them.
   - Use `git stash push -m "WIP"` to stash the changes.
   - Make a few new commits and then apply the stashed changes using `git stash pop`.

   **Solution:**
   ```sh
   echo "Stash change 1" > src/code11.py
   echo "Stash change 2" > src/code12.py
   ```
   *Explanation: Modifies `code11.py` and `code12.py`.*

   ```sh
   git stash push -m "WIP"
   ```
   *Explanation: Stashes the current changes with a message "WIP".*

   ```sh
   echo "New commit after stash 1" > src/code13.py
   git add .
   git commit -m "New commit after stash 1: Add code13.py"
   ```
   *Explanation: Creates `code13.py`, stages it, and commits it.*

   ```sh
   echo "New commit after stash 2" > src/code14.py
   git add .
   git commit -m "New commit after stash 2: Add code14.py"
   ```
   *Explanation: Repeats the process for `code14.py`.*

   ```sh
   git stash pop
   ```
   *Explanation: Applies the stashed changes back to the working directory.*

   **Visualization:**
   ```mathematica
   A---B---C---D---E---F---G---H---I---J (main)
                        \             /
                         K---L---M---N (stashed changes applied)
   ```

3. **Switching Branches:**
   - Create a new branch and switch to it using `git switch <branch_name>`.
   - Make a few commits on this new branch.
   - Switch back to the main branch and make another commit.

   **Solution:**
   ```sh
   git switch -c new-branch
   ```
   *Explanation: Creates and switches to a new branch named `new-branch`.*

   ```sh
   echo "New branch commit 1" > src/code15.py
   git add .
   git commit -m "New branch commit 1: Add code15.py"
   ```
   *Explanation: Creates `code15.py`, stages it, and commits it.*

   ```sh
   echo "New branch commit 2" > src/code16.py
   git add .
   git commit -m "New branch commit 2: Add code16.py"
   ```
   *Explanation: Repeats the process for `code16.py`.*

   ```sh
   git switch main
   ```
   *Explanation: Switches back to the main branch.*

   ```sh
   echo "Main branch commit" > src/code17.py
   git add .
   git commit -m "Main branch commit: Add code17.py"
   ```
   *Explanation: Creates `code17.py`, stages it, and commits it.*

   **Visualization:**
   ```mathematica
   A---B---C---D---E---F---G---H---I---J (main)
                        \                   \
                         K---L---M---N (new-branch)
   ```

4. **Rebasing a Branch:**
   - Create another branch from the main branch.
   - Make a few commits on both branches.
   - Use `git rebase main` on the new branch to reapply its commits on top of the main branch.
   - Resolve any conflicts that arise.

   **Solution:**
   ```sh
   git switch -c another-branch
   ```
   *Explanation: Creates and switches to another new branch named `another-branch`.*

   ```sh
   echo "Another branch commit 1" > src/code18.py
   git add .
   git commit -m "Another branch commit 1: Add code18.py"
   ```
   *Explanation: Creates `code18.py`, stages it, and commits it.*

   ```sh
   echo "Another branch commit 2" > src/code19.py
   git add .
   git commit -m "Another branch commit 2: Add code19.py"
   ```
   *Explanation: Repeats the process for `code19.py`.*

   ```sh
   git switch main
   ```
   *Explanation: Switches back to the main branch.*

   ```sh
   echo "Main branch new commit" > src/code20.py
   git add .
   git commit -m "Main branch new commit: Add code20.py"
   ```
   *Explanation: Creates `code20.py`, stages it, and commits it.*

   ```sh
   git switch another-branch
   git rebase main
   ```
   *Explanation: Switches to `another-branch` and rebases it onto the main branch, reapplying its commits on top of the main branch. If conflicts arise, they must be resolved to complete the rebase.*

   **Visualization:**
   ```mathematica
   A---B---C---D---E---F---G---H---I---J (main)
                        \                 \
                         K---L---M---N (another-branch rebased)
   ```

### Task Set 4: Blame, Clean, Bisect, and Cherry-pick

#### Task: Analyzing Changes and Managing Commits

1. **Using Git Blame:**
   - Modify some lines in different files across multiple commits.
   - Use `git blame <file>` to see the changes and their respective authors.

   **Solution:**
   ```sh
   echo "Blame test 1" > src/code21.py
   git add .
   git commit -m "Blame test 1: Add code21.py"
   ```
   *Explanation: Creates `code21.py`, stages it, and commits it.*

   ```sh
   echo "Blame test 2" > src/code21.py
   git add .
   git commit -m "Blame test 2: Modify code21.py"
   ```
   *Explanation: Modifies `code21.py`, stages it, and commits it.*

   ```sh
   git blame src/code21.py
   ```
   *Explanation: Uses `git blame` to show the last modification made to each line of `code21.py` along with the author.*

   **Visualization:**
   ```mathematica
   A---B---C---D---E---F---G---H---I---J (main)
   ```

2. **Cleaning the Working Directory:**
   - Create some untracked files and directories in your project.
   - Use `git clean -n` to preview the files that will be removed.
   - Use `git clean -f` to forcefully remove the untracked files.

   **Solution:**
   ```sh
   mkdir tempdir
   touch tempdir/tempfile.txt
   ```
   *Explanation: Creates an untracked directory `tempdir` with a file `tempfile.txt`.*

   ```sh
   git clean -n
   ```
   *Explanation: Previews the untracked files and directories that would be removed.*

   ```sh
   git clean -f
   ```
   *Explanation: Forcefully removes the untracked files and directories.*

   **Visualization:**
   ```mathematica
   A---B---C---D---E---F---G---H---I---J (main)
   ```

3. **Finding Bugs with Git Bisect:**
   - Intentionally introduce a bug in your project in one of the commits.
   - Use `git bisect start`, `git bisect bad`, and `git bis

ect good <commit_hash>` to find the commit that introduced the bug.

   **Solution:**
   ```sh
   echo "Bug introduction" > src/buggycode.py
   git add .
   git commit -m "Introduce a bug in code"
   ```
   *Explanation: Introduces a bug in `buggycode.py`, stages it, and commits it.*

   ```sh
   git bisect start
   git bisect bad
   git bisect good <commit_hash>
   ```
   *Explanation: Starts the bisect process, marks the current commit as bad, and a known good commit using its hash. Git will now guide through a binary search to find the commit that introduced the bug.*

   ```sh
   # Follow the bisect steps to identify the bad commit
   git bisect reset
   ```
   *Explanation: Completes the bisect process and resets the repository to its original state.*

   **Visualization:**
   ```mathematica
   A---B---C---D---E---F---G---H---I---J (main)
                             \
                              K (bug introduced)
   ```

4. **Applying Changes with Git Cherry-pick:**
   - Create a new branch and make a few commits.
   - Use `git cherry-pick <commit_hash>` to apply a specific commit from the main branch to the new branch.
   - Push the changes and verify the commit history.

   **Solution:**
   ```sh
   git switch -c feature-branch
   ```
   *Explanation: Creates and switches to a new branch named `feature-branch`.*

   ```sh
   echo "Feature branch commit 1" > src/code22.py
   git add .
   git commit -m "Feature branch commit 1: Add code22.py"
   ```
   *Explanation: Creates `code22.py`, stages it, and commits it.*

   ```sh
   git switch main
   ```
   *Explanation: Switches back to the main branch.*

   ```sh
   echo "Main branch specific commit" > src/code23.py
   git add .
   git commit -m "Main branch specific commit: Add code23.py"
   ```
   *Explanation: Creates `code23.py`, stages it, and commits it.*

   ```sh
   git switch feature-branch
   git cherry-pick <commit_hash>
   ```
   *Explanation: Switches to `feature-branch` and cherry-picks a specific commit from the main branch using its hash. This applies the changes of the selected commit to the current branch.*

   ```sh
   git push origin feature-branch
   ```
   *Explanation: Pushes the commits in `feature-branch` to the remote repository.*

   **Visualization:**
   ```mathematica
   A---B---C---D---E---F---G---H---I---J---K (main)
                        \                   \
                         L---M---N (feature-branch with cherry-pick)
   ```

