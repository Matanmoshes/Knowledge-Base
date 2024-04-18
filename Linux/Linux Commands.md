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

### Summary
Together, `|` and `tee` provide a robust mechanism for data processing workflows in Unix-like systems, allowing you to manipulate and redirect data efficiently across multiple command-line utilities. They are essential for scripting, data analysis, and system administration tasks that involve handling streams of data.


---
---

# cut Command
[cut]
The `cut` command in Bash is used to extract sections of lines from a file or input received from a pipeline. It selects portions of each line from a file and outputs these portions to the standard output. The `cut` command is particularly useful for extracting columns of text from structured files such as CSV or delimited log files.

### Key Options of `cut`:
- `-d` (delimiter): Specifies a delimiter that separates fields; the default delimiter is the tab character.
- `-f` (fields): Identifies which fields to extract, can be a single number, a comma-separated list, or a range (e.g., 1,3,5 or 1-5).
- `-c` (characters): Specifies character positions.

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

This example demonstrates how `cut` can be an efficient tool for parsing and manipulating data in shell scripting.



---

---

 
# paste Command
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
# join command
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

# split command
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

This example demonstrates the straightforward application of `split` for dividing large files into smaller, more manageable segments based on line count, which is especially useful for processing large datasets in parts or distributing them for parallel processing.

---

---


# sed Command

The `sed` command in Bash, short for "stream editor," is used for performing text transformations on an input stream (a file or input from a pipeline). It's an extremely powerful tool for text processing, especially useful for editing files programmatically without opening them.

### Key Features of `sed`:
- **In-place editing**: Using the `-i` option, you can modify files in place (i.e., change the original file).
- **Regular expressions**: `sed` supports extensive use of regular expressions, allowing for complex pattern matching and substitution.
- **Scripting**: Multiple `sed` commands can be combined into a script to perform a series of actions on text data.

### Example Usage of `sed`:

**Scenario**: Imagine you have a file named `example.txt` with the following content:

```
Hello World
Hello Bash
Hello sed
```

**Task**: Replace "Hello" with "Hi" in the file.

Here’s how you could write a Bash script using `sed` to accomplish this:

```bash
#!/bin/bash

# File name
file_name="example.txt"

# Use sed to replace 'Hello' with 'Hi'
sed -i 's/Hello/Hi/g' "$file_name"

# Display the updated file content
cat "$file_name"
```

This script uses `sed` with the `-i` option for in-place editing, meaning it will directly modify `example.txt`. The `s/Hello/Hi/g` part of the command is a substitution command in `sed`:
- `s`: Specifies the substitution operation.
- `Hello`: The pattern to search for.
- `Hi`: The replacement string.
- `g`: A flag to perform a global replacement (i.e., replace all occurrences in a line, not just the first one).

After running this script, the content of `example.txt` would be:

```
Hi World
Hi Bash
Hi sed
```

This example shows how `sed` can be used for simple text replacements, which is a common task in text processing and automation scripts.


---
---

# wc Command
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
# awk Command
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
# sort Command
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
# tr Command
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

---
# uniq Command
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

### Summary:

The `uniq` command is a valuable tool for processing sorted text data, especially in conjunction with `sort`. It is widely used in scripts for data cleaning, report generation, and analysis tasks where identifying or removing duplicate entries is necessary.


---
---

