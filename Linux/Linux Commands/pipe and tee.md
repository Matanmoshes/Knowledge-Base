# pipe (`|`) and `tee` commands
[|] [tee]
The pipe (`|`) and `tee` commands are powerful tools in Unix and Linux that facilitate efficient data manipulation and redirection in command-line environments. Here's how each is used:

### Pipe (`|`)
The pipe symbol (`|`) is used to send the output of one command as the input to another command. This allows you to chain commands together into a pipeline, which can perform complex operations on data by passing it through multiple processing stages.

**Example Usage**:
Suppose you want to filter and then sort the contents of a file. You could use `grep` to find specific lines and then `sort` to order them:

```bash
grep "pattern" file.txt | sort
```
This command sequence filters lines that contain "pattern" in `file.txt` and passes them to `sort`, which outputs them in ascending order.

### `tee` Command
The `tee` command reads from standard input and writes to standard output and files. This enables you to view data on the screen and save it to a file at the same time, or to log data while also passing it along the pipeline.

**Syntax**:
```bash
command1 | tee file1.txt | command2
```

- **`file1.txt`**: The file into which the output of `command1` is saved.
- Output is also passed to `command2` for further processing.

**Options**:
- **`-a`**: Append to the given files, do not overwrite.

**Example Usage**:
If you want to list the contents of a directory, see the output immediately, and save it to a file, you might use:

```bash
ls -l | tee directory_contents.txt
```
This will display the directory listing on the terminal and simultaneously save it to `directory_contents.txt`.

For more complex operations, where you might want to continue processing the data, you could use:

```bash
cat server.log | grep "ERROR" | tee error_logs.txt | sort
```
This command sequence does the following:
1. `cat server.log`: Outputs the contents of `server.log`.
2. `grep "ERROR"`: Filters lines containing "ERROR".
3. `tee error_logs.txt`: Writes these lines to `error_logs.txt` and passes them to the next command.
4. `sort`: Sorts the filtered lines.

This setup is especially useful for monitoring or debugging where you need to both log errors for later review and continue processing or inspecting them real-time.

