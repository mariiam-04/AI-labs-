## Section 4: Task 1

### Task 1: Create the state and print it

```python
state = [1, 3, 0, 2]
print("State:", state)
```

**Output:**
```text
State: [1, 3, 0, 2]
```

---

## Section 11: Interpret the Graph

### Question 1: Does the evaluation score increase as the algorithm moves to better states?
Yes. Because each accepted move transitions to a neighbor with strictly fewer attacking pairs of queens and `evaluation(state) = -count_conflicts(state)`, the evaluation score strictly increases at every step taken.

### Question 2: Does the score decrease during a move accepted by this implementation?
No. The implementation enforces `if best_score <= current_score: break`, so it only accepts moves where `best_score > current_score` and never accepts a downhill or sideways move.

### Question 3: What happens when the algorithm reaches a state with no better neighbor?
When `best_score <= current_score` (no neighbor has a strictly higher evaluation score), the `while True` loop hits `break` and terminates, returning the current state and the list of recorded `scores`.

### Question 4: What does an evaluation score of zero mean?
An evaluation score of `0` means `count_conflicts(state) == 0`—no two queens share the same row, column, or diagonal, so a valid goal solution to the N-Queens problem has been found.

### Question 5: Can the algorithm stop at a score below zero? Explain why.
Yes. Because basic Hill-Climbing is greedy and only examines immediate single-queen-move neighbors, it can reach a **local maximum** (or plateau) where every neighboring state has the same or more conflicts (`best_score <= current_score`) even though conflicts still remain (`current_score < 0`). At that point it terminates prematurely without reaching the global maximum (`0`).

---

## Section 12: Task 2 — Experimenting with Different Board Sizes

### Task 2 Table: Recorded Observations

| N | Run | Final Conflicts | Iterations | Solved? |
| --- | --- | --- | --- | --- |
| 4 | 1 | 1 | 2 | False |
| 4 | 2 | 0 | 3 | True |
| 8 | 1 | 0 | 6 | True |
| 8 | 2 | 2 | 3 | False |
| 8 | 3 | 1 | 3 | False |
| 10 | 1 | 1 | 3 | False |
| 10 | 2 | 1 | 7 | False |
| 10 | 3 | 2 | 4 | False |

### Task 2 Discussion: Does the same board size always produce the same result?
No, the same board size does not always produce the same result. Because `random_state(n)` generates a different random starting configuration on each run, some initial states lie in basins of attraction that lead smoothly to a conflict-free global maximum (`0`), while other initial states lead into non-goal local maxima where no single-queen move reduces conflicts—resulting in different final conflict counts, iteration lengths, and success outcomes across runs.

---

## Section 16: Task 3 — Comparing Basic and Random-Restart Hill-Climbing

### Experimental Runs (N = 8, `max_restarts = 100`)

**Basic Hill-Climbing (5 Runs):**
- Run 1: Conflicts = 1, Iterations = 6, Solved = False
- Run 2: Conflicts = 2, Iterations = 4, Solved = False
- Run 3: Conflicts = 2, Iterations = 3, Solved = False
- Run 4: Conflicts = 0, Iterations = 4, Solved = True
- Run 5: Conflicts = 2, Iterations = 4, Solved = False

**Random-Restart Hill-Climbing (5 Runs):**
- Run 1: Conflicts = 0, Restarts = 6, Solved = True
- Run 2: Conflicts = 0, Restarts = 5, Solved = True
- Run 3: Conflicts = 0, Restarts = 2, Solved = True
- Run 4: Conflicts = 0, Restarts = 8, Solved = True
- Run 5: Conflicts = 0, Restarts = 3, Solved = True

### Question 1: Did basic Hill-Climbing find a solution in every run?
No. Basic Hill-Climbing only solved **1 out of 5** runs (typically succeeding around ~14% of the time for $N = 8$), getting stuck in local maxima with 1 or 2 remaining conflicts on the other 4 runs.

### Question 2: Did Random-Restart Hill-Climbing improve the results?
Yes, substantially. Random-Restart Hill-Climbing solved **5 out of 5** runs with `0` conflicts by escaping local maxima through fresh random initial states.

### Question 3: How many restarts were needed to find a solution?
Across the 5 runs, it required between **2 and 8 restarts** (specifically 6, 5, 2, 8, and 3 restarts, averaging **4.8 restarts**).

### Question 4: Did any random-restart experiment reach its restart limit without finding a solution?
No. Every run found a valid solution well before reaching `max_restarts = 100` (since the failure probability over 100 independent runs for $N = 8$ is roughly $(1 - 0.14)^{100} \approx 2.6 \times 10^{-7}$).

### Question 5: Why can Random-Restart improve the probability of success without guaranteeing success within a fixed number of attempts?
If each independent run has a nonzero success probability $p > 0$, then across $k$ independent restarts the probability of finding at least one solution increases exponentially toward 1 according to $P(\text{success}) = 1 - (1 - p)^k$. However, for any finite fixed limit $k$, the failure probability $(1 - p)^k$ remains strictly greater than zero, so there is always a small nonzero chance that all $k$ random initial states land in local maxima.

---

## Section 17: Lab Questions

### Question 1: What is the purpose of the evaluation function in Hill-Climbing?
The evaluation function assigns a numerical quality score to every candidate state so the algorithm can compare the current state against its neighbors, determine which neighbor improves the configuration most, and decide when to stop climbing.

