
# Week 4 Lab: A* Search for the 8-Puzzle

**Course:** BSCS – Artificial Intelligence  
**Platform:** Google Colab  
**Programming Language:** Python 3

## 1. Objective

The objective of this lab is to implement the A* search algorithm and apply it to the 8-Puzzle problem.

We implement two heuristics:

1. Misplaced Tiles
2. Manhattan Distance

We compare both heuristics by measuring the number of nodes expanded.

## 2. Problem Representation

- State: Tuple of 9 integers.
- Blank tile: 0 represents the empty space.
- Goal state: (1, 2, 3, 4, 5, 6, 7, 8, 0).
- Actions: Up, Down, Left, Right.
- Goal test: Checks whether the current state matches the goal state.

### Cell 2: Imports and Constants

```python
import heapq
import itertools
import random
import pandas as pd
import matplotlib.pyplot as plt

GOAL_STATE = (1, 2, 3, 4, 5, 6, 7, 8, 0)
```

### Cell 3: State Helper Functions

```python
def get_blank_position(state):
    return state.index(0)


def get_neighbors(state):

    neighbors = []

    blank = get_blank_position(state)

    row, col = divmod(blank, 3)

    moves = {
        "Up": (row - 1, col),
        "Down": (row + 1, col),
        "Left": (row, col - 1),
        "Right": (row, col + 1)
    }

    for action, (new_row, new_col) in moves.items():

        if 0 <= new_row < 3 and 0 <= new_col < 3:

            new_blank = new_row * 3 + new_col

            new_state = list(state)

            new_state[blank], new_state[new_blank] = (
                new_state[new_blank],
                new_state[blank]
            )

            neighbors.append((tuple(new_state), action))

    return neighbors


def is_goal(state):
    return state == GOAL_STATE
```

### Cell 4: Checkpoint

```python
neighbors = get_neighbors(GOAL_STATE)

print(neighbors)

assert len(neighbors) == 2

print("Cell 3 checkpoint passed.")
```

Expected result: Two valid neighbors, Up and Left.

---

## 3. Heuristics

A heuristic estimates the remaining cost from the current state to the goal.

A* uses the following formula:

**f(n) = g(n) + h(n)**

Where:

- g(n) = Actual cost from the start state.
- h(n) = Estimated cost to reach the goal.
- f(n) = Total estimated cost.

An admissible heuristic never overestimates the actual minimum cost.

### Cell 6: Misplaced Tiles Heuristic

```python
def misplaced_tiles(state):

    count = 0

    for i in range(9):

        if state[i] != 0 and state[i] != GOAL_STATE[i]:
            count += 1

    return count
```

**Explanation:**

1. Check every tile.
2. Ignore the blank tile.
3. Compare each tile with its goal position.
4. Count incorrectly positioned tiles.

### Cell 7: Manhattan Distance Heuristic

```python
def manhattan_distance(state):

    distance = 0

    for i, tile in enumerate(state):

        if tile != 0:

            current_row, current_col = divmod(i, 3)

            goal_index = tile - 1

            goal_row, goal_col = divmod(goal_index, 3)

            distance += abs(current_row - goal_row)

            distance += abs(current_col - goal_col)

    return distance
```

**Explanation:**

1. Find the current position of each tile.
2. Find its correct goal position.
3. Calculate horizontal and vertical distances.
4. Add both distances.
5. Ignore the blank tile.

### Cell 8: Sanity Check

```python
test_state = (1, 2, 3, 4, 5, 6, 7, 0, 8)

assert misplaced_tiles(test_state) <= 1

assert manhattan_distance(test_state) <= 1

print("Cell 8 checkpoint passed.")
```

### Discussion: Why must h(n) = 0 at the goal state?

When the current state is the goal state, no additional moves are required.

Therefore, the remaining cost is zero.

Both heuristics must return zero at the goal state. Otherwise, the heuristic would incorrectly estimate the remaining distance and could affect the optimality guarantee of A*.

---

## 4. A* Search Implementation

A* is an informed search algorithm that selects the node with the lowest f(n) value.

Formula:

**f(n) = g(n) + h(n)**

### Cell 10: A* Search Algorithm

```python
def a_star_search(start_state, heuristic_fn):

    counter = itertools.count()

    frontier = []

    heapq.heappush(
        frontier,
        (
            heuristic_fn(start_state),
            next(counter),
            0,
            start_state,
            []
        )
    )

    best_g = {start_state: 0}

    nodes_expanded = 0

    while frontier:

        f, _, g, state, path = heapq.heappop(frontier)

        if g > best_g[state]:
            continue

        nodes_expanded += 1

        if is_goal(state):

            return {
                "path": path,
                "cost": g,
                "nodes_expanded": nodes_expanded
            }

        for neighbor_state, action in get_neighbors(state):

            new_g = g + 1

            if new_g < best_g.get(neighbor_state, float('inf')):

                best_g[neighbor_state] = new_g

                new_f = new_g + heuristic_fn(neighbor_state)

                heapq.heappush(
                    frontier,
                    (
                        new_f,
                        next(counter),
                        new_g,
                        neighbor_state,
                        path + [action]
                    )
                )

    return {
        "path": None,
        "cost": None,
        "nodes_expanded": nodes_expanded
    }
```

### Cell 11: Quick Smoke Test

```python
easy_state = (1, 2, 3, 4, 5, 6, 7, 0, 8)

result = a_star_search(easy_state, manhattan_distance)

print(result)

assert result["cost"] == 1

print("Cell 11 checkpoint passed.")
```

Expected result:

- Solution cost: 1
- Path: Right
- Nodes expanded: 2

---

## 5. Testing

In this section, we test A* using different puzzle states.

