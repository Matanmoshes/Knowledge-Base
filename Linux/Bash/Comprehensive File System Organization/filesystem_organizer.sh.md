
- Create a Bash script to organize a multi-purpose file system with directories for educational materials, development projects, and personal media (music, videos).
- The script need to include options for adding new item, modify item name and delete item. (Item can be everything in the example structure).
- While adding an item, it should verify its extension.
- The script should ask if your are sure you want to delete/modify an item.
- Example structure:
    
    ```
    /home
    ├── education
    │   ├── courses
    │   └── books
    ├── development
    │   ├── projects
    │   ├── archives
    │   └── tools
    └── personal
        ├── music
        ├── videos
        └── pictures
    ```

---

###  Code:

```bash
#!/bin/bash

set -o errexit
set -o nounset
set -o pipefail

# Paths for the directory structure
EDUCATION_DIR="/home/education"
DEVELOPMENT_DIR="/home/development"
PERSONAL_DIR="/home/personal"

# Setup initial directories
setup_directories() {
    mkdir -p "${EDUCATION_DIR}"/{courses,books}
    mkdir -p "${DEVELOPMENT_DIR}"/{projects,archives,tools}
    mkdir -p "${PERSONAL_DIR}"/{music,videos,pictures}
}

# Add a new item
add_item() {
    local dir="$1"
    local item="$2"
    local extension="${item##*.}"
    local valid_extensions="pdf docx mp3 mp4 zip tar.gz"

    if [[ " $valid_extensions " =~ " $extension " ]]; then
        touch "${dir}/${item}"
        printf "Added item: %s\n" "${item}"
    else
        printf "Invalid file extension: %s\n" "${extension}" >&2
        return 1
    fi
}

# Modify an item name
modify_item() {
    local dir="$1"
    local old_name="$2"
    local new_name="$3"

    if [[ -e "${dir}/${old_name}" ]]; then
        printf "Are you sure you want to rename %s to %s? [y/N]: " "${old_name}" "${new_name}"
        read -r confirm
        if [[ "${confirm,,}" == "y" ]]; then
            mv "${dir}/${old_name}" "${dir}/${new_name}"
            printf "Renamed %s to %s\n" "${old_name}" "${new_name}"
        else
            printf "Rename cancelled\n"
        fi
    else
        printf "Item %s does not exist\n" "${old_name}" >&2
        return 1
    fi
}

# Delete an item
delete_item() {
    local dir="$1"
    local item="$2"

    if [[ -e "${dir}/${item}" ]]; then
        printf "Are you sure you want to delete %s? [y/N]: " "${item}"
        read -r confirm
        if [[ "${confirm,,}" == "y" ]]; then
            rm -r "${dir}/${item}"
            printf "Deleted %s\n" "${item}"
        else
            printf "Deletion cancelled\n"
        fi
    else
        printf "Item %s does not exist\n" "${item}" >&2
        return 1
    fi
}

# Display usage if no arguments are provided
usage() {
    printf "Usage: %s <action> <directory> [item] [new_name]\n" "$0"
    printf "Actions:\n"
    printf "  setup                  - Set up directory structure\n"
    printf "  add <dir> <item>       - Add a new item with validation\n"
    printf "  modify <dir> <old> <new> - Modify an item name\n"
    printf "  delete <dir> <item>    - Delete an item\n"
    exit 1
}

# Main function
main() {
    if [[ $# -eq 0 ]]; then
        usage
    fi

    local action="$1"
    local directory="$2"
    local name="$3"
    local new_name="$4"

    case "${action}" in
        setup)
            setup_directories
            ;;
        add)
            add_item "${directory}" "${name}"
            ;;
        modify)
            modify_item "${directory}" "${name}" "${new_name}"
            ;;
        delete)
            delete_item "${directory}" "${name}"
            ;;
        *)
            printf "Invalid action: %s\n" "${action}" >&2
            usage
            ;;
    esac
}

# Call main with all arguments
main "$@"

```

---

### Code Breakdown


### Script Setup and Variable Declarations
```bash
#!/bin/bash
set -o errexit
set -o nounset
set -o pipefail

# Paths for the directory structure
EDUCATION_DIR="/home/education"
DEVELOPMENT_DIR="/home/development"
PERSONAL_DIR="/home/personal"
```
**Explanation**:
- `#!/bin/bash`: This is the shebang line, which tells the system that this script should be run with Bash.
- `set -o errexit`: Causes the script to exit immediately if any command exits with a non-zero status.
- `set -o nounset`: Prevents the script from running if it tries to use undeclared variables.
- `set -o pipefail`: Ensures that the script captures errors in pipelines.
- Directory variables (`EDUCATION_DIR`, `DEVELOPMENT_DIR`, `PERSONAL_DIR`) store the paths where the directory structures will be set up.

### Function: setup_directories
```bash
setup_directories() {
    mkdir -p "${EDUCATION_DIR}"/{courses,books}
    mkdir -p "${DEVELOPMENT_DIR}"/{projects,archives,tools}
    mkdir -p "${PERSONAL_DIR}"/{music,videos,pictures}
}
```
**Explanation**:
- **Purpose**: Initializes the directory structure for education, development, and personal use.
- `mkdir -p`: Creates multiple directories; the `-p` flag makes intermediate directories as required and prevents errors if the directories already exist.
- The curly braces `{}` with directory names allow multiple directories to be created under each specified path in one command.

