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