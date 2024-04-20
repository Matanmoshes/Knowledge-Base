# Commands

Bash (Bourne Again SHell) is a widely-used shell and command language in Unix and Linux environments. Here's a brief overview of some common Bash commands:

1. **File Operations**:
   - `ls`: Lists directory contents. Use `-l` for detailed listing, `-a` to show hidden files.
   - `cp [source] [destination]`: Copies files or directories.
   - `mv [source] [destination]`: Moves or renames files or directories.
   - `rm [file]`: Deletes a file. Use `-r` for recursive delete (directories).
   - `touch [file]`: Creates a new file or updates the timestamp of an existing file.
   - `mkdir [directory]`: Creates a new directory.
   - `rmdir [directory]`: Removes an empty directory.

2. **Text Processing**:
   - `echo [text]`: Displays a line of text.
   - `cat [file]`: Displays the contents of a file.
   - `less [file]`, `more [file]`: View the contents of a file one screen at a time.
   - `grep [pattern] [file]`: Searches for a pattern in a file.
   - `sed [script] [file]`: Stream editor for filtering and transforming text.
   - `awk [program] [file]`: A powerful pattern scanning and processing language.

3. **File Permissions**:
   - `chmod [permissions] [file]`: Changes the permissions of a file or directory.
   - `chown [owner][:group] [file]`: Changes the owner (and group) of a file or directory.

4. **Networking**:
   - `ping [host]`: Checks connectivity to a host.
   - `curl [URL]`: Transfers data from or to a server.
   - `wget [URL]`: Downloads files from the web.

5. **System Information**:
   - `df`: Displays disk space usage.
   - `du`: Shows the disk usage of files and directories.
   - `top`: Displays active processes and system performance.
   - `ps`: Shows information about active processes.
   - `whoami`: Displays the current user.
   - `hostname`: Shows or sets the system's host name.

6. **File Viewing and Editing**:
   - `nano [file]`, `vi [file]`, `vim [file]`: Opens a text editor.

7. **Shell Scripting and Control**:
   - `if`, `else`, `fi`: Conditional statements.
   - `for`, `while`, `do`, `done`: Looping statements.
   - `function`: Defines a function.
   - `exit`: Exits the shell or a script.

8. **Redirection and Pipes**:
   - `>`: Redirects output to a file.
   - `>>`: Appends output to a file.
   - `|`: Sends the output of one command to another command as input.

9. **Environment and Variables**:
   - `export VAR=value`: Sets an environment variable.
   - `echo $VAR`: Displays the value of an environment variable.

10. **Process Management**:
    - `kill [PID]`: Terminates a process.
    - `nohup [command] &`: Runs a command immune to hangups, with output to a non-tty.


---

common Bash commands along with examples that you can try in the shell:

1. **Listing Files and Directories**:
   - Command: `ls`
   - Example in Shell:
     ```bash
     ls -l
     ```

2. **Copying Files**:
   - Command: `cp`
   - Example in Shell:
     ```bash
     cp source.txt destination.txt
     ```

3. **Moving or Renaming Files**:
   - Command: `mv`
   - Example in Shell:
     ```bash
     mv oldname.txt newname.txt
     ```

4. **Deleting Files**:
   - Command: `rm`
   - Example in Shell:
     ```bash
     rm file.txt
     ```

5. **Creating a New Directory**:
   - Command: `mkdir`
   - Example in Shell:
     ```bash
     mkdir new_directory
     ```

6. **Changing Directory**:
   - Command: `cd`
   - Example in Shell:
     ```bash
     cd /path/to/directory
     ```

7. **Displaying the Contents of a File**:
   - Command: `cat`
   - Example in Shell:
     ```bash
     cat file.txt
     ```

8. **Finding Text in Files**:
   - Command: `grep`
   - Example in Shell:
     ```bash
     grep "search text" file.txt
     ```

9. **Redirecting Output to a File**:
   - Command: `>`
   - Example in Shell:
     ```bash
     echo "Hello World" > output.txt
     ```

10. **Appending Output to a File**:
    - Command: `>>`
    - Example in Shell:
      ```bash
      echo "This will be appended" >> output.txt
      ```

11. **Viewing Running Processes**:
    - Command: `ps`
    - Example in Shell:
      ```bash
      ps aux
      ```

12. **Killing a Process**:
    - Command: `kill`
    - Example in Shell:
      ```bash
      kill 1234
      ```

13. **Downloading Files**:
    - Command: `wget`
    - Example in Shell:
      ```bash
      wget https://example.com/file
      ```

14. **Printing Text**:
    - Command: `echo`
    - Example in Shell:
      ```bash
      echo "Hello, Bash!"
      ```

