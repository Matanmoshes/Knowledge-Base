

```python
def justify_text(text, width):
    def add_spaces(words, num_spaces, is_last_line):
        if len(words) == 1 or is_last_line:
            return ' '.join(words).ljust(width)
        spaces = [num_spaces // (len(words) - 1)] * (len(words) - 1)
        for i in range(num_spaces % (len(words) - 1)):
            spaces[i] += 1
        justified_line = ''.join([word + ' ' * spaces[i] for i, word in enumerate(words[:-1])]) + words[-1]
        return justified_line

    words = text.split()
    result, current_line, current_length = [], [], 0

    for word in words:
        if current_length + len(current_line) + len(word) > width:
            result.append(add_spaces(current_line, width - current_length, False))
            current_line, current_length = [], 0
        current_line.append(word)
        current_length += len(word)

    result.append(add_spaces(current_line, width - current_length, True))
    return '\n'.join(result)

# Example usage
text = "This is an example of text justification."
width = 16
print(justify_text(text, width))

```


---

# Code Breakdown
Sure, let's break down the `justify_text` function step-by-step:

### Function Definition

```python
def justify_text(text, width):
```
This defines the function `justify_text` which takes two parameters:
- `text`: a string containing the text to be justified.
- `width`: an integer specifying the width of each justified line.

### Inner Function: `add_spaces`

```python
    def add_spaces(words, num_spaces, is_last_line):
```
This is a nested function that handles the spacing for a line of words. It takes three parameters:
- `words`: a list of words to be justified.
- `num_spaces`: the total number of spaces to distribute across the line.
- `is_last_line`: a boolean indicating whether the current line is the last line of the text.

#### Handling Single Word or Last Line

```python
        if len(words) == 1 or is_last_line:
            return ' '.join(words).ljust(width)
```
If the line contains only one word or if it's the last line, the words are joined with a single space and then left-justified to the specified width using `ljust`.

#### Distributing Spaces Evenly

```python
        spaces = [num_spaces // (len(words) - 1)] * (len(words) - 1)
        for i in range(num_spaces % (len(words) - 1)):
            spaces[i] += 1
```
If the line contains multiple words and it's not the last line, we calculate the spaces to be distributed:
- `spaces` list initially assigns an even distribution of spaces.
- The loop distributes any remaining spaces (remainder) one by one from the beginning.

#### Constructing the Justified Line

```python
        justified_line = ''.join([word + ' ' * spaces[i] for i, word in enumerate(words[:-1])]) + words[-1]
        return justified_line
```
This line constructs the justified line by adding each word followed by its respective spaces (from the `spaces` list). The last word is added without extra spaces at the end.

### Main Function Logic

#### Splitting the Text into Words

```python
    words = text.split()
```
The text is split into individual words using the `split()` method.

#### Initializing Variables

```python
    result, current_line, current_length = [], [], 0
```
- `result` is a list to store the justified lines.
- `current_line` is a list to accumulate words for the current line.
- `current_length` tracks the total length of words in the current line (excluding spaces).

#### Building Lines

```python
    for word in words:
        if current_length + len(current_line) + len(word) > width:
            result.append(add_spaces(current_line, width - current_length, False))
            current_line, current_length = [], 0
        current_line.append(word)
        current_length += len(word)
```
- For each word in the `words` list, it checks if adding the word would exceed the specified width (`current_length + len(current_line)` accounts for spaces between words).
- If it would exceed, it calls `add_spaces` to justify the current line, adds it to `result`, and resets `current_line` and `current_length`.
- Otherwise, the word is added to `current_line` and `current_length` is updated.

#### Handling the Last Line

```python
    result.append(add_spaces(current_line, width - current_length, True))
```
After processing all words, the last line (which may not be fully justified) is handled separately by calling `add_spaces` with `is_last_line` set to `True`.

### Returning the Result

```python
    return '\n'.join(result)
```
The justified lines stored in `result` are joined with newline characters (`\n`) and returned as a single string.

### Example Usage

```python
text = "This is an example of text justification."
width = 16
print(justify_text(text, width))
```
An example is provided to demonstrate the function. Given the text and width, it prints the justified text.

### Summary

- The function splits the input text into words.
- It processes each word, building lines that fit within the specified width.
- Each line (except the last) is fully justified by evenly distributing spaces.
- The last line is left-justified.
- The result is a string with all justified lines joined by newline characters.