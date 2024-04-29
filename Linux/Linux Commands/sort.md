# `sort` Command
[sort]
The `sort` command in Bash is a utility for sorting lines in text files. It is widely used in Unix and Linux systems for organizing file content in a specified order, either numerically or alphabetically, among other options.

### Basic Syntax of `sort`:
```bash
sort [options] [file...]
```

- **options**: Modify how the sorting is performed, such as numerical, reverse, or checking uniqueness.
- **file...**: The file or files to sort. If no file is specified, `sort` reads from standard input.

### Key Options for `sort`:
- **`-n`**: Sort numerically (e.g., 10 will sort after 2).
- **`-r`**: Reverse the result of comparisons (sort in descending order).
- **`-k`**: Sort via a specified key (e.g., `-k 2` sorts by the second field).
- **`-u`**: Unique sorting that removes duplicate entries.
- **`-o`**: Output to a specified file.
- **`-t`**: Specify a delimiter with the `-t` option followed by a character.
- **`-f`**: Ignore case while sorting.

### Examples of Using `sort`:

1. **Alphabetical Sorting of a File**:
   ```bash
   sort file.txt
   ```
   Sorts the lines of `file.txt` alphabetically.

2. **Numerical Sorting**:
   ```bash
   sort -n file.txt
   ```
   Sorts the lines of `file.txt` based on numerical values.

3. **Sorting with a Specific Field**:
   ```bash
   sort -k 2 file.txt
   ```
   Sorts `file.txt` based on the second field of each line, which can be further combined with `-n` or `-r`.

4. **Reverse Sorting**:
   ```bash
   sort -r file.txt
   ```
   Sorts the lines of `file.txt` in reverse order (descending).

5. **Unique Sorting**:
   ```bash
   sort -u file.txt
   ```
   Sorts `file.txt` and removes duplicate lines.

6. **Sorting with a Custom Delimiter**:
   ```bash
   sort -t, -k 3n file.csv
   ```
   Sorts a CSV file based on the third column numerically.

### Practical Example:

Suppose you have a file named `data.txt` with the following content:
```
b 10
a 20
c 5
```

If you want to sort this file numerically based on the second column, you would use:
```bash
sort -k2,2n data.txt
```
Output:
```
c 5
b 10
a 20
```

This example demonstrates the utility of `sort` in handling structured text data, making it an essential tool for data processing and analysis in shell scripting environments.
