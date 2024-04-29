# `diff` Command
[diff]

The `diff` command in Linux is a utility used to compare the contents of files line by line. It's especially useful in scenarios where you need to see changes between two versions of the same file, such as in programming or configuration management.

### Basic Syntax

```bash
diff [options] file1 file2
```

- **file1**, **file2**: These are the two files you want to compare.

### Common Options

- **`-u`, `--unified`**: This option provides a unified diff, which shows several lines of context, making it easier to understand the differences. This is the most commonly used format for patches.
  ```bash
  diff -u file1 file2
  ```

- **`-c`, `--context`**: Like unified, but provides a more detailed context for each change. Useful for getting a broader view of changes.
  ```bash
  diff -c file1 file2
  ```

- **`-i`**: Ignores case differences in file contents.
  ```bash
  diff -i file1 file2
  ```

- **`-w`, `--ignore-all-space`**: Ignores all white space in the files, useful for when changes in indentation or spacing aren't relevant.
  ```bash
  diff -w file1 file2
  ```

- **`--side-by-side`**: Displays the files next to each other for easy comparison.
  ```bash
  diff --side-by-side file1 file2
  ```

- **`-r`, `--recursive`**: Used to compare directories recursively. This option is helpful when comparing directories of files.
  ```bash
  diff -r dir1 dir2
  ```

### Understanding Output

The output of the `diff` command can be interpreted as follows:

- **Lines prefixed with `<`**: These lines are from `file1`.
- **Lines prefixed with `>`**: These lines are from `file2`.
- **Context lines** (in unified or context mode): Shown for reference and not prefixed by any symbol.
- **Change line numbers**: The line numbers before and after the change are shown, helping to identify the exact location in both files.

### Example of Unified Diff

If you run `diff -u file1 file2`, you might get output like this:

```diff
--- file1   2021-04-01 14:20:15.000000000 -0400
+++ file2   2021-04-01 14:20:30.000000000 -0400
@@ -1,5 +1,5 @@
-Old line in file1
+New line in file2
 Context line
 Context line
-Another old line in file1
+Another new line in file2
```

This output indicates:
- Lines beginning with `-` were in `file1` but changed in `file2`.
- Lines beginning with `+` are new or modified lines appearing in `file2`.


