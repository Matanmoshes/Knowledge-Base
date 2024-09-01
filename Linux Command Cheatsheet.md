# **Linux Commands Guide**

## **1. Navigating the File System**

Understanding how to navigate and manipulate the file system is foundational in Linux. Here are some essential commands:

- **`cd`: Change Directory**
  - **Usage:** `cd <directory_path>`
  - Moves you to the specified directory.
  - Example: `cd /home/username/projects/`

- **`mkdir`: Make Directory**
  - **Usage:** `mkdir <directory_name>`
  - Creates a new directory within the current directory.
  - Example: `mkdir linux-devops`

- **`pwd`: Print Working Directory**
  - **Usage:** `pwd`
  - Displays the current directory you are working in.
  - Example: `/home/username/linux-devops/`

- **`touch`: Create an Empty File**
  - **Usage:** `touch <filename>`
  - Creates a new, empty file.
  - Example: `touch file1 file2`

- **`ls`: List Directory Contents**
  - **Usage:** `ls <options>`
  - Lists files and directories within the current directory.
  - Example: `ls -l` (displays detailed information)

- **`cp`: Copy Files or Directories**
  - **Usage:** `cp <source> <destination>`
  - Copies files or directories from one location to another.
  - Example: `cp file1 /home/username/`

- **`mv`: Move or Rename Files or Directories**
  - **Usage:** `mv <source> <destination>`
  - Moves or renames files or directories.
  - Example: `mv file2 file3` (renames `file2` to `file3`)

- **`rm`: Remove Files or Directories**
  - **Usage:** `rm <file_or_directory>`
  - Deletes files or directories.
  - Example: `rm file1`

### Example Workflow:
```bash
mkdir linux-devops
cd /home/username/linux-devops/
touch file1 file2
pwd
ls
cp file1 /home/username/
mv file2 file3
rm file1
```


---
## **2. Managing Processes**

Process management is critical for ensuring your applications and services run smoothly:

- **`ps`: Process Status**
  - **Usage:** `ps aux`
  - Displays information about all running processes.

- **`top`: Real-Time Process Monitoring**
  - **Usage:** `top`
  - Provides a dynamic, real-time view of the system’s processes.

- **`kill`: Terminate a Process**
  - **Usage:** `kill -9 <process_id>`
  - Sends a signal to terminate a process, where `-9` forcefully kills the process.
  - Example: `kill -9 1234`

- **`nice`: Adjust Process Priority**
  - **Usage:** `nice -n <priority> <command>`
  - Adjusts the priority of a process, where lower values mean higher priority.
  - Example: `nice -n 10 ./script.sh`

### Example Commands:
```bash
ps aux
top
kill -9 <process_id>
nice -n 10 <process_name>
```


---

## **3. User and Permissions Management**

Managing users and file permissions is essential for maintaining a secure system:

- **`useradd`: Create a New User**
  - **Usage:** `useradd <username>`
  - Adds a new user to the system.
  - Example: `useradd newuser`

- **`passwd`: Set or Change User Password**
  - **Usage:** `passwd <username>`
  - Sets or changes the password for a user.
  - Example: `passwd newuser`

- **`chmod`: Change File Permissions**
  - **Usage:** `chmod <permissions> <file>`
  - Modifies file or directory permissions. The permission codes are:
    - **`r` (read) = 4**
    - **`w` (write) = 2**
    - **`x` (execute) = 1**
  - Example: `chmod 755 file1.sh` (`7`=rwx, `5`=rx)

- **`chown`: Change File Owner**
  - **Usage:** `chown <user>:<group> <file>`
  - Changes the ownership of a file or directory.
  - Example: `chown username:groupname file1.sh`

### Example Commands:
```bash
useradd newuser
passwd newuser
chmod 755 file1.sh
chown username:groupname file1.sh
```


---

## **4. Text Processing**

Text processing commands are invaluable for filtering, searching, and editing text files:

- **`cat`: Concatenate and Display Files**
  - **Usage:** `cat <file>`
  - Displays the content of a file.
  - Example: `cat file1.txt`

- **`grep`: Search Text Using Patterns**
  - **Usage:** `grep <pattern> <file>`
  - Searches for a pattern within a file or files.
  - Example: `grep error log.txt`

- **`sort`: Sort Lines in Text Files**
  - **Usage:** `sort <file>`
  - Sorts the lines in a file alphabetically.
  - Example: `sort file.txt`

- **`uniq`: Remove Duplicate Lines**
  - **Usage:** `sort file.txt | uniq`
  - Removes duplicate lines from a sorted file.

- **`sed`: Stream Editor**
  - **Usage:** `sed 's/old/new/' <file>`
  - Performs text manipulation, like search and replace.
  - Example: `sed 's/error/success/' file.txt`

- **`head` & `tail`: View File Start/End**
  - **Usage:** `head <file>` and `tail <file>`
  - Displays the first or last few lines of a file.
  - Example: `head file.txt; tail file.txt`

