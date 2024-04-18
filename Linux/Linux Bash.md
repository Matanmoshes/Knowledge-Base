
# <span style="color:#0070c0">Bash Script
</span>

1. **Reading User Input**:
   - Use `read` command to take input from the user.
   - Example:
     ```bash
     echo "Enter your name:"
     read name
     echo "Welcome, $name!"
     ```

2. **Variable Assignment**:
   - Assign values to variables directly.
   - Example:
     ```bash
     greeting="Hello"
     echo $greeting
     ```

3. **Command Substitution**:
   - Capture the output of a command into a variable.
   - Example:
     ```bash
     current_date=$(date)
     echo "Today is $current_date"
     ```

4. **Arithmetic Operations**:
   - Perform basic arithmetic operations.
   - Example:
     ```bash
     num1=5
     num2=3
     sum=$((num1 + num2))
     echo "Sum is: $sum"
     ```

5. **Conditional Statements** (`if`, `else`, `elif`):
   - Make decisions based on conditions.
   - Example:
     ```bash
     if [ $num1 -gt $num2 ]; then
       echo "$num1 is greater than $num2"
     else
       echo "$num1 is less than or equal to $num2"
     fi
     ```

6. **<span style="font-weight:bold; color:#ff0000">Case Statements</span>:**
   - Execute different blocks of code based on a specific value.
   - Example:
     ```bash
     read input
     case $input in
       start) echo "Starting";;
       stop) echo "Stopping";;
       *) echo "Invalid input";;
     esac
     ```

7. **<span style="font-weight:bold; color:#ff0000">Loops (for, while, until)</span>:**
   - Repeat a series of commands.
   - For Loop Example:
     ```bash
     for i in {1..5}; do
       echo "Number $i"
     done
     ```
   - While Loop Example:
     ```bash
     count=1
     while [ $count -le 5 ]; do
       echo "Count: $count"
       count=$((count + 1))
     done
     ```
   - Until Loop Example:
     ```bash
     count=1
     until [ $count -gt 5 ]; do
       echo "Count: $count"
       count=$((count + 1))
     done
     ```

8. **<span style="font-weight:bold; color:#ff0000">Functions</span>:**
   - Define reusable code blocks.******
   - Example:
     ```bash
     greet() {
       echo "Hello, $1!"
     }
     greet "World"
     ```

9. **Passing Arguments to a Script**:
   - Use `$1`, `$2`, etc., to access script arguments.
   - Example Script (`myscript.sh`):
     ```bash
     echo "First argument: $1"
     echo "Second argument: $2"
     ```
   - Run: `bash myscript.sh Hello World`

10. **Exit Status**: [$?]
    - `$?` gives the exit status of the last command executed.
    - Example:
      ```bash
      ls /nonexistentfile
      echo $?  # Non-zero exit status
      ```

11. **Using `echo` for Output**:
    - Display messages or variables.
    - Example:
      ```bash
      echo "This is a message"
      ```

12. **Redirecting Output (>, >>, 2>, &>)**:
    - Redirect output to files and handle errors.
    - Example:
      ```bash
      echo "Log entry" >> logfile.txt  # Append output to a file
      ```


---
## Loops

1. **Array Variables**:
   - Bash supports one-dimensional indexed and associative arrays.
   - Example (Indexed Array):
     ```bash
     fruits=("apple" "banana" "cherry")
     echo "${fruits[1]}"  # Outputs 'banana'
     ```

2. **Iterating Over Array Elements**:
   - Use a `for` loop to iterate over array elements.
   - Example:
     ```bash
     for fruit in "${fruits[@]}"; do
       echo "Fruit: $fruit"
     done
     ```

3. **String Manipulation**:
   - Perform operations like substring extraction and replacement.
   - Example (Substring Extraction):
     ```bash
     str="Bash scripting is fun"
     echo "${str:5:9}"  # Outputs 'scripting'
     ```

4. **Checking File and Directory Existence**:
   - Use conditional expressions to check if a file or directory exists.
   - Example:
     ```bash
     if [ -f "file.txt" ]; then
       echo "file.txt exists."
     fi
     ```

