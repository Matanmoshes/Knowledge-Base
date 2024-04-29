# `rsync` Command

[rsync]

`rsync` is a highly versatile file-copying tool used on Unix-like systems for efficiently transferring and synchronizing files across local drives, mounted file systems, or networked computers using a secure shell (SSH). It is widely appreciated for its robust feature set, including the ability to only transfer the differences between files, support for symbolic links, permissions, and much more.

![[Pasted image 20240420102837.png]]



### Basic Syntax

The basic syntax of `rsync` is as follows:

```bash
rsync [options] source destination
```

- **source**: The path to the source file(s) or directory.
- **destination**: The path to the destination where the file(s) or directory should be copied.

### Key Options

- **`-a`**, **`--archive`**: Enables archive mode, which preserves symbolic links, permissions, timestamps, and group and owner information.
- **`-v`**, **`--verbose`**: Provides detailed output of what `rsync` is doing.
- **`-z`**, **`--compress`**: Compresses file data during the transfer, which is useful over slow connections.
- **`-r`**, **`--recursive`**: Recursively copies entire directories.
- **`--delete`**: Deletes files from the destination that are not present on the source.
- **`-e`**, **`--rsh=COMMAND`**: Specifies the remote shell to use; often used to specify SSH.
- **`--exclude`**: Excludes files that match the pattern.
- **`--include`**: Includes files that match the pattern, useful with `--exclude`.
- **`-n`**, **`--dry-run`**: Performs a trial run with no changes made.

### Common Usage Scenarios

#### 1. **Local File Transfer**
Copying data from one directory to another on the same machine can be done using:

```bash
rsync -av /path/to/source/ /path/to/destination/
```

This command uses `-a` for archive mode to ensure that all characteristics of the files are preserved and `-v` for verbose output.

#### 2. **Remote File Transfer**
To copy data from a local machine to a remote server:

```bash
rsync -avz -e ssh /path/to/source/ user@remote_host:/path/to/destination/
```

This adds `-z` to compress the data during transfer and `-e ssh` to use SSH for secure data transfer.

#### 3. **Synchronizing Files**
To synchronize the contents of a local directory with a remote directory, ensuring that the remote directory exactly matches the local directory:

```bash
rsync -avz --delete /path/to/source/ user@remote_host:/path/to/destination/
```

The `--delete` option ensures that files not present in the source are removed from the destination, making both directories identical.

#### 4. **Using Include and Exclude**
To transfer files while excluding some types of files:

```bash
rsync -av --exclude '*.tmp' /path/to/source/ /path/to/destination/
```

To include specific files while excluding others:

```bash
rsync -av --include '*.txt' --exclude '*' /path/to/source/ /path/to/destination/
```

### Tips and Best Practices

- **Use Absolute Paths**: Especially when scripting, to avoid errors related to relative paths.
- **Check Firewall and Network Settings**: Ensure that any required ports (like SSH's port 22) are open when syncing over a network.
- **Regularly Test**: Regularly test your `rsync` commands using the `--dry-run` option to ensure they perform as expected without actually transferring files.


