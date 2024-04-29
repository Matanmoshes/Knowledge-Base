# `grep` Command
[grep]

The `grep` command in Linux is a powerful tool used for searching text data sets for lines that match a regular expression. Its name comes from the ed command `g/re/p` (globally search a regular expression and print), which has a similar function.

### Basic Syntax

```bash
grep [options] pattern [file...]
```

- **pattern**: The text or regular expression you want to search for.
- **file...**: One or more files to search. If no file is specified, `grep` reads from standard input.

### Key Options

- **`-i`**: Ignore case distinctions in both the pattern and the input files.
  ```bash
  grep -i "example" file.txt
  ```

- **`-v`**: Invert the match, i.e., show only lines that do not match the pattern.
  ```bash
  grep -v "example" file.txt
  ```

- **`-r` or `--recursive`**: Recursively search subdirectories listed.
  ```bash
  grep -r "example" /path/to/directory/
  ```

- **`-n`**: Prefix each line of output with the line number within its input file.
  ```bash
  grep -n "example" file.txt
  ```

- **`-c`**: Count the number of lines that match the pattern.
  ```bash
  grep -c "example" file.txt
  ```

- **`-l` (lowercase L)**: Print only the names of files with matching lines, once for each file.
  ```bash
  grep -l "example" *.txt
  ```

- **`-o`**: Show only the part of a matching line that matches PATTERN.
  ```bash
  grep -o "example" file.txt
  ```

- **`--color=auto`**: Mark up the matching text with color on the terminal.
  ```bash
  grep --color=auto "example" file.txt
  ```

### Practical Examples

1. **Search for a pattern in a file ignoring case**:
   ```bash
   grep -i "search term" filename
   ```

2. **Count the number of lines that contain the pattern**:
   ```bash
   grep -c "search term" filename
   ```

3. **List all files in a directory containing the pattern**:
   ```bash
   grep -rl "search term" /path/to/directory/
   ```

4. **Display lines that do not contain the pattern**:
   ```bash
   grep -v "search term" filename
   ```
