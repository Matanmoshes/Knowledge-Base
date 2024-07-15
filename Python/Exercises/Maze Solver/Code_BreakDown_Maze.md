### Part 1: `generate_random_maze`

### Code:

```python
def generate_random_maze(size=5, wall_prob=0.3):
    maze = [[1 if random.random() < wall_prob else 0 for _ in range(size)] for _ in range(size)]
    maze[0][0] = 0
    maze[size-1][size-1] = 0
    return maze

```

### Explanation:

1. **Function Definition:**
    
    ```python
    def generate_random_maze(size=5, wall_prob=0.3):
    
    ```
    
    - Defines a function named `generate_random_maze` that takes two optional parameters: `size` (default is 5) and `wall_prob` (default is 0.3).
2. **Generate Maze:**
    
    ```python
    maze = [[1 if random.random() < wall_prob else 0 for _ in range(size)] for _ in range(size)]
    
    ```
    
    - Creates a 2D list (a list of lists) to represent the maze.
    - `[[... for _ in range(size)] for _ in range(size)]` constructs a square grid (2D list) of given `size`.
    - `1 if random.random() < wall_prob else 0` determines the value of each cell:
        - `random.random()` generates a random float between 0 and 1.
        - If this random float is less than `wall_prob`, the cell is set to `1` (wall).
        - Otherwise, the cell is set to `0` (path).
3. **Ensure Start Cell is Path:**
    
    ```python
    maze[0][0] = 0
    
    ```
    
    - Ensures that the start cell at the top-left corner `(0, 0)` is always a path (`0`).
4. **Ensure End Cell is Path:**
    
    ```python
    maze[size-1][size-1] = 0
    
    ```
    
    - Ensures that the end cell at the bottom-right corner `(size-1, size-1)` is always a path (`0`).
5. **Return Maze:**
    
    ```python
    return maze
    
    ```
    
    - Returns the generated maze.

### Part 2: `solve_maze`

### Code:

```python
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

    shortest_path = []
    step = end
    while step is not None:
        shortest_path.append(step)
        step = path[step]
    shortest_path.reverse()

    return shortest_path

```

### Explanation:

1. **Function Definition:**
    
    ```python
    def solve_maze(maze):
    
    ```
    
    - Defines a function named `solve_maze` that takes a single parameter `maze`.
2. **Initialization:**
    
    ```python
    rows, cols = len(maze), len(maze[0])
    start, end = (0, 0), (rows-1, cols-1)
    
    ```
    
    - Determines the number of rows and columns in the maze.
    - Sets the start cell at the top-left corner `(0, 0)` and the end cell at the bottom-right corner `(rows-1, cols-1)`.
3. **Define Directions:**
    
    ```python
    directions = [(-1, 0), (1, 0), (0, -1), (0, 1)]
    
    ```
    
    - Lists possible movement directions: up `(-1, 0)`, down `(1, 0)`, left `(0, -1)`, and right `(0, 1)`.
4. **Initialize BFS Structures:**
    
    ```python
    queue = deque([start])
    visited = set()
    visited.add(start)
    path = {start: None}
    
    ```
    
    - Initializes a deque (double-ended queue) with the start cell for BFS traversal.
    - Creates an empty set `visited` to keep track of visited cells and adds the start cell.
    - Initializes a dictionary `path` to keep track of the path, mapping each cell to its predecessor. The start cell maps to `None`.
5. **BFS Loop:**
    
    ```python
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
    
    ```
    
    - Continues as long as there are cells in the queue.
    - Dequeues the front cell from the queue and assigns it to `current`.
    - Checks if the current cell is the end cell; if so, breaks the loop.
    - Iterates over the possible movement directions.
    - Calculates the coordinates of the next cell.
    - Checks if the next cell is within bounds, has not been visited, and is a path.
    - Enqueues the next cell to the queue, marks it as visited, and updates the path dictionary.
6. **Check for Path Existence:**
    
    ```python
    if end not in path:
        return "No path found"
    
    ```
    
    - After the loop, checks if the end cell is not in the path dictionary, indicating no path was found. If so, returns "No path found".
7. **Path Reconstruction:**
    
    ```python
    shortest_path = []
    step = end
    while step is not None:
        shortest_path.append(step)
        step = path[step]
    shortest_path.reverse()
    
    ```
    
    - Initializes an empty list `shortest_path` to store the shortest path.
    - Starts tracing back the path from the end cell.
    - Continues tracing back until reaching the start cell.
    - Appends the current step to the shortest path and moves to the predecessor cell.
    - Reverses the shortest path list to get the correct order from start to end.
8. **Return Path:**
    
    ```python
    return shortest_path
    
    ```
    
    - Returns the shortest path.

### Example Usage

### Code:

```python
random_maze = generate_random_maze()
for row in random_maze:
    print(row)
print("Path found:", solve_maze(random_maze))

```

### Explanation:

1. **Generate Maze:**
    
    ```python
    random_maze = generate_random_maze()
    
    ```
    
    - Calls the `generate_random_maze` function to create a random maze and assigns it to `random_maze`.
2. **Print Maze:**
    
    ```python
    for row in random_maze:
        print(row)
    
    ```
    
    - Iterates through each row of the generated maze and prints it.
3. **Solve Maze:**
    
    ```python
    print("Path found:", solve_maze(random_maze))
    
    ```
    
    - Calls the `solve_maze` function to find the shortest path in the maze and prints the result.

This detailed breakdown should help you understand how each part of the code works and how the functions operate to generate and solve the maze.
