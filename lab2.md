## Section 7 — Compare BFS and DFS

| Algorithm | Path Length | Maximum Frontier |
| --- | --- | --- |
| BFS | 8 | 3 |
| DFS | 8 | 3 |

1. Neither algorithm found a shorter path; both found paths of equal length (**8**), though they followed different routes through the maze.
2. Neither algorithm had a larger maximum frontier; both reached a maximum frontier size of **3**.
3. BFS uses a FIFO queue (`popleft()`), exploring all neighbors at distance 1 (`(1, 0)` and `(0, 1)`) before expanding deeper, finding the inner route first because `(1, 0)` was queued before `(0, 1)`. DFS uses a LIFO stack (`pop()`), immediately committing to the most recently pushed valid neighbor (`(0, 1)`) and following the outer top-right corridor all the way to the goal before backtracking.
4. No, DFS does not always produce the shortest path. Because it commits deeply to a single branch until hitting a dead end or the goal, it can take a long, winding route to a goal even when a much shorter path exists on an unexplored branch.
5. Yes, BFS always produces the shortest path when every movement costs 1. Because its FIFO queue explores states strictly level-by-level (in non-decreasing order of steps from the start), the first time it reaches the goal is guaranteed to be via the minimum possible number of moves.

---

## Section 8 — Experiment 1 — Change the Neighbor Order

1. Yes, the DFS path changed from the outer top-right route (`(0, 0) → (0, 1) → ... → (4, 4)`) to the inner middle route (`(0, 0) → (1, 0) → (2, 0) → (2, 1) → (2, 2) → (3, 2) → (3, 3) → (3, 4) → (4, 4)`), because `(1, 0)` (down) was pushed onto the stack after `(0, 1)` (right) and therefore popped first. BFS's path also changed to the outer top-right route because `(0, 1)` was enqueued before `(1, 0)`, though BFS's path **length** (`8`) remained unchanged—unlike DFS, neighbor ordering in BFS only breaks ties among equally short paths rather than altering optimality.

---

## Section 9 — Experiment 2 — Change `start` and `goal`

| Start | Goal | BFS Length | BFS Max Frontier | DFS Length | DFS Max Frontier |
| --- | --- | --- | --- | --- | --- |
| `(0, 0)` | `(2, 2)` | 4 | 3 | 10 | 3 |
| `(0, 0)` | `(4, 0)` | 4 | 3 | 14 | 3 |
| `(2, 0)` | `(4, 4)` | 6 | 3 | 6 | 4 |
| `(0, 2)` | `(3, 4)` | 5 | 3 | 5 | 2 |

1. Neither BFS's nor DFS's path length changes when re-run with the exact same `start`, `goal`, and `directions`, because both implementations are completely deterministic (neighbor order and tie-breaking are fixed).
2. As the distance between `start` and `goal` grows, BFS's maximum frontier size grows or stays at its peak width as it expands wider concentric layers across branching corridors, whereas DFS's maximum frontier size stays relatively small and proportional to the branching along a single deep path rather than the total distance.
3. Yes, `(2, 0)` to `(4, 4)` (and `(0, 2)` to `(3, 4)`) returns the exact same path for both BFS and DFS. This indicates that the viable route between those two points acts like a single non-branching corridor (or that DFS's neighbor priority immediately enters the unique shortest branch without detours).
4. If `goal` is unreachable from `start`, both `bfs()` and `dfs()` exhaust all reachable open cells, empty the frontier, and return `(None, max_frontier)` (for example, `(None, 3)` for BFS and `(None, 2)` for DFS when `(3, 4)` is walled off).
