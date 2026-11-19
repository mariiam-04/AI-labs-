
# Lab 2: Uninformed Search — BFS and DFS on a 2D Maze

**Course:** Artificial Intelligence (BSCS)  
**Topic:** Uninformed Search Algorithms  
**Algorithms:** Breadth-First Search (BFS) and Depth-First Search (DFS)

## Lab Objective

The purpose of this lab is to implement BFS and DFS on a 2D maze, find paths from a starting cell to a goal cell, visualize the paths, and compare their performance.

---

## 1. The Maze

We represent the maze as a grid containing `0`s and `1`s:

- `0` = Open cell (the agent can move through it).
- `1` = Wall (the agent cannot move through it).

The agent starts at `(0, 0)` and must reach `(4, 4)`.

### Python Code

```python
maze = [
    [0, 0, 0, 0, 0],
    [0, 1, 1, 1, 0],
    [0, 0, 0, 1, 0],
    [0, 1, 0, 0, 0],
    [0, 1, 1, 1, 0]
]

start = (0, 0)
goal = (4, 4)

for row in maze:
    print(row)
```

### Explanation

The maze is a 5 × 5 grid. Each cell is identified by its row and column.

- Start `(0, 0)` is the top-left cell.
- Goal `(4, 4)` is the bottom-right cell.
- The algorithm must find a path without passing through walls.

---

## 2. Movements

The agent can move in four directions:

| Movement | Row Change | Column Change |
|---|---:|---:|
| UP | -1 | 0 |
| DOWN | 1 | 0 |
| LEFT | 0 | -1 |
| RIGHT | 0 | 1 |

### Python Code

```python
directions = [
    (-1, 0),   # up
    (1, 0),    # down
    (0, -1),   # left
    (0, 1)     # right
]
```

### Explanation

Each pair represents a change in the row and column.

For example, if the current position is `(2, 2)` and we move right, the new position becomes `(2, 3)`.

---

## 3. Checking a Valid Move

A move is valid when:

1. The new position is inside the maze.
2. The new position is not a wall.

### Python Code

```python
def valid_move(maze, row, col):
    rows = len(maze)
    cols = len(maze[0])

    return (
        0 <= row < rows
        and 0 <= col < cols
        and maze[row][col] == 0
    )

# Quick checks
print(valid_move(maze, 0, 0))   # True
print(valid_move(maze, 1, 1))   # False
print(valid_move(maze, -1, 0))  # False
```

### Expected Output

```text
True
False
False
```

### Explanation

- `(0, 0)` is an open cell, so the result is `True`.
- `(1, 1)` is a wall, so the result is `False`.
- `(-1, 0)` is outside the maze, so the result is `False`.

---

## 4. Breadth-First Search (BFS)

### Definition

Breadth-First Search (BFS) is an uninformed search algorithm that explores neighboring nodes level by level.

It uses a **Queue (FIFO)** data structure.

FIFO means First In, First Out. The element inserted first is removed first.

### Python Code

```python
from collections import deque

def bfs(maze, start, goal):
    frontier = deque([(start, [start])])
    visited = {start}
    max_frontier = 1

    while frontier:
        max_frontier = max(max_frontier, len(frontier))

        current, path = frontier.popleft()

        if current == goal:
            return path, max_frontier

        row, col = current

        for dr, dc in directions:
            nr, nc = row + dr, col + dc
            neighbor = (nr, nc)

            if valid_move(maze, nr, nc) and neighbor not in visited:
                visited.add(neighbor)
                frontier.append((neighbor, path + [neighbor]))

    return None, max_frontier
```

### Running BFS

```python
bfs_path, bfs_frontier = bfs(maze, start, goal)

print("BFS path:", bfs_path)
print("BFS path length:", len(bfs_path) - 1)
print("BFS maximum frontier:", bfs_frontier)
```

### Expected Output

```text
BFS path: [(0, 0), (1, 0), (2, 0), (2, 1), (2, 2), (3, 2), (3, 3), (3, 4), (4, 4)]
BFS path length: 8
BFS maximum frontier: 3
```

### Explanation

BFS explores the maze level by level using a queue.

Important instructions:

- `popleft()` removes the oldest element from the queue.
- `visited` prevents the algorithm from visiting the same cell repeatedly.
- `path` stores the route from the starting position.
- `max_frontier` records the largest number of states waiting in the queue.