5. **Redirecting Standard Error**:
   - Redirect standard error (`stderr`) to a file or standard output (`stdout`).
   - Example:
     ```bash
     ls non_existing_file 2> error_log.txt
     ```

6. **Appending Standard Output and Standard Error to a File**:
   - Example:
     ```bash
     command >> output_log.txt 2>&1
     ```

7. **Reading File Content in a Loop**:
   - Use `while` loop with `read` command to process text files line by line.
   - Example:
     ```bash
     while IFS= read -r line; do
       echo "Line: $line"
     done < file.txt
     ```

8. **Command-Line Options Parsing**:
   - Use `getopts` for parsing command-line options.
   - Example:
     ```bash
     while getopts "a:b:" opt; do
       case $opt in
         a) a_value="$OPTARG" ;;
         b) b_value="$OPTARG" ;;
         \?) echo "Invalid option -$OPTARG" >&2 ;;
       esac
     done
     ```

9. **Arithmetic Expansion**:
   - Perform arithmetic operations directly within the script.
   - Example:
     ```bash
     echo $((2 + 3))  # Outputs 5
     ```

10. **Here Documents**:
    - Used to redirect input into an interactive shell script or program.
    - Example:
     ```bash
    
      ```
 


11. **Processing Command-Line Arguments**:
    - `$@` and `$#` can be used to process arguments.
    - Example:
      ```bash
      echo "Number of arguments: $#"
      echo "All arguments: $@"
      ```

12. **Exit Command**:
    - Exit the script with a specific status.
    - Example:
      ```bash
      exit 1
      ```

13. **Subshells**:
    - Enclosing a series of commands within parentheses `()` runs them in a subshell.
    - Example:
      ```bash
      (cd some_directory; ls)
      ```

14. **Select Construct for Menus**:
    - Create simple menus easily.
    - Example:
      ```bash
      select option in "cat" "dog" "quit"; do
        case $option in
          cat) echo "Cats are great";;
          dog) echo "Dogs are loyal";;
          quit) break;;
          *) echo "Invalid option";;
        esac
      done
      ```

15. **Function Parameters**:
    - Functions can take parameters.
    - Example:
      ```bash
      myfunc() {
        echo "Parameter 1 is $1"
      }
      myfunc "hello"
      ```

16. **Testing String Length**:
    - Use `#` to get the length of a string.
    - Example:
      ```bash
      str="Hello"
      echo "${#str}"  # Outputs 5
      ```

These additional concepts and examples can greatly enhance the functionality and flexibility of your Bash scripts. Remember to always test your scripts in a controlled environment before using them in a production scenario.



In Bash scripting, `$#` is a special variable that represents the number of positional arguments (parameters) passed to a script or a function. Positional arguments are typically used to provide input data or options to a script.

