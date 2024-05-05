Here's a straightforward Bash script to create user accounts specifically tailored for education, development, and personal use. The script will set up user accounts, create their home directories, and output basic information about each user's purpose.

```bash
#!/bin/bash

# Function to create a user with specified home directory and description
create_user() {
    local username="$1"
    local purpose="$2"

    # Check if the user already exists
    if id "$username" &>/dev/null; then
        echo "User $username already exists."
    else
        # Create a new user with a home directory and set the default shell to bash
        sudo useradd -m -d "/home/$username" -s /bin/bash "$username"
        # Set a description for the user
        sudo usermod -c "$purpose" "$username"
        if [[ $? -eq 0 ]]; then
            echo "User $username created successfully for $purpose."
        else
            echo "Failed to create user $username." >&2
            return 1
        fi
    fi
}

# Main function to create specific users
main() {
    echo "Creating user accounts..."
    create_user "edu_user" "User account for educational purposes"
    create_user "dev_user" "User account for development activities"
    create_user "personal_user" "User account for personal use"
    echo "User account creation process completed."
}

# Execute the main function
main
```


---

### Breakdown of the Script

### Script Overview

```bash
#!/bin/bash
```
- **Shebang Line**: This line tells the system that this script should be executed with Bash, which is located at `/bin/bash`.

### Function: `create_user`
```bash
create_user() {
    local username="$1"
    local purpose="$2"
```
- **Function Definition**: `create_user` is a function that takes two arguments. `username` is the name of the user to be created, and `purpose` is a description of the user's intended use.
- **Local Variables**: The `local` keyword restricts the scope of the variables `username` and `purpose` to the function, preventing them from affecting the global script environment.

```bash
    if id "$username" &>/dev/null; then
        echo "User $username already exists."
    else
```
- **Check User Existence**: The `id` command is used to check if the user already exists in the system. Output and errors are redirected to `/dev/null` to suppress them. If the user exists, a message is printed; otherwise, the script proceeds to create the user.

```bash
        sudo useradd -m -d "/home/$username" -s /bin/bash "$username"
```
- **Create User**: The `useradd` command is used with several options:
  - `-m`: Create the user's home directory.
  - `-d "/home/$username"`: Specify the path of the home directory.
  - `-s /bin/bash`: Set `/bin/bash` as the default shell for the user.

```bash
        sudo usermod -c "$purpose" "$username"
```
- **Set Description**: The `usermod -c` command modifies the user account's comment field (often used for the user description), setting it to the value of `$purpose`.

```bash
        if [[ $? -eq 0 ]]; then
            echo "User $username created successfully for $purpose."
        else
            echo "Failed to create user $username." >&2
            return 1
        fi
    fi
}
```
- **Error Checking**: The special variable `$?` captures the exit status of the last command executed (`usermod` in this case). If the exit status is `0` (success), a success message is printed. If not, an error message is sent to standard error (`>&2`) and `return 1` exits the function with a status of 1, indicating failure.

### Main Function: `main`
```bash
main() {
    echo "Creating user accounts..."
    create_user "edu_user" "User account for educational purposes"
    create_user "dev_user" "User account for development activities"
    create_user "personal_user" "User account for personal use"
    echo "User account creation process completed."
}
```
- **Executing User Creation**: This block calls the `create_user` function for three different user types: educational, development, and personal. It starts by announcing the start of the process and ends with a completion message.

### Script Execution Entry Point
```bash
main
```
- **Invoke Main Function**: The `main` function is called to execute the script, initiating the user creation tasks described.

### Final Notes
- **Execution Requirements**: This script should be run with superuser privileges (`sudo`) because creating users is an administrative task.
- **Script Permissions**: Before running, ensure the script is executable with `chmod +x create_users.sh`.

---


### Instructions to run `create_users.sh` script:

### 1. **Make the Script Executable**
Set the executable permission for the script:
```bash
chmod +x create_users.sh
```

### 2. **Run the Script with Superuser Privileges**
Execute the script using `sudo` to ensure it has the necessary permissions to create user accounts:
```bash
sudo ./create_users.sh
```

### Notes
- **No Arguments Required**: The script runs without any external arguments as it uses predefined user details.
- Ensure you review the script contents before execution to verify it meets your needs.

This will run your script, creating the specified user accounts if they do not already exist.

---

### Check if the users were created
### 1. **List all Users from the `/etc/passwd` File**
This file contains a list of all users. You can view it using `grep` to filter for specific usernames or `cat` to view all entries:

```bash
cat /etc/passwd
```

To filter and check for the specific users created by your script, you might use:

```bash
grep 'edu_user\|dev_user\|personal_user' /etc/passwd
```

This command uses `grep` to search for the usernames in the `/etc/passwd` file, which lists all accounts.

### 2. **Check Individual User with the `id` Command**
The `id` command provides user and group information for any username:

```bash
id edu_user
id dev_user
id personal_user
```

These commands will show the user IDs (UID), group IDs (GID), and the groups the users belong to.

### 3. **Using the `getent` Command**
`getent` fetches entries from databases configured in `/etc/nsswitch.conf`, including user account information:

```bash
getent passwd edu_user
getent passwd dev_user
getent passwd personal_user
```

Each `getent` command will display the entry from the `passwd` database for the specified user, showing details like UID, GID, home directory, and default shell.

### 4. **Check User Home Directories**
Verify that the home directories for the users were created:

```bash
ls -ld /home/edu_user
ls -ld /home/dev_user
ls -ld /home/personal_user
```

These commands list the directory details to ensure that the home directories exist and are correctly assigned to each user.
