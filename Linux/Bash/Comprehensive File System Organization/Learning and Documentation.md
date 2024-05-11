
To adapt the code to handle answers in the format you described (e.g., `1. B)`), the script will need to adjust how it reads and compares the answers. Specifically, it will ignore everything but the letter in parentheses to perform the answer comparisons.

Here's an updated version of the script:

```bash
#!/bin/bash

# (1) Define file paths
QUESTIONS_FILE="questions.txt"
ANSWERS_FILE="correct_answer.txt"
USER_ANSWERS_FILE="user_answer.txt"

# (2) Verify necessary files exist
if [[ ! -f "$QUESTIONS_FILE" || ! -f "$ANSWERS_FILE" ]]; then
    echo "Error: Questions or Answers file missing. Ensure both files are available."
    exit 1
fi

# (3) Initialize score
score=0
total_questions=0

# (4) Clear previous user answers
> "$USER_ANSWERS_FILE"

# (5) Read questions into an array, shuffle them, and ask each question
mapfile -t questions < <(awk 'BEGIN {RS=""; FS="\n"} {print $0}' "$QUESTIONS_FILE" | shuf)

for ((i = 0; i < ${#questions[@]}; i++)); do
    echo "Question $((i + 1)):"
    echo "${questions[$i]}" | head -n -1  # Print all lines except the last
    read -p "Your answer (A, B, C, or D): " user_answer
    echo "$user_answer" >> "$USER_ANSWERS_FILE"
    ((total_questions++))
    echo ""
done

# (6) Read correct answers, extract only the letter, and compare with user answers
mapfile -t correct_answers < <(awk -F'[()]' '{print $2}' "$ANSWERS_FILE")
mapfile -t user_answers < "$USER_ANSWERS_FILE"

for ((i = 0; i < ${#correct_answers[@]}; i++)); do
    if [[ "${user_answers[$i]}" == "${correct_answers[$i]}" ]]; then
        ((score++))
    fi
done

# (7) Display final score
echo "Quiz completed."
echo "You scored $score out of $total_questions."

```



---

### Code Breakdown

Here's a detailed breakdown of the script, step by step:

```bash
#!/bin/bash
```
- **Shebang**: Specifies that the script should be run using Bash, which is located in `/bin/bash`.

```bash
# Files required
QUESTIONS_FILE="questions.txt"
ANSWERS_FILE="correct_answer.txt"
USER_ANSWERS_FILE="user_answer.txt"
```
- **File Paths**: Defines the variables `QUESTIONS_FILE`, `ANSWERS_FILE`, and `USER_ANSWERS_FILE` which contain the paths to the required files for the quiz (questions, answers, and storing user responses).

```bash
# Check if the required files exist
if [[ ! -f "$QUESTIONS_FILE" || ! -f "$ANSWERS_FILE" ]]; then
    echo "Questions or Answers file missing! Please ensure both are present."
    exit 1
fi
```
- **File Existence Check**:
  - `if [[ ! -f ... ]]`: Checks if each file exists (`-f`).
  - If either file is missing, prints an error message and exits with a status of 1.

```bash
# Initialize score
score=0
total_questions=0
```
- **Initialize Variables**:
  - `score`: Variable to track the number of correct answers the user gives.
  - `total_questions`: Tracks the total number of questions asked.

```bash
# Clear any previous answers
> "$USER_ANSWERS_FILE"
```
- **Clear Previous Answers**:
  - The `>` symbol truncates `USER_ANSWERS_FILE` to an empty file, effectively clearing any previous data.

```bash
# Read questions into an array and shuffle
mapfile -t questions < <(awk 'BEGIN {RS=""; FS="\n"} {print $0}' "$QUESTIONS_FILE" | shuf)
```
- **Reading Questions**:
  - `mapfile`: Reads each question into the `questions` array, separating questions using a blank line (`RS=""`).
  - `awk`: Reads the entire `QUESTIONS_FILE` and separates blocks of questions.
  - `shuf`: Randomizes the question order to mix up the quiz.

```bash
# Ask each question
for ((i = 0; i < ${#questions[@]}; i++)); do
    echo "Question $((i + 1)):"
    echo "${questions[$i]}" | head -n -1
    read -p "Your answer: " user_answer
    echo "$user_answer" >> "$USER_ANSWERS_FILE"
    ((total_questions++))
    echo ""
done
```
- **Loop Over Questions**:
  - `for ((...))`: Loops through each question in the `questions` array.
  - `echo "Question $((i + 1)):"`: Prints the question number.
  - `head -n -1`: Displays all lines of the current question block except the last line (which likely contains the answer).
  - `read -p`: Prompts the user to enter their answer.
  - `echo "$user_answer" >> "$USER_ANSWERS_FILE"`: Appends the user's response to `USER_ANSWERS_FILE`.
  - `((total_questions++))`: Increments the question count.

```bash
# Check answers and calculate score
echo "Evaluating your answers..."

mapfile -t correct_answers < "$ANSWERS_FILE"
mapfile -t user_answers < "$USER_ANSWERS_FILE"
```
- **Read Answers**:
  - `mapfile -t`: Reads answers from `ANSWERS_FILE` and `USER_ANSWERS_FILE` into arrays.
  - `correct_answers`: Stores the correct answers from `ANSWERS_FILE`.
  - `user_answers`: Stores the user's answers from `USER_ANSWERS_FILE`.

```bash
for ((i = 0; i < ${#correct_answers[@]}; i++)); do
    if [[ "${user_answers[$i]}" == "${correct_answers[$i]}" ]]; then
        ((score++))
    fi
done
```
- **Evaluate Answers**:
  - `for ((...))`: Iterates over each answer.
  - `if [[ "${user_answers[$i]}" == "${correct_answers[$i]}" ]]; then`: Checks if the user's answer matches the correct answer.
  - `((score++))`: Increments the score if the answers match.

```bash
# Display final score
echo "Quiz completed."
echo "You scored $score out of $total_questions."
```
- **Display Results**:
  - Prints the final score, showing how many questions the user answered correctly out of the total questions asked.

This breakdown provides a structured look into each part of the script, giving you a clear understanding of the logic and the flow involved in administering and evaluating a quiz.