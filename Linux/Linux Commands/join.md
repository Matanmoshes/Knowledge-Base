# `join` command
[join]

The `join` command in Linux is a versatile utility used to combine the lines of two files based on a common field. This tool is particularly useful for merging two sets of data that share at least one attribute, much like a JOIN operation in SQL. It works best on sorted files, as the default behavior of `join` relies on the files being sorted on the join field.

### Basic Syntax

The basic syntax of the `join` command is:

```bash
join [OPTIONS] FILE1 FILE2
```

- **FILE1** and **FILE2**: These are the two files you want to join. The files should be sorted on the join field. If they are not sorted, you should sort them beforehand or use `join` with the sort command.

### Key Options

- **-a FILENUM**: Print a line for each unpairable line in file FILENUM, where FILENUM is 1 or 2, corresponding to FILE1 or FILE2.
- **-e EMPTY**: Replace missing input fields with EMPTY.
- **-1 FIELD**: Join on this FIELD of file 1.
- **-2 FIELD**: Join on this FIELD of file 2.
- **-o FORMAT**: Specify the output format. FORMAT is a space-separated list of fields, introduced by 0 (for the join field), 1, or 2 (for fields from FILE1 or FILE2).
- **-t CHAR**: Use CHAR as the input and output field separator.
- **-i**: Ignore differences in case when comparing fields.

### Common Usage Examples

Here are some practical examples of how to use the `join` command:

1. **Basic Join**:
   Assume you have two files, each containing a list of items with an ID and some attribute. To join these files on the first column:

   ```bash
   join file1.txt file2.txt
   ```

2. **Specifying the Join Field**:
   If you want to join on, say, the second column of each file:

   ```bash
   join -1 2 -2 2 file1.txt file2.txt
   ```

   Here, `-1 2` specifies that the join field for FILE1 is column 2 and `-2 2` for FILE2.

3. **Including Unpairable Lines**:
   To include lines from both files that do not have a matching join field:

   ```bash
   join -a 1 -a 2 file1.txt file2.txt
   ```

4. **Customizing the Output Format**:
   If you only want to display specific fields from each file, such as the join field and the second field from both files:

   ```bash
   join -o 0,1.2,2.2 file1.txt file2.txt
   ```

5. **Using a Different Field Separator**:
   If your files use a comma (`,`) instead of whitespace as a field separator:

   ```bash
   join -t , file1.csv file2.csv
   ```

6. **Combining with Sort**:
   If your files are not pre-sorted and you want to sort them by the join field (e.g., the first column) before joining:

   ```bash
   join <(sort file1.txt) <(sort file2.txt)
   ```

   This uses process substitution to sort the files on-the-fly.

>[! Tips]
>
>
>
>- **Sorting**: `join` operates most effectively on sorted files. Always ensure your files are sorted on the join field, or use the sort utility as shown above.
>- **Data Integrity**: Make sure that the fields used for joining do not have embedded spaces, or use `-t` to specify a different delimiter that is not part of the data.
>- **Performance**: `join` can handle large files efficiently, but performance can degrade if the files are very large and not properly sorted.


---

### Example Usage of `join`:

**Scenario**: Assume you have two files, `employees.txt` and `departments.txt`. `employees.txt` lists employee IDs and their names, and `departments.txt` lists employee IDs and their respective departments.

`employees.txt`:
```
1 Alice
2 Bob
3 Charlie
```

`departments.txt`:
```
1 Accounting
2 Marketing
3 Engineering
```

**Task**: Merge these files to list each employee's name alongside their department.

Here's a Bash script that uses the `join` command to accomplish this:

```bash
#!/bin/bash

# File names
employees_file="employees.txt"
departments_file="departments.txt"
output_file="joined_data.txt"

# Join files on the first field of each, which is the employee ID
join -t ' ' -1 1 -2 1 "$employees_file" "$departments_file" > "$output_file"

# Display the output
cat "$output_file"
```

This script merges `employees.txt` and `departments.txt` on the first field of each file (employee ID) using space (' ') as the delimiter. The joined data looks like this:

```
1 Alice Accounting
2 Bob Marketing
3 Charlie Engineering
```

The result is redirected to `joined_data.txt`, and `cat` is used to display the contents. This example shows how `join` is effective for combining related records from two structured text files based on a shared key.