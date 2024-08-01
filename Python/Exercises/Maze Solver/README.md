
# Maze Solver

This repository contains a Python implementation of a maze generator and solver. The maze is generated with random walls, and the solver uses a breadth-first search (BFS) algorithm to find the shortest path from the start to the end of the maze.

## Files

- `maze_solver.py`: Contains the implementation of the maze generator and solver.
- `test_maze_first.py`: Unit tests for the `generate_random_maze` function using `unittest`.
- `test_maze_sec.py`: Unit tests for the `solve_maze` function using `unittest`.
- `test_maze_third.py`: Unit tests for the `solve_maze` function using `pytest`.

## Usage

### Generate and Solve a Maze

To generate a random maze and solve it, you can run the `maze_solver.py` script:

```bash
python maze_solver.py
```

### Example Output

```plaintext
[0, 0, 1, 0, 0]
[0, 0, 0, 0, 0]
[1, 0, 1, 0, 0]
[0, 1, 0, 1, 0]
[1, 0, 0, 0, 0]
Path found: [(0, 0), (1, 0), (1, 1), (1, 2), (1, 3), (2, 3), (2, 4), (3, 4), (4, 4)]
```

## Testing

### Using `unittest`

To run the `unittest` tests, use the following command:

```bash
python -m unittest discover -s . -p 'test_*.py'
```

### Using `pytest`

To run the `pytest` tests, use the following command:

```bash
pytest test_maze_third.py
```

## Installation

1. Clone the repository:

```bash
git clone https://github.com/yourusername/maze_solver.git
cd maze_solver
```

2. (Optional) Create and activate a virtual environment:

```bash
python -m venv .venv
.venv\Scripts\activate  # On Windows
source .venv/bin/activate  # On Unix or MacOS
```

3. Install dependencies:

```bash
pip install -r requirements.txt
```

## Project Structure

```plaintext
maze_solver/
│
├── maze_solver.py          # Implementation of the maze generator and solver
├── test_maze_first.py      # Unit tests for generate_random_maze using unittest
├── test_maze_sec.py        # Unit tests for solve_maze using unittest
├── test_maze_third.py      # Unit tests for solve_maze using pytest
├── requirements.txt        # Python dependencies
└── README.md               # Project documentation
```

## Dependencies

- Python 3.6+
- `unittest` (included in Python standard library)
- `pytest` (install via `pip install pytest`)



