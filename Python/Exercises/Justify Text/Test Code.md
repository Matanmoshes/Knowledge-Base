# Text Justification

## Overview

This project provides a Python implementation for text justification. The main function, `justify_text`, adjusts the spacing of words in a given text to ensure that each line fits within a specified width. This functionality can be useful for formatting text in documents, console applications, or any other scenario where neatly justified text is desired.

## Features

- Justifies text to a specified width.
- Handles single long words by placing them on a new line.
- Distributes spaces evenly between words, with extra spaces added to the leftmost gaps.
- Supports edge cases like empty strings and words longer than the specified width.

## Requirements

- Python 3.6 or higher

## Installation

1. Clone the repository:
   ```bash
   git clone https://github.com/yourusername/justify_text.git
   ```
2. Navigate to the project directory:
   ```bash
   cd justify_text
   ```

## Usage

To use the `justify_text` function, simply import it into your Python script and call it with the desired text and width:

```python
from justify_text import justify_text

text = "This is an example of text justification."
width = 16
justified_text = justify_text(text, width)
print(justified_text)
```

## Example

Given the following text and width:
- Text: "This is an example of text justification."
- Width: 16

The output will be:
```
This    is    an
example  of text
justification.  
```

## Folder Hierarchy

The project is organized as follows:

```
Task_2_Text_Justification/
├── justify_text.py
├── tests/
│   ├── __init__.py
│   ├── test_text_justification_basic.py
│   ├── test_text_justification_edge_cases.py
│   └── test_text_justification_varied_widths.py
└── README.md
```

- `justify_text.py`: Contains the implementation of the `justify_text` function.
- `tests/`: Directory containing test files.
  - `__init__.py`: Marks the directory as a Python package.
  - `test_text_justification_basic.py`: Basic functionality tests.
  - `test_text_justification_edge_cases.py`: Edge case tests.
  - `test_text_justification_varied_widths.py`: Tests with varied line widths.
- `README.md`: This README file.

## Tests

The project includes a comprehensive test suite to ensure the functionality of the `justify_text` function. The tests cover basic usage, edge cases, and varied widths.

### Running Tests

To run the tests, navigate to the project directory and use the following command:

```bash
python3 -m unittest discover -s tests -p "*.py"
```

