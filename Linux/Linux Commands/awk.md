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
