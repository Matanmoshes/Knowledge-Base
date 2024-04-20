# `stdin` and `stdout`

In the context of Linux and programming, `stdin` and `stdout` refer to standard input and standard output streams, respectively. These are fundamental concepts used in handling input and output operations in most operating systems, including Unix-like systems such as Linux.

- **Standard Input (stdin):** This is the default input stream which provides input to programs. By default, it reads input from the keyboard, but it can be redirected to read input from files or other devices. In programming, stdin is often accessed using file descriptor 0.

- **Standard Output (stdout):** This is the default output stream used by programs to write their output data. Normally, stdout displays output directly to the terminal, but it can be redirected to write to files or other outputs. In programming, stdout is generally accessed using file descriptor 1.

These streams can be manipulated in the shell using redirection operators. For example:
- To redirect the output of a command to a file, you can use `command > file.txt`.
- To feed the contents of a file to a program, you can use `command < file.txt`.

![[Pasted image 20240420113559.png]]

---


# Text Editor vim
[vi]  [vim]
Certainly! Here are some commonly used Vim commands, organized by their mode of operation:

### Basic Commands (Normal Mode):

1. **Opening and Saving Files**:
   - Open a file: `vim filename`
   - Save file: `:w`
   - Save as: `:w filename`
   - Save and exit: `:wq` or `ZZ`
   - Exit without saving: `:q!`

2. **Insert Mode**:
   - Enter Insert mode: `i` (insert before cursor), `I` (insert at the beginning of the line), `a` (insert after cursor), `A` (insert at the end of the line), `o` (open new line below), `O` (open new line above)
   - Exit Insert mode: `Esc`

3. **Movement**:
   - Left, down, up, right: `h`, `j`, `k`, `l`
   - Start of the line: `0`
   - End of the line: `$`
   - Next word: `w`
   - Previous word: `b`
   - Start of the file: `gg`
   - End of the file: `G`
   - Go to line n: `nG` or `:n`

4. **Editing**:
   - Delete character: `x`
   - Delete line: `dd`
   - Delete from cursor to end of line: `D`
   - Undo: `u`
   - Redo: `Ctrl + r`
   - Copy (yank) line: `yy`
   - Copy n lines: `nyy` (where n is the number of lines)
   - Paste below/above: `p`/`P`

5. **Visual Mode**:
   - Enter Visual mode: `v`
   - Visual Line mode: `V`
   - Visual Block mode: `Ctrl + v`
   - Exit Visual mode: `Esc`

6. **Search and Replace**:
   - Search: `:/pattern`
   - Next match: `n`
   - Previous match: `N`
   - Replace first instance in line: `:s/pattern/replacement`
   - Replace all instances in line: `:s/pattern/replacement/g`
   - Replace all instances in file: `:%s/pattern/replacement/g`

7. **Command Mode**:
   - Execute command: `:[command]` (e.g., `:!ls` to list directory contents)

Remember, Vim modes are an essential aspect of its operation. Normal mode is for navigating and manipulating text, Insert mode is for typing text, and Visual mode is for selecting blocks of text. Mastery of Vim commands greatly enhances editing efficiency.


---


# Linux File System
[linux file system]

In Linux, a Unix-like operating system, the file system is organized hierarchically, starting from the root (`/`) directory. Here's a consolidated overview of key directories:

1. **`/` (Root Directory):** The top level of the file system hierarchy. All other directories, files, and devices branch off from here. Example: `cd /` takes you to the root.

2. **`/bin` and `/sbin` (Binary/System Binaries):** Contain essential user and system commands and utilities. `/bin` has common commands like `ls`, `cp`, while `/sbin` holds system administration commands like `reboot`.

3. **`/boot`:** Holds boot-related files, including the Linux kernel and bootloader.

4. **`/dev` (Devices):** Contains device files representing and interfacing with hardware components.

5. **`/etc`:** Stores system-wide configuration files, which are usually text-based and modifiable by administrators.

6. **`/home`:** Home directories for regular users, typically named after each user. For example, `/home/john` for user 'john'.

7. **`/lib` (Library):** Contains shared libraries needed for system booting and binary file operations, as well as kernel modules.

8. **`/media` and `/mnt` (Mount):** Designated for mounting external and temporary filesystems. `/media` is commonly used for removable media like USB drives, while `/mnt` is for temporary mounts.

9. **`/opt` (Optional):** Used for optional or third-party software installations.

10. **`/proc`:** A virtual filesystem with runtime system information and kernel settings, such as `/proc/cpuinfo`.

11. **`/srv` (Service):** Stores data for services offered by the system.

12. **`/tmp` (Temporary):** A place for temporary files, cleared at reboot or periodically.

13. **`/usr` (Unix System Resources):** A secondary hierarchy for user utilities and applications, including a vast majority of user-executable binaries, libraries, and documentation.

14. **`/var` (Variable):** Used for variable data like logs, databases, and email storage.


---




# File System
### Virtual File System
[virtual file system]
The Virtual File System (VFS) is an abstraction layer in an operating system that provides a uniform interface to different file systems. It allows applications to access various file systems (like ext4, NTFS, NFS) using the same system calls, regardless of their specific implementation. VFS simplifies file operations for applications, ensuring compatibility and flexibility in handling files stored on different types of file systems.

---
### Journaling
[journaling]
Journaling in file systems is a technique for protecting data integrity. It involves recording changes to a journal before they're actually made to the file system. If a crash or power failure occurs, this journal helps in quickly restoring the system to a consistent state, reducing the risk of data corruption. Journaling is essential for maintaining data reliability, especially in systems where unexpected shutdowns can happen. It's used in file systems like ext3/ext4, NTFS, and HFS+.

---
### Block storage
[block storage]
Block storage is a type of data storage where data is stored in fixed-size blocks, each with a unique address. Common in storage-area networks (SANs) and cloud storage, it offers high performance by allowing direct and individual access to each block. Ideal for databases and virtualized environments, block storage lacks metadata, providing only raw data storage without contextual information. Its key advantage is speed and flexibility, though it can be more complex to manage compared to file-based storage.

---
### inode
[inode]
An inode in UNIX-like file systems is a data structure that stores important information about a file or directory, such as size, permissions, ownership, timestamps, and pointers to data blocks. Each file or directory has a unique inode, which contains metadata but not the file name or content. Inodes play a key role in file management, allowing the system to locate and access files efficiently.

---

### Partitioning
[Partitioning]
Partitioning is the process of dividing a hard drive or storage device into distinct sections, known as partitions, which are managed as separate logical units. This allows for efficient organization of data, installation of multiple operating systems on a single device, and can improve system performance. Each partition can have a different file system and be used for different purposes, such as separating system files from user data. Partitioning is a key aspect of managing and optimizing storage in computing.

1. **Types of Partitions**:
   - **Primary Partition**: A primary partition can contain one file system and is one of the partitions from which an operating system can be booted.
   - **Extended Partition**: An extended partition is a special type of partition that can hold multiple logical partitions.
   - **Logical Partition**: Inside an extended partition, you can create multiple logical partitions, each acting like a separate disk.


---
## CPU Core

A core is an individual processing unit within the CPU that can work
on a task. A CPU can have multiple cores, allowing it to perform
multiple processes or threads simultaneously, which is known as

multi-core processing.

---
### Process

A process is an instance of a computer program that is being
executed. It contains the program code and its activity. Depending
on the operating system, a process may be made up of multiple
threads of execution that execute instructions concurrently.

---

### Thread

A thread is the smallest sequence of programmed instructions that can be managed independently by a scheduler. It is a component of a process that can execute independently, particularly in multi-threaded applications, allowing for parallel execution within a single process.


---

---
# Users & Groups in Linux

---
## Users
[users]

In Linux, users are the entities with permission to interact with the system. Each user has a unique username and is assigned a user ID (UID). Users can belong to groups, which also have their own IDs (GID).

1. **Types of Users**:
   - **Root User**: The superuser with complete control over the system. It has UID 0 and can perform any action, including those restricted for other users.
   - **System Users**: Created for running specific system tasks and services, typically without login capabilities. They have UIDs less than 1000 (this can vary).
   - **Regular Users**: These are the general users created for human operators of the system, each with its own environment and permissions.

2. **User Management**:
   - Users are created, modified, and deleted using commands like `useradd`, `usermod`, and `userdel`.
   - Each user has a home directory, typically under `/home/username`.
   - User-specific settings are stored in their home directory, often in hidden files/folders (starting with a dot).

