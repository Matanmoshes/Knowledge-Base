### Git Push: Comprehensive Guide

The `git push` command is used to upload local repository content to a remote repository. Pushing is how you transfer commits from your local repository to a remote one.

### Basic Usage

To push changes from your local repository to the remote repository, use:

```bash
git push <remote-name> <branch-name>
```

For example, to push the changes on your `main` branch to the remote repository named `origin`:

```bash
git push origin main
```

### Common Options

1. **Set Upstream (`--set-upstream`, `-u`)**:
   - Used when pushing a branch for the first time.
   ```bash
   git push --set-upstream origin <branch-name>
   ```

2. **Force Push (`--force`, `-f`)**:
   - Overrides remote changes. Use with caution as it can overwrite commits on the remote branch.
   ```bash
   git push --force origin <branch-name>
   ```

3. **All Branches (`--all`)**:
   - Pushes all branches to the remote repository.
   ```bash
   git push --all
   ```

4. **Delete a Remote Branch**:
   - Deletes a branch on the remote repository.
   ```bash
   git push origin --delete <branch-name>
   ```

5. **Tags**:
   - Push a single tag:
     ```bash
     git push origin <tagname>
     ```
   - Push all tags:
     ```bash
     git push origin --tags
     ```

### Example Workflows

#### Pushing a New Branch

When you create a new branch and want to push it to the remote repository:

1. **Create and Switch to the New Branch**:
   ```bash
   git checkout -b new-branch
   ```

2. **Push the New Branch and Set Upstream**:
   ```bash
   git push -u origin new-branch
   ```

#### Pushing After Committing Changes

1. **Make Some Changes and Commit**:
   ```bash
   git add .
   git commit -m "Implement new feature"
   ```

2. **Push the Changes to the Remote Repository**:
   ```bash
   git push origin main
   ```

### Handling Authentication

When pushing to a remote repository, you might need to authenticate. This can be done using:

- **Username and Password**: 
  ```bash
  git push https://username@github.com/username/repository.git
  ```
- **SSH Key**:
  Configure SSH keys for seamless authentication.

### Troubleshooting

#### Push Rejected

If your push is rejected, it could be due to the remote branch having changes that your local branch does not. You can:

1. **Fetch and Merge**:
   ```bash
   git fetch origin
   git merge origin/main
   git push origin main
   ```

2. **Rebase and Push**:
   ```bash
   git pull --rebase origin main
   git push origin main
   ```

#### Conflicts

If there are conflicts when trying to push, resolve them locally before pushing again.

### Advanced Options

1. **Push to a Specific Repository**:
   ```bash
   git push https://github.com/username/repository.git
   ```

2. **Push with Verbose Output**:
   ```bash
   git push --verbose
   ```

3. **Dry Run**:
   - Simulate the push to see what would happen without actually pushing.
   ```bash
   git push --dry-run
   ```

4. **Clean Up and Push the Changes** (clean up the repository):
```Shell
git reflog expire --expire=now --all 
git gc --prune=now --aggressive
```


