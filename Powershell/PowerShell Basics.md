# PowerShell Basics

1. **Listing Files and Folders**: To get a list of all files and folders in the current directory, you can use:

   ```powershell
   Get-ChildItem
   ```

2. **Creating a New Folder**: To create a new folder named "TestFolder" in the current directory:

   ```powershell
   New-Item -Path . -Name "TestFolder" -ItemType "directory"
   ```

3. **Finding All Files with a Specific Extension**: To find all `.txt` files in the current directory:

   ```powershell
   Get-ChildItem -Filter *.txt
   ```

4. **Reading a Text File**: To display the content of a text file named "example.txt":

   ```powershell
   Get-Content example.txt
   ```

5. **Copying Files**: To copy a file named "example.txt" to a folder named "Backup":

   ```powershell
   Copy-Item -Path example.txt -Destination .\Backup\
   ```

6. **Moving Files**: To move a file named "example.txt" to a folder named "Archive":

   ```powershell
   Move-Item -Path example.txt -Destination .\Archive\
   ```

7. **Deleting a File**: To delete a file named "oldfile.txt":

   ```powershell
   Remove-Item oldfile.txt
   ```

8. **Writing to a File**: To add text to a file, creating the file if it doesn't exist:

   ```powershell
   Add-Content -Path log.txt -Value "New log entry"
   ```

9. **Getting System Information**: To display basic system information:

   ```powershell
   Get-ComputerInfo
   ```

10. **Searching for a Specific Process**: To find a process with the name "notepad":

    ```powershell
    Get-Process | Where-Object { $_.ProcessName -eq "notepad" }
    ```
