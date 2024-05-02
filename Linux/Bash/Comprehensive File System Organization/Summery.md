
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
