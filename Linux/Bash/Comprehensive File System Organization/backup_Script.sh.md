### 1. Backup Script
This script is intended to be run as a cron job and will back up the "education" and "development" directories to a specified backup location.

```bash
#!/bin/bash

# Set the source directories for backup
EDUCATION_DIR="/home/education"
DEVELOPMENT_DIR="/home/development"

# Set the destination directory for backups
BACKUP_DEST="/mnt/external_drive/backups"

# Current date for naming the backup file
DATE=$(date +%Y-%m-%d)

# Backup function
backup_directory() {
    local source="$1"
    local dest="$2/$DATE"

    # Ensure the destination directory exists
    mkdir -p "$dest"

    # Perform the backup using tar and handle possible errors
    if tar -czf "${dest}/$(basename "$source")-$DATE.tar.gz" "$source"; then
        echo "Backup of $source completed successfully."
    else
        echo "Error: Backup failed for $source" >&2
        return 1
    fi
}

# Main execution block
main() {
    # Backup education directory
    backup_directory "$EDUCATION_DIR" "$BACKUP_DEST" || exit 1

    # Backup development directory
    backup_directory "$DEVELOPMENT_DIR" "$BACKUP_DEST" || exit 1
}

# Run the main function
main
```

**Note**: Ensure the `BACKUP_DEST` directory exists on an external drive or change it to a valid backup location. The script uses `tar` to compress and save each directory, providing basic error handling if the operation fails.

----

Let's dive into the detailed workings of the `backup_directory` function from your backup script:

### Function: `backup_directory()`
This function performs the core task of backing up a specified source directory to a target backup directory. Each step and its functionality are explained in detail below:

```bash
backup_directory() {
    local source="$1"
    local dest="$2/$DATE"
```
**Parameters**:
- `source`: The first parameter, `$1`, is the path to the directory that needs to be backed up. This is stored in the local variable `source`.
- `dest`: The second parameter, `$2`, combines with the global variable `$DATE` to create a unique backup directory for each day. This is stored in the local variable `dest`.

```bash
    # Ensure the destination directory exists
    mkdir -p "$dest"
```
**Directory Creation**:
- `mkdir -p "$dest"`: Before attempting to create the backup, the function ensures that the destination directory exists. The `-p` option allows `mkdir` to create the parent directories as needed and ensures no error is thrown if the directory already exists.

```bash
    # Perform the backup using tar and handle possible errors
    if tar -czf "${dest}/$(basename "$source")-$DATE.tar.gz" "$source"; then
        echo "Backup of $source completed successfully."
    else
        echo "Error: Backup failed for $source" >&2
        return 1
    fi
}
```
**Backup Process**:
- `tar -czf "${dest}/$(basename "$source")-$DATE.tar.gz" "$source"`: This command is used to create a compressed archive of the `source` directory.
  - `tar`: The command to archive files.
  - `-czf`: Options passed to `tar`.
    - `-c`: Create a new archive.
    - `-z`: Filter the archive through gzip for compression.
    - `-f`: Use the following string as the name of the archive.
  - `"${dest}/$(basename "$source")-$DATE.tar.gz"`: The path and name of the output file. It uses `basename` to extract the name of the source directory, appending the current date to make it unique.
  - `$source`: The directory being backed up.

**Error Handling**:
- The `if` condition checks whether the `tar` command completes successfully.
  - If successful, it prints a success message.
  - If the `tar` command fails (which could happen due to permissions issues, insufficient space, or the source directory not existing), an error message is printed to standard error (`stderr`) using `>&2`, and the function returns `1` to indicate an error.

**Flow Control**:
- The function uses `return 1` upon encountering an error to communicate failure to the calling context. This return value can trigger further error handling actions in the `main` function.

### Main Execution in `main()` Function
The `main` function orchestrates the backup process:
```bash
main() {
    # Backup education directory
    backup_directory "$EDUCATION_DIR" "$BACKUP_DEST" || exit 1

    # Backup development directory
    backup_directory "$DEVELOPMENT_DIR" "$BACKUP_DEST" || exit 1
}
```
- Each call to `backup_directory` includes a fallback action using `|| exit 1`. This means if `backup_directory` returns `1` (indicating an error), the script will immediately exit with a status of `1`. This prevents the execution of subsequent commands and signals an error condition to the environment where the script runs, which is particularly useful when monitoring cron jobs for failures.

By using these detailed and robust error-handling mechanisms, the script ensures that backups are not only attempted but their failures are appropriately reported and managed. This makes the script reliable for use in environments where backups are critical, such as in automated server maintenance and data preservation tasks.