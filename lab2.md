# Lab 2: BFS and DFS Answers

## 7. Compare BFS and DFS

| Algorithm | Path Length | Maximum Frontier |
|---|---:|---:|
| BFS | 8 | 8 |
| DFS | 12 | 10 |

### 1. Which algorithm found the shorter path?

**Answer: BFS**

- BFS path length: 8
- DFS path length: 12

### 2. Which algorithm had the larger maximum frontier?

**DFS**

- BFS maximum frontier: 8
- DFS maximum frontier: 10

### 3. Why did the two algorithms produce different paths on the same maze?

BFS uses a FIFO queue and explores level by level.

DFS uses a LIFO stack and follows branches deeply before backtracking.

### 4. Does DFS always produce the shortest path?

**No.**

DFS may find a longer valid path before it explores a shorter path.

### 5. Does BFS always produce the shortest path when every movement costs 1?

**Yes.**

BFS explores states in increasing distance from the start, so the first discovered goal has the shortest path.

# Experiment 8: Change the Neighbor Order

## 8.1 Changed Direction Order

```python
directions = [
    (0, 1),   # right
    (1, 0),   # down
    (0, -1),  # left
    (-1, 0)   # up
]
8.2 Re-run DFS
Python
dfs_path2, dfs_frontier2 = dfs(
    maze,
    start,
    goal
)

print(
    "DFS path (new order):",
    dfs_path2
)

print(
    "DFS path length:",
    len(dfs_path2) - 1
)

print(
    "DFS maximum frontier:",
    dfs_frontier2
)

print()
print_maze(
    maze,
    dfs_path2
)
8.3 Question 1
Did the DFS path change?

Yes.

Changing the neighbor order changes the branch that DFS explores first.

DFS uses a LIFO stack:

Python
current, path = frontier.pop()
Therefore, changing the order in which valid neighbors are added to the stack can change:

The final DFS path
The DFS path length
The DFS maximum frontier
8.4 Question 2
Did BFS's path depend on this ordering the same way?

No, not in the same way.

BFS may choose a different exact path if multiple shortest paths exist, because the neighbor order determines the order in which equally short paths are discovered.

However, changing the neighbor order does not change BFS's shortest-path guarantee.

BFS uses a FIFO queue:

Python
current, path = frontier.popleft()
This means that it explores states level by level regardless of neighbor order.

8.5 Reset Directions
Reset directions before continuing to Experiment 2:

Python
directions = [
    (-1, 0),  # up
    (1, 0),   # down
    (0, -1),  # left
    (0, 1)    # right
]


Markdown
# Experiment 9: Change Start and Goal

## 9.1 Reset the Direction Order

```python
directions = [
    (-1, 0),  # up
    (1, 0),   # down
    (0, -1),  # left
    (0, 1)    # right
]
9.2 Run BFS and DFS
Python
# Try changing these to any open (0) cells
start = (0, 0)
goal = (2, 2)

bfs_path, bfs_frontier = bfs(
    maze,
    start,
    goal
)

dfs_path, dfs_frontier = dfs(
    maze,
    start,
    goal
)

print(
    "BFS path:",
    bfs_path,
    "| length:",
    len(bfs_path) - 1,
    "| max frontier:",
    bfs_frontier
)

print(
    "DFS path:",
    dfs_path,
    "| length:",
    len(dfs_path) - 1,
    "| max frontier:",
    dfs_frontier
)

print()
print("BFS solution:")
print_maze(maze, bfs_path)

print()
print("DFS solution:")
print_maze(maze, dfs_path)
9.3 Experiment Table
Start	Goal	BFS Length	BFS Max Frontier	DFS Length	DFS Max Frontier
(0, 0)	(2, 2)	4	5	4	5
(0, 0)	(4, 4)	8	8	12	10
(0, 4)	(4, 0)	8	9	8	7
Run the notebook and replace or verify these values using the original neighbor order.

9.4 Question 6
Does BFS's path length ever change if you re-run it with the same start and goal?

No.

For the same maze, start, goal, movement cost, and neighbor order, BFS always has the same shortest path length.

Does DFS's path length change?

DFS normally remains the same for unchanged maze, start, goal, and neighbor order.

However, changing the neighbor order can change DFS's path and possibly its path length.

9.5 Question 7
As the distance between start and goal grows, what happens to the maximum frontier size for each algorithm?

BFS generally explores more states as the distance grows, so its maximum frontier usually increases.

DFS follows deep branches, so its maximum frontier is less predictable and may not increase consistently.

The actual frontier size also depends on:

The maze structure
Branching
Walls
Dead ends
Neighbor order
Start and goal positions
9.6 Question 8
Can you find a start and goal pair where BFS and DFS return the same path?

Yes.

Example:

Text
Start: (0, 0)
Goal: (2, 2)
Possible common path:

Python
[
    (0, 0),
    (1, 0),
    (2, 0),
    (2, 1),
    (2, 2)
]
What does this tell you about the maze structure between those two points?

It suggests that there are few or no alternative branches between the selected points.

The available route may be restricted or unique because of walls.

9.7 Question 9
What happens if the goal is unreachable from the start?

Python
start = (0, 0)
goal = (1, 1)
Here, (1, 1) is a wall, so the goal is unreachable.

Python
bfs_path, bfs_frontier = bfs(
    maze,
    start,
    goal
)

dfs_path, dfs_frontier = dfs(
    maze,
    start,
    goal
)

print("BFS:", bfs_path, bfs_frontier)
print("DFS:", dfs_path, dfs_frontier)
Both algorithms return:

Python
None
for the path.

The complete return values have this form:

Python
return None, max_frontier
If the goal is an open cell separated from the start by walls, BFS and DFS explore every reachable state and then terminate because the frontier becomes empty.