<span style="color:#92d050">Here's a brief overview with shell examples:</span>

1. **Listing Users:**
   - To see all users, you can view the `/etc/passwd` file.
     ```bash
     $ cat /etc/passwd
     ```

2. **Adding a User:**
   - To add a new user (e.g., `newuser`):
     ```bash
     $ sudo adduser newuser
     ```

3. **Changing Passwords:**
   - To change a user's password:
     ```bash
     $ sudo passwd username
     ```

4. **Switching Users:**
   - To switch to another user (e.g., `anotheruser`):
     ```bash
     $ su - anotheruser
     ```

5. **Using Superuser Privileges:**
   - For running a command with superuser (root) privileges:
     ```bash
     $ sudo command
     ```

>[!info]
Each user has a home directory (usually in `/home/username`) for personal files, and different users have different permission levels for system access. The `root` user has the highest access level, capable of making any changes to the system.



---
### `/etc/passwd`
[passwd]

The `/etc/passwd` file in Linux systems is a text-based database containing information about user accounts. Each line in this file represents a single user account and is structured in a series of fields separated by colons. Here's a breakdown of the line you provided as an example:

```
dan:x:1001:1001:Dan Smith,,,:/home/dan:/bin/bash
```

1. **Username (`dan`)**: This is the user's login name. It's used when logging into the system and for user-specific settings and files.

2. **Password (`x`)**: Traditionally, this field contained the user's password in an encrypted format. However, in modern systems for increased security, this field is usually set to 'x' or '*', indicating that the actual encrypted password is stored in the `/etc/shadow` file, which is accessible only by privileged users.

3. **User ID (UID, `1001`)**: This is a unique numerical identifier for the user. The UID is used by the system to identify the user, manage permissions, and control access to resources. The UID 0 is reserved for the root user.

4. **Group ID (GID, `1001`)**: This is the primary group ID for the user. Each user belongs to at least one group, and this field specifies the default group. Like UIDs, GIDs are also numerical and used to manage group permissions and access.

5. **User Information (GECOS, `Dan Smith,,,`)**: This field, known as GECOS, can contain additional information about the user, like the full name, contact details, etc. It's not used by the system for any control or security purposes and is purely informational.

6. **Home Directory (`/home/dan`)**: This specifies the path to the user's home directory, where their personal files and user-specific settings are stored.

7. **Login Shell (`/bin/bash`)**: This field specifies the command interpreter or shell that is run when the user logs into their account. In this case, it's `/bin/bash`, the Bourne-Again Shell, a common choice on Linux systems.


---

### <span style="color:#9263b6">User Magnet Commands</span>

1. **Creating a New User**:
   ```bash
   useradd [options] [username]
   ```
   Example: `useradd alice` - This creates a new user named 'alice'.

2. **Setting or Changing a User's Password**:
   ```bash
   passwd [username]
   ```
   Example: `passwd alice` - This sets or changes the password for the user 'alice'.

3. **Deleting a User**:
   ```bash
   userdel [options] [username]
   ```
   Example: `userdel alice` - This deletes the user 'alice'. Use the `-r` option to remove the home directory and mail spool.

4. **Modifying a User Account**:
   ```bash
   usermod [options] [username]
   ```
   Example: `usermod -d /new/home/dir -m alice` - This changes the home directory for 'alice' to `/new/home/dir` and moves her files there.

5. **Changing the Default Shell for a User**:
   ```bash
   usermod -s [shell] [username]
   ```
   Example: `usermod -s /bin/zsh alice` - This sets Zsh as the default shell for 'alice'.

6. **Listing Information About a User**:
   ```bash
   id [username]
   ```
   Example: `id alice` - This displays the user ID, group ID, and groups the user 'alice' is in.

7. **Displaying the Last Users Who Logged In**:
   ```bash
   last
   ```
   This shows a list of the last logged-in users.

8. **Switching to Another User Account**:
   ```bash
   su - [username]
   ```
   Example: `su - alice` - This switches to the user 'alice'.
   
>[!info]
Remember that some of these commands (like `useradd`, `userdel`, and `usermod`) typically require superuser privileges, so they are often used with `sudo`.

---
### Groups
[Groups]

In Linux, groups are collections of users, functioning as a method for organizing users and managing file permissions. A group is identified by a group name and a unique group ID (GID). Here's an overview along with an example:

#### Purpose of Groups:

1. **Permission Management**: Groups simplify the process of assigning permissions to files and directories. Instead of setting permissions for individual users, a group can be assigned, and all members inherit these permissions.

2. **Organization**: Groups help organize users with similar roles or requirements, like a group for developers, another for editors, etc.

#### Types of Groups:

1. **Primary Group**: When a user is created, they are assigned a primary group. Files and directories created by the user are typically owned by their primary group.

2. **Secondary Group**: Users can be members of multiple secondary groups. These groups grant additional access rights beyond those of the primary group.


>[!info]
>
>#### <span style="color:#00b050">Group File</span>:
>
>- The `/etc/group` file contains group information.
>- Format: `groupname:x:GID:user1,user2,...`
>- Example: `developers:x:1001:alice,bob,charlie`
>
><span style="color:#9263b6">In this example:</span>
>
>- `developers`: Group name.
>- `x`: Password placeholder, but like `/etc/passwd`, actual passwords are in `/etc/gshadow`.
>- `1001`: Unique Group ID.
>- `alice,bob,charlie`: Members of the group.

#### <span style="color:#9263b6">Managing Groups</span>:

- **Create Groups**: `groupadd [groupname]`
- **Add Users to Group**: `usermod -a -G [groupname] [username]`
- **Delete Groups**: `groupdel [groupname]`

<span style="color:#ffc000">here are some common commands used for managing groups in Linux:</span>

1. **Creating a New Group**:
   ```bash
   groupadd [groupname]
   ```
   Example: `groupadd developers` - This creates a new group named 'developers'.

2. **Adding a User to a Group**:
   ```bash
   usermod -aG [groupname] [username]
   ```
   Example: `usermod -aG developers alice` - This adds the user 'alice' to the 'developers' group.

3. **Deleting a Group**:
   ```bash
   groupdel [groupname]
   ```
   Example: `groupdel developers` - This deletes the 'developers' group.

4. **Changing the Primary Group of a User**:
   ```bash
   usermod -g [groupname] [username]
   ```
   Example: `usermod -g developers bob` - This sets 'developers' as the primary group for the user 'bob'.

5. **Listing All Groups**:
   ```bash
   groups [username]
   ```
   Example: `groups alice` - This lists all groups the user 'alice' is a part of.

6. **Viewing Group Information**:
   ```bash
   getent group [groupname]
   ```
   Example: `getent group developers` - This shows information about the 'developers' group, including its members.

These commands are typically run with superuser privileges (using `sudo`), especially for adding, modifying, or deleting groups, to ensure proper permissions are maintained.

---



---
# File Permutations
[file permutations]
>[!Success]
File permissions in Linux are rules that specify who can access files and directories and how they can interact with them. There are three types of permissions:
>
>1. **Read (r)**: Permission to view the contents of the file or directory.
>2. **Write (w)**: Permission to modify the contents of the file or directory.
>3. **Execute (x)**: Permission to execute a file or access a directory.
>
>These permissions apply to three categories of users:
>
>1. **User (u)**: The owner of the file.
>2. **Group (g)**: Users who are members of the file’s group.
>3. **Others (o)**: Everyone else.
>
### Example of File Permissions:

Consider a file with the following permissions:

```bash
-rwxr-xr--
```

- `-rwx` for the user: The owner can read, write, and execute.
- `r-x` for the group: Group members can read and execute, but not write.
- `r--` for others: Others can only read.


![[Pasted image 20240419134244.png]]

---
## chmod
[chmod]
#### Changing Permissions:

To modify file permissions, use the `chmod` (change mode) command. Permissions can be set either symbolically or numerically (using octal values).

- **Symbolically**:
  ```bash
  chmod [who][+|-][permissions] [filename]
  ```
  Examples:
  - `chmod u+x file.txt` - Adds execute permission for the user.
  - `chmod go-wx file.txt` - Removes write and execute permissions for group and others.

- **Numerically**:
  Each permission type has a value: read (4), write (2), and execute (1). To set permissions, add up these values.

  ```bash
  chmod [mode] [filename]
  ```
  Example:
  - `chmod 755 file.txt` - Sets permissions to `-rwxr-xr-x` (user: rwx, group: rx, others: rx).

