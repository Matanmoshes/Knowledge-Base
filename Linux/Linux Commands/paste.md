# `paste` Command
[paste]
The `paste` command in Bash is used to merge lines of files horizontally, effectively combining multiple files or streams side-by-side. This command is particularly useful for collating data from separate sources into a single file.

### Key Features of `paste`:
- **Serial merging**: By default, `paste` merges the corresponding lines of the input files. If the files have different numbers of lines, the output will extend as far as the longest file, with missing fields filled with blanks for shorter files.
- **Delimiters**: The `-d` option allows specifying a delimiter set to separate merged lines (default is a tab).
- **Single-file handling**: With a single input, `paste` can be used to format its output differently, like converting a vertical list into a horizontal one.

### Example Usage of `paste`:

**Scenario**: Suppose you have two files, `names.txt` and `ages.txt`, with corresponding data that you want to merge into a single file.

`names.txt` contains:
```
Alice
Bob
Charlie
```

`ages.txt` contains:
```
30
22
25
```

**Task**: Merge these two files so that each name is next to its corresponding age.

Here's a Bash script that uses `paste` to perform this task:

```bash
#!/bin/bash

# File names
names_file="names.txt"
ages_file="ages.txt"
output_file="combined.txt"

# Merge files with a comma as a delimiter
paste -d ',' "$names_file" "$ages_file" > "$output_file"

# Display the output
cat "$output_file"
```

This script specifies a comma as the delimiter with the `-d ','` option, merging `names.txt` and `ages.txt` line by line. The output will look like this:

```
Alice,30
Bob,22
Charlie,25
```

The merged data is then redirected to `combined.txt`, and `cat` is used to display the result. This use of `paste` demonstrates its utility in data processing tasks where files with related data need to be aligned side by side.