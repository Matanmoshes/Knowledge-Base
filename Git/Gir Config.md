
### Levels of Configuration

1. **System Level**:
   - Applies to all users on the system and all their repositories.
   - Configuration file: `/etc/gitconfig`.
   ```sh
   git config --system
   ```

2. **Global Level**:
   - Applies to all repositories for the current user.
   - Configuration file: `~/.gitconfig` or `~/.config/git/config`.
   ```sh
   git config --global
   ```

3. **Local Level**:
   - Applies only to the specific repository where the command is run.
   - Configuration file: `.git/config` in the repository’s directory.
   ```sh
   git config --local
   ```

### Basic Usage

1. **Setting a Configuration Value**:
   ```sh
   git config [--system | --global | --local] <key> <value>
   ```
   For example, to set the user email globally:
   ```sh
   git config --global user.email "you@example.com"
   ```

2. **Getting a Configuration Value**:
   ```sh
   git config [--system | --global | --local] --get <key>
   ```
   For example, to get the global user email:
   ```sh
   git config --global --get user.email
   ```

3. **Listing All Configuration Settings**:
   ```sh
   git config [--system | --global | --local] --list
   ```
   For example, to list all global settings:
   ```sh
   git config --global --list
   ```

4. **Unsetting a Configuration Value**:
   ```sh
   git config [--system | --global | --local] --unset <key>
   ```
   For example, to unset the global user email:
   ```sh
   git config --global --unset user.email
   ```

### Common Configuration Options

1. **User Information**:
   ```sh
   git config --global user.name "Your Name"
   git config --global user.email "you@example.com"
   ```

2. **Default Text Editor**:
   ```sh
   git config --global core.editor "code --wait"  # VS Code
   git config --global core.editor "nano"         # Nano
   git config --global core.editor "vim"          # Vim
   ```

3. **Default Merge Tool**:
   ```sh
   git config --global merge.tool vimdiff
   ```

4. **Enabling Color Output**:
   ```sh
   git config --global color.ui auto
   ```

5. **Aliasing Commands**:
   ```sh
   git config --global alias.co checkout
   git config --global alias.br branch
   git config --global alias.ci commit
   git config --global alias.st status
   ```

### <span style="color:#ff0000">Storing Credentials</span>

The `credential.helper` setting in Git helps you manage your credentials for accessing remote repositories. The `store` option saves your credentials in a plain-text file, which is not the most secure but can be convenient for certain use cases.

1. **Set Credential Helper to Store**:
   ```sh
   git config --global credential.helper store
   ```
   This command configures Git to store your credentials in a plain-text file in your home directory (`~/.git-credentials`).

2. **Using Credential Store**:
   - The next time you perform an operation that requires authentication, Git will prompt you for your username and password, and then store these credentials in the file.
   - Future operations will use these stored credentials automatically, avoiding repeated prompts.

3. **Security Considerations**:
   - Since credentials are stored in plain text, this method is not secure. Use it only if you are sure that your system is secure.
   - For more secure credential storage, consider using `git credential.helper cache` (temporarily stores credentials in memory) or platform-specific helpers like `osxkeychain` for macOS or `wincred` for Windows.

### Example Workflow

1. **Set Up User Information**:
   ```sh
   git config --global user.name "John Doe"
   git config --global user.email "john.doe@example.com"
   ```

2. **Configure Default Editor**:
   ```sh
   git config --global core.editor "code --wait"
   ```

3. **Enable Color Output**:
   ```sh
   git config --global color.ui auto
   ```

4. **Alias Common Commands**:
   ```sh
   git config --global alias.co checkout
   git config --global alias.br branch
   git config --global alias.ci commit
   git config --global alias.st status
   ```

5. **Store Credentials**:
   ```sh
   git config --global credential.helper store
   ```

6. **Verify Configuration**:
   ```sh
   git config --global --list
   ```

### Conclusion

The `git config` command is a versatile tool for managing your Git environment. By configuring your user information, default editor, color settings, command aliases, and credential storage, you can tailor Git to your preferences and streamline your workflow. Remember to be cautious with storing credentials, especially when using the `store` option, and consider more secure methods if necessary.