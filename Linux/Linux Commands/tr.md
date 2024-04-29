# `tr` Command
[tr]

The `tr` command in Bash (short for translate) is a useful utility for transforming or deleting characters from the standard input and writing the result to the standard output. It is frequently used for replacing, removing, or squeezing repeating characters.

### Basic Syntax of `tr`:
```bash
tr [options] set1 [set2]
```

- **options**: Modify how the translation or deletion affects the data.
- **set1**: The set of characters to be replaced or removed.
- **set2**: The set of characters that `set1` should be replaced with or mapped to.

### Key Options for `tr`:
- **`-d`**: Delete characters in `set1` from the input.
- **`-s`**: Squeeze repeated characters listed in the first set into a single character.
- **`-c`, `-C`, `--complement`**: Use the complement of `set1`.

### Examples of Using `tr`:

1. **Replacing Characters**:
   ```bash
   echo "hello world" | tr 'a-z' 'A-Z'
   ```
   Converts lowercase letters to uppercase, outputting "HELLO WORLD".

2. **Deleting Characters**:
   ```bash
   echo "hello 123 world 456" | tr -d '0-9'
   ```
   Removes all digits, outputting "hello  world ".

3. **Squeezing Characters**:
   ```bash
   echo "hello    world" | tr -s ' '
   ```
   Replaces repeated spaces with a single space, outputting "hello world".

4. **Using Character Classes**:
   ```bash
   echo "hello 123 world" | tr -d '[:digit:]'
   ```
   Deletes all digits, utilizing character classes, outputting "hello  world".

5. **Complementing Sets**:
   ```bash
   echo "abcde12345" | tr -c '0-9' '-'
   ```
   Replaces all characters not in `0-9` with `-`, resulting in "-----12345".

### Practical Example:

Suppose you have a text that includes several accented characters and you want to replace them with their unaccented versions for simplicity or compatibility reasons:

```bash
echo "café au lait" | tr 'é' 'e'
```
Output:
```
cafe au lait
```

