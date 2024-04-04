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

Certainly! Here are some common Bash commands along with examples that you can try in the shell:

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

Remember that these examples should be adjusted according to your specific needs and the actual names of files and directories on your system. Some commands might require superuser privileges and should be used with care to avoid unintended effects.

---
# Bash Script

Bash scripting provides a powerful way to automate tasks in Linux. Here's an overview of commands and constructs you can use in a Bash script, particularly for taking input from users, working with variables, and more

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
### Understanding `$#`:
[$#]
- When you run a Bash script, you can pass arguments to it, much like passing arguments to a function in programming languages.
- These arguments are accessible inside the script through special variables `$1`, `$2`, `$3`, and so on, where `$1` is the first argument, `$2` is the second, etc.
- The `$#` variable contains the count of these arguments.

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