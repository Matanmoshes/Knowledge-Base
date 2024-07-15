```python
import random
from collections import deque

def generate_random_maze(size=5, wall_prob=0.3):
    maze = [[1 if random.random() < wall_prob else 0 for _ in range(size)] for _ in range(size)]
    maze[0][0] = 0
    maze[size-1][size-1] = 0
    return maze

def solve_maze(maze):
    rows, cols = len(maze), len(maze[0])
    start, end = (0, 0), (rows-1, cols-1)
    directions = [(-1, 0), (1, 0), (0, -1), (0, 1)]
    queue = deque([start])
    visited = set()
    visited.add(start)
    path = {start: None}

    while queue:
        current = queue.popleft()
        if current == end:
            break
        for direction in directions:
            next_cell = (current[0] + direction[0], current[1] + direction[1])
            if (0 <= next_cell[0] < rows and 0 <= next_cell[1] < cols and
                next_cell not in visited and maze[next_cell[0]][next_cell[1]] == 0):
                queue.append(next_cell)
                visited.add(next_cell)
                path[next_cell] = current

    if end not in path:
        return "No path found"

    # Trace back the path
    shortest_path = []
    step = end
    while step is not None:
        shortest_path.append(step)
        step = path[step]
    shortest_path.reverse()

    return shortest_path

# Example usage
random_maze = generate_random_maze()
for row in random_maze:
    print(row)
print("Path found:", solve_maze(random_maze))

```

**Explanation**:

1. **generate_random_maze**: This function generates a random maze of a given size, ensuring the start and end points are open paths.
2. **solve_maze**: This function uses BFS to find the shortest path in the maze. It uses a queue to explore each node and a dictionary to keep track of the path.

Let's test these functions by running the code.

[python]

The code successfully generates a random 5x5 maze and finds the shortest path from the start (top-left corner) to the end (bottom-right corner). Here's a quick summary of the output:

1. **Generated Maze**:
    
    ```
    [0, 0, 0, 0, 1]
    [0, 0, 0, 0, 0]
    [0, 0, 0, 0, 0]
    [0, 0, 0, 0, 0]
    [1, 0, 0, 1, 0]
    
    ```
    
    The maze has a clear path from the start to the end.
    
2. **Path Found**:
    
    ```
    [(0, 0), (1, 0), (2, 0), (3, 0), (3, 1), (3, 2), (3, 3), (3, 4), (4, 4)]
    
    ```
    
    The path traces through the maze efficiently from start to end.
    