### Why do we use `len(path) - 1`?

A path containing 4 positions has only 3 movements.

For example:

`Start → A → B → Goal`

There are four positions but only three movements.

Therefore:

`Path Length = Number of Positions - 1`

Because every movement costs 1, the path length is also the path cost.

---

## 5. Depth-First Search (DFS)

### Definition

Depth-First Search (DFS) is an uninformed search algorithm that explores one branch as deeply as possible before backtracking.

It uses a **Stack (LIFO)** data structure.

LIFO means Last In, First Out. The element inserted last is removed first.

### Python Code

```python
def dfs(maze, start, goal):
    frontier = [(start, [start])]
    visited = {start}
    max_frontier = 1

    while frontier:
        max_frontier = max(max_frontier, len(frontier))

        current, path = frontier.pop()

        if current == goal:
            return path, max_frontier

        row, col = current

        for dr, dc in directions:
            nr, nc = row + dr, col + dc
            neighbor = (nr, nc)

            if valid_move(maze, nr, nc) and neighbor not in visited:
                visited.add(neighbor)
                frontier.append((neighbor, path + [neighbor]))

    return None, max_frontier
```

### Running DFS

```python
dfs_path, dfs_frontier = dfs(maze, start, goal)

print("DFS path:", dfs_path)
print("DFS path length:", len(dfs_path) - 1)
print("DFS maximum frontier:", dfs_frontier)
```

### Expected Output

```text
DFS path: [(0, 0), (0, 1), (0, 2), (0, 3), (0, 4), (1, 4), (2, 4), (3, 4), (4, 4)]
DFS path length: 8
DFS maximum frontier: 3
```

### Explanation

DFS explores one branch deeply before exploring other branches.

Important instructions:

- `pop()` removes the most recently added element.
- The stack follows LIFO.
- `visited` prevents repeated exploration.
- `path` stores the route followed by DFS.

In this maze, DFS finds a path of length 8. However, DFS does not guarantee the shortest path in every maze.

---

## 6. Visualizing the Paths

We use the following symbols:

- `*` = Solution path
- `#` = Wall
- `.` = Open cell

### Python Code

```python
def print_maze(maze, path=None):
    path = set(path or [])

    for r in range(len(maze)):
        row = ""

        for c in range(len(maze[0])):
            if (r, c) in path:
                row += "* "
            elif maze[r][c] == 1:
                row += "# "
            else:
                row += ". "

        print(row)
```

### Display BFS and DFS Paths

```python
print("BFS solution:")
print_maze(maze, bfs_path)

print()

print("DFS solution:")
print_maze(maze, dfs_path)
```

### Expected Output

```text
BFS solution:
* . . . .
* # # # .
* * * # .
. # * * *
. # # # *

DFS solution:
* * * * *
. # # # *
. . . # *
. # . . *
. # # # *
```

### Explanation

The visualization shows the different routes selected by the two algorithms.

- BFS follows the left side and then moves toward the goal.
- DFS follows the top row and then moves down toward the goal.

Both paths reach the same goal, but they follow different routes.

---

## 7. Compare BFS and DFS

### Comparison Table

| Algorithm | Path Length | Maximum Frontier |
|---|---:|---:|
| BFS | 8 | 3 |
| DFS | 8 | 3 |

### Answers

**1. Which algorithm found the shorter path?**

Both algorithms found paths of equal length in this maze. Each path has 8 movements.

**2. Which algorithm had the larger maximum frontier?**

Neither algorithm had a larger maximum frontier. Both had a maximum frontier size of 3.

**3. Why did the two algorithms produce different paths on the same maze?**

They use different data structures.

- BFS uses a queue (FIFO), so it explores level by level.
- DFS uses a stack (LIFO), so it explores the most recently discovered branch first.

Therefore, they can select different routes even when the maze is the same.

**4. Does DFS always produce the shortest path? Why or why not?**

No. DFS does not guarantee the shortest path because it explores one branch deeply without comparing all paths according to their lengths.

It may reach the goal through a longer route before exploring a shorter route.

**5. Does BFS always produce the shortest path when every movement costs 1? Why?**

Yes. BFS guarantees a shortest path when every movement has equal cost.