15. **Viewing Disk Usage**:
    - Command: `df`
    - Example in Shell:
      ```bash
      df -h
      ```

16. **Displaying Network Configuration**:
    - Command: `ifconfig` or `ip addr`
    - Example in Shell:
      ```bash
      ip addr
      ```

---


# `find` command

The `find` command in Linux is a powerful utility for searching and locating the list of files and directories based on conditions you specify from a specified directory. It allows you to search for files by permissions, user ownership, modification date/time, size, and other criteria.

Here’s a basic overview of how to use the `find` command:

### Basic Syntax

```bash
find [path] [options] [expression]
```

- **path**: Specifies the directory to start the search from. If no directory is provided, `find` uses the current directory by default.
- **options**: Modifies the behavior of the `find` command.
- **expression**: Defines what to search for and what to do with the matched files.

### Common Examples

1. **Find Files by Name**
   ```bash
   find /path/to/directory -name "filename"
   ```
   This command searches for files named "filename" starting from "/path/to/directory".

2. **Find Files with Certain Extensions**
   ```bash
   find /path/to/directory -type f -name "*.txt"
   ```
   This command finds all `.txt` files in the specified directory and its subdirectories.

3. **Find Directories by Name**
   ```bash
   find /path/to/directory -type d -name "docs"
   ```
   This searches for directories named "docs".

4. **Find Files by Size**
   ```bash
   find /path/to/directory -type f -size +500k
   ```
   This finds files larger than 500 kilobytes.

5. **Find Files Modified in the Last X Days**
   ```bash
   find /path/to/directory -mtime -7
   ```
   This finds files modified in the last 7 days.

6. **Execute Commands on Files Found**
   ```bash
   find /path/to/directory -type f -name "*.tmp" -exec rm {} \;
   ```
   This finds all `.tmp` files and removes each one.

7. **Find Files with Specific Permissions**
   ```bash
   find /path -type f -perm 0664
   ```
   This finds files with permissions set to 0664.

### Advanced Use

- **Combining Expressions**
  You can combine expressions with logical operators like `-and`, `-or`, and `-not` to refine your search criteria.

- **Using Regular Expressions**
  ```bash
  find /path/to/directory -regex ".*\.(txt|doc)"
  ```
  This uses a regular expression to find files ending in `.txt` or `.doc`.

- **Limiting Depth of Search**
  ```bash
  find /path/to/directory -maxdepth 2 -name "filename"
  ```
  This limits the search to two directory levels below the starting point.



---


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


---
---

# `cut` Command
[cut]
The `cut` command in Bash is used to extract sections of lines from a file or input received from a pipeline. It selects portions of each line from a file and outputs these portions to the standard output. The `cut` command is particularly useful for extracting columns of text from structured files such as CSV or delimited log files.

### Key Options of `cut`:
- `-d` (delimiter): Specifies a delimiter that separates fields; the default delimiter is the tab character.
- `-f` (fields): Identifies which fields to extract, can be a single number, a comma-separated list, or a range (e.g., 1,3,5 or 1-5).
- `-c` (characters): Specifies character positions.

### Basic Syntax

```bash
cut [options] [file]
```

- **options**: Define what sections of each line should be extracted.
- **file**: The input file to process. If no file is specified, `cut` reads from standard input.

### Key Options

1. **`-d` (delimiter)**: Specifies a delimiter that will be used as the field separator. By default, the delimiter is the tab character.
   ```bash
   cut -d ',' -f 1 file.csv  # Extracts the first field from a CSV file
   ```

2. **`-f` (fields)**: Selects the fields to extract. Fields are numbered starting with 1.
   ```bash
   cut -d ':' -f 1,3 /etc/passwd  # Extracts the first and third fields from each line
   ```

3. **`-c` (characters)**: Specifies the character positions to extract. 
   ```bash
   cut -c 1-5 file.txt  # Extracts the first five characters of each line
   ```

4. **`--complement`**: Inverts the selection, outputting all fields or characters except those specified.
   ```bash
   cut -d ',' --complement -f 1 file.csv  # Outputs all fields except the first
   ```

5. **`-s` (only-delimited)**: Suppresses lines that do not contain the delimiter (normally `cut` will output these lines unchanged).
   ```bash
   cut -d ',' -f 2 -s file.csv  # Outputs the second field, but only for lines that contain a comma
   ```

### Practical Examples

- **Extracting Usernames and Home Directories from `/etc/passwd`**:
  ```bash
  cut -d ':' -f 1,6 /etc/passwd
  ```
  This command splits each line at colons (since `/etc/passwd` uses `:` as a delimiter) and extracts the first and sixth fields, typically representing the username and home directory.

