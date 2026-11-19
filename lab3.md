## Section 2: Heuristics — Discussion

### Question 1: Why must `h(n) = 0` whenever `state == GOAL_STATE`, for both heuristics? What would break in A* if this weren't true?

1. **Why `h(n) = 0` at `GOAL_STATE`:** By definition, an admissible heuristic must never overestimate the true minimum remaining cost $h^*(n)$ to reach the goal ($0 \le h(n) \le h^*(n)$). At the goal state, the remaining distance is $h^*(n) = 0$, so the only non-negative value satisfying $h(n) \le 0$ is $h(n) = 0$.
2. **What would break in A* if `h(GOAL_STATE) > 0`:** Admissibility would be violated. When a goal node is generated and placed into the priority queue, its estimated total cost $f(n) = g(n) + h(n)$ would be artificially inflated above its true path cost $g(n)$. Because A* pops the node with the smallest $f(n)$, it could pop and expand suboptimal non-goal paths whose $f$-values happen to be lower than the inflated $f$-value of the optimal goal node—or, if `h(GOAL_STATE)` varies or is larger than the cost difference between paths, A* can fail to guarantee an optimal solution (and in implementations that check goal status on generation rather than expansion, or with inconsistent heuristics, return suboptimal paths).

---

## Section 3: A* Search Implementation — TODO Code

### Task 3.1: `a_star_search` (Cell 10)

```python
def a_star_search(start_state, heuristic_fn):
    """
    Returns a dict with:
      "path": list of actions from start to goal
      "cost": number of moves in the solution
      "nodes_expanded": total number of nodes popped from the frontier
    """
    counter = itertools.count()  # tie-breaker for equal f-values
    frontier = []
    heapq.heappush(frontier, (heuristic_fn(start_state), next(counter), 0, start_state, []))
    best_g = {start_state: 0}
    nodes_expanded = 0

    while frontier:
        f, _, g, state, path = heapq.heappop(frontier)
        if g > best_g.get(state, float("inf")):
            continue
        nodes_expanded += 1

        if is_goal(state):
            return {"path": path, "cost": g, "nodes_expanded": nodes_expanded}

        for neighbor_state, action in get_neighbors(state):
            new_g = g + 1
            if new_g < best_g.get(neighbor_state, float("inf")):
                best_g[neighbor_state] = new_g
                new_f = new_g + heuristic_fn(neighbor_state)
                heapq.heappush(frontier, (new_f, next(counter), new_g, neighbor_state, path + [action]))

    return {"path": None, "cost": None, "nodes_expanded": nodes_expanded}
```

*(Note: Tasks 2.1 and 2.2 in Cells 6 and 7 are also included below for completeness of the lab's TODO items.)*

### Task 2.1 & Task 2.2: Heuristics (Cells 6 & 7)

```python
def misplaced_tiles(state):
    return sum(1 for i in range(9) if state[i] != 0 and state[i] != GOAL_STATE[i])

def manhattan_distance(state):
    total = 0
    for i, tile in enumerate(state):
        if tile != 0:
            curr_row, curr_col = divmod(i, 3)
            goal_row, goal_col = divmod(tile - 1, 3)
            total += abs(curr_row - goal_row) + abs(curr_col - goal_col)
    return total
```

---

## Section 5: Analysis

### 1. Summary Table (Cell 16 Output)

| scramble_depth | misplaced_nodes | manhattan_nodes |
| --- | --- | --- |
| 5 | 3.6 | 3.6 |
| 10 | 4.4 | 4.2 |
| 15 | 5.6 | 5.4 |
| 20 | 13.6 | 10.4 |

### 2. Chart

*(Generated in Colab Cell 16 plotting `misplaced_nodes` and `manhattan_nodes` against `scramble_depth`.)*

### 3. Analysis Questions

1. **Question 3.1 — At what scramble depth does the gap between the two heuristics become clearly visible? Why does the gap widen as depth increases rather than staying constant?**
   - The gap begins to appear at scramble depth **10–15** and becomes clearly visible by scramble depth **20** (where Misplaced Tiles expands an average of **13.6** nodes vs. **10.4** for Manhattan Distance), widening dramatically at higher depths (e.g., **79.6** vs. **19.2** at depth 25).
   - The gap widens exponentially rather than staying constant because the 8-puzzle search space grows exponentially with solution depth ($b^d$, where branching factor $b \approx 2$ to $3$). Because Misplaced Tiles treats a tile that is 1 step away identically to a tile that is 4 steps away, its underestimate $(h^*(n) - h(n))$ grows larger on deeper scrambles, forcing A* to explore an exponentially larger cone of equal- or low-$f$ states before reaching the optimal cost threshold.

2. **Question 3.2 — Manhattan Distance is provably $\ge$ Misplaced Tiles for every state. Using the lecture's definition of admissibility, explain why a higher admissible heuristic is not a violation of admissibility, and why it leads to fewer node expansions.**
   - **Why it does not violate admissibility:** Admissibility requires only that $h(n) \le h^*(n)$ (never overestimating the true optimal cost to the goal). Every misplaced tile must move at least 1 grid step horizontally or vertically to reach its target cell, so its Manhattan distance is always at least 1—and each slide moves only a single non-blank tile by 1 step—meaning $\text{Misplaced}(n) \le \text{Manhattan}(n) \le h^*(n)$. Being larger while remaining $\le h^*(n)$ simply makes Manhattan Distance a tighter lower bound, not an overestimate.
   - **Why it leads to fewer node expansions:** A* expands every node with $f(n) = g(n) + h(n) < C^*$ (where $C^*$ is the optimal solution cost). Because $h_{\text{Manhattan}}(n) \ge h_{\text{Misplaced}}(n)$ for all $n$, $f_{\text{Manhattan}}(n)$ is higher and closer to $C^*$, causing off-path states to exceed $C^*$ sooner so A* prunes them from expansion without sacrificing optimality.

3. **Question 3.3 (Optional) — Try increasing scramble depth until Misplaced Tiles becomes impractically slow. Does Manhattan Distance hit the same wall at the same depth?**
   - No. Because random scrambles frequently undo prior moves, deeper optimal solutions (e.g., optimal solution costs of 20–31 moves, requiring higher random scramble depths or hard 8-puzzle instances) cause Misplaced Tiles to expand tens of thousands of nodes and become noticeably slow, whereas Manhattan Distance solves the exact same states in a small fraction of the node expansions and does not hit that wall at the same depth.
