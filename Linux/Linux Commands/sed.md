# `sed` Command
[sed]
The `sed` command in Linux, short for "stream editor," is a powerful utility for performing text transformations on an input stream (like a file or input from a pipeline). It is primarily used for extracting and transforming text in files or output from other commands.

### Basic Syntax

```bash
sed [options] 'script' [input_file]
```

- **options**: Control the behavior of `sed`. Common options include `-i` for editing files in-place (i.e., saves the output back to the input file) and `-e` to add multiple scripts.
- **script**: The set of commands that `sed` will execute. The commands specify what to search for and how to modify it.
- **input_file**: The file on which `sed` will operate. If no input file is provided, `sed` reads from standard input.

### Common Commands in `sed`

- **`s` command**: Substitute command, used to replace text.
  ```bash
  sed 's/find/replace/' file.txt  # Replaces the first occurrence of 'find' with 'replace' in each line
  ```

- **`g` flag**: Global replacement flag. Without this flag, `s` replaces only the first occurrence in each line.
  ```bash
  sed 's/find/replace/g' file.txt  # Replaces all occurrences of 'find' with 'replace' in each line
  ```

- **Addressing lines**: You can specify which lines `sed` should operate on.
  ```bash
  sed '2,5s/find/replace/' file.txt  # Only apply the substitution on lines 2 through 5
  sed '/pattern/s/find/replace/' file.txt  # Only apply the substitution on lines matching 'pattern'
  ```

- **`-i` option**: Edit files in-place, but use it with caution as it modifies the source file.
  ```bash
  sed -i 's/find/replace/g' file.txt  # Modifies file.txt directly
  ```

- **`d` command**: Delete lines.
  ```bash
  sed '3d' file.txt  # Deletes the third line
  sed '/pattern/d' file.txt  # Deletes lines matching 'pattern'
  ```

- **`p` command**: Print lines explicitly, used often with `-n` option to suppress default output.
  ```bash
  sed -n 'p' file.txt  # Prints each line, combined with -n it prints nothing unless specified
  sed -n '/pattern/p' file.txt  # Prints only the lines that match 'pattern'
  ```

- **`a`, `i`, and `c` commands**: Append, insert, or change a line.
  ```bash
  sed '2a new_line' file.txt  # Appends 'new_line' after line 2
  sed '3i new_line' file.txt  # Inserts 'new_line' before line 3
  sed '4c new_line' file.txt  # Changes line 4 to 'new_line'
  ```

### Advanced Usage

`sed` can also execute more complex scripts involving multiple commands, utilize hold and pattern spaces for advanced text manipulation, and even execute commands conditionally or loop over sections of text.

For instance, you can use a semi-colon to separate multiple commands:

```bash
sed 's/find/replace/; s/search/replace/' file.txt
```

Or use a file to hold multiple `sed` commands:

```bash
sed -f script.sed input_file
```