- **Extracting the First Name from a List**:
  ```bash
  cut -d ',' -f 1 names.csv
  ```
  If you have a CSV file `names.csv` where each line is a name in the format `Lastname,Firstname`, this command extracts just the first names.

- **Cutting Characters for Formatting**:
  ```bash
  cut -c 1-10,20-30 file.txt
  ```
  This extracts characters 1 through 10 and 20 through 30 from each line of `file.txt`, useful for slicing out specific positions in a fixed-width file.


### Example Usage of `cut`:

**Scenario**: Consider a CSV file named `data.csv` with the following content:

```
name,age,gender
Alice,30,Female
Bob,22,Male
Charlie,25,Male
```

**Task**: Extract the names and ages from this CSV file.

Here's a Bash script that uses `cut` to perform this task:

```bash
#!/bin/bash

input_file="data.csv"
output_file="extracted_data.csv"


# Using cut to extract the first and second columns (name and age)
cut -d ',' -f 1,2 "$input_file" > "$output_file"

# Display the output
cat "$output_file"
```

This script sets up variables for the input and output files. It uses `cut` with `-d ','` to specify the comma as a field delimiter and `-f 1,2` to select the first and second fields (name and age). The output is redirected to `extracted_data.csv`, and then the `cat` command displays the content of this output file. 




---

---

 
# `paste` Command
[paste]
The `paste` command in Bash is used to merge lines of files horizontally, effectively combining multiple files or streams side-by-side. This command is particularly useful for collating data from separate sources into a single file.

### Key Features of `paste`:
- **Serial merging**: By default, `paste` merges the corresponding lines of the input files. If the files have different numbers of lines, the output will extend as far as the longest file, with missing fields filled with blanks for shorter files.
- **Delimiters**: The `-d` option allows specifying a delimiter set to separate merged lines (default is a tab).
- **Single-file handling**: With a single input, `paste` can be used to format its output differently, like converting a vertical list into a horizontal one.

### Example Usage of `paste`:

**Scenario**: Suppose you have two files, `names.txt` and `ages.txt`, with corresponding data that you want to merge into a single file.

`names.txt` contains:
```
Alice
Bob
Charlie
```

`ages.txt` contains:
```
30
22
25
```

**Task**: Merge these two files so that each name is next to its corresponding age.

Here's a Bash script that uses `paste` to perform this task:

```bash
#!/bin/bash

# File names
names_file="names.txt"
ages_file="ages.txt"
output_file="combined.txt"

# Merge files with a comma as a delimiter
paste -d ',' "$names_file" "$ages_file" > "$output_file"

# Display the output
cat "$output_file"
```

This script specifies a comma as the delimiter with the `-d ','` option, merging `names.txt` and `ages.txt` line by line. The output will look like this:

```
Alice,30
Bob,22
Charlie,25
```

The merged data is then redirected to `combined.txt`, and `cat` is used to display the result. This use of `paste` demonstrates its utility in data processing tasks where files with related data need to be aligned side by side.

---
---
# `join` command
[join]
The `join` command in Bash is used to combine two files based on a common field. It's particularly useful when you want to merge data from two files where lines share a common element, usually denoted as a key.

### Key Features of `join`:
- **Field specification**: You can specify which field in each file should be used as the join key with the `-1` and `-2` options, where the numbers indicate the first or second file, respectively.
- **Delimiter specification**: The `-t` option allows you to set a delimiter for fields within lines, such as a comma, space, or tab.
- **Output format customization**: You can decide which fields to output with the `-o` option, specifying fields from each file.

### Example Usage of `join`:

**Scenario**: Assume you have two files, `employees.txt` and `departments.txt`. `employees.txt` lists employee IDs and their names, and `departments.txt` lists employee IDs and their respective departments.

`employees.txt`:
```
1 Alice
2 Bob
3 Charlie
```

`departments.txt`:
```
1 Accounting
2 Marketing
3 Engineering
```

**Task**: Merge these files to list each employee's name alongside their department.

Here's a Bash script that uses the `join` command to accomplish this:

```bash
#!/bin/bash

# File names
employees_file="employees.txt"
departments_file="departments.txt"
output_file="joined_data.txt"

# Join files on the first field of each, which is the employee ID
join -t ' ' -1 1 -2 1 "$employees_file" "$departments_file" > "$output_file"

# Display the output
cat "$output_file"
```

This script merges `employees.txt` and `departments.txt` on the first field of each file (employee ID) using space (' ') as the delimiter. The joined data looks like this:

```
1 Alice Accounting
2 Bob Marketing
3 Charlie Engineering
```