It explores all positions at one distance before exploring positions at the next distance. Therefore, the first time it reaches the goal, it has found a shortest path.

---

## 8. Experiment 1 — Change the Neighbor Order

In this experiment, we change the order in which neighboring cells are explored.

### Python Code

```python
directions = [
    (0, 1),    # right
    (1, 0),    # down
    (0, -1),   # left
    (-1, 0)    # up
]

dfs_path2, dfs_frontier2 = dfs(maze, start, goal)

print("DFS path (new order):", dfs_path2)
print("DFS path length:", len(dfs_path2) - 1)
print("DFS maximum frontier:", dfs_frontier2)

print()
print_maze(maze, dfs_path2)
```

### Expected Output

```text
DFS path (new order): [(0, 0), (1, 0), (2, 0), (2, 1), (2, 2), (3, 2), (3, 3), (3, 4), (4, 4)]
DFS path length: 8
DFS maximum frontier: 3
```

### Answer

**Did the DFS path change?**

No. In this particular maze, DFS still returned the same path as the original BFS path.

**Did BFS's path depend on this ordering the same way? Why or why not?**

BFS can also select a different path when the neighbor order changes, especially when multiple shortest paths exist.

For this maze, changing the order to right, down, left, up makes BFS select the top route instead of the bottom route.

However, BFS still guarantees a shortest path because the order of exploration does not change its level-by-level behavior.

### Important Note

The neighbor order affects which path is selected when multiple possible paths exist. It does not remove the shortest-path guarantee of BFS.

### Reset Directions

Before continuing, restore the original order:

```python
directions = [
    (-1, 0),   # up
    (1, 0),    # down
    (0, -1),   # left
    (0, 1)     # right
]
```

---

## 9. Experiment 2 — Change Start and Goal

In this experiment, we change the starting and goal positions while keeping the same maze.

Both positions must be open cells (`0`).

### Python Code

```python
directions = [
    (-1, 0),
    (1, 0),
    (0, -1),
    (0, 1)
]

start = (0, 0)
goal = (2, 2)

bfs_path, bfs_frontier = bfs(maze, start, goal)
dfs_path, dfs_frontier = dfs(maze, start, goal)

print("BFS path:", bfs_path,
      "| length:", len(bfs_path) - 1,
      "| max frontier:", bfs_frontier)

print("DFS path:", dfs_path,
      "| length:", len(dfs_path) - 1,
      "| max frontier:", dfs_frontier)

print()
print("BFS solution:")
print_maze(maze, bfs_path)

print()
print("DFS solution:")
print_maze(maze, dfs_path)
```

### Expected Output for This Pair

```text
Start: (0, 0)
Goal: (2, 2)

BFS path: [(0, 0), (1, 0), (2, 0), (2, 1), (2, 2)]
BFS length: 4
BFS maximum frontier: 3

DFS path: [(0, 0), (0, 1), (0, 2), (0, 3), (0, 4), (1, 4), (2, 4), (3, 4), (3, 3), (3, 2), (2, 2)]
DFS length: 10
DFS maximum frontier: 3
```

### Results for Three Different Start and Goal Pairs

The following table records results obtained using the given maze and original direction order.

| Start | Goal | BFS Length | BFS Max Frontier | DFS Length | DFS Max Frontier |
|---|---|---:|---:|---:|---:|
| (0, 0) | (2, 2) | 4 | 3 | 10 | 3 |
| (0, 4) | (4, 4) | 4 | 3 | 12 | 4 |
| (2, 0) | (4, 4) | 6 | 3 | 6 | 4 |

### Python Code to Try Different Pairs

```python
test_pairs = [
    ((0, 0), (2, 2)),
    ((0, 4), (4, 4)),
    ((2, 0), (4, 4))
]

for start, goal in test_pairs:

    bfs_path, bfs_frontier = bfs(maze, start, goal)
    dfs_path, dfs_frontier = dfs(maze, start, goal)

    print("\nStart:", start, "Goal:", goal)

    print("BFS length:", len(bfs_path) - 1)
    print("BFS maximum frontier:", bfs_frontier)

    print("DFS length:", len(dfs_path) - 1)
    print("DFS maximum frontier:", dfs_frontier)
```

### Answers to Experiment 2

