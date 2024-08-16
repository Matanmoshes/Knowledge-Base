

# Code:

```python
import os

import shutil

# Define the path to the Documents directory using the user's home directory

documents_dir = os.path.expanduser('~/Documents')

  

# Define the path to the centralized GitHub directories folder inside Documents

github_directories = os.path.join(documents_dir, 'github_directories')

  

# Ensure the centralized GitHub directories folder exists; create it if it doesn't

if not os.path.exists(github_directories):

os.makedirs(github_directories) # Create the directory if it does not exist

  

# Walk through all directories and subdirectories in the Documents directory
for root, dirs, files in os.walk(documents_dir):

for dir_name in dirs:

# Construct the full path to the current directory being examined
dir_path = os.path.join(root, dir_name)

# Construct the full path to the .git directory within the current directory
git_dir = os.path.join(dir_path, '.git')

  

# Check if the .git directory exists, indicating this is a Git repository

if os.path.exists(git_dir):

try:

# Move the entire directory containing the .git folder to github_directories

shutil.move(dir_path, os.path.join(github_directories, dir_name))

print(f"Moved: {dir_path}") # Print the path of the directory that was moved

except Exception as e:

# Print an error message if the move operation fails

print(f"Failed to move {dir_path}: {e}")

  

print("ALL git directories have been moved.")
```