---
## Understanding `$#`:
[$#]
>[!info]
>- When you run a Bash script, you can pass arguments to it, much like passing arguments to a function in programming languages.
>- These arguments are accessible inside the script through special variables `$1`, `$2`, `$3`, and so on, where `$1` is the first argument, `$2` is the second, etc.
>- The `$#` variable contains the count of these arguments.

#### Example Usage:

Let's say you have a script named `myscript.sh`:

```bash
#!/bin/bash
echo "The number of arguments passed is: $#"
```

If you run this script as:

```bash
bash myscript.sh arg1 arg2 arg3
```

It will output:

```
The number of arguments passed is: 3
```

#### Practical Applications:

1. **Checking Argument Counts**: `$#` is commonly used to check if the correct number of arguments was passed to the script. This is useful for ensuring the script operates correctly.

   ```bash
   if [ $# -ne 2 ]; then
       echo "Usage: $0 arg1 arg2"
       exit 1
   fi
   ```

2. **Iterating Over Arguments**: While `$@` or `$*` is used to loop over all passed arguments, `$#` can be used to control the loop or for array indexing if needed.

3. **Conditional Scripts**: You can create scripts that behave differently based on the number of arguments passed.

Remember, `$#` is a built-in Bash feature and is automatically set when a script is executed; you don't need to manually set it. Its primary role is to help scripts become more dynamic and responsive to user inputs.

---


---

---
# <span style="color:#00b050">Exercises</span>

## Linux process

### Create a Bash script that performs the following actions:

1. Lists all current user processes, including the PID, PPID, and controlling terminal.
2. Demonstrates process creation by starting a background process.
3. Sends a signal to the background process to terminate it after a certain amount of time.
4. Adjusts the niceness of a process and demonstrates the change in priority.
5. Utilizes job control to stop and resume a process.

#### Version 1:

```bash
#!/bin/bash

# Function to list current user processes
list_user_processes() {
    printf "Current user processes including PID, PPID, and controlling terminal:\n"
    ps -o pid,ppid,tty,cmd -u "$(whoami)"
}

# Function to start a background process
start_background_process() {
    local cmd=$1
    $cmd &
    bg_pid=$!
    printf "Background process started with PID: %d\n" "$bg_pid"
}

# Function to send a signal to terminate a background process
terminate_background_process() {
    local pid=$1
    local delay=$2
    sleep "$delay"
    kill "$pid" && printf "Process %d terminated after %d seconds\n" "$pid" "$delay"
}

# Function to adjust the niceness of a process
adjust_niceness() {
    local pid=$1
    local new_nice=$2
    renice "$new_nice" -p "$pid" && printf "Niceness of process %d adjusted to %d\n" "$pid" "$new_nice"
}

# Function to demonstrate job control
job_control() {
    local cmd=$1
    $cmd &
    job_pid=$!
    printf "Job started with PID: %d\n" "$job_pid"

    # Stop the process
    kill -STOP "$job_pid"
    printf "Job %d stopped\n" "$job_pid"

    # Resume the process
    kill -CONT "$job_pid"
    printf "Job %d resumed\n" "$job_pid"
}

main() {
    # List current user processes
    list_user_processes

    # Start a background process
    start_background_process "sleep 30"

    # Terminate the background process after 10 seconds
    terminate_background_process "$bg_pid" 10

    # Adjust the niceness of the background process
    adjust_niceness "$bg_pid" 10

    # Demonstrate job control
    job_control "sleep 30"
}

main "$@"
```

>[!info]
This script encapsulates each functionality into a separate function. Run the script to observe the actions in order: listing processes, starting and terminating a background process, adjusting its niceness, and demonstrating job control.

##### Simplification of the code 

In the `printf` command:

```bash
kill "$pid" && printf "Process %d terminated after %d seconds\n" "$pid" "$delay"
```

Each `%d` is a format specifier used by `printf` for integer formatting. It instructs `printf` to format and insert an integer value at that position in the output string. In this specific line:

- The first `%d` is replaced with the value of `$pid`, which is expected to be the Process ID (PID) of the background process you are terminating. This PID is an integer.

- The second `%d` is replaced with the value of `$delay`, which is the number of seconds after which the process is terminated. `$delay` is also an integer.

So, if `$pid` is 1234 and `$delay` is 10, the output will be: "Process 1234 terminated after 10 seconds". This output is printed only if the `kill` command successfully sends the termination signal to the process.



---
#### Version 2:

```bash
#!/bin/bash

# Function to list current user processes
list_user_processes() {
    printf "Current user processes including PID, PPID, and controlling terminal:\n"
    ps -o pid,ppid,tty,cmd -u "$(whoami)"
}

# Function to start a predefined background process
start_background_process() {
    local cmd="sleep 30"
    $cmd &
    bg_pid=$!
    printf "Background process started with PID: %d\n" "$bg_pid"
}

# Function to send a signal to terminate a predefined background process
terminate_background_process() {
    local pid=$bg_pid
    local delay=10
    sleep "$delay"
    kill "$pid" && printf "Process %d terminated after %d seconds\n" "$pid" "$delay"
}

# Function to adjust the niceness of a predefined process
adjust_niceness() {
    local pid=$bg_pid
    local new_nice=10
    renice "$new_nice" -p "$pid" && printf "Niceness of process %d adjusted to %d\n" "$pid" "$new_nice"
}

# Function to demonstrate job control on a predefined job
job_control() {
    local cmd="sleep 30"
    $cmd &
    job_pid=$!
    printf "Job started with PID: %d\n" "$job_pid"

    # Stop the process
    kill -STOP "$job_pid"
    printf "Job %d stopped\n" "$job_pid"

    # Resume the process
    kill -CONT "$job_pid"
    printf "Job %d resumed\n" "$job_pid"
}

main() {
    # List current user processes
    list_user_processes

    # Start a background process
    start_background_process

    # Terminate the background process after a predefined delay
    terminate_background_process

    # Adjust the niceness of the background process
    adjust_niceness

    # Demonstrate job control on a predefined job
    job_control
}

main "$@"
```





## Manipulate data linux_commands

You are provided with a log file (`server.log`) containing information about various server events. Your task is to create a Bash script that processes this log file and generates a report with the following information:

1. The total number of requests made to the server.
2. A list of unique IP addresses that made requests to the server, sorted alphabetically.
3. The top 2 most requested URLs, along with their request count.
4. The number of requests that resulted in server errors (HTTP status codes 5xx).

**Initial Input (`server.log`):**

```
192.168.1.1 - - [01/Jan/2022:10:15:30 +0000] "GET /index.html HTTP/1.1" 200 1024
192.168.1.2 - - [01/Jan/2022:10:16:45 +0000] "POST /login HTTP/1.1" 200 512
192.168.1.1 - - [01/Jan/2022:10:17:10 +0000] "GET /about.html HTTP/1.1" 404 256
192.168.1.3 - - [01/Jan/2022:10:18:30 +0000] "GET /index.html HTTP/1.1" 200 1024
192.168.1.2 - - [01/Jan/2022:10:20:15 +0000] "GET /contact.html HTTP/1.1" 500 2048
192.168.1.1 - - [01/Jan/2022:10:19:10 +0000] "GET /about.html HTTP/1.1" 502 256
```

1. Analyze the provided `server.log` file.
2. Write a Bash script `process_log.sh` that generates a report based on the criteria outlined above.
3. Test your script to ensure it produces the correct output in `report.txt`.

**Advanced Task:**

1. The distribution of HTTP status codes (e.g., how many 200, 404, 500, etc.).
2. The average size of the responses (extracted from the last field in each log entry).
3. A breakdown of requests by hour.

### <span style="color:#ffc000">code</span>:

Below is a Bash script named `process_log.sh` designed to process the `server.log` file as per your requirements. It will generate a report with the specified metrics and handle advanced tasks like analyzing the distribution of HTTP status codes, calculating the average size of responses, and breaking down requests by hour.

```bash
#!/bin/bash

log_file="server.log"
report_file="report.txt"

# Function to count total number of requests
count_requests() {
    local total_requests=$(wc -l < "$log_file")
    printf "Total number of requests: %d\n" "$total_requests"
}

# Function to list unique IP addresses
list_unique_ips() { 
    local ips=$(awk '{print $1}' "$log_file" | sort -u)
    printf "List of unique IP addresses:\n%s\n" "$ips"
}

# Function to find the top 2 most requested URLs
top_two_urls() {
    local top_urls=$(awk '{print $7}' "$log_file" | sort | uniq -c | sort -nr | head -2)
    printf "Top 2 most requested URLs:\n%s\n" "$top_urls"
}

# Function to count requests resulting in server errors (HTTP status codes 5xx)
count_server_errors() {
    local error_count=$(awk '$9 ~ /^5/ {count++} END {print count}' "$log_file")
    printf "Number of requests that resulted in server errors: %d\n" "${error_count:-0}"
}

# Function to analyze HTTP status codes distribution
status_code_distribution() {
    local distribution=$(awk '{print $9}' "$log_file" | sort | uniq -c | sort -nr)
    printf "HTTP status code distribution:\n%s\n" "$distribution"
}

# Function to calculate the average size of the responses
average_response_size() {
    local total_size=$(awk '{total+=$10} END {print total}' "$log_file")
    local count=$(awk 'END {print NR}' "$log_file")
    local average_size=$(echo "scale=2; $total_size / $count" | bc)
    printf "Average size of the responses: %.2f bytes\n" "$average_size"
}

# Function to break down requests by hour
requests_by_hour() {
    local by_hour=$(awk -F'[:[]' '{print $2}' "$log_file" | cut -d':' -f1 | sort | uniq -c | sort -k2)
    printf "Breakdown of requests by hour:\n%s\n" "$by_hour"
}

# Main function to orchestrate the script
main() {
    {
        count_requests
        echo ""
        list_unique_ips
        echo ""
        top_two_urls
        echo ""
        count_server_errors
        echo ""
        status_code_distribution
        echo ""
        average_response_size
        echo ""
        requests_by_hour
    } > "$report_file"
}

# Execute main function
main
```


>[!info]
>Each function focuses on a specific task and outputs its findings to `report.txt` using redirection. Make sure that `server.log` is in the same directory as this script when running it, or modify the script to accept the log file path as an argument.

#### Code analysis 

### 1. `count_requests`
This function counts the total number of requests made to the server. It uses the `wc -l` command to count lines in the log file, which represents the number of requests, assuming each request is logged on a new line.

```bash
wc -l < "$log_file"
```
- `wc -l`: Counts the lines in the file.
- `"$log_file"`: Redirects the content of `server.log` directly into `wc`, avoiding a subshell invocation that `cat "$log_file" | wc -l` would entail.

### 2. `list_unique_ips`
This function extracts and lists all unique IP addresses that made requests to the server. It processes the first field from each log entry (typically the IP address), sorts them, and then filters out duplicates using `sort -u`.

```bash
awk '{print $1}' "$log_file" | sort -u
```
- `awk '{print $1}'`: Extracts the first column (IP address).
- `sort -u`: Sorts the IP addresses and removes duplicates.

### 3. `top_two_urls`
This function identifies the top two most requested URLs. It extracts the URLs (assuming they are located in the seventh field of each log entry), counts occurrences, sorts them in descending order, and then picks the top two.

```bash
awk '{print $7}' "$log_file" | sort | uniq -c | sort -nr | head -2
```
- `awk '{print $7}'`: Extracts the URL.
- `uniq -c`: Counts each unique line.
- `sort -nr`: Sorts numerically in reverse to get the most frequent at the top.
- `head -2`: Retrieves only the top two results.

### 4. `count_server_errors`
This function counts the number of requests that resulted in server errors (HTTP status codes starting with '5'). It scans the ninth field for status codes, counts those starting with '5', and prints the count.

```bash
awk '$9 ~ /^5/ {count++} END {print count}' "$log_file"
```
- `$9 ~ /^5/`: Matches lines where the ninth field starts with '5'.
- `count++`: Increments a counter each time a match is found.
- `END {print count}`: At the end of input, print the final count.

### 5. `status_code_distribution`
This function analyzes the distribution of all HTTP status codes. It sorts and counts each unique status code.

```bash
awk '{print $9}' "$log_file" | sort | uniq -c | sort -nr
```
- `awk '{print $9}'`: Extracts the status code.
- `uniq -c`: Counts each unique status code.
- `sort -nr`: Sorts the results numerically in reverse order.

### 6. `average_response_size`
This function calculates the average size of the responses by analyzing the last field, which presumably contains the size of the response in bytes. It totals these values and divides by the number of entries.

```bash
awk '{total+=$10} END {print total}' "$log_file"
echo "scale=2; $total_size / $count" | bc
```
- `total+=$10`: Adds the value of the tenth field (response size) to a running total.
- `scale=2`: Ensures the division result has two decimal places.
- `bc`: Performs precise floating-point arithmetic.

### 7. `requests_by_hour`
This function breaks down the number of requests by hour, extracting the hour from timestamps in the log entries and counting occurrences.

```bash
awk -F'[:[]' '{print $2}' "$log_file" | cut -d':' -f1 | sort | uniq -c | sort -k2
```
- `awk -F'[:[]' '{print $2}'`: Splits the line on colons and brackets, extracting the hour.
- `cut -d':' -f1`: Further refines the extraction of hours.
- `uniq -c`: Counts occurrences of each hour.
- `sort -k2`: Sorts by the second field (the hour).

---
---






## package managment

Create a Bash script that performs the following tasks:

1. Compress the `/var/log` directory using `tar` and `gzip`, then decompress it to a directory named `log_backup`.
2. Create a text file named `test.txt`, compress it with both `zip` and `gzip`, and compare the sizes of the compressed files.
3. List the first 10 installed packages on the system using `dpkg` (for Debian-based systems) or `rpm` (for Red Hat-based systems).
4. Search for the package `curl` using `apt` (Debian-based) or `yum` (Red Hat-based) and display its dependencies.
5. Install the package `tree`, verify its installation by running `tree --version`, and then remove it.

### code:

```bash
#!/bin/bash

set -o pipefail

# Global variable for the backup directory
LOG_BACKUP_DIR="log_backup"

# Main function
main() {
    # Compress and decompress log directory
    compress_decompress_logs

    # Compress test.txt with zip and gzip, and compare sizes
    create_and_compress_text_file

    # List first 10 installed packages
    list_installed_packages

    # Search for curl package dependencies
    check_curl_dependencies

    # Install tree, verify, and remove
    manage_tree_package
}

# Compress /var/log directory using tar and gzip, then decompress to log_backup
compress_decompress_logs() {
    local tar_file="/tmp/var_log.tar.gz"

    # Compress logs
    if ! tar -czf "$tar_file" /var/log; then
        printf "Failed to compress /var/log directory\n" >&2
        return 1
    fi

    # Create backup directory
    mkdir -p "$LOG_BACKUP_DIR"

    # Decompress logs
    if ! tar -xzf "$tar_file" -C "$LOG_BACKUP_DIR"; then
        printf "Failed to decompress to $LOG_BACKUP_DIR\n" >&2
        return 1
    fi
}

# Create test.txt, compress with zip and gzip, compare sizes
create_and_compress_text_file() {
    local txt_file="test.txt"
    local zip_file="test.zip"
    local gz_file="test.gz"

    # Create test.txt
    printf "Sample text for compression" > "$txt_file"

    # Compress with zip
    zip "$zip_file" "$txt_file"

    # Compress with gzip
    gzip -k "$txt_file"

    # Compare file sizes
    printf "Sizes of compressed files:\n"
    ls -lh "$zip_file" "$gz_file"
}

# List the first 10 installed packages
list_installed_packages() {
    if command -v dpkg &> /dev/null; then
        dpkg -l | head -n 11
    elif command -v rpm &> /dev/null; then
        rpm -qa | head -n 10
    else
        printf "No package manager found for listing packages\n" >&2
        return 1
    fi
}

# Search for the package curl and display dependencies
check_curl_dependencies() {
    if command -v apt &> /dev/null; then
        apt show curl | grep -i depends
    elif command -v yum &> /dev/null; then
        yum deplist curl
    else
        printf "No package manager found for checking curl dependencies\n" >&2
        return 1
    fi
}

# Install tree, verify installation, and remove it
manage_tree_package() {
    # Install tree
    if ! install_package "tree"; then
        printf "Failed to install tree\n" >&2
        return 1
    fi

    # Verify installation
    if ! tree --version; then
        printf "Failed to verify tree installation\n" >&2
        return 1
    fi

    # Remove tree
    remove_package "tree"
}

# Install a package
install_package() {
    local package=$1
    if command -v apt &> /dev/null; then
        apt install -y "$package"
    elif command -v yum &> /dev/null; then
        yum install -y "$package"
    else
        printf "No package manager available to install packages\n" >&2
        return 1
    fi
}

# Remove a package
remove_package() {
    local package=$1
    if command -v apt &> /dev/null; then
        apt remove -y "$package"
    elif command -v yum &> /dev/null; then
        yum remove -y "$package"
    else
        printf "No package manager available to remove packages\n" >&2
        return 1
    fi
}

# Call main function
main
```


### analysis

Sure, let's dive deeper into the workings of each function within the Bash script, detailing the commands and their interactions:

### Function: `compress_decompress_logs`
```bash
compress_decompress_logs() {
    local tar_file="/tmp/var_log.tar.gz"

    # Compress logs
    if ! tar -czf "$tar_file" /var/log; then
        printf "Failed to compress /var/log directory\n" >&2
        return 1
    fi

    # Create backup directory
    mkdir -p "$LOG_BACKUP_DIR"

    # Decompress logs
    if ! tar -xzf "$tar_file" -C "$LOG_BACKUP_DIR"; then
        printf "Failed to decompress to $LOG_BACKUP_DIR\n" >&2
        return 1
    fi
}
```
**Detailed Explanation**:
- **Compression**: The `tar -czf` command is used to compress the `/var/log` directory. The `-c` flag creates an archive, `-z` uses gzip for compression, and `-f` specifies the filename of the archive. The `!` operator is used to check the success of the command. If the compression fails, an error message is printed to `stderr` and the function returns 1, indicating an error.
- **Directory Creation**: `mkdir -p "$LOG_BACKUP_DIR"` ensures the backup directory exists by creating it if it doesn't. The `-p` flag prevents an error if the directory already exists.
- **Decompression**: The `tar -xzf` command decompresses the archive into the specified directory (`$LOG_BACKUP_DIR`). The `-x` flag extracts files from an archive, `-z` is for gzip decompression, and `-f` specifies the archive file. If decompression fails, it logs an error to `stderr` and returns 1.

### Function: `create_and_compress_text_file`
```bash
create_and_compress_text_file() {
    local txt_file="test.txt"
    local zip_file="test.zip"
    local gz_file="test.gz"

    # Create test.txt
    printf "Sample text for compression" > "$txt_file"

    # Compress with zip
    zip "$zip_file" "$txt_file"

    # Compress with gzip
    gzip -k "$txt_file"

    # Compare file sizes
    printf "Sizes of compressed files:\n"
    ls -lh "$zip_file" "$gz_file"
}
```
**Detailed Explanation**:
- **File Creation**: `printf` is used to create `test.txt` with some initial text. The redirection `>` writes the text to the file.
- **ZIP Compression**: The `zip` command compresses the text file into a ZIP archive. The compressed file is named `test.zip`.
- **GZIP Compression**: `gzip -k` compresses the file using GZIP but keeps the original file (`-k` flag).
- **Size Comparison**: `ls -lh` lists the file sizes in a human-readable format, allowing comparison of the efficiency of the two compression methods.

### Function: `list_installed_packages`
```bash
list_installed_packages() {
    if command -v dpkg &> /dev/null; then
        dpkg -l | head -n 11
    elif command -v rpm &> /dev/null; then
        rpm -qa | head -n 10
    elif command -v apt &> /dev/null; then 
		apt list --installed | head -n 10
    else
        printf "No package manager found for listing packages\n" >&2
        return 1
    fi
}
```
**Detailed Explanation**:
- **Package Manager Detection**: Uses `command -v` to check for the presence of `dpkg` or `rpm`. This command returns the path of the command if it exists, suppressing output with `&> /dev/null`.
- **Package Listing**: Depending on the package manager, it lists installed packages. `dpkg -l` lists all packages in Debian systems, and `rpm -qa` lists all in RPM systems. `head -n 10` or `head -n 11` limits the output to the first 10 entries.

### Function: `check_curl_dependencies`
```bash
check_curl_dependencies() {
    if command -v apt &> /dev/null; then
        apt show curl | grep -i depends
    elif command -v yum &> /dev/null; then
        yum deplist curl
    else
        printf "No package manager found for checking curl dependencies\n" >&2
        return 1
    fi
}
```
**Detailed Explanation**:
- **Dependency Check**: The script first determines whether to use `apt` or `yum` based on system type. `apt show curl` displays detailed information for the `curl` package in Debian-based systems, and `grep -i depends` filters this to show only dependency lines. For RPM systems, `yum deplist curl` directly lists all dependencies