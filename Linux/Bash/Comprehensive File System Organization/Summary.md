
# Linux System Setup and Management Scripts

This document provides a summary of the various scripts used for system setup and management, including their purpose, usage, and important logs.

## Preparation:
1. **Download/Save the script** to a directory on your system.
```bash
git clone https://github.com/nirsht/DevOpsCourseINT2024.git
```

2. **Navigate to `linux_final_test` directory
```bash
ls /home/your_user_name/Documents/DevOpsCourseINT2024/Submission/MatanMoshe/linux_final_test
```
3. **Make the script executable**:
   ```bash
   chmod +x example_script.sh
   ```


## **Scripts and Their Purpose**

1. **`backup_Script.sh`**:
    - **Purpose**: To back up specific directories by compressing them into a tar archive.
    - **Usage**:
      - Set source directories to be backed up (e.g., `/home/education`, `/home/development`).
      - Set the backup destination directory.
      - Run the script to create compressed archives of the specified directories.
    - **Logs/Output**:
      - Prints a success message if the backup is created successfully.
      - Prints an error message if the backup fails.

2. **`create_users.sh`**:
    - **Purpose**: To create specific user accounts with appropriate descriptions.
    - **Usage**:
      - Predefine user accounts and their descriptions in the script.
      - Run the script to create these user accounts if they don't already exist.
    - **Logs/Output**:
      - Logs a success message for each user created.
      - Logs an error message if user creation fails or the user already exists.

3. **`filesystem_organizer.sh`**:
    - **Purpose**: To create an organized directory structure and manage files within.
    - **Usage**:
      - Use the `setup` action to set up the directory structure.
      - Use the `add`, `modify`, and `delete` actions to manage files within the directories.
    - **Logs/Output**:
      - Logs successful actions like adding, modifying, or deleting files.
      - Logs errors when file actions fail.
        
    **Running the Script:
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

4. **`monitor_processes.sh`**:
    - **Purpose**: To monitor processes for high CPU or memory usage and send email alerts.
    - **Usage**:
      - Set the thresholds for CPU and memory usage and specify the alert email address.
      - Run the script to monitor processes continuously, checking every 60 seconds.
    - **Logs/Output**:
      - Sends email alerts when processes exceed the specified usage thresholds.

5. **`exam_script.sh`**:
    - **Purpose**: To conduct a quiz by reading questions from a file, accepting user answers, and comparing them against the correct answers.
    - **Usage**:
      - Prepare `questions.txt` and `correct_answer.txt` files with properly formatted questions and answers.
      - Run the script to shuffle the questions, ask the user for their answers, and compare the answers to the correct ones.
    - **Logs/Output**:
      - Logs scores and error messages for invalid inputs.

6. **`maintenance_script.sh`**:
    - **Purpose**: To clean up temporary and log files older than a specified number of days.
    - **Usage**:
      - Specify the paths to directories containing temporary and log files.
      - Run the script to delete files older than the specified retention days.
    - **Logs/Output**:
      - Logs successful cleanups of old files.
      - Logs errors encountered during cleanup.

## **Grading Parameters and Coverage**

1. **Coverage and Integration**:
   - **Skillset**: The scripts demonstrate skills in file management, process monitoring, user management, and system maintenance.
   - **Integration**: They integrate well together for system setup and management purposes.

2. **Functionality and Robustness**:
   - **Functionality**: All scripts provide error handling and informative logging for their intended actions.
   - **Robustness**: Scripts use proper input validation, file verification, and logical flow.

3. **Documentation and Understanding**:
   - **Clarity**: Documentation for each script is detailed in this summary, providing a clear understanding of usage and purpose.
   - **Commands**:
     - **`mail`**: Install with `sudo apt-get install mailutils`.
     - **`awk`**: Usually pre-installed on most Linux systems.
     - **`tar`**: Typically pre-installed on most Linux systems.

