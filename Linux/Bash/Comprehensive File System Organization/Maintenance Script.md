### Maintenance Script
This script cleans up temporary files and old logs, with a configurable retention period.

```bash
#!/bin/bash

# Paths to directories containing temporary files and logs
TEMP_DIR="/tmp"
LOG_DIR="/var/log"

# File retention policy: files older than 30 days will be deleted
RETENTION_DAYS=30

# Cleanup function to delete old files
cleanup_old_files() {
    local target_directory="$1"
    local days="$2"

    # Find and delete files older than a certain number of days in the specified directory
    # Handles errors such as permission issues or missing files
    find "$target_directory" -type f -mtime +"$days" -exec rm -f {} + 2>/dev/null

    if [ $? -eq 0 ]; then
        echo "Successfully cleaned up files older than $days days in $target_directory."
    else
        echo "Error occurred during cleanup in $target_directory." >&2
        return 1
    fi
}

# Main execution block
main() {
    # Cleanup temporary files
    cleanup_old_files "$TEMP_DIR" "$RETENTION_DAYS" || exit 1

    # Cleanup log files
    cleanup_old_files "$LOG_DIR" "$RETENTION_DAYS" || exit 1
}

# Run the main function
main
```

**Explanation**:
- **Retention Days**: The number of days after which files are considered old and eligible for deletion is set to 30, but can be adjusted.
- **Error Handling**: Errors, such as trying to delete files without sufficient permissions or missing files, are redirected to `/dev/null`, and the script checks for errors after attempting cleanup.

### Setting up Cron Jobs
To schedule these scripts to run automatically, you would add entries to the `crontab`:

1. Open the crontab with the command `crontab -e`.
2. Add lines similar to the following to schedule your scripts:

   ```bash
   # Backup script to run every day at 1 AM
   0 1 * * * /path/to/backup_script.sh

   # Maintenance script to run every Sunday at 2 AM
   0 2 * * 0 /path/to/maintenance_script.sh
   ```

Make sure the scripts are executable (`chmod +x script_name.sh`) and test them manually before setting them up in cron to ensure they work as expected.


---


Let’s examine the `cleanup_old_files` function from your maintenance script. This function is designed to delete files that are older than a specified number of days from a given directory, focusing on system maintenance through the removal of outdated temporary files and logs.

### Function: `cleanup_old_files()`

```bash
cleanup_old_files() {
    local target_directory="$1"
    local days="$2"
```

**Local Variables**:
- `target_directory`: This local variable holds the directory path from which old files will be removed. It's passed as the first argument to the function (`$1`).
- `days`: This local variable specifies the age in days beyond which files will be considered old and eligible for deletion. It's provided as the second argument to the function (`$2`).

```bash
    # Find and delete files older than a certain number of days in the specified directory
    # Handles errors such as permission issues or missing files
    find "$target_directory" -type f -mtime +"$days" -exec rm -f {} + 2>/dev/null
```

**Command Explanation**:
- `find "$target_directory"`: The `find` command is used to search for files within the specified directory.
- `-type f`: This option restricts the `find` command to look for files only, excluding directories.
- `-mtime +"$days"`: The `-mtime` option is used to select files modified more than `days` days ago. The `+` sign indicates more than `days` days.
- `-exec rm -f {} +`: This part of the command specifies what to do with each file found. The `rm -f` command deletes files without prompting for confirmation (`-f` stands for force). The `{}` is a placeholder that `find` replaces with the current file name. The `+` sign at the end causes `find` to pass all the found file names to `rm` at once, rather than one at a time, which is more efficient.
- `2>/dev/null`: Redirects any error messages (e.g., permission errors, missing files) to `/dev/null`, effectively discarding them.

```bash
    if [ $? -eq 0 ]; then
        echo "Successfully cleaned up files older than $days days in $target_directory."
    else
        echo "Error occurred during cleanup in $target_directory." >&2
        return 1
    fi
}
```

**Error Handling**:
- `$?`: This special variable holds the exit status of the last command executed, which in this case is the `find` command.
- `if [ $? -eq 0 ]; then`: Checks if the `find` command was successful (`$?` equals `0`). If successful, it prints a success message.
- `else`: If the `find` command fails (`$?` is not `0`), it prints an error message to standard error (`stderr`) using `>&2` and returns `1` from the function to indicate an error.

### Usage in Main Function
```bash
main() {
    # Cleanup temporary files
    cleanup_old_files "$TEMP_DIR" "$RETENTION_DAYS" || exit 1

    # Cleanup log files
    cleanup_old_files "$LOG_DIR" "$RETENTION_DAYS" || exit 1
}
```

**Main Function Execution**:
- `cleanup_old_files "$TEMP_DIR" "$RETENTION_DAYS"`: Calls the `cleanup_old_files` function to clean up the temporary directory.
- `cleanup_old_files "$LOG_DIR" "$RETENTION_DAYS"`: Repeats the cleanup for the log directory.
- `|| exit 1`: This operator is used after each function call. If `cleanup_old_files` returns `1` (indicating an error), the script will immediately exit with a status of `1`, signaling a failure condition to the system.

This script provides a robust and efficient way to maintain cleanliness in crucial system directories, minimizing disk space usage and potentially improving system performance by removing clutter. The approach taken also ensures that script failures are handled gracefully, with appropriate notifications and exit statuses.