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

# Users & Groups in Linux
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

# Linux Process
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