The result is redirected to `joined_data.txt`, and `cat` is used to display the contents. This example shows how `join` is effective for combining related records from two structured text files based on a shared key.

---
---

# `split` command
[split]
The `split` command in Bash is used to split a large file into smaller files. It's commonly used to manage large datasets by dividing them into manageable pieces or to split files for easier distribution and storage.

### Key Features of `split`:
- **Size-based splitting**: You can split files based on the size of the output files (e.g., lines, bytes).
- **Suffix size**: You can specify the length of the suffixes for the output files with the `-a` option.
- **Custom prefixes**: Instead of using the default `x` prefix for output files, you can specify a prefix using the `--additional-suffix` option or simply by placing the desired prefix directly after the split command.
- **Line count**: The `-l` option allows you to specify the number of lines each split file should contain.

### Example Usage of `split`:

**Scenario**: Suppose you have a large text file named `data.txt` that you want to split into smaller files. Each file should contain no more than 100 lines.

Here's a Bash script that uses the `split` command to perform this task:

```bash
#!/bin/bash

# Input and parameters
input_file="data.txt"
lines_per_file=100
prefix="data_part_"

# Split the file
split -l "$lines_per_file" "$input_file" "$prefix"

# Show the created files
ls -l "${prefix}"*
```

This script splits `data.txt` into multiple smaller files, each containing up to 100 lines. The split files are named starting with `data_part_` followed by a sequential suffix (e.g., `data_part_aa`, `data_part_ab`, etc.). The `ls -l` command at the end lists the details of the files created by the `split` command to confirm the operation.

---




# `sed` Command

The `sed` command in Linux, short for "stream editor," is a powerful utility for performing text transformations on an input stream (like a file or input from a pipeline). It is primarily used for extracting and transforming text in files or output from other commands.

### Basic Syntax

```bash
sed [options] 'script' [input_file]
```

- **options**: Control the behavior of `sed`. Common options include `-i` for editing files in-place (i.e., saves the output back to the input file) and `-e` to add multiple scripts.
- **script**: The set of commands that `sed` will execute. The commands specify what to search for and how to modify it.
- **input_file**: The file on which `sed` will operate. If no input file is provided, `sed` reads from standard input.

### Common Commands in `sed`

- **`s` command**: Substitute command, used to replace text.
  ```bash
  sed 's/find/replace/' file.txt  # Replaces the first occurrence of 'find' with 'replace' in each line
  ```

- **`g` flag**: Global replacement flag. Without this flag, `s` replaces only the first occurrence in each line.
  ```bash
  sed 's/find/replace/g' file.txt  # Replaces all occurrences of 'find' with 'replace' in each line
  ```

- **Addressing lines**: You can specify which lines `sed` should operate on.
  ```bash
  sed '2,5s/find/replace/' file.txt  # Only apply the substitution on lines 2 through 5
  sed '/pattern/s/find/replace/' file.txt  # Only apply the substitution on lines matching 'pattern'
  ```

- **`-i` option**: Edit files in-place, but use it with caution as it modifies the source file.
  ```bash
  sed -i 's/find/replace/g' file.txt  # Modifies file.txt directly
  ```

- **`d` command**: Delete lines.
  ```bash
  sed '3d' file.txt  # Deletes the third line
  sed '/pattern/d' file.txt  # Deletes lines matching 'pattern'
  ```

- **`p` command**: Print lines explicitly, used often with `-n` option to suppress default output.
  ```bash
  sed -n 'p' file.txt  # Prints each line, combined with -n it prints nothing unless specified
  sed -n '/pattern/p' file.txt  # Prints only the lines that match 'pattern'
  ```

- **`a`, `i`, and `c` commands**: Append, insert, or change a line.
  ```bash
  sed '2a new_line' file.txt  # Appends 'new_line' after line 2
  sed '3i new_line' file.txt  # Inserts 'new_line' before line 3
  sed '4c new_line' file.txt  # Changes line 4 to 'new_line'
  ```

### Advanced Usage

`sed` can also execute more complex scripts involving multiple commands, utilize hold and pattern spaces for advanced text manipulation, and even execute commands conditionally or loop over sections of text.

For instance, you can use a semi-colon to separate multiple commands:

```bash
sed 's/find/replace/; s/search/replace/' file.txt
```

Or use a file to hold multiple `sed` commands:

```bash
sed -f script.sed input_file
```

This utility is extremely powerful for scripting and text processing tasks in Linux environments. If you need specific examples or have questions about more complex `sed` operations, feel free to ask!


---
---

# `wc` Command
[wc]
The `wc` (word count) command in Unix and Linux is a simple utility used to count lines, words, and bytes (or characters) in files or standard input. It's commonly employed in scripting and command line operations to measure the size or length of input.

