

```python
import pytest
from maze import solve_maze

def test_no_walls():
    maze = [[0, 0, 0],
            [0, 0, 0],
            [0, 0, 0]]
    path = solve_maze(maze)
    assert path[0] == (0, 0)
    assert path[-1] == (2, 2)
    assert all(maze[x][y] == 0 for x, y in path)

def test_with_walls():
    maze = [[0, 1, 0],
            [0, 1, 0],
            [0, 0, 0]]
    path = solve_maze(maze)
    assert path[0] == (0, 0)
    assert path[-1] == (2, 2)
    assert all(maze[x][y] == 0 for x, y in path)

def test_no_path():
    maze = [[0, 1, 0],
            [1, 1, 0],
            [0, 0, 0]]
    path = solve_maze(maze)
    assert path == "No path found"

```