**6. Does BFS's path length ever change if you re-run it with the same start and goal? Does DFS's?**

With the same maze, start, goal, and direction order, both algorithms are deterministic in this implementation.

- BFS returns the same shortest path length each time.
- DFS also returns the same path length each time.

If the neighbor order changes, the selected paths may change. BFS still returns a shortest path, while DFS may return a different-length path.

**7. As the distance between start and goal grows, what happens to the maximum frontier size for each algorithm?**

The maximum frontier size may increase as more cells need to be explored, but it does not necessarily increase every time.

- BFS may have a larger frontier because it stores multiple neighboring states at the same level.
- DFS often follows one branch at a time, but its frontier can also grow when several branches are discovered.

The actual frontier size depends on the maze structure and neighbor order.

**8. Can you find a start and goal pair where BFS and DFS return the same path? What does that tell you about the maze structure between those two points?**

Yes.

For example:

`Start = (2, 0)`  
`Goal = (4, 4)`

Both algorithms return:

```text
[(2, 0), (2, 1), (2, 2), (3, 2), (3, 3), (3, 4), (4, 4)]
```

Both paths have a length of 6.

This shows that, for this pair and the given neighbor order, DFS follows the same route as BFS. The available route is sufficiently constrained that both algorithms select the same path.

**9. What happens if the goal is unreachable from the start?**

If the goal cannot be reached, both algorithms eventually explore all reachable cells and stop.

They return:

```python
(None, max_frontier)
```

Here, `None` means that no path was found.

The following separate example creates a wall barrier between the start and goal:

```python
test_maze = [
    [0, 1, 0],
    [0, 1, 0],
    [0, 1, 0]
]

test_start = (0, 0)
test_goal = (0, 2)

bfs_result = bfs(test_maze, test_start, test_goal)
dfs_result = dfs(test_maze, test_start, test_goal)

print("BFS result:", bfs_result)
print("DFS result:", dfs_result)
```

### Expected Output

```text
BFS result: (None, 1)
DFS result: (None, 1)
```

### Explanation

The middle column contains walls, so the start and goal are disconnected.

Neither BFS nor DFS can cross the wall barrier. Therefore, both return `None` for the path.

---

## 10. Where BFS and DFS Are Used

### Breadth-First Search (BFS)

BFS is used in:

1. Finding shortest paths in unweighted graphs.
2. Network exploration.
3. Finding degrees of separation in social networks.
4. Web crawling.
5. Level-order traversal of trees.

### Depth-First Search (DFS)

DFS is used in:

1. Maze and graph traversal.
2. Cycle detection.
3. Topological sorting.
4. Backtracking search.
5. File-system traversal.

### Uninformed Search

Both BFS and DFS are called uninformed search algorithms because they do not use additional knowledge about how close a state is to the goal.

They explore the search space using the structure of the problem.

### Informed Search

Informed search algorithms use additional information called a **heuristic**.

For example, A* search uses a heuristic to estimate the distance from the current state to the goal.

---

## 11. Key Takeaway

The main difference between BFS and DFS is how they manage their frontier.

### BFS

```python
current, path = frontier.popleft()
```

BFS uses FIFO (First In, First Out).

It explores the oldest discovered state first.

### DFS

```python
current, path = frontier.pop()
```

DFS uses LIFO (Last In, First Out).

It explores the most recently discovered state first.

### Final Comparison Table

| Feature | BFS | DFS |
|---|---|---|
| Full form | Breadth-First Search | Depth-First Search |
| Data structure | Queue | Stack |
| Rule | FIFO | LIFO |
| Exploration | Level by level | Deep branch first |
| Shortest path guarantee (equal costs) | Yes | No |
| Memory usage | Usually higher | Often lower |
| Can get stuck in an infinite-depth branch? | No, for finite branching and finite-depth solutions | Yes, in an infinite search space |
| Uses heuristic? | No | No |

### Conclusion

BFS and DFS are two important uninformed search algorithms.

BFS explores the maze level by level and guarantees the shortest path when every movement has equal cost. DFS explores one branch deeply before backtracking and does not guarantee the shortest path.

The main difference is the data structure used:

- BFS uses a Queue (FIFO).
- DFS uses a Stack (LIFO).

Both algorithms can solve maze problems, but their paths, exploration order, and memory requirements can be different.
