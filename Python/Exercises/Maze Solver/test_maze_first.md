

```python
import unittest
from maze import generate_random_maze

class TestGenerateRandomMaze(unittest.TestCase):
    def test_maze_size(self):
        size = 5
        maze = generate_random_maze(size)
        self.assertEqual(len(maze), size)
        self.assertEqual(len(maze[0]), size)
    
    def test_maze_start_end_open(self):
        size = 5
        maze = generate_random_maze(size)
        self.assertEqual(maze[0][0], 0)
        self.assertEqual(maze[size-1][size-1], 0)
    
    def test_maze_walls(self):
        size = 5
        wall_prob = 0.3
        maze = generate_random_maze(size, wall_prob)
        wall_count = sum(row.count(1) for row in maze)
        expected_wall_count = size * size * wall_prob
        self.assertAlmostEqual(wall_count, expected_wall_count, delta=size)

if __name__ == '__main__':
    unittest.main()

```
