

```python
import unittest
from maze import solve_maze

class TestSolveMaze(unittest.TestCase):
    def test_no_walls(self):
        maze = [[0, 0, 0],
                [0, 0, 0],
                [0, 0, 0]]
        path = solve_maze(maze)
        self.assertEqual(path[0], (0, 0))
        self.assertEqual(path[-1], (2, 2))
        self.assertTrue(all(maze[x][y] == 0 for x, y in path))

    def test_with_walls(self):
        maze = [[0, 1, 0],
                [0, 1, 0],
                [0, 0, 0]]
        path = solve_maze(maze)
        self.assertEqual(path[0], (0, 0))
        self.assertEqual(path[-1], (2, 2))
        self.assertTrue(all(maze[x][y] == 0 for x, y in path))
    
    def test_no_path(self):
        maze = [[0, 1, 0],
                [1, 1, 0],
                [0, 0, 0]]
        path = solve_maze(maze)
        self.assertEqual(path, "No path found")

if __name__ == '__main__':
    unittest.main()


```