### Function: add_item
```bash
add_item() {
    local dir="$1"
    local item="$2"
    local extension="${item##*.}"
    local valid_extensions="pdf docx mp3 mp4 zip tar.gz"

    if [[ " $valid_extensions " =~ " $extension " ]]; then
        touch "${dir}/${item}"
        printf "Added item: %s\n" "${item}"
    else
        printf "Invalid file extension: %s\n" "${extension}" >&2
        return 1
    fi
}
```
**Explanation**:
- **Purpose**: Adds a new item to the specified directory after validating its file extension.
- `local`: Declares variables that are local to the function, preventing them from affecting the global script environment.
- `extension="${item##*.}"`: Extracts the extension from the item name.
- `[[ " $valid_extensions " =~ " $extension " ]]`: Regex match to check if the extension is within the allowed list.
- `touch "${dir}/${item}"`: Creates the new file.
- Conditional statements manage the creation based on the validity of the file extension, with appropriate messaging.

### Function: modify_item
```bash
modify_item() {
    local dir="$1"
    local old_name="$2"
    local new_name="$3"

    if [[ -e "${dir}/${old_name}" ]]; then
        printf "Are you sure you want to rename %s to %s? [y/N]: " "${old_name}" "${new_name}"
        read -r confirm
        if [[ "${confirm,,}" == "y" ]]; then
            mv "${dir}/${old_name}" "${dir}/${new_name}"
            printf "Renamed %s to %s\n" "${old_name}" "${new_name}"
        else
            printf "Rename cancelled\n"
        fi
    else
        printf "Item %s does not exist\n" "${old_name}" >&2
        return 1
    fi
}
```
**Explanation**:
- **Purpose**: Renames an item within the specified directory after confirmation from the user.
- `[[ -e "${dir}/${old_name}" ]]`: Checks if the old item exists.
- `read -r confirm`: Prompts the user for confirmation to proceed with renaming.
- `mv "${dir}/${old_name}" "${dir}/${new_name}"`: Performs the actual renaming if confirmed.
- Conditional checks and prompts ensure that the user has control over the rename operation.

### Function: delete_item
```bash
delete_item() {
    local dir="$1"
    local item="$2"

    if [[ -e "${dir}/${item}" ]]; then
        printf "Are you sure you want to delete %s? [y/N]: " "${item}"
        read -r confirm
        if [[ "${confirm,,}" == "y" ]]; then
            rm -r "${dir}/${item}"
            printf "Deleted %s\n" "${item}"
        else
            printf "Deletion cancelled\n"
        fi
    else
        printf "Item %s does not exist\n" "${item}" >&2
        return 1
    fi


}
```
**Explanation**:
- **Purpose**: Deletes an item from the specified directory after user confirmation.
- `[[ -e "${dir}/${item}" ]]`: Checks if the item exists before attempting to delete.
- `read -r confirm`: Gets confirmation from the user to proceed with the deletion.
- `rm -r "${dir}/${item}"`: Removes the item; `-r` allows recursive deletion if it's a directory.
- Confirmation and checks ensure the user is making an informed decision, preventing accidental deletions.

### Function: usage and main
```bash
usage() {
    printf "Usage: %s <action> [directory] [item] [new_name]\n" "$0"
    printf "Actions:\n"
    printf "  setup                  - Set up directory structure\n"
    printf "  add <dir> <item>       - Add a new item with validation\n"
    printf "  modify <dir> <old> <new> - Modify an item name\n"
    printf "  delete <dir> <item>    - Delete an item\n"
    exit 1
}

main() {
    if [[ $# -eq 0 ]]; then
        usage
    fi

    local action="$1"

    case "${action}" in
        setup)
            setup_directories
            ;;
        add)
            if [[ $# -ne 3 ]]; then
                printf "Incorrect number of arguments for add\n" >&2
                usage
            fi
            add_item "$2" "$3"
            ;;
        modify)
            if [[ $# -ne 4 ]]; then
                printf "Incorrect number of arguments for modify\n" >&2
                usage
            fi
            modify_item "$2" "$3" "$4"
            ;;
        delete)
            if [[ $# -ne 3 ]]; then
                printf "Incorrect number of arguments for delete\n" >&2
                usage
            fi
            delete_item "$2" "$3"
            ;;
        *)
            printf "Invalid action: %s\n" "${action}" >&2
            usage
            ;;
    esac
}

main "$@"
```
**Explanation**:
- `usage()`: Provides help information if the script is run incorrectly or without parameters.
- `main()`: The script's entry point, directing to the appropriate function based on the command-line argument. It includes argument count checks to ensure proper usage.


---


## Requirements:
- **Linux or Unix-like OS** with Bash.
- **Write permissions** for the directory where you're creating files and directories, or **sudo privileges** if you need to modify protected areas.

### Preparation:
1. **Download/Save the script** to a directory on your system.
2. **Make the script executable**:
   ```bash
   chmod +x filesystem_organizer.sh
   ```

### Running the Script:
- Open a terminal and navigate to the script's location.
- Execute the script with the required action and parameters:
  - **Setup directories**:
    ```bash
    sudo ./filesystem_organizer.sh setup
    ```
  - **Add an item**:
    ```bash
    ./filesystem_organizer.sh add /path/to/directory "filename.ext"
    ```
  - **Modify an item**:
    ```bash
    ./filesystem_organizer.sh modify /path/to/directory "oldname.ext" "newname.ext"
    ```
  - **Delete an item**:
    ```bash
    ./filesystem_organizer.sh delete /path/to/directory "filename.ext"
    ```

Follow these steps to manage files and directories using your script. Adjust the paths and command parameters based on your specific needs and directory structure.