### Question 2: Why is an evaluation score of zero the best result in our N-Queens implementation?
Because `evaluation(state)` is defined as `-count_conflicts(state)` and `count_conflicts(state)` counts pairs of attacking queens (which cannot be negative), `0` is the highest possible score and represents a board with zero attacking pairs—a completely valid N-Queens solution.

### Question 3: What is the difference between a local maximum and a global maximum?
A **local maximum** is a state whose evaluation score is greater than or equal to all of its immediate neighbors (so no single move improves it), even though better states exist elsewhere in the state space. A **global maximum** is the highest possible evaluation score across the entire state space (`0` conflicts in N-Queens).

### Question 4: Why can basic Hill-Climbing stop without finding a solution even when a solution exists?
Basic Hill-Climbing only inspects immediate neighbors (states differing by moving one queen within its column) and strictly requires an improving move (`best_score > current_score`). If it enters a local maximum where every single-queen move leaves conflicts unchanged or increases them, it terminates even though a zero-conflict global maximum exists multiple moves away.

### Question 5: Does reaching a local maximum always mean that the algorithm has failed? Explain.
No. Every global maximum is also a local maximum (since no neighbor can improve upon a score of `0`). If the local maximum where the algorithm stops has an evaluation score of `0` (zero conflicts), it is also the global maximum and represents a successful solution; it only fails when it stops at a *non-goal* local maximum with a negative score.

### Question 6: For N = 8, how many neighbors does each state have in our implementation?
Each state has $N \times (N - 1) = 8 \times 7 = \mathbf{56}$ neighbors (8 columns, with 7 alternative rows per column).

### Question 7: What does the evaluation-score graph tell you about the behavior of Hill-Climbing?
The evaluation-score graph shows that Hill-Climbing is monotonically **non-decreasing** (each step strictly climbs upward toward `0` as conflicts are resolved) and typically converges rapidly in just a few iterations before flattening out and stopping—either at `0` (solved) or at a negative plateau/local maximum (stuck).

### Question 8: Explain probabilistic completeness in your own words.
Probabilistic completeness means that while an algorithm is not guaranteed to find a solution within a fixed deadline, the likelihood that it finds an existing solution gets arbitrarily close to 100% as you allow it to run longer or take more independent random attempts.

### Question 9: Why does Random-Restart Hill-Climbing improve the chance of finding a solution?
Instead of terminating permanently after a single run gets trapped in a local maximum, Random-Restart Hill-Climbing repeatedly samples new random starting states across different regions of the search space, giving each attempt an independent chance to land in a basin of attraction that leads to a global maximum.

### Question 10: Does probabilistic completeness guarantee that a solution will be found within 100 restarts? Why or why not?
No. Probabilistic completeness only guarantees that $\lim_{k \to \infty} [1 - (1 - p)^k] = 1$ as restarts $k$ approach infinity. For a finite cap of $k = 100$ restarts, $(1 - p)^{100} > 0$, meaning an unlucky sequence of 100 consecutive bad initial states is still mathematically possible.

---

## Section 18: Extension Task (N = 20)

### Modified Code for N = 20

```python
# Basic Hill-Climbing for N = 20
solution, scores = hill_climbing(20)
print("Basic N=20 | Final conflicts:", count_conflicts(solution),
      "| Iterations:", len(scores),
      "| Solved:", count_conflicts(solution) == 0)

# Random-Restart Hill-Climbing for N = 20 (increased restart budget)
solution_rr, restarts, best_scores = random_restart_hill_climbing(20, max_restarts=500)
print("Random-Restart N=20 | Final conflicts:", count_conflicts(solution_rr),
      "| Restarts:", restarts,
      "| Solved:", count_conflicts(solution_rr) == 0)
```

### Recorded Experimental Results (N = 20)

| Algorithm | Run | Final Conflicts | Iterations / Restarts | Solved? |
| --- | --- | --- | --- | --- |
| Basic Hill-Climbing | 1 | 1 | 9 iterations | False |
| Basic Hill-Climbing | 2 | 3 | 11 iterations | False |
| Basic Hill-Climbing | 3 | 3 | 7 iterations | False |
| Random-Restart (`max_restarts=500`) | 1 | 0 | 146 restarts | True |
| Random-Restart (`max_restarts=500`) | 2 | 0 | 162 restarts | True |
| Random-Restart (`max_restarts=500`) | 3 | 0 | 8 restarts | True |

### Comparison and Discussion
- **Comparison of Basic vs. Random-Restart for $N = 20$:** Basic Hill-Climbing failed in all 3 runs, getting trapped in local maxima with 1 to 3 remaining conflicts after 7–11 iterations. Random-Restart Hill-Climbing succeeded in all 3 runs (`0` conflicts), though it required significantly more restarts (up to 162 restarts) than $N = 8$, meaning `max_restarts=100` often hits its restart limit for $N = 20$ whereas `max_restarts=500` reliably finds a solution.
- **Effect of Number of Queens ($N$) and Random Initial State:** Increasing $N$ from 8 to 20 expands the state space from $8^8 \approx 1.68 \times 10^7$ to $20^{20} \approx 1.05 \times 10^{26}$ states and increases the branching neighborhood from $56$ to $20 \times 19 = 380$ neighbors per step. While each individual hill climb takes slightly more iterations (around 7–15 steps) to descend from higher initial conflict counts, the proportion of local maxima relative to global maxima increases sharply—lowering the single-run success probability $p$ (to roughly ~1–2% for $N = 20$) and making the outcome heavily dependent on whether the random initial state starts near a global-optimum basin.