We compare Misplaced Tiles and Manhattan Distance by measuring the number of nodes expanded.

### Cell 13: Solvability Check

```python
def is_solvable(state):

    tiles = [t for t in state if t != 0]

    inversions = 0

    for i in range(len(tiles)):

        for j in range(i + 1, len(tiles)):

            if tiles[i] > tiles[j]:
                inversions += 1

    return inversions % 2 == 0
```

**Explanation:**

An inversion occurs when a larger tile appears before a smaller tile.

For a 3 × 3 puzzle:

- Even inversions = Solvable.
- Odd inversions = Unsolvable.

### Cell 14: Scramble Generator

```python
def generate_scramble(num_moves, seed=None):

    if seed is not None:
        random.seed(seed)

    state = GOAL_STATE

    for _ in range(num_moves):

        neighbors = get_neighbors(state)

        state, _ = random.choice(neighbors)

    return state
```

**Explanation:**

1. Start from the goal state.
2. Generate neighboring states.
3. Randomly select one neighbor.
4. Repeat for the given number of moves.
5. Return the scrambled puzzle.

### Cell 15: Run Comparison

```python
def run_comparison(num_moves_list, trials_per_level=5):

    results = []

    for num_moves in num_moves_list:

        for trial in range(trials_per_level):

            start = generate_scramble(
                num_moves,
                seed=trial * 1000 + num_moves
            )

            result_misplaced = a_star_search(
                start, misplaced_tiles
            )

            result_manhattan = a_star_search(
                start, manhattan_distance
            )

            results.append({

                "scramble_depth": num_moves,

                "trial": trial,

                "misplaced_nodes":
                    result_misplaced["nodes_expanded"],

                "manhattan_nodes":
                    result_manhattan["nodes_expanded"],

                "misplaced_cost": result_misplaced["cost"],

                "manhattan_cost": result_manhattan["cost"]

            })

    return results


# Run the experiment

results = run_comparison(
    num_moves_list=[5, 10, 15, 20],
    trials_per_level=5
)

for r in results:
    print(r)
```

**Explanation:**

- Four scramble depths are tested.
- Five trials are performed at each depth.
- Both heuristics solve the same puzzle.
- Their expanded nodes and solution costs are recorded.

### Checkpoint

Both heuristics should produce identical optimal solution costs for every trial.

Their node expansion counts may differ.

---

## 6. Results Table and Chart

### Cell 16: Generate Table and Chart

```python
df = pd.DataFrame(results)

# Display experimental results

print("Complete Results:")
print(df)

# Calculate average expanded nodes

summary = df.groupby("scramble_depth")[
    ["misplaced_nodes", "manhattan_nodes"]
].mean()

print("\nAverage Nodes Expanded:")
print(summary)

# Plot comparison

summary.plot(marker="o")

plt.xlabel("Scramble Depth (moves from goal)")

plt.ylabel("Average Nodes Expanded")

plt.title(
    "A* Node Expansion: Misplaced Tiles vs. Manhattan Distance"
)

plt.legend([
    "Misplaced Tiles",
    "Manhattan Distance"
])

plt.grid(True)

plt.show()
```

**Explanation:**

- Pandas creates the results table.
- Groupby groups results according to scramble depth.
- Mean calculates the average expanded nodes.
- Matplotlib generates a comparison graph.

---

## 7. Analysis

### Q1. At what scramble depth does the gap between the two heuristics become clearly visible? Why does the gap widen as depth increases?

**Answer:**

The difference generally becomes more visible at higher scramble depths.

Manhattan Distance provides a more accurate estimate of the remaining cost than Misplaced Tiles.

As puzzle difficulty increases, Misplaced Tiles may expand many more unnecessary nodes.

Therefore, the gap between the two heuristics generally increases as the puzzle becomes more difficult.

### Q2. Manhattan Distance is provably greater than or equal to Misplaced Tiles for every state. Explain why a higher admissible heuristic does not violate admissibility and why it leads to fewer node expansions.

**Answer:**

Admissibility means that a heuristic never overestimates the actual minimum cost of reaching the goal.

Manhattan Distance is greater than or equal to Misplaced Tiles because it considers the horizontal and vertical distances of misplaced tiles.

However, it still never overestimates the actual cost.

A stronger heuristic provides a more accurate estimate of the remaining distance.

Therefore, A* can make better decisions and generally expands fewer nodes.

### Q3. Does Manhattan Distance hit the same performance limit as Misplaced Tiles at the same depth?

**Answer:**

No. Manhattan Distance generally performs better because it provides a more accurate estimate of the remaining solution cost.

It expands fewer nodes and can solve many difficult puzzles more efficiently.

However, both heuristics can become computationally expensive for sufficiently difficult puzzles.

---

## 8. Final Conclusion

A* is an informed search algorithm that finds an optimal solution using the evaluation function:

**f(n) = g(n) + h(n)**

In this lab, we implemented two admissible heuristics:

1. Misplaced Tiles
2. Manhattan Distance

The experimental comparison demonstrates that Manhattan Distance generally expands fewer nodes because it provides a stronger heuristic estimate.

Therefore, Manhattan Distance is generally more efficient than Misplaced Tiles for solving the 8-Puzzle problem.

---

## 9. Submission Checklist

- [x] Problem representation implemented.
- [x] State helper functions implemented.
- [x] Misplaced Tiles heuristic implemented.
- [x] Manhattan Distance heuristic implemented.
- [x] A* search algorithm implemented.
- [x] Solvability check implemented.
- [x] Scramble generator implemented.
- [x] Heuristic comparison implemented.
- [x] Results table and chart implemented.
- [x] Analysis questions answered.

**Final Instruction:** Run all Python code in Google Colab to generate the actual experimental results and chart before submitting.
