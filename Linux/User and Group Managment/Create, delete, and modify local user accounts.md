Managing local user accounts on a Linux system involves creating, deleting, and modifying users according to your administrative needs. Here's a detailed guide on how to perform these operations using command-line tools typically available on most Linux distributions.

### Command to View Usernames

```bash
cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin

# Each line in the `/etc/passwd` file represents a user and contains seven fields separated by colons (`:`):
username:password:UID:GID:comment:home_directory:shell

```


### Creating a Local User Account

**1. Add a new user:**
The `useradd` command is used to create a new user. It allows several options to specify the user's environment.

```bash
sudo useradd [options] username
```

- **Example**: Create a new user named `john`:
  ```bash
  sudo useradd john
  ```

**2. Set a password for the user:**
After creating a user, you need to set a password with the `passwd` command.

```bash
sudo passwd username
```

- **Example**: Set a password for `john`:
  ```bash
  sudo passwd john
  ```

### Deleting a Local User Account

**1. Remove a user:**
The `userdel` command is used to delete a user account from the system. You can also choose to remove the home directory.

```bash
sudo userdel [options] username
```

- **Example**: Delete the user `john` and remove their home directory:
  ```bash
  sudo userdel -r john
  ```

### Modifying a Local User Account

**1. Change username:**
To change a user's username, use the `usermod` command.

```bash
sudo usermod -l new_username old_username
```

- **Example**: Change `john`'s username to `john_doe`:
  ```bash
  sudo usermod -l john_doe john
  ```

**2. Change a user’s home directory:**
If you need to change a user’s home directory, use the `-d` option with `usermod`, and use `-m` to move the contents of the user’s current directory to the new directory.

```bash
sudo usermod -d new_home_directory -m username
```

- **Example**: Change `john_doe`'s home directory to `/home/new_john`:
  ```bash
  sudo usermod -d /home/new_john -m john_doe
  ```

**3. Add or remove the user from groups:**
Use the `-G` option to set the groups which the user will be a member of, or use `-aG` to append the user to the additional groups.

- **To add to existing groups without removing from current ones**:
  ```bash
  sudo usermod -aG groupname username
  ```
- **To set the user's groups (removing from others not listed)**:
  ```bash
  sudo usermod -G groupname1,groupname2 username
  ```

- **Example**: Add `john_doe` to the `admin` group:
  ```bash
  sudo usermod -aG admin john_doe
  ```

### Additional Useful Commands

- **List users**: Use `cut` and `sort` with `/etc/passwd` to list all users:
  ```bash
  cut -d: -f1 /etc/passwd | sort
  ```
- **Get user details**: The `id` command provides the user ID, group IDs, and the groups the user belongs to.
  ```bash
  id username
  ```

- If we want to see what username and group owns files or directories.
```bash
ls -l /home/
ls -ln /home/ # numeric
```


This guide covers basic tasks related to managing user accounts in Linux. Always ensure you have backups of important data, especially when deleting users or making significant changes to user accounts.

