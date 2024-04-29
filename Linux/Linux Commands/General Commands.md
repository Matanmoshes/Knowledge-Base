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