### Basic Syntax of `wc`:
```bash
wc [options] [file...]
```
- options**: Modify what counts are returned (lines, words, bytes/characters).
- **file...**: One or more files to analyze. If no file is specified, `wc` reads from standard input.

### Key Options for `wc`:
- **`-l`**: Count the number of lines.
- **`-w`**: Count the number of words.
- **`-c`**: Count the number of bytes.
- **`-m`**: Count the number of characters. This option is useful in multibyte character sets.

### Examples of Using `wc`:

1. **Counting Lines in a File**:
   ```bash
   wc -l file.txt
   ```
   This command will output the number of lines in `file.txt`.

2. **Counting Words and Characters**:
   ```bash
   wc -w -m file.txt
   ```
   Outputs the number of words and characters in `file.txt`.

3. **Counting Bytes**:
   ```bash
   wc -c file.txt
   ```
   Reports the number of bytes in `file.txt`.

4. **Using `wc` with Other Commands**:
   ```bash
   cat file.txt | wc -l
   ```
   This uses a pipe to direct the output of `cat file.txt` to `wc -l`, counting the lines output by `cat`.

5. **Counting Multiple Files**:
   ```bash
   wc file1.txt file2.txt
   ```
   Outputs the line, word, and byte count for each file and a total line for all files.

### Practical Example:

If you want to monitor the number of lines being added to a log file over time, you might use:

```bash
watch "wc -l log_file.txt"
```
This command uses `watch` to repeatedly execute `wc -l log_file.txt`, allowing you to see the line count update in real-time.

### Summary:

`wc` is a versatile tool that serves fundamental counting needs in text processing tasks. Whether you're scripting, analyzing data, or simply assessing the size of documents, `wc` provides straightforward options to quantify various aspects of text data efficiently. Its ability to interface seamlessly with other Unix commands via pipes and redirection makes it an indispensable part of command-line workflows.




---
---
# `awk` Command
[awk]

`awk` is a versatile tool used for parsing and manipulating text based on patterns, handling text files line by line and field by field. It supports extensive programming capabilities, including loops, conditionals, and arrays for complex text processing tasks.

### Basic Syntax
```bash
awk [options] 'pattern {action}' file
```
- **options**: Modify `awk` behavior (e.g., `-F` sets the field separator).
- **pattern**: Condition for executing the associated action.
- **action**: Commands executed when the pattern matches.
- **file**: The input file; if omitted, `awk` reads from standard input.

### Key Components

1. **Field Separator**:
   ```bash
   awk -F',' '{print $1}' file.txt
   ```
   - **`-F`**: Defines a delimiter, such as a comma.

2. **Patterns**:
   ```bash
   awk '/pattern/ {print}' file
   ```
   - Patterns direct `awk` to process lines that match.

3. **Actions**:
   ```bash
   awk '{print $1}' file
   ```
   - Commands in `{}` are performed on matching lines.

4. **Built-in Variables**:
   - `NR`: Current record number (line number).
   - `NF`: Number of fields in the current record.
   - `FS`: Field separator (default is space).

5. **Common Uses**:
   - Print lines matching "error":
     ```bash
     awk '/error/ {print}' server.log
     ```
   - Calculate sum and average of numbers:
     ```bash
     awk '{sum+=$1} END {print "Sum:", sum; print "Average:", sum/NR}' numbers.txt
     ```


## Example:

Simpler example with `awk` to analyze a basic server log file. Suppose you have a log file named `server.log` that includes entries like IP addresses and HTTP request methods. Here's a sample content for `server.log`:

```
192.168.1.1 GET /index.html
192.168.1.2 POST /submit_form.php
192.168.1.3 GET /about.html
192.168.1.1 GET /contact.html
192.168.1.2 GET /index.html
```

### **Scenario**:
You want to count how many times each HTTP method (GET, POST, etc.) is used.

### **`awk` Command**:
Here's how you could use `awk` in a Bash script to perform this analysis:

```bash
#!/bin/bash

# Log file name
log_file="server.log"

# Use awk to count HTTP methods
awk '{count[$2]++} END {for (method in count) print method, count[method]}' "$log_file"
```

### **Explanation**:
1. **`awk` Command Breakdown**:
   - `{count[$2]++}`: For each line in the file, this part of the script increments a count in an associative array named `count` where the index is the second field `$2` (which is the HTTP method in this log format).
   - `END {for (method in count) print method, count[method]}`: After processing all lines, this part runs. It iterates over all indices in the `count` array (each unique HTTP method) and prints each method along with its counted value.

This script will produce an output that might look like this:
```
GET 4
POST 1
```

### **Usage**:
To use this script:
1. Place it in the same directory as your `server.log`.
2. Make sure the script file is executable. You can make it executable with the command:
   ```bash
   chmod +x your_script_name.sh
   ```
3. Run the script by typing `./your_script_name.sh` in your terminal.




---

---
# `sort` Command
[sort]
The `sort` command in Bash is a utility for sorting lines in text files. It is widely used in Unix and Linux systems for organizing file content in a specified order, either numerically or alphabetically, among other options.

### Basic Syntax of `sort`:
```bash
sort [options] [file...]
```

- **options**: Modify how the sorting is performed, such as numerical, reverse, or checking uniqueness.
- **file...**: The file or files to sort. If no file is specified, `sort` reads from standard input.

### Key Options for `sort`:
- **`-n`**: Sort numerically (e.g., 10 will sort after 2).
- **`-r`**: Reverse the result of comparisons (sort in descending order).
- **`-k`**: Sort via a specified key (e.g., `-k 2` sorts by the second field).
- **`-u`**: Unique sorting that removes duplicate entries.
- **`-o`**: Output to a specified file.
- **`-t`**: Specify a delimiter with the `-t` option followed by a character.
- **`-f`**: Ignore case while sorting.

### Examples of Using `sort`:

1. **Alphabetical Sorting of a File**:
   ```bash
   sort file.txt
   ```
   Sorts the lines of `file.txt` alphabetically.

2. **Numerical Sorting**:
   ```bash
   sort -n file.txt
   ```
   Sorts the lines of `file.txt` based on numerical values.

3. **Sorting with a Specific Field**:
   ```bash
   sort -k 2 file.txt
   ```
   Sorts `file.txt` based on the second field of each line, which can be further combined with `-n` or `-r`.

4. **Reverse Sorting**:
   ```bash
   sort -r file.txt
   ```
   Sorts the lines of `file.txt` in reverse order (descending).

5. **Unique Sorting**:
   ```bash
   sort -u file.txt
   ```
   Sorts `file.txt` and removes duplicate lines.

6. **Sorting with a Custom Delimiter**:
   ```bash
   sort -t, -k 3n file.csv
   ```
   Sorts a CSV file based on the third column numerically.

### Practical Example:

Suppose you have a file named `data.txt` with the following content:
```
b 10
a 20
c 5
```

If you want to sort this file numerically based on the second column, you would use:
```bash
sort -k2,2n data.txt
```
Output:
```
c 5
b 10
a 20
```

This example demonstrates the utility of `sort` in handling structured text data, making it an essential tool for data processing and analysis in shell scripting environments.

---

---


---
# `tr` Command
[tr]

The `tr` command in Bash (short for translate) is a useful utility for transforming or deleting characters from the standard input and writing the result to the standard output. It is frequently used for replacing, removing, or squeezing repeating characters.

### Basic Syntax of `tr`:
```bash
tr [options] set1 [set2]
```

- **options**: Modify how the translation or deletion affects the data.
- **set1**: The set of characters to be replaced or removed.
- **set2**: The set of characters that `set1` should be replaced with or mapped to.

### Key Options for `tr`:
- **`-d`**: Delete characters in `set1` from the input.
- **`-s`**: Squeeze repeated characters listed in the first set into a single character.
- **`-c`, `-C`, `--complement`**: Use the complement of `set1`.

### Examples of Using `tr`:

1. **Replacing Characters**:
   ```bash
   echo "hello world" | tr 'a-z' 'A-Z'
   ```
   Converts lowercase letters to uppercase, outputting "HELLO WORLD".

2. **Deleting Characters**:
   ```bash
   echo "hello 123 world 456" | tr -d '0-9'
   ```
   Removes all digits, outputting "hello  world ".

3. **Squeezing Characters**:
   ```bash
   echo "hello    world" | tr -s ' '
   ```
   Replaces repeated spaces with a single space, outputting "hello world".

4. **Using Character Classes**:
   ```bash
   echo "hello 123 world" | tr -d '[:digit:]'
   ```
   Deletes all digits, utilizing character classes, outputting "hello  world".

5. **Complementing Sets**:
   ```bash
   echo "abcde12345" | tr -c '0-9' '-'
   ```
   Replaces all characters not in `0-9` with `-`, resulting in "-----12345".

### Practical Example:

Suppose you have a text that includes several accented characters and you want to replace them with their unaccented versions for simplicity or compatibility reasons:

```bash
echo "café au lait" | tr 'é' 'e'
```
Output:
```
cafe au lait
```


---
---


# `uniq` Command
[uniq]

The `uniq` command in Unix and Linux is used to filter or report repeated lines in a file. Particularly useful when dealing with sorted data, `uniq` helps in removing or counting duplicate entries. The command typically requires sorted input, as it only removes duplicates that are adjacent to each other.

### Basic Syntax of `uniq`:
```bash
uniq [options] [input [output]]
```
- **input**: The file to process. If not provided, `uniq` reads from standard input.
- **output**: The file where the result is written. If not specified, output goes to standard output.

### Key Options for `uniq`:
- **`-c`**: Prefix lines by the number of occurrences.
- **`-d`**: Only print duplicate lines, one for each group of identical lines.
- **`-u`**: Only print unique lines, lines that never repeat.
- **`-i`**: Ignore differences in case when comparing lines.
- **`-s N`**: Skip the first N characters when comparing lines.
- **`-w N`**: Compare no more than N characters in lines.

### Examples of Using `uniq`:

1. **Removing Adjacent Duplicates**:
   ```bash
   sort file.txt | uniq
   ```
   This common pattern sorts `file.txt` and pipes it to `uniq`, effectively removing all adjacent duplicate lines.

2. **Counting Duplicates**:
   ```bash
   sort file.txt | uniq -c
   ```
   Sorts the file and passes it to `uniq`, which then prefixes each line with the number of occurrences.

3. **Finding Only Duplicates**:
   ```bash
   sort file.txt | uniq -d
   ```
   This will display only the lines that have duplicates, showing one instance of each duplicated line.

4. **Extracting Unique Lines**:
   ```bash
   sort file.txt | uniq -u
   ```
   Outputs lines that appear only once in the file.

5. **Ignoring Case**:
   ```bash
   sort file.txt | uniq -i
   ```
   Ignores case when filtering duplicates, useful for case-insensitive data.

### Practical Example:

Imagine you have a log file (`access.log`) with listed accesses by IP address and you want to see which IPs have accessed more than once, ignoring variations in case:

```bash
cat access.log | cut -d' ' -f1 | sort | uniq -i -d
```
This command sequence:
- Extracts the first field (assuming IP addresses are the first part of each log entry),
- Sorts these IPs,
- Removes duplicates, ignoring case, and
- Displays only those lines that were duplicated.


---
---

# `diff` Command
[diff]

The `diff` command in Linux is a utility used to compare the contents of files line by line. It's especially useful in scenarios where you need to see changes between two versions of the same file, such as in programming or configuration management.

### Basic Syntax

```bash
diff [options] file1 file2
```

- **file1**, **file2**: These are the two files you want to compare.

### Common Options

- **`-u`, `--unified`**: This option provides a unified diff, which shows several lines of context, making it easier to understand the differences. This is the most commonly used format for patches.
  ```bash
  diff -u file1 file2
  ```

- **`-c`, `--context`**: Like unified, but provides a more detailed context for each change. Useful for getting a broader view of changes.
  ```bash
  diff -c file1 file2
  ```

- **`-i`**: Ignores case differences in file contents.
  ```bash
  diff -i file1 file2
  ```

- **`-w`, `--ignore-all-space`**: Ignores all white space in the files, useful for when changes in indentation or spacing aren't relevant.
  ```bash
  diff -w file1 file2
  ```

- **`--side-by-side`**: Displays the files next to each other for easy comparison.
  ```bash
  diff --side-by-side file1 file2
  ```

- **`-r`, `--recursive`**: Used to compare directories recursively. This option is helpful when comparing directories of files.
  ```bash
  diff -r dir1 dir2
  ```

### Understanding Output

The output of the `diff` command can be interpreted as follows:

- **Lines prefixed with `<`**: These lines are from `file1`.
- **Lines prefixed with `>`**: These lines are from `file2`.
- **Context lines** (in unified or context mode): Shown for reference and not prefixed by any symbol.
- **Change line numbers**: The line numbers before and after the change are shown, helping to identify the exact location in both files.

### Example of Unified Diff

If you run `diff -u file1 file2`, you might get output like this:

```diff
--- file1   2021-04-01 14:20:15.000000000 -0400
+++ file2   2021-04-01 14:20:30.000000000 -0400
@@ -1,5 +1,5 @@
-Old line in file1
+New line in file2
 Context line
 Context line
-Another old line in file1
+Another new line in file2
```

This output indicates:
- Lines beginning with `-` were in `file1` but changed in `file2`.
- Lines beginning with `+` are new or modified lines appearing in `file2`.

---

# `grep` Command
[grep]

The `grep` command in Linux is a powerful tool used for searching text data sets for lines that match a regular expression. Its name comes from the ed command `g/re/p` (globally search a regular expression and print), which has a similar function.

### Basic Syntax

```bash
grep [options] pattern [file...]
```

- **pattern**: The text or regular expression you want to search for.
- **file...**: One or more files to search. If no file is specified, `grep` reads from standard input.

### Key Options

- **`-i`**: Ignore case distinctions in both the pattern and the input files.
  ```bash
  grep -i "example" file.txt
  ```

- **`-v`**: Invert the match, i.e., show only lines that do not match the pattern.
  ```bash
  grep -v "example" file.txt
  ```

- **`-r` or `--recursive`**: Recursively search subdirectories listed.
  ```bash
  grep -r "example" /path/to/directory/
  ```

- **`-n`**: Prefix each line of output with the line number within its input file.
  ```bash
  grep -n "example" file.txt
  ```

- **`-c`**: Count the number of lines that match the pattern.
  ```bash
  grep -c "example" file.txt
  ```

- **`-l` (lowercase L)**: Print only the names of files with matching lines, once for each file.
  ```bash
  grep -l "example" *.txt
  ```

- **`-o`**: Show only the part of a matching line that matches PATTERN.
  ```bash
  grep -o "example" file.txt
  ```

- **`--color=auto`**: Mark up the matching text with color on the terminal.
  ```bash
  grep --color=auto "example" file.txt
  ```

### Practical Examples

1. **Search for a pattern in a file ignoring case**:
   ```bash
   grep -i "search term" filename
   ```

2. **Count the number of lines that contain the pattern**:
   ```bash
   grep -c "search term" filename
   ```

3. **List all files in a directory containing the pattern**:
   ```bash
   grep -rl "search term" /path/to/directory/
   ```

4. **Display lines that do not contain the pattern**:
   ```bash
   grep -v "search term" filename
   ```

---

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

---

# `dd` Command

The `dd` command in Unix and Linux is a low-level utility for converting and copying files, but it is particularly famed for its capability to perform direct copying from one storage device to another at the block level. This makes it especially powerful for tasks involving disk imaging, backups, and recovery.

### Basic Syntax

The basic syntax of `dd` is:

```bash
dd if=input_file of=output_file [options]
```

- **`if=`** specifies the input file (or device).
- **`of=`** specifies the output file (or device).
- **options** include several parameters that can be adjusted to control how `dd` operates.

### Key Options

- **`bs=`**: Sets both input and output block size. Typical values are `512` bytes (for traditional disk sectors), `4K` (for modern drives), and larger sizes for efficiency in certain situations.
- **`count=`**: Number of blocks to copy (combined with `bs`, determines total amount of data copied).
- **`skip=`**: Number of input blocks to skip before starting to copy (useful for offsetting into the input).
- **`seek=`**: Number of output blocks to skip before starting to write (useful for offsetting into the output).
- **`conv=`**: A set of options to convert the file as per the specified keywords, like `notrunc` (do not truncate the output file), `sync` (pad every input block to size of `bs` with nulls), `noerror` (continue after read errors), etc.

### Common Usage Examples

#### 1. **Creating Disk Images**
To create an image of a disk:

```bash
dd if=/dev/sda of=/path/to/disk_image.img bs=4M
```
This command will create an image (`disk_image.img`) of the `/dev/sda` device using a block size of 4 Megabytes, which can enhance the efficiency of the operation.

#### 2. **Restoring Disk Images**
To restore an image to a disk:

```bash
dd if=/path/to/disk_image.img of=/dev/sda bs=4M
```
This reverses the first example, writing the disk image back to a physical disk.

#### 3. **Creating Bootable USB Drives**
Copying an ISO image to a USB drive to make it bootable:

```bash
dd if=/path/to/linux.iso of=/dev/sdb bs=4M
```
This assumes that `/dev/sdb` is the USB drive. Care must be taken to verify the device name with tools like `lsblk` or `fdisk` before running the command to avoid overwriting the wrong disk.

#### 4. **Backup of a Partition**
To backup a single partition:

```bash
dd if=/dev/sda1 of=/path/to/partition_backup.img bs=4M
```
This command creates a backup image of the first partition on the disk `/dev/sda`.

### Safety and Precautions

- **Double-check device names**: `dd` can cause irreparable data loss if misused (commonly joked as "Disk Destroyer" due to its power and potential for damage).
- **Root permissions**: `dd` often requires root access, especially for reading/writing directly to disk devices.
- **Monitoring progress**: `dd` does not show progress in its traditional form. To view progress during the operation, you can use `status=progress` option in newer versions:

  ```bash
  dd if=/dev/sda of=/path/to/disk_image.img bs=4M status=progress
  ```

The `dd` command is extremely powerful and useful for a wide range of lower-level data manipulation tasks, especially those involving block devices. Always use it with care to ensure that you do not accidentally overwrite valuable data.

---