### Example Commands:
```bash
cat file1.txt
grep error log.txt
sort file.txt | uniq
sed 's/old/new/' file.txt
head file.txt
tail file.txt
```


---

## **5. Network Commands**

These commands help you manage and troubleshoot network-related issues:

- **`ping`: Check Network Connectivity**
  - **Usage:** `ping <host>`
  - Checks if a host is reachable.
  - Example: `ping google.com`

- **`ifconfig` or `ip`: Network Configuration**
  - **Usage:** `ifconfig` or `ip a`
  - Displays network interface configuration.

- **`netstat`: Network Status**
  - **Usage:** `netstat -an`
  - Displays network connections, routing tables, and interface statistics.

- **`dig`: DNS Lookup**
  - **Usage:** `dig <domain>`
  - Queries DNS for information about a domain.
  - Example: `dig example.com`

- **`hostname`: View/Set System Hostname**
  - **Usage:** `hostname`
  - Displays or sets the system’s hostname.

### Example Commands:
```bash
ping google.com
ifconfig
netstat -an
dig example.com
hostname
```

---

## **6. Package Management**

Managing packages is essential for installing and updating software:

- **`apt-get` (Debian/Ubuntu):**
  - **Usage:** `sudo apt-get install <package_name>`
  - Installs a package on Debian-based systems.
  - Example: `sudo apt-get install nginx`

- **`yum` (RHEL/CentOS):**
  - **Usage:** `sudo yum install <package_name>`
  - Installs a package on RHEL-based systems.
  - Example: `sudo yum install httpd`

### Example Commands:
```bash
sudo apt-get update
sudo apt-get install nginx
sudo yum update
sudo yum install httpd
```

---

## **7. File Compression and Archiving**

Compression and archiving help in managing backups and reducing file sizes:

- **`tar`: Archive Files**
  - **Usage:** `tar -cvf <archive_name>.tar <files>`
  - Archives files into a tarball.
  - Example: `tar -cvf backup.tar /home/user/`

- **`gzip`/`gunzip`: Compress/Decompress Files**
  - **Usage:** `gzip <file>` and `gunzip <file>.gz`
  - Compresses or decompresses files.
  - Example: `gzip file.txt`

- **`zip`: Create Zip Archives**
  - **Usage:** `zip <archive_name>.zip <files>`
  - Compresses files into a zip archive.
  - Example: `zip backup.zip file1 file2`

### Example Commands:
```bash
tar -cvf backup.tar /home/user/
gzip file.txt
gunzip file.txt.gz
zip backup.zip file1 file2
```

---
## **8. SSH and Remote Access**

These commands are essential for managing remote servers:

- **`ssh`: Secure Shell**
  - **Usage:** `ssh <user>@<remote_host>`
  - Connects to a remote server securely.
  - Example: `ssh ec2-user@54.123.45.67`

- **`scp`: Secure Copy**
  - **Usage:**

 `scp <local_file> <user>@<remote_host>:<destination_path>`
  - Copies files between local and remote systems.
  - Example: `scp file.txt ec2-user@54.123.45.67:/home/ec2-user/`

### Example Commands:
```bash
ssh ec2-user@54.123.45.67
scp file.txt ec2-user@54.123.45.67:/home/ec2-user/
```

---

## **9. System Information**

These commands provide details about your system’s hardware and resource usage:

- **`uname`: System Information**
  - **Usage:** `uname -a`
  - Displays system information.
  - Example: `uname -a`

- **`df`: Disk Space Usage**
  - **Usage:** `df -h`
  - Shows disk space usage for file systems.
  - Example: `df -h`

- **`du`: Directory Disk Usage**
  - **Usage:** `du -sh <directory>`
  - Displays the disk usage of a directory.
  - Example: `du -sh /var/log`

- **`uptime`: System Uptime**
  - **Usage:** `uptime`
  - Shows how long the system has been running.
  - Example: `uptime`

- **`free -m`: Memory Usage**
  - **Usage:** `free -m`
  - Displays the system’s memory usage.
  - Example: `free -m`

### Example Commands:
```bash
uname -a
df -h
du -sh /var/log
uptime
free -m
```

---

## **10. Shell Scripting Basics**

Shell scripting is key for automating repetitive tasks:

- **`echo`: Display a Message**
  - **Usage:** `echo <message>`
  - Prints a message to the terminal.
  - Example: `echo "Hello, DevOps"`

- **Make a Script Executable:**
  - **Usage:** `chmod +x <script.sh>`
  - Changes the script’s permissions to make it executable.
  - Example: `chmod +x deploy.sh`

- **Run a Script:**
  - **Usage:** `./<script.sh>`
  - Executes the script.
  - Example: `./deploy.sh`

### Example Commands:
```bash
echo "Hello, DevOps"
chmod +x deploy.sh
./deploy.sh
```

---


