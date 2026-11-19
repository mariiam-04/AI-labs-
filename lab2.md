#EXERCISE1 :LAB 2
directions = [
    (-1, 0),  # up
    (1, 0),   # down
    (0, -1),  # left
    (0, 1)    # right
]
``>

## Sample Results

| Start | Goal | BFS Length | BFS Max Frontier | DFS Length | DFS Max Frontier |
|---|---|---:|---:|---:|---:|
| `(0, 0)` | `(2, 2)` | 4 | 5 | 4 | 5 |
| `(0, 0)` | `(4, 4)` | 8 | 8 | 12 | 10 |
| `(0, 4)` | `(4, 0)` | 8 | 9 | 8 | 7 |
| `(2, 0)` | `(4, 2)` | 4 | 4 | 4 | 4 |

---

## 6. Does the path length change when the search is repeated?

With the same:

- Maze
- Start
- Goal
- Neighbor order

**BFS path length remains the same.**

**DFS path length usually remains the same**, but DFS does not guarantee that it is the shortest path.

If the direction order changes, DFS may return a different path.

---

## 7. What happens when the distance grows?

As the distance between the start and goal increases:

- BFS normally explores more states.
- BFS's maximum frontier generally grows.
- DFS may explore a deep branch and keep fewer states in its frontier.
- DFS frontier growth is less predictable.

A larger search distance does not always guarantee a larger frontier.

---

## 8. Can BFS and DFS return the same path?

**Yes.** For example:

```text
Start: (0, 0)
Goal: (2, 2)