### Viewing Permissions:

Use the `ls -l` command to view permissions:

```bash
ls -l [filename]
```

Understanding and correctly setting file permissions is crucial for security and proper functioning in a Linux environment.

---
## chown

[chown]
`chown`, short for "change owner", is a command in Linux used to change the ownership of files and directories. It allows you to set or modify the owner and the group that owns a file or directory.

#### Basic Usage:

1. **Changing the Owner**:
   ```bash
   chown [new-owner] [file]
   ```
   Example: `chown alice file.txt` - This command changes the owner of `file.txt` to the user 'alice'.

2. **Changing the Owner and Group**:
   ```bash
   chown [new-owner]:[new-group] [file]
   ```
   Example: `chown alice:developers file.txt` - This changes the owner to 'alice' and the group to 'developers'.

3. **Changing the Group Only**:
   Although `chown` is primarily for changing file owners, it can also change groups when used with a colon:
   ```bash
   chown :[new-group] [file]
   ```
   Example: `chown :developers file.txt` - This changes the group of `file.txt` to 'developers' without altering the file's owner.

4. **Recursive Change**:
   For directories, you can change ownership of the directory and all its contents recursively using the `-R` option:
   ```bash
   chown -R [new-owner]:[new-group] [directory]
   ```
   Example: `chown -R alice:developers /home/alice` - This recursively changes the owner and group of all files and subdirectories inside `/home/alice`.
   
>[!Tip]
>### Notes:
>
>- `chown` is a privileged command, often requiring superuser access. In practice, it is often used with `sudo` for administrative purposes.
>- Only the root user or users with sufficient privileges can change the ownership of files/directories.


---


---
# Environment Variables
[environment variable]

>[!info]
Environment variables in Linux are key-value pairs that provide context and configuration settings for the operating system and for applications running on it. They store data that is used by the shell and other programs to determine the functioning of various processes.

---

#### Key Characteristics:

1. **`HOME`**: 
   - Description: Specifies the current user's home directory.
   - Example: If your username is `john`, `HOME` might be `/home/john`.

2. **`PATH`**: 
   - Description: Contains a colon-separated list of directories where the shell looks for executable files.
   - Example: `/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin`

3. **`USER`**: 
   - Description: Shows the username of the current user.
   - Example: If logged in as John, `USER` would be `john`.

4. **`PWD`**: 
   - Description: Stands for 'Print Working Directory', showing the current working directory.
   - Example: If you're in `/home/john/Documents`, `PWD` would be `/home/john/Documents`.

5. **`SHELL`**: 
   - Description: Specifies the path to the shell program being used.
   - Example: For Bash, `SHELL` might be `/bin/bash`.

6. **`LANG`**: 
   - Description: Defines the system’s language and regional setting (locale).
   - Example: `en_US.UTF-8` for US English with UTF-8 encoding.

7. **`TERM`**: 
   - Description: Describes the type of terminal to emulate when running the shell.
   - Example: `xterm-256color` for an xterm with 256 colors support.

8. **`EDITOR`**: 
   - Description: The default text editor program.
   - Example: Could be set to `vim`, `nano`, or another text editor like `gedit`.

9. **`UID`**: 
   - Description: Shows the user ID of the current user.
   - Example: For the root user, `UID` would be `0`.

1. **System-Wide Influence**: They affect the behavior and operation of scripts and programs. For instance, they can dictate the search paths for executable files or define network settings.

2. **User-Specific and System-Wide Settings**: Some environment variables are global (affecting all users), while others can be set specifically for individual user sessions.

---

### Common Examples:

1. **`PATH`**: Contains a list of directories that the shell searches for executable programs. When you type a command, the shell looks in these directories to find the program.

   Example: `/usr/bin:/bin:/usr/sbin:/sbin`

2. **`HOME`**: Specifies the current user's home directory, where their personal files and subdirectories are located.

   Example: `/home/username`

3. **`USER`**: Indicates the username of the current user.

   Example: `username`

4. **`LANG`**: Defines the locale setting, which includes language, country, and character encoding.

   Example: `en_US.UTF-8`

5. **`SHELL`**: Specifies the path to the shell program.

   Example: `/bin/bash`

#### Setting and Viewing:

- **Setting a Variable** (temporarily, in the current shell session): 
  ```bash
  export VARNAME=value
  ```
  Example: `export EDITOR=vim` sets `EDITOR` to `vim`.

- **Viewing Environment Variables**:
  - To view the value of a specific variable: `echo $VARNAME`
  - To list all environment variables: `printenv` or `env`

#### Permanent Changes:

To make changes permanent, they're set in shell configuration files like `~/.bashrc`, `~/.profile`, or `/etc/environment`.

#### Example:

Suppose you want to add a directory to your PATH, so it includes a directory with your scripts:

1. Temporary change (current session only):
   ```bash
   export PATH=$PATH:/path/to/your/scripts
   ```

2. Permanent change (edit `~/.bashrc`):
   - Add `export PATH=$PATH:/path/to/your/scripts` to the end of `~/.bashrc`.
   - Apply changes: `source ~/.bashrc`



Environment variables in Linux are used to pass information to processes that are started by the shell. They are key-value pa
irs that define specific aspects of the system's environment, such as file locations, user data, and system settings.


---


---
# <span style="color:#9263b6">Linux Process</span>
[linux process]

>[!info]
A process in Linux is an instance of a running program. When you start an application or execute a command, the Linux operating system creates a process for it. Each process has a unique Process ID (PID).

---
### ps command
[ps]
1. **PID:** Process ID, a unique number identifying each running process.

2. **TTY:** Terminal type that the user is logged into.

3. **TIME:** The total amount of CPU time that the process has used since it started.

4. **CMD:** The command that launched the process.

Here's an example of what the output might look like when you run `ps` in your shell:

```bash
$ ps
  PID TTY          TIME CMD
 1234 pts/0    00:00:00 bash
 5678 pts/0    00:00:00 ps
```

- **PID 1234** is the Bash shell you're currently using.
- **PID 5678** represents the `ps` command that was just executed.

The `ps` command in Linux has several options that change what information is displayed about processes. Here are some of the common types:

1. **Default `ps`:**
   Shows processes associated with the current terminal.
   ```bash
   $ ps
   ```

2. **`ps -e` or `ps -A`:**
   Lists all running processes.
   ```bash
   $ ps -e
   ```

3. **`ps -f`:**
   Provides a full-format listing, which includes more details like the parent process ID (PPID).
   ```bash
   $ ps -f
   ```

4. **`ps -ef`:**
   Combines `-e` and `-f`, showing extended information about all processes.
   ```bash
   $ ps -ef
   ```

5. **`ps -u [user]`:**
   Shows processes for a specific user.
   ```bash
   $ ps -u username
   ```

6. **`ps -aux`:** [ps -aux]
   Though technically a combination of BSD-style options, it's commonly used in Linux to show detailed information about all running processes.
   ```bash
   $ ps aux
   ```

7. **`ps -l`:**
   Displays a long format list of processes.
   ```bash
   $ ps -l
   ```

8. **`ps -o [format]`:**
   Customizes the output by specifying a format. For example, `ps -o pid,cmd` shows only the PID and command columns.
   ```bash
   $ ps -o pid,cmd
   ```

9. **`ps -T`:**
   Lists all the threads of the process.
   ```bash
   $ ps -T
   ```

10. **`ps -C [command]`:**
    Shows processes by command name.
    ```bash
    $ ps -C command_name
    ```

11.  **`ps -p [PID]`:**
    This command shows the parents process of a process
       ```bash
    $ ps -p 77860
    ```

Each variation provides different levels of detail or formats the output in different ways, making `ps` a versatile tool for process management and monitoring.



---
## Process Commands

Here are some basic commands related to processes in the shell:

1. **<span style="color:#ffc000">Viewing Processes:</span>**
   - `ps` shows processes in the current shell.
     ```bash
     $ ps
     ```
   - `ps aux` <span style="color:#d68a8a">lists all running processes with detailed information.</span>
     ```bash
     $ ps aux
     ```

2. **Killing a Process:**
   - `kill` terminates a process using its PID.
     ```bash
     $ kill [PID]
     ```
   - If the process doesn't stop, you can use `kill -9 [PID]` for a forceful stop.

3. **Viewing Real-time Processes:**
   - `top` shows an ongoing view of process activity.
     ```bash
     $ top
     ```
   - `htop` (an enhanced version of top) needs to be installed but offers a more user-friendly interface.
     ```bash
     $ htop
     ```

4. **Background and Foreground Processes:**
   - To put a running process into the background, you can use `Ctrl + Z` and then `bg`.
     ```bash
     $ bg
     ```
   - To bring it back to the foreground, use `fg`.
     ```bash
     $ fg
     ```

5. **Starting a Background Process:**
   - You can start a process in the background by appending `&` to the command.
     ```bash
     $ mycommand &
     ```

These commands are fundamental for managing and interacting with processes in Linux. They allow you to monitor, control, and terminate processes as needed.

---
## <span style="color:#9263b6">Process Termination
</span>
### <span style="color:#ffc000">Normal Termination</span>
1. **How it Occurs:**
   - Completing its execution naturally.
   - Explicitly calling an exit function, like `exit()` in C.

2. **Exit Status:**
   - The process returns an exit status to its parent process.
   - A standard convention is to return `0` to indicate success.
   - Non-zero values indicate different types of errors.

3. **Resource Handling:**
   - The process can perform clean-up operations before termination.
   - It might close files, free memory, or write final output.

4. **Example:**
   ```c
   int main() {
       // Code execution
       exit(0); // Successful completion
   }
   ```

### <span style="color:#ffc000">Abnormal Termination</span>
1. **How it Occurs:**
   - Due to external interruptions like signals (e.g., `SIGKILL`, `SIGTERM`).
   - Caused by errors during execution, such as segmentation faults.

2. **Exit Status:**
   - The process is forced to terminate, often without a meaningful exit status.
   - The parent process receives a signal-specific exit code.

3. **Resource Handling:**
   - The process might not release resources properly, potentially leaving files or sockets open, or memory allocated.
   - Can lead to issues like memory leaks or locked files.

4. **Example:**
   - A process receiving a `kill -9 [PID]` command is forcibly terminated.
   - A C program encountering a segmentation fault due to invalid memory access.
>[!info]
In both cases, the operating system's kernel handles the termination process, ensuring that system resources are reclaimed. However, in abnormal termination, the lack of cleanup can lead to fragmented resources or inconsistent states, which is why graceful shutdowns and error handling within programs are important.

---

## <span style="color:#9263b6">Signal handling</span>

Signal handling in Linux and Unix-like operating systems is a way to manage inter-process communication and control. Signals are a form of limited message-sending system, primarily used to notify a process that a particular event has occurred.

### <span style="color:#ffc000">Key Points of Signal Handling</span>:

1. **What Are Signals?**
   - Signals are notifications sent to a process to prompt a predetermined action, like termination or interruption.

2. **Common Signals:**
   - `SIGTERM` (signal 15): Requests a process to terminate gracefully.
   - `SIGKILL` (signal 9): Forces a process to terminate immediately.
   - `SIGINT` (signal 2): Sent from the keyboard, usually the Ctrl+C command, to interrupt a process.
   - `SIGSTOP` (signal 17): Pauses a process.
   - `SIGHUP` (signal 1): Hang up; often used to reload configurations.

3. **Handling Signals:**
   In Bash scripting, you can handle signals using the `trap` command. It allows you to specify a script or a command to execute when a particular signal is received. Here's an example of how you might use signal handling in a Bash script:

```bash
#!/bin/bash

# Define a signal handler
handle_sigint() {
    echo "Interrupt signal (SIGINT) received. Exiting."
    exit 0
}

# Assign the handler to SIGINT (Ctrl+C)
trap handle_sigint SIGINT

echo "Script running... (press Ctrl+C to stop)"

# Infinite loop to keep the script running
while true; do
    sleep 1
done
```

In this script:
- The function `handle_sigint` is defined to handle the SIGINT signal (usually triggered by pressing Ctrl+C).
- The `trap` command then assigns this function to the SIGINT signal.
- The script enters an infinite loop, which keeps it running until you interrupt it with Ctrl+C.
- When you do interrupt, the `handle_sigint` function gets called, printing a message and exiting the script gracefully.

This technique is widely used in scripts that require cleanup or specific actions to be taken when they are interrupted or terminated.

---
## <span style="color:#9263b6">Niceness</span>

Niceness in Linux is a way to influence the scheduling priority of processes. It's used to indicate how "nice" a process should be to other processes on the system. Here’s a closer look:

### <span style="color:#ffc000">Understanding Niceness</span>

1. **What It Is:**
   - A niceness (or "nice") value is a number from -20 (highest priority) to 19 (lowest priority). A process with a high nice value is more "polite," meaning it's less demanding on CPU time.

2. **Default Niceness:**
   - By default, processes usually start with a niceness of 0.

### <span style="color:#ffc000">Using</span> `top` <span style="color:#ffc000">to View Niceness
</span>
- The `top` command shows real-time system statistics including CPU usage, memory usage, and process information.
- It also displays the nice value of each process.

### <span style="color:#ffc000">Adjusting Niceness</span>

1. **Setting Niceness with `nice`:**
   - When starting a new process, you can set its initial nice value using `nice`.
   - Example: Start a command with a niceness of 10.
     ```bash
     $ nice -n 10 <command>
     ```

2. **Modifying Niceness with `renice`:**
   - To change the nice value of an existing process, use `renice`.
   - Example: Change the niceness of a running process.
     ```bash
     $ renice 10 -p <processID>
     ```

3. **Viewing Niceness with `ps`:**
   - Use `ps -eo pid,ppid,ni,comm` to view process IDs, parent process IDs, nice values, and command names.

>[!Tip] Points to Remember
>
>- Regular users can only increase the niceness (make processes less priority).
>- Only the root user (or using `sudo`) can set a negative niceness (increase priority).
>- Adjusting niceness is a way to manage CPU time allocation among processes, especially >useful in environments with multiple important tasks running concurrently.

---
## <span style="color:#9263b6">Process states</span>
[process states]
In Linux, process states indicate the current condition of a process:

1. **R (Running or Runnable):** The process is actively running or ready to run.

2. **S (Interruptible Sleep):** The process is sleeping, waiting for some condition to be met or event to occur.

3. **D (Uninterruptible Sleep):** The process is in deep sleep, usually waiting for I/O operations. It cannot be interrupted.

4. **Z (Zombie):** A process that has completed but is still in the process table waiting for its parent to retrieve its exit status.

5. **T (Stopped):** The process is paused or suspended, typically by a user or system debugger.
### <span style="color:#ffc000">Example:</span>

1. **Listing Processes with States:**
   - The following command lists processes with their states.
     ```bash
     $ ps -aux
     ```
   - Look for the `STAT` column in the output. This column shows the state of each process (R, S, D, Z, T).

2. **Understanding the Output:**
   - For instance, the output might include lines like these:
     ```
     USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
     root         1  0.0  0.1 185044  5872 ?        Ss   Mar07   2:03 /sbin/init
     jane      1234  0.0  0.1 113660  1120 pts/0    R+   10:02   0:00 ps -aux
     ```
   - Here, the `init` process (PID 1) is in an interruptible sleep state (`S`), while the `ps -aux` command itself (PID 1234) is running (`R`).

3. **Creating a Dummy Process:**
   - To demonstrate a stopped (T) state, you can create a dummy background process and then stop it:
     ```bash
     $ sleep 300 &
     $ kill -STOP [PID of sleep]
     ```

4. **Checking Process States Again:**
   - Run `ps` again to see the state of the `sleep` process.
     ```bash
     $ ps -aux | grep sleep
     ```
   - The `sleep` process should now show a `T` state, indicating it's stopped.

---
## `/proc` <span style="color:#9263b6">for Process Information</span>

>[!info]
The `/proc` filesystem in Linux is a special pseudo-filesystem that provides a window into the kernel's view of the system. It doesn't contain real files but rather virtual files that provide an interface to internal data structures in the kernel. It's used primarily for obtaining information about the system and its processes.

Each process on a Linux system has a directory in `/proc` with its process ID (PID) as the directory name. For instance, the directory `/proc/1234` contains information about the process with PID 1234. One of the key files in this directory is `status`.

- **<span style="color:#d68a8a">Viewing Process Status</span>:**
  To view detailed status information about a specific process, you use the `cat` command followed by the path to the `status` file in the `/proc` directory. For example:
  ```bash
  $ cat /proc/<ProcessID>/status
  ```
  Replace `<ProcessID>` with the actual PID of the process you're interested in.

