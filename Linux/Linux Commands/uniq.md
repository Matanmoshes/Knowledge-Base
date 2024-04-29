# `uniq` Command
[uniq]

The `uniq` command in Unix and Linux is used to filter or report repeated lines in a file. Particularly useful when dealing with sorted data, `uniq` helps in removing or counting duplicate entries. The command typically requires sorted input, as it only removes duplicates that are adjacent to each other.

### Basic Syntax of `uniq`:
```bash
uniq [options] [input [output]]
```
- **input**: The file to process. If not provided, `uniq` reads from standard input.
- **output**: The file where the result is written. If not specified, output goes to standard output.

### Key Options for `uniq`:
- **`-c`**: Prefix lines by the number of occurrences.
- **`-d`**: Only print duplicate lines, one for each group of identical lines.
- **`-u`**: Only print unique lines, lines that never repeat.
- **`-i`**: Ignore differences in case when comparing lines.
- **`-s N`**: Skip the first N characters when comparing lines.
- **`-w N`**: Compare no more than N characters in lines.

### Examples of Using `uniq`:

1. **Removing Adjacent Duplicates**:
   ```bash
   sort file.txt | uniq
   ```
   This common pattern sorts `file.txt` and pipes it to `uniq`, effectively removing all adjacent duplicate lines.

2. **Counting Duplicates**:
   ```bash
   sort file.txt | uniq -c
   ```
   Sorts the file and passes it to `uniq`, which then prefixes each line with the number of occurrences.

3. **Finding Only Duplicates**:
   ```bash
   sort file.txt | uniq -d
   ```
   This will display only the lines that have duplicates, showing one instance of each duplicated line.

4. **Extracting Unique Lines**:
   ```bash
   sort file.txt | uniq -u
   ```
   Outputs lines that appear only once in the file.

5. **Ignoring Case**:
   ```bash
   sort file.txt | uniq -i
   ```
   Ignores case when filtering duplicates, useful for case-insensitive data.

### Practical Example:

Imagine you have a log file (`access.log`) with listed accesses by IP address and you want to see which IPs have accessed more than once, ignoring variations in case:

```bash
cat access.log | cut -d' ' -f1 | sort | uniq -i -d
```
This command sequence:
- Extracts the first field (assuming IP addresses are the first part of each log entry),
- Sorts these IPs,
- Removes duplicates, ignoring case, and
- Displays only those lines that were duplicated.