- **Content of `/proc/<ProcessID>/status`:**
  The `status` file contains various details about the process, including:
  - The process ID (PID)
  - Parent Process ID (PPID)
  - Process state (R, S, D, etc.)
  - Memory usage
  - UID and GID
  - Thread count
  - And more

- **Example:**
  ```bash
  $ cat /proc/1/status
  ```
  This would display the status of the process with PID 1, which is typically the init process.
---

## <span style="color:#9263b6">Jobs</span>
[jobs]

Job control in Linux shell allows you to manage multiple tasks (jobs) from a single terminal session, enabling you to run processes in the background, bring them to the foreground, and suspend or terminate them. Here's a brief explanation of the key concepts:

1. **<span style="color:#ffc000">Running a Job in the Background:</span>**
   - Use an ampersand (`&`) at the end of a command to run it in the background.
     ```bash
     $ command &
     ```
   - This allows the shell to immediately return to the prompt, ready for new commands while the job runs in the background.

2. **<span style="color:#ffc000">Listing All Jobs:</span>**
   - The `jobs` command lists all jobs in the current session.
     ```bash
     $ jobs
     ```

3. **<span style="color:#ffc000">Suspending a Job:</span>**
   - Press `Ctrl+Z` to suspend (stop) the currently running foreground job, putting it into the background in a stopped state.

4. **<span style="color:#ffc000">Running a Job in the Background:</span>**
   - To continue a stopped job in the background, use the `bg` command.
     ```bash
     $ bg %job_spec
     ```
   - `%job_spec` refers to the job specification, usually a number found next to the job in the `jobs` output.

5. **<span style="color:#ffc000">Bringing a Job to the Foreground:</span>**
   - The `fg` command brings a background job into the foreground.
     ```bash
     $ fg %1
     ```
   - `%1` would bring the first job listed in `jobs` to the foreground.

6. **<span style="color:#ffc000">Killing a Job:</span>**
   - You can kill a job using `kill` with a job specification.
     ```bash
     $ kill %1
     ```
   - `%1` refers to the job you want to terminate.

---



---
# <span style="color:#9263b6">Certification & Encryption</span>

In the context of data security, "encryption" refers to the process of converting plain text or data into a coded form (ciphertext) to prevent unauthorized access. There are various types of encryption methods used, each with its unique characteristics and use cases. Here's an overview of some common encryption types:

1. **Symmetric Encryption:**
   - **How it Works:** Uses the same key for both encryption and decryption. 
   - **Examples:** AES (Advanced Encryption Standard), DES (Data Encryption Standard), 3DES.
   - **Use Cases:** Fast and efficient for large volumes of data. Commonly used for file and disk encryption.

2. **Asymmetric Encryption:**
   - **How it Works:** Uses a pair of keys - a public key for encryption and a private key for decryption.
   - **Examples:** RSA (Rivest–Shamir–Adleman), ECC (Elliptic Curve Cryptography), ElGamal.
   - **Use Cases:** Secure key distribution, digital signatures, SSL/TLS for secure web communications.

3. **Hash Functions:**
   - **How it Works:** Converts data into a fixed-size hash value or hash code. It’s a one-way function – the hash cannot be converted back to the original data.
   - **Examples:** SHA (Secure Hash Algorithm) series like SHA-256, MD5 (Message Digest Algorithm 5).
   - **Use Cases:** Verifying data integrity, storing passwords securely.

4. **Hybrid Encryption:**
   - **How it Works:** Combines symmetric and asymmetric encryption, benefiting from the strengths of both.
   - **Examples:** Often used in secure communications protocols like TLS/SSL.
   - **Use Cases:** Securely transmitting data over networks (like the internet).

5. **Homomorphic Encryption:**
   - **How it Works:** Allows computations on ciphertexts, generating an encrypted result which, when decrypted, matches the result of operations performed on the plaintext.
   - **Use Cases:** Cloud computing, data privacy, secure voting systems.

6. **Quantum Cryptography:**
   - **How it Works:** Uses principles of quantum mechanics for secure communication, for example, quantum key distribution (QKD).
   - **Use Cases:** Extremely secure communication channels, future-proofing against quantum computer threats.

7. **Stream and Block Ciphers:**
   - **Stream Ciphers:** Encrypt data one bit (or byte) at a time (e.g., RC4).
   - **Block Ciphers:** Encrypt data in fixed-size blocks (e.g., AES uses 128-bit blocks).

---

## How Asymmetric Encryption Works:

1. **Key Pair:**
   - **Public Key:** Available to anyone and used for encrypting data.
   - **Private Key:** Kept secret by the owner and used for decrypting data.

2. **Encryption and Decryption:**
   - Data encrypted with the public key can only be decrypted by the corresponding private key, and vice versa.

### Real-Life Example: Secure Communication with a Website

Imagine you're logging into a website, like your online banking portal:

1. **Initiating a Secure Connection:**
   - Your browser requests the website’s public key, usually as part of an SSL/TLS handshake process.
   - The website sends its SSL certificate, which includes the public key.

2. **Encryption:**
   - Your browser verifies the certificate (ensuring it's from a trusted source) and then uses the public key to encrypt information, such as login credentials.

3. **Transmission:**
   - The encrypted data is sent to the website's server.

4. **Decryption and Response:**
   - The server, which possesses the private key, decrypts the information.
   - It then processes your login request and establishes a secure session, often using symmetric encryption for efficiency.

### Advantages in Communication:

- **Confidentiality:** Intercepted data is unreadable without the private key.
- **Authentication:** The website's ownership of the private key (matched to its public key in the SSL certificate) authenticates its identity.
- **Integrity:** It also ensures that the data has not been tampered with during transit.

### Common Usage:

- **Web Browsing:** HTTPS uses SSL/TLS, employing asymmetric encryption for securing initial communication.
- **Email Encryption:** Technologies like PGP and S/MIME.
- **Digital Signatures:** Verifying authorship and integrity of messages or documents.

This scenario demonstrates how asymmetric encryption enables secure, confidential, and authenticated communication over the internet, a vital element in maintaining privacy and security in our digital interactions.

---

## <span style="color:#9263b6">Detailed Process of Secure Communication</span>

1. **<span style="color:#ffc000">Initiating a Secure Connection (SSL/TLS Handshake):</span>**
   - **Client Hello:** When you navigate to a secure website (HTTPS), your browser (the client) starts by sending a "Client Hello" message to the server. This message includes the browser's SSL/TLS version, the cipher suites it supports (encryption algorithms), and a random byte string used in subsequent steps for security.
   
   - **<span style="color:#92d050">Server Response:</span>** The server responds with a "Server Hello" message. This message contains the server's SSL/TLS version, the chosen cipher suite from the ones offered by the browser, and another random byte string.

   - **<span style="color:#92d050">Certificate Presentation:</span>** The server then sends its SSL certificate. This certificate includes the server's public key and is typically issued by a <span style="color:#d68a8a">trusted Certificate Authority (CA)</span>. The certificate ensures the server’s authenticity.

2. **<span style="color:#ffc000">Encryption:</span>**
   - **<span style="color:#92d050">Certificate Verification:</span>** Your browser verifies the server's certificate. It checks if it was issued by a trusted CA, whether it's valid (not expired), and if the certificate's public key matches the server's identity (domain name).
   
   - **<span style="color:#92d050">Key Exchange:</span>** The browser uses the public key from the certificate to encrypt a "pre-master secret" and sends it to the server. Only the server's private key can decrypt this secret.
Í
3. **<span style="color:#ffc000">Transmission:</span>**
   - **<span style="color:#92d050">Encrypted Data:</span>** The browser and server use the "pre-master secret" along with the previously exchanged random byte strings to generate a common "session key." This session key is used for symmetric encryption during the session, which is more efficient than asymmetric encryption for continuous data exchange.

4. **<span style="color:#ffc000">Decryption and Response:</span>**
   - **<span style="color:#92d050">Decryption by Server:</span>** Upon receiving the encrypted "pre-master secret," the server decrypts it with its private key. Both the server and browser now have the session key for symmetric encryption.
   
   - **<span style="color:#92d050">Secure Session:</span>** The server confirms the start of the secure session. Now, any data transmitted (like your login credentials) is encrypted with the session key. The server decrypts this data using the same key.

### Why Use Both Asymmetric and Symmetric Encryption?

- **Asymmetric Encryption:** Used initially for securely exchanging the keys because it's secure against eavesdropping (even if someone intercepts the public key or the encrypted message, they can't decrypt the data).
  
- **Symmetric Encryption:** Once the secure channel is established, symmetric encryption takes over because it's faster and less resource-intensive, ideal for encrypting large amounts of data exchanged during the session.

### Ensuring Confidentiality, Integrity, and Authentication:

- **Confidentiality:** Encrypted transmission prevents unauthorized access to the data.
- **Integrity:** SSL/TLS includes mechanisms (like Message Authentication Code) to ensure the data hasn't been tampered with.
- **Authentication:** The server's use of a valid certificate ensures that you are communicating with the genuine entity (your bank), not an imposter.

This process exemplifies how asymmetric and symmetric encryption work together to secure online communications, protecting sensitive data like your banking information from potential cyber threats.

---

![[Pasted image 20240406183630.png]]
![[Pasted image 20240406185040.png]]
![[Pasted image 20240406185153.png]]


---

### Certificate:

1. **What It Is:**
   - A digital certificate is an electronic document that proves the ownership of a public key.
   - It includes information like the organization's name, the certificate's validity period, the public key, the certificate issuer, and a digital signature from the issuer.

2. **Purpose:**
   - The primary role is to ensure that a public key belongs to the entity named in the certificate. This is crucial for secure communications on the internet, like HTTPS.

3. **Components:**
   - **Public Key:** The key that can be used to encrypt messages to the owner.
   - **Identity Information:** Details about the owner of the certificate (e.g., a website's domain name).
   - **Digital Signature:** Created by the issuing CA, it verifies the authenticity of the certificate.

4. **Types:**
   - Certificates can vary in type and validation level, from Domain Validated (DV) certificates, which have a low level of validation, to Extended Validation (EV) certificates, which require thorough verification of the organization's identity.

### Certificate Authority (CA):

1. **What It Is:**
   - A Certificate Authority is a trusted entity that issues digital certificates. CAs are critical in establishing a chain of trust.

2. **Role:**
   - The CA verifies the identity and legitimacy of individuals or organizations requesting a certificate. Once verified, it issues a certificate, signing it digitally.

3. **Trust Model:**
   - Operating systems and web browsers come with a list of trusted CAs. A certificate is trusted if it's signed by one of these CAs.

4. **Process:**
   - **Issuance:** An entity requests a certificate from a CA, providing their public key and identity information. The CA verifies the information and then issues a signed certificate.
   - **Verification:** When a user visits a secure website, their browser checks the website's SSL certificate. If it's signed by a known CA, and other checks (like expiry date) pass, the browser trusts the connection.

5. **Chain of Trust:**
   - Sometimes, a certificate is issued by an Intermediate CA, which itself has a certificate issued by a Root CA. This chain leads back to a trusted Root CA.

### Why Are They Important?

- **Security and Trust:** They form the backbone of secure communication on the internet, allowing users to trust the authenticity of websites and the confidentiality of their communication.
- **Preventing Man-in-the-Middle Attacks:** Without certificates and CAs, it would be challenging to ensure that you're communicating with the legitimate entity and not an impostor.

In summary, certificates and Certificate Authorities play a pivotal role in the secure transmission of data over the internet, providing the necessary framework for encryption and trust.

---
![[Pasted image 20240406185756.png]]

---

A Root Certificate is a key element in securing web communications. It's a self-signed certificate at the top of a certificate hierarchy, issued by a trusted Certificate Authority (CA).

### Example:

1. **Trusted by Devices:** Root certificates are pre-installed and trusted by operating systems and web browsers. For example, your computer or smartphone has a built-in list of trusted root certificates from various CAs.

2. **Validating Website Certificates:**
   - When you visit a secure website (using HTTPS), your browser checks the website's SSL certificate.
   - The browser validates this certificate against its list of trusted root certificates. 
   - If there's a match (directly with the root certificate or through a chain of intermediate certificates), the browser trusts the website's certificate.

3. **Chain of Trust:**
   - **Website's SSL Certificate:** Issued by an Intermediate CA.
   - **Intermediate CA's Certificate:** Signed by the Root CA.
   - **Root CA's Certificate:** Self-signed and pre-installed in the browser.
   - This forms a chain of trust from the website to the Root CA.

Root certificates are fundamental in the digital trust system, enabling secure and encrypted internet communication, such as when you do online banking or shopping.

---

## Openssl

OpenSSL is an open-source software library that provides tools for secure communications over networks using the SSL (Secure Sockets Layer) and TLS (Transport Layer Security) protocols. It's widely used for encryption and decryption, creating certificates and keys, and other security-related tasks.

### Important OpenSSL Commands:

1. **Generating Private Keys:**
   - Generate a private RSA key:
     ```bash
     openssl genpkey -algorithm RSA -out private_key.pem
     ```

2. **Creating a Certificate Signing Request (CSR):**
   - Generate a CSR using a private key (for obtaining an SSL certificate from a CA):
     ```bash
     openssl req -new -key private_key.pem -out certificate.csr
     ```

3. **Generating Self-Signed Certificates:**
   - Create a self-signed certificate (for testing purposes):
     ```bash
     openssl req -new -x509 -days 365 -key private_key.pem -out certificate.crt
     ```

4. **Checking Certificates:**
   - View the details of a certificate:
     ```bash
     openssl x509 -in certificate.crt -text -noout
     ```

5. **Checking CSR Files:**
   - View the details of a CSR:
     ```bash
     openssl req -text -noout -verify -in CSR.csr
     ```

6. **Encrypting and Decrypting Files:**
   - Encrypt a file:
     ```bash
     openssl enc -aes-256-cbc -in file.txt -out file.enc
     ```
   - Decrypt a file:
     ```bash
     openssl enc -d -aes-256-cbc -in file.enc -out file.txt
     ```

7. **Generating Random Numbers:**
   - Generate a random number (useful for cryptographic operations):
     ```bash
     openssl rand -hex 12
     ```

8. **Generating a Public Key from a Private Key:**
   - Extract the public key from a private RSA key:
     ```bash
     openssl rsa -pubout -in private_key.pem -out public_key.pem
     ```

9. **Checking SSL Connections:**
   - Test an SSL connection to a server:
     ```bash
     openssl s_client -connect example.com:443
     ```

10. **Hashing Files:**
    - Create a hash (digest) of a file:
      ```bash
      openssl dgst -sha256 file.txt
      ```


---

This OpenSSL command generates a new self-signed SSL certificate along with a new private key. Let's break down what each part of the command does:

```bash
openssl req -x509 -newkey rsa:4096 -keyout server.key -out server.crt -days 365
```

1. **`openssl`**: The command-line tool for using the different cryptographic functions of OpenSSL's crypto library.

2. **`req`**: This subcommand specifies that you're using X.509 Certificate Signing Request (CSR) management. Here it's being used to generate a self-signed certificate.

3. **`-x509`**: This tells OpenSSL to create a self-signed certificate instead of a CSR.

4. **`-newkey rsa:4096`**: 
   - `-newkey`: This option indicates that you are generating a new private key alongside the certificate.
   - `rsa:4096`: Specifies the type of key (RSA) and the bit length (4096 bits). 4096 bits is a good choice for strong encryption as of my last update.

5. **`-keyout server.key`**: 
   - Specifies the file to which the newly created private key will be saved (`server.key` in this case).

6. **`-out server.crt`**: 
   - Directs OpenSSL to write the generated certificate to `server.crt`.

7. **`-days 365`**: 
   - Sets the validity of the certificate to 365 days. After this period, the certificate will expire and need to be renewed.

When you execute this command, OpenSSL will ask you a series of questions (like country name, organization, common name - which typically is the domain name for SSL certificates, etc.) to include this information in the certificate. Once the process is complete, you will have two files: `server.key` (the private key) and `server.crt` (the self-signed certificate). 

This command is commonly used for creating certificates for testing purposes or internal servers where the drawbacks of a self-signed certificate (mainly, lack of trust by clients outside of your control) are not an issue.


---





# Packages 
## gzip
[gzip]

`gzip` (GNU zip) is a file compression utility in Linux and other Unix-like operating systems. It's commonly used to reduce the size of files, making them easier to store or transmit. `gzip` typically operates on single files and appends a `.gz` extension to the compressed files. It uses the DEFLATE algorithm for compression.

### Basic Usage
Here's how you can use `gzip` in its most basic form:

```bash
gzip filename
```

This command will compress the file named `filename` and replace it with `filename.gz` in the same directory.

### Common Options
- `-d`: Decompress a file
- `-k`: Keep the input files (does not delete the original file after compression or decompression)
- `-l`: List information about a compressed file
- `-r`: Recursively compress files in a directory
- `-1` to `-9`: Set the compression level (`-1` for fastest compression and `-9` for best compression)

### Examples

**Compressing a file while keeping the original:**
```bash
gzip -k filename
```

**Decompressing a file:**
```bash
gzip -d filename.gz
```

**Compressing all files in a directory recursively:**
```bash
gzip -r directory_name
```

**Checking compressed file details:**
```bash
gzip -l filename.gz
```

**Return to normal file 
```bash
gunzip filename.gz
```

`gzip` is widely used in scripts and pipelines in Linux systems for handling logs, backups, and any other scenarios where saving space is beneficial. If you need to compress multiple files into a single archive, you might typically pair `gzip` with `tar`, as `gzip` itself does not handle multiple files.

![[Pasted image 20240417201937.png]]


---


## tar
[tar]

The `tar` command in Linux is a powerful tool used for collecting multiple files into a single archive file, often referred to as a tarball. The name "tar" stands for "tape archive," reflecting its original purpose for writing data to sequential I/O devices without a file system. While `tar` can handle file compression through its own options, it is commonly used with external compression tools like `gzip`, `bzip2`, or `xz` to reduce the size of the archive.

### Basic Usage
Here’s how you can use `tar` to create, extract, and manage tarball files:

**Creating an archive:**
```bash
tar -cvf archive_name.tar directory_or_files
```
- `-c`: Create a new archive.
- `-v`: Verbosely list files processed (optional).
- `-f`: Use the following argument as the filename of the archive.

**Extracting an archive:**
```bash
tar -xvf archive_name.tar
```
- `-x`: Extract files from an archive.
- `-v`: Verbosely list files processed (optional).
- `-f`: Use the following argument as the filename of the archive.

### Examples with Compression

**Creating a gzip-compressed tarball:**
```bash
tar -czvf archive_name.tar.gz directory_or_files
```
- `-z`: Filter the archive through `gzip` for compression.

**Extracting a gzip-compressed tarball:**
```bash
tar -xzvf archive_name.tar.gz
```

**Creating a bzip2-compressed tarball:**
```bash
tar -cjvf archive_name.tar.bz2 directory_or_files
```
- `-j`: Filter the archive through `bzip2` for compression.

**Extracting a bzip2-compressed tarball:**
```bash
tar -xjvf archive_name.tar.bz2
```

**Creating an xz-compressed tarball:**
```bash
tar -cJvf archive_name.tar.xz directory_or_files
```
- `-J`: Filter the archive through `xz` for compression.

**Extracting an xz-compressed tarball:**
```bash
tar -xJvf archive_name.tar.xz
```

### Advanced Options
- `--exclude`: Exclude files or directories from the archive.
- `-p`: Preserve permissions.
- `-u`: Update an existing archive by adding newer versions of files.

`tar` is extensively used in backups, data distribution, and software packaging in Unix/Linux environments. Its ability to handle large files and numerous files efficiently makes it indispensable in system administration.


![[Pasted image 20240420102202.png]]

---

## dpkg
[dpkg]

`dpkg` is a package management program used on Debian-based Linux distributions, including Ubuntu. It is used to install, remove, and manage Debian (.deb) packages, but it does not handle dependency resolution automatically. This is typically managed by higher-level tools like `apt` or `apt-get`, which call `dpkg` in the background and handle dependencies for you.

### Basic Usage of `dpkg`
Here's a rundown of the most common operations you can perform with `dpkg`:

**Installing a package:**
```bash
sudo dpkg -i package_file.deb
```
- `-i`: Install a package.

**Removing a package:**
```bash
sudo dpkg -r package_name
```
- `-r`: Remove a package but keep its configuration files.

**Purging a package (remove including config files):**
```bash
sudo dpkg -P package_name
```
- `-P`: Purge a package, including its configuration files.

**Listing all installed packages:**
```bash
dpkg -l
```
- `-l`: List packages.

**Checking if a package is installed and getting its status:**
```bash
dpkg -s package_name
```
- `-s`: Show package status along with version and other details.

**Checking the contents of a package file:**
```bash
dpkg -c package_file.deb
```
- `-c`: List contents of a .deb file.

### Handling Errors and Configuration Issues
If `dpkg` encounters problems, such as conflicts or missing dependencies, it will not proceed with the installation and will typically report an error. This can usually be fixed by manually installing the missing dependencies or by using `apt-get` which will automatically handle these dependencies for you.

For example, if you try to install a package that depends on another package that isn't installed, `dpkg` will fail with an error. You can fix this by installing the required package or by running:

```bash
sudo apt-get install -f
```
- The `-f` option means "fix broken." This command asks `apt-get` to correct any broken dependencies.

### Advanced Features
`dpkg` also supports configuration files management, where it allows administrators to handle package-specific configuration through scripts executed during the package installation or removal.

### Example Scenario: Installing a Package
Suppose you have downloaded a package called `example.deb` and want to install it. You would use the following command:

```bash
sudo dpkg -i example.deb
```

If `dpkg` reports missing dependencies, you would then run:

```bash
sudo apt-get install -f
```

This sequence ensures that all dependencies are satisfied and that your package is correctly installed using the combination of `dpkg` and `apt-get`.

`dpkg` is a crucial tool for Debian and Ubuntu users, especially when dealing with locally downloaded `.deb` files or managing packages on systems without internet access.

## rpm
[rpm]

`rpm` stands for Red Hat Package Manager and is a powerful package management system used by Red Hat-based distributions like Red Hat Enterprise Linux (RHEL), CentOS, and Fedora. It handles the installation, upgrading, and removal of software packages in these distributions.

### Basic Usage of `rpm`
Here are some fundamental `rpm` commands that are used for managing software packages:

**Installing a package:**
```bash
sudo rpm -ivh package_file.rpm
```
- `-i`: Install a package.
- `-v`: Verbose output; shows more information during the installation process.
- `-h`: Print hash marks during the installation process, which provides a visual indication of progress.

**Upgrading a package:**
```bash
sudo rpm -Uvh package_file.rpm
```
- `-U`: Upgrade a package. This option will install the package if it's not already installed, or upgrade it if it is.

**Removing a package:**
```bash
sudo rpm -e package_name
```
- `-e`: Erase (remove) an installed package.

**Querying an installed package for information:**
```bash
rpm -qi package_name
```
- `-q`: Query an installed package.
- `-i`: Display package information, including version, release, installation date, and description.

**Querying a package file for information:**
```bash
rpm -qpi package_file.rpm
```
- `-p`: Query an uninstalled package file.

**Listing all installed packages:**
```bash
rpm -qa
```
- `-a`: List all installed packages.

**Verifying a package:**
```bash
rpm -V package_name
```
- `-V`: Verify a package. This checks various aspects like permissions, MD5 sums, file sizes, and documentation against the information stored in the RPM database.

### Dependency Management
Unlike `dpkg` in Debian-based systems, `rpm` does not automatically handle dependencies. If you attempt to install a package that requires dependencies that are not present on the system, `rpm` will fail and report what is missing. Dependency management is typically handled by higher-level tools like `yum` or `dnf`, which automatically resolve and install required dependencies.

### Advanced Features
`rpm` also supports querying package databases for specific files, checking configuration files, and verifying the integrity of installed packages.

### Example Scenario: Installing a Package
Suppose you have a file called `example.rpm` and want to install it. You would use the following command:

```bash
sudo rpm -ivh example.rpm
```

If `rpm` reports missing dependencies, you would need to locate and install those dependencies manually, or you could use `yum` or `dnf` (depending on your distribution) to handle these dependencies automatically:

For RHEL/CentOS systems:
```bash
sudo yum localinstall example.rpm
```

For Fedora systems:
```bash
sudo dnf install example.rpm
```

These commands will automatically search for and install the required dependencies, making package management more straightforward.

`rpm` is a cornerstone tool for managing software in Red Hat-based environments, providing detailed control over package management tasks.

## apt
[apt]

`apt` (Advanced Package Tool) is a powerful and user-friendly command-line tool used for managing packages on Debian-based Linux distributions, such as Ubuntu. It simplifies the process of managing software by automating the retrieval, configuration, and installation of software packages from predefined repositories.

### Key Features of `apt`
- **Automatically handles package dependencies.**
- **Supports easy installation, removal, updating, and upgrading of packages.**
- **Maintains a database of available packages from configured sources.**

### Commonly Used `apt` Commands

**Updating package list:**
```bash
sudo apt update
```
- Fetches the list of available updates from configured repositories.

**Upgrading all installed packages:**
```bash
sudo apt upgrade
```
- Upgrades all upgradable packages to the latest versions.

**Installing a package:**
```bash
sudo apt install package_name
```
- Installs a new package along with its dependencies.

**Removing a package:**
```bash
sudo apt remove package_name
```
- Removes a package but keeps its configuration files.

**Purging a package (remove including config files):**
```bash
sudo apt purge package_name
```
- Removes a package and its configuration files.

**Searching for packages:**
```bash
apt search keyword
```
- Searches for packages related to the given keyword.

**Listing all installed packages:**
```bash
apt list --installed
```
- Displays a list of all installed packages.

### Example Usage
To update the package list and upgrade all your system's packages, you would use:
```bash
sudo apt update
sudo apt upgrade
```

`apt` is preferred for its simplicity and effectiveness in managing packages through high-level commands that handle complex functions with ease.

## yum
[yum]

`yum` (Yellowdog Updater, Modified) is a command-line package management utility for RPM-based Linux distributions such as Red Hat Enterprise Linux (RHEL), CentOS, and Fedora (before Fedora 22, which now uses `dnf`, a successor to `yum`). It automates the process of installing, updating, and removing packages and resolves dependencies for RPM packages.

### Key Features of `yum`
- **Dependency resolution**: Automatically finds and installs dependencies for packages.
- **Package groups**: Allows the installation of package groups, which are collections of related packages that serve a common purpose.
- **Repository management**: Manages software repositories from which packages are installed and updated.
- **Configuration**: Supports detailed configuration files for managing repositories and `yum` behavior.

### Common `yum` Commands

**Updating package repository data:**
```bash
sudo yum check-update
```
- Checks for available updates in the repositories.

**Installing a new package:**
```bash
sudo yum install package_name
```
- Installs a package and all of its dependencies.

**Updating a package:**
```bash
sudo yum update package_name
```
- Updates the specified package. If no package name is given, `yum` updates all packages.

**Removing a package:**
```bash
sudo yum remove package_name
```
- Removes a package along with any packages that depend on it.

**Listing all installed packages:**
```bash
yum list installed
```
- Shows all packages installed on the system.

**Searching for a package:**
```bash
yum search keyword
```
- Searches all available repositories for packages matching the keyword.

**Listing available package groups:**
```bash
yum group list
```
- Lists all available package groups.

**Installing a package group:**
```bash
sudo yum groupinstall "Group Name"
```
- Installs all packages in the specified group.

### Example Usage
To update all packages on your system with `yum`, you can use:
```bash
sudo yum update
```

This command refreshes repository metadata and upgrades all installed packages to their latest versions. If updates are available, `yum` handles all dependency resolutions automatically.

`yum` provides a comprehensive and reliable package management solution for administrators and users of RPM-based Linux systems, simplifying software management tasks significantly.

# Analyze text using basic regular expressions
[regex]

Analyzing text using basic regular expressions in Linux can be effectively accomplished using tools like `grep`. Regular expressions (regex) allow you to specify complex search patterns, which can be used to match various text strings within files, streams, or other data.

Here’s a basic guide on how to use regular expressions with `grep` for text analysis:

![[Pasted image 20240419160224.png]]


### Regular Expression Basics

Regular expressions consist of:
- **Literals**: Ordinary characters that match themselves.
- **Special characters**: Symbols that control how the regex matches text.

### Key Regular Expression Elements

1. **`.` (Dot)**: Matches any single character, except a newline.
2. **`^`**: Anchors the match at the beginning of the line.
3. **`$`**: Anchors the match at the end of the line.
4. **`[ ]`**: Matches any one of the characters inside the brackets.
5. **`-` (inside brackets)**: Indicates a range of characters.
6. **`*`**: Matches zero or more occurrences of the preceding element.
7. **`+`** (extended regex): Matches one or more of the preceding element.
8. **`?`** (extended regex): Makes the preceding element optional.
9. **`|`** (extended regex): Logical OR between expressions.
10. **`( )`** (extended regex): Groups expressions.

### Using `grep` with Regular Expressions

To use regular expressions with `grep`, you can use the `-E` option for extended regex support, or just use basic regex by default.

#### Examples

1. **Find lines starting with a specific word**:
   ```bash
   grep '^start' filename
   ```
   This command finds lines that start with the word "start".

2. **Find lines that end with a specific word**:
   ```bash
   grep 'end$' filename
   ```
   This command finds lines that end with the word "end".

3. **Find lines containing any digits**:
   ```bash
   grep '[0-9]' filename
   ```
   Matches lines containing any digit.

4. **Find lines that do not contain vowels**:
   ```bash
   grep -v '[aeiou]' filename
   ```
   Uses `-v` to invert the match, showing only lines without lowercase vowels.

5. **Match multiple specific characters (either A, B, or C)**:
   ```bash
   grep '[ABC]' filename
   ```
   Matches lines containing any one of 'A', 'B', or 'C'.

6. **Using wildcards to match patterns**:
   ```bash
   grep 'e*r' filename
   ```
   Matches lines containing an 'e' followed by zero or more of any characters, followed by an 'r'.



## Extended Regular Expressions

Extended Regular Expressions (EREs) expand the capabilities and flexibility of Basic Regular Expressions (BREs) used in text processing tools like `grep`, `sed`, and `awk`. EREs introduce additional meta-characters and constructs that simplify writing complex patterns.

### Key Differences and Features in ERE

Here are the primary enhancements in ERE compared to BRE:

1. **`+` Operator**: Matches one or more occurrences of the preceding element.
   ```bash
   grep -E 'o+' file.txt   # Matches 'o', 'oo', 'ooo', etc.
   ```

2. **`?` Operator**: Matches zero or one occurrence of the preceding element, making it optional.
   ```bash
   grep -E 'lo?s' file.txt  # Matches 'ls' or 'los'
   ```

3. **`|` Operator**: Acts as a logical OR between multiple expressions.
   ```bash
   grep -E 'cat|dog' file.txt  # Matches lines containing 'cat' or 'dog'
   ```

4. **Parentheses for Grouping (`( )`)**: Used to group expressions or to limit the scope of `|` operators.
   ```bash
   grep -E '(cat|dog)s' file.txt  # Matches 'cats' or 'dogs'
   ```

5. **Braces for Specified Repetitions `{m,n}`**: Matches a specified number of repetitions of the previous token, where `m` is the minimum and `n` is the maximum.
   ```bash
   grep -E 'lo{2,4}k' file.txt  # Matches 'look', 'loook', 'loook'
   ```

### Using `grep` with Extended Regular Expressions

To enable extended regular expressions in `grep`, use the `-E` flag. This allows you to use the additional ERE features without escaping the special characters, which is required in BRE.

#### Examples of ERE with `grep`

1. **Matching Optional Characters**:
   ```bash
   grep -E 'Colou?r' file.txt  # Matches both 'Color' and 'Colour'
   ```

2. **Matching Multiple Repetitions**:
   ```bash
   grep -E 'wo{2,3}d' file.txt  # Matches 'wood' or 'woood'
   ```

3. **Combining Multiple Patterns**:
   ```bash
   grep -E 'gr(ea|a)y' file.txt  # Matches 'grey' or 'gray'
   ```

4. **Grouping and Alternations**:
   ```bash
   grep -E '(file|grep) (found|missing)' log.txt  # Matches 'file found', 'file missing', 'grep found', 'grep missing'
   ```

5. **Counting Specific Repetitions**:
   ```bash
   grep -E 'ha{3}!' file.txt  # Matches 'haaa!'
   ```

### Practical Usage Tips

- **Combine with Other `grep` Options**: You can combine ERE with options like `-i` for case-insensitive matching, `-v` for inverting the match, or `-o` to only output the matching parts of a line.
- **Use in Scripts**: EREs are powerful in scripting and can be used to validate input, extract specific data, or conditionally process lines in a file based on complex patterns.

---
