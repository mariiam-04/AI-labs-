
# Lab 4: Hill-Climbing Search with the N-Queens Problem

**Course:** BSCS – Artificial Intelligence  
**Programming Language:** Python 3  
**Platform:** Google Colab

---

# 1. Lab Objectives

The objectives of this lab are:

1. Represent a problem as a state.
2. Define an objective or evaluation function.
3. Generate neighboring states.
4. Implement basic Hill-Climbing.
5. Record and plot evaluation scores over iterations.
6. Understand why Hill-Climbing may terminate without finding a solution.
7. Implement Random-Restart Hill-Climbing.
8. Compare single search with repeated randomized searches.

# 2. Background: Local Search and Hill-Climbing

Traditional search algorithms such as BFS, DFS, and A* explore a search space to find a path from an initial state to a goal state.

Local search focuses on improving the current state rather than finding a complete path.

Hill-Climbing is a greedy local search algorithm that repeatedly examines neighboring states and moves toward a better state.

### Basic Process

1. Start with an initial state.
2. Evaluate the current state.
3. Generate neighboring states.
4. Select the best neighbor.
5. Move to that neighbor if it improves the evaluation score.
6. Stop when no better neighbor is available.

### Does Hill-Climbing Always Get Stuck?

No. There are two possible outcomes:

**Successful termination:**

The algorithm reaches a goal state. In N-Queens, no two queens attack each other.

**Premature termination:**

The algorithm reaches a state that is better than all its immediate neighbors but is not a goal state.

This is called the local maximum problem.

A local maximum is not necessarily a failure. If it is also the global maximum and represents a goal, the algorithm has succeeded.

However, basic Hill-Climbing cannot guarantee a global optimum from every starting state.

# 3. The N-Queens Problem

The N-Queens problem asks us to place N queens on an N × N chessboard so that no two queens attack each other.

Queens attack each other when they occupy:

- The same row.
- The same column.
- The same diagonal.

### Example: Four Queens Solution

```text
. Q . .
. . . Q
Q . . .
. . Q .
```

No two queens share a row, column, or diagonal.

# 4. Representing a State

We represent the board using a Python list.

Example:

```python
state = [1, 3, 0, 2]
```

The index represents the column, and the value represents the row of the queen.

```text
Column:  0  1  2  3
Row:     1  3  0  2
```

Corresponding board:

```text
. . Q .
Q . . .
. . . Q
. Q . .
```

This representation automatically guarantees that each column contains exactly one queen.

### Task 1: Create and Print the State

```python
state = [1, 3, 0, 2]

print("State:", state)
```

**Expected Output:**

```text
State: [1, 3, 0, 2]
```

# 5. Generating a Random Initial State

Hill-Climbing starts from an initial state. We generate a random row position for each queen.

### Code

```python
import random

def random_state(n):
    return [random.randrange(n) for _ in range(n)]

state = random_state(8)

print("Random initial state:", state)
```

**Example Output:**

```text
Random initial state: [2, 5, 1, 7, 0, 3, 6, 4]
```

The output may differ each time because the state is randomly generated.

A random state is not necessarily a solution because some queens may attack each other.

# 6. Defining the Evaluation Function

The evaluation function measures the quality of a state.

In this problem, we count the number of pairs of queens attacking each other.

Our objective is to minimize the number of conflicts.

A valid solution has zero conflicts.

To express the problem as a maximization problem, we define:

**Evaluation Score = - Number of Conflicts**

### Examples

| Number of Conflicts | Evaluation Score |
|---:|---:|
| 5 | -5 |
| 3 | -3 |
| 1 | -1 |
| 0 | 0 |

A higher score is better. Zero is the best possible score.

### Code: Count Conflicts

```python
def count_conflicts(state):

    conflicts = 0
    n = len(state)

    for i in range(n):

        for j in range(i + 1, n):

            # Same row
            if state[i] == state[j]:
                conflicts += 1

            # Same diagonal
            elif abs(state[i] - state[j]) == abs(i - j):
                conflicts += 1

    return conflicts
```

### Test the Function

```python
state = [1, 3, 0, 2]

print("Conflicts:", count_conflicts(state))
```

**Expected Output:**

```text
Conflicts: 0
```

### Evaluation Function

```python
def evaluation(state):
    return -count_conflicts(state)

state = [1, 3, 0, 2]

print("Evaluation score:", evaluation(state))
```

**Expected Output:**

```text
Evaluation score: 0
```

# 7. Generating Neighboring States

A neighbor is a state that can be reached by making a small change to the current state.

In our implementation, we move one queen to another row in the same column.

Example:

Current state:

```python
state = [1, 3, 0, 2]
```

Moving the queen in column 0 to row 0:

```python
[0, 3, 0, 2]
```

Moving the same queen to row 2:

```python
[2, 3, 0, 2]
```

### Code: Generate Neighbors

```python
def get_neighbors(state):

    neighbors = []
    n = len(state)

    for col in range(n):

        for row in range(n):

            if row != state[col]:

                neighbor = state.copy()
                neighbor[col] = row

                neighbors.append(neighbor)

    return neighbors
```

### Test the Function

```python
state = [1, 3, 0, 2]

neighbors = get_neighbors(state)

print("Number of neighbors:", len(neighbors))
print("First five neighbors:")

for neighbor in neighbors[:5]:
    print(neighbor)
```

**Expected Output:**

```text
Number of neighbors: 12
First five neighbors:
[0, 3, 0, 2]
[2, 3, 0, 2]
[3, 3, 0, 2]
[1, 0, 0, 2]
[1, 1, 0, 2]
```

For a board of size N:

**Number of Neighbors = N × (N - 1)**

For N = 8:

8 × 7 = 56 neighbors.

# 8. Implementing Basic Hill-Climbing

We now have three main components:

1. State representation.
2. Evaluation function.
3. Neighbor-generation function.

### Code: Hill-Climbing Algorithm

```python
def hill_climbing(n):

    current = random_state(n)
    scores = []

    while True:

        # Evaluate current state
        current_score = evaluation(current)
        scores.append(current_score)

        # Generate neighboring states
        neighbors = get_neighbors(current)

        # Find the best neighbor
        best_neighbor = max(neighbors, key=evaluation)

        best_score = evaluation(best_neighbor)

        # Stop if no neighbor improves the score
        if best_score <= current_score:
            break

        # Move to the better neighbor
        current = best_neighbor

    return current, scores
```

**Explanation:**

1. Generate a random initial state.
2. Calculate its evaluation score.
3. Generate all neighboring states.
4. Find the neighbor with the highest score.
5. Compare its score with the current score.
6. Move if the score improves.
7. Stop if no better neighbor exists.

# 9. Running Hill-Climbing

Run the algorithm for eight queens.

### Code

```python
solution, scores = hill_climbing(8)

print("Final state:", solution)
print("Final conflicts:", count_conflicts(solution))
print("Final evaluation score:", evaluation(solution))
print("Number of iterations:", len(scores))

if count_conflicts(solution) == 0:
    print("A valid solution was found.")
else:
    print("Hill-Climbing stopped without finding a solution.")
```

**Explanation:**

The algorithm generates an initial random state and attempts to improve it.

Possible outcomes:

- Zero conflicts: A valid solution is found.
- Remaining conflicts: The algorithm stops at a non-goal local maximum.

The results may differ between runs.

### Why Can the Algorithm Stop Without Solving the Problem?

Suppose the current evaluation score is -2 and neighboring states have these scores:

```text
-3, -4, -2, -5
```

None of the neighbors is better than the current state.

Therefore, Hill-Climbing stops.

However, a state with a score of zero may exist elsewhere in the search space.

The algorithm has reached a local maximum but not the global maximum.

# 10. Visualizing the Chessboard

### Code: Print Board

```python
def print_board(state):

    n = len(state)

    for row in range(n):

        line = ""

        for col in range(n):

            if state[col] == row:
                line += "Q "
            else:
                line += ". "

        print(line)
```

### Display the Result

```python
print_board(solution)
```

**Explanation:**

- Q represents a queen.
- . represents an empty square.
- Each column contains exactly one queen.

If the solution has zero conflicts, no two queens attack each other.

# 11. Plotting the Evaluation Score

### Code

```python
import matplotlib.pyplot as plt

plt.figure(figsize=(9, 5))

plt.plot(
    range(len(scores)),
    scores,
    marker="o"
)

plt.xlabel("Iteration")
plt.ylabel("Evaluation Score")
plt.title("Hill-Climbing Optimization: N-Queens")

plt.grid(True)
plt.show()
```

### Graph Interpretation: Answers

**Q1. Does the evaluation score increase as the algorithm moves to better states?**

Answer:

Yes. The evaluation score increases when the algorithm moves to a better neighboring state because it accepts only improving moves.

**Q2. Does the score decrease during a move accepted by this implementation?**

Answer:

No. The algorithm only accepts a neighbor whose evaluation score is higher than the current score.

**Q3. What happens when the algorithm reaches a state with no better neighbor?**

Answer:

The algorithm terminates because no neighboring state can improve the current evaluation score.

**Q4. What does an evaluation score of zero mean?**

Answer:

It means there are zero conflicts between queens. Therefore, a valid N-Queens solution has been found.

**Q5. Can the algorithm stop at a score below zero? Explain why.**

Answer:

Yes. The algorithm may stop at a local maximum where all neighboring states have equal or lower evaluation scores, even though some queens are still attacking each other.

# 12. Experimenting with Different Board Sizes

### Code

```python
for n in [4, 8, 10, 12]:

    solution, scores = hill_climbing(n)

    print(
        f"N = {n:2d} | "
        f"Conflicts = {count_conflicts(solution):2d} | "
        f"Iterations = {len(scores):3d} | "
        f"Solved = {count_conflicts(solution) == 0}"
    )
```

### Task 2: Experimental Results

Run the experiment several times and record the actual results.

| N | Run | Final Conflicts | Iterations | Solved? |
|---:|---:|---:|---:|---|
| 4 | 1 | Run code | Run code | Run code |
| 4 | 2 | Run code | Run code | Run code |
| 8 | 1 | Run code | Run code | Run code |
| 8 | 2 | Run code | Run code | Run code |
| 8 | 3 | Run code | Run code | Run code |
| 10 | 1 | Run code | Run code | Run code |
| 10 | 2 | Run code | Run code | Run code |
| 10 | 3 | Run code | Run code | Run code |

### Discussion: Does the Same Board Size Always Produce the Same Result?

Answer:

No. The same board size does not always produce the same result because Hill-Climbing starts from a random initial state.

Different initial states can lead to different neighboring states, numbers of iterations, and final conflicts.

Therefore, the algorithm may find a solution in one run and stop at a local maximum in another run.

# 13. Improving the Search: Random-Restart Hill-Climbing

Basic Hill-Climbing starts from a single initial state.

If it stops at a non-goal local maximum, it terminates without finding a solution.

Random-Restart Hill-Climbing improves the chances of success by repeatedly starting from new random states.

### Basic Process

1. Generate a random initial state.
2. Run Hill-Climbing.
3. Check whether a solution was found.
4. If a solution is found, return it.
5. Otherwise, restart from another random state.
6. Stop when a solution is found or the restart limit is reached.

### What Does Probabilistically Complete Mean?

An algorithm is probabilistically complete if the probability of finding a solution approaches 1 as the search effort increases, provided a solution exists and the required assumptions hold.

In simple words:

If we continue giving the algorithm independent opportunities to search, its chance of finding a solution can get closer and closer to 100%.

This does not guarantee success within a fixed number of restarts.

### Probability Example

Suppose a randomized search has a 20% probability of finding a solution in each independent run.

Probability of failure:

1 - 0.20 = 0.80

Probability that all k runs fail:

P(all fail) = 0.80^k

Probability of finding at least one solution:

P(success) = 1 - 0.80^k

### Examples

| Independent Runs | Probability of Finding a Solution |
|---:|---:|
| 1 | 20.00% |
| 2 | 36.00% |
| 5 | 67.23% |
| 10 | 89.26% |
| 20 | 98.85% |

These values are illustrative, not measured N-Queens success rates.

If the probability of success in one independent run is p, where p is greater than zero:

P(success within k runs) = 1 - (1 - p)^k

As k increases, the probability approaches 1.

If no possible restart can lead to a solution under the chosen algorithm and neighborhood, repeated restarts will not help.

# 14. Implementing Random-Restart Hill-Climbing

### Code

```python
def random_restart_hill_climbing(n, max_restarts=100):

    best_state = None
    best_score = float("-inf")

    best_scores = []

    for restart in range(max_restarts):

        # Start a new Hill-Climbing run
        state, scores = hill_climbing(n)

        score = evaluation(state)

        # Keep the best state
        if score > best_score:

            best_state = state
            best_score = score

        # Record best score
        best_scores.append(best_score)

        # Stop if a valid solution is found
        if best_score == 0:

            return best_state, restart + 1, best_scores

    return best_state, max_restarts, best_scores
```

### Run the Algorithm

```python
solution, restarts, best_scores = (
    random_restart_hill_climbing(8, max_restarts=100)
)

print("Final state:", solution)
print("Final conflicts:", count_conflicts(solution))
print("Restarts performed:", restarts)

if count_conflicts(solution) == 0:
    print("A valid solution was found.")
else:
    print("No solution was found within the restart limit.")

print_board(solution)
```

**Explanation:**

- The algorithm performs repeated Hill-Climbing runs.
- It remembers the best state found.
- It stops early if the evaluation score reaches zero.
- Otherwise, it continues until the restart limit.

A valid solution has zero conflicts.

If no solution is found within 100 restarts, the best state is returned.

# 15. Plotting Random-Restart Progress

### Code

```python
plt.figure(figsize=(9, 5))

plt.plot(
    range(1, len(best_scores) + 1),
    best_scores,
    marker="o"
)

plt.xlabel("Restart")
plt.ylabel("Best Evaluation Score So Far")
plt.title("Random-Restart Hill-Climbing: N-Queens")

plt.grid(True)
plt.show()
```

### Graph Interpretation

The graph should be non-decreasing because the algorithm retains the best result found so far.

Example:

```text
-5, -3, -3, -2, -1, 0
```

The best-known result improves across several restarts.

When the score reaches zero, a valid solution has been found.

### Difference Between the Two Graphs

**Single-run graph:**

Shows the evaluation score during one Hill-Climbing search.

**Random-restart graph:**

Shows the best evaluation score retained across repeated searches.

# 16. Comparing Basic and Random-Restart Hill-Climbing

### Code

```python
print("Basic Hill-Climbing")
print("-------------------")

for i in range(5):

    state, scores = hill_climbing(8)

    conflicts = count_conflicts(state)

    print(
        f"Run {i + 1}: "
        f"Conflicts = {conflicts}, "
        f"Iterations = {len(scores)}, "
        f"Solved = {conflicts == 0}"
    )


print("\nRandom-Restart Hill-Climbing")
print("----------------------------")

for i in range(5):

    state, restarts, best_scores = (
        random_restart_hill_climbing(8, max_restarts=100)
    )

    conflicts = count_conflicts(state)

    print(
        f"Run {i + 1}: "
        f"Conflicts = {conflicts}, "
        f"Restarts = {restarts}, "
        f"Solved = {conflicts == 0}"
    )
```

### Task 3: Comparison Questions

**Q1. Did basic Hill-Climbing find a solution in every run?**

Answer:

Not necessarily. Basic Hill-Climbing may find a solution in some runs but may stop at a local maximum in other runs.

**Q2. Did Random-Restart Hill-Climbing improve the results?**

Answer:

Generally, yes. Random-Restart improves the chances of finding a solution by starting from different random initial states.

**Q3. How many restarts were needed to find a solution?**

Answer:

The number of restarts depends on the randomly generated initial states. It must be determined by running the experiment.

**Q4. Did any random-restart experiment reach its restart limit without finding a solution?**

Answer:

It is possible. If no valid solution is found within 100 attempts, the algorithm reaches its restart limit and returns the best state encountered.

**Q5. Why can Random-Restart improve the probability of success without guaranteeing success within a fixed number of attempts?**

Answer:

Each restart provides another opportunity to find a solution from a different initial state.

However, every run can still fail. Therefore, a fixed number of restarts does not guarantee that a solution will be found.

# 17. Lab Questions

### Question 1: What is the purpose of the evaluation function in Hill-Climbing?

**Answer:**

The evaluation function measures the quality of the current state. It helps Hill-Climbing compare neighboring states and select the best available state.

In N-Queens, it counts the number of conflicts and returns the negative value of that count.

### Question 2: Why is an evaluation score of zero the best result in our N-Queens implementation?

**Answer:**

A score of zero means there are no conflicts between queens. Therefore, no two queens attack each other, and the N-Queens goal has been achieved.

### Question 3: What is the difference between a local maximum and a global maximum?

**Answer:**

A local maximum is a state that has a better evaluation score than all its immediate neighboring states.

A global maximum is the best possible state in the entire search space.

In N-Queens, a global maximum has an evaluation score of zero.

### Question 4: Why can basic Hill-Climbing stop without finding a solution even when a solution exists?

**Answer:**

Basic Hill-Climbing only examines immediate neighboring states.

It may reach a local maximum where no neighbor improves the current score, even though a valid solution exists elsewhere in the search space.

### Question 5: Does reaching a local maximum always mean that the algorithm has failed? Explain.

**Answer:**

No. A local maximum may also be a global maximum.

If its evaluation score is zero in N-Queens, it is a valid solution. However, if its score is below zero, the algorithm has stopped without solving the problem.

### Question 6: For N = 8, how many neighbors does each state have in our implementation?

**Answer:**

The formula is:

N × (N - 1)

For N = 8:

8 × 7 = 56 neighbors.

Therefore, each state has 56 neighboring states.

### Question 7: What does the evaluation-score graph tell you about the behavior of Hill-Climbing?

**Answer:**

The graph shows how the evaluation score changes during the search.

The score is non-decreasing because the algorithm accepts only improving moves.

If the score reaches zero, a valid solution is found. If it stops below zero, the algorithm has reached a non-goal local maximum.

### Question 8: Explain probabilistic completeness in your own words.

**Answer:**

Probabilistic completeness means that the probability of finding a solution approaches 100% as the number of independent search attempts increases, provided that a solution exists and each attempt has a nonzero probability of success.

### Question 9: Why does Random-Restart Hill-Climbing improve the chance of finding a solution?

**Answer:**

Random-Restart Hill-Climbing begins from different random initial states.

This reduces dependence on one starting state and provides additional opportunities to escape the problem of getting stuck at a non-goal local maximum.

### Question 10: Does probabilistic completeness guarantee that a solution will be found within 100 restarts? Why or why not?

**Answer:**

No. Probabilistic completeness does not guarantee success within a fixed number of attempts.

It means that the probability of success approaches 100% as independent attempts increase, assuming a nonzero probability of success in each run.

# 18. Extension Task: N = 20

Modify the experiment to solve the N-Queens problem for N = 20.

### Code

```python
# Basic Hill-Climbing for N = 20

solution, scores = hill_climbing(20)

print("Basic Hill-Climbing")
print("Final Conflicts:", count_conflicts(solution))
print("Iterations:", len(scores))
print("Solved:", count_conflicts(solution) == 0)


# Random-Restart Hill-Climbing for N = 20

solution, restarts, best_scores = (
    random_restart_hill_climbing(20, max_restarts=100)
)

print("\nRandom-Restart Hill-Climbing")
print("Final Conflicts:", count_conflicts(solution))
print("Restarts:", restarts)
print("Solved:", count_conflicts(solution) == 0)
```

### Required Observations

Record the following:

- Number of conflicts in the final state.
- Number of iterations in a basic Hill-Climbing run.
- Number of restarts required by Random-Restart Hill-Climbing.
- Whether a valid solution was found.

### Discussion

The number of queens and the random initial state affect the search process.

As N increases, the number of possible states and neighboring states also increases.

For each state, the number of neighbors is:

N × (N - 1)

For N = 20:

20 × 19 = 380 neighbors.

Random initial states may produce different results, and Random-Restart provides additional opportunities to find a valid arrangement.

# 19. Final Learning Outcomes

After completing this lab, we can:

1. Explain the difference between traditional search and local search.
2. Represent the N-Queens problem as a state-space problem.
3. Define an objective function.
4. Generate neighboring states.
5. Implement basic Hill-Climbing in Python.
6. Plot evaluation scores over iterations.
7. Explain local maxima and premature termination.
8. Implement Random-Restart Hill-Climbing.
9. Explain probabilistic completeness and its assumptions.
10. Compare optimization behavior across randomized runs.

# 20. Final Conclusion

Hill-Climbing is a greedy local search algorithm that repeatedly improves the current state until no better neighboring state is available.

In the N-Queens problem, the objective is to place queens on a chessboard so that no two queens attack each other.

The evaluation function counts the conflicts between queens. A score of zero represents a valid solution.

Basic Hill-Climbing may stop at a non-goal local maximum. Random-Restart Hill-Climbing improves the probability of finding a solution by using different random initial states.

Therefore, Random-Restart is generally more reliable than a single Hill-Climbing run, although it does not guarantee success within a fixed number of attempts.

---

# Submission Checklist

- [ ] State representation implemented.
- [ ] Random initial state generated.
- [ ] Conflict-counting function implemented.
- [ ] Evaluation function implemented.
- [ ] Neighbor-generation function implemented.
- [ ] Basic Hill-Climbing implemented.
- [ ] Final chessboard displayed.
- [ ] Evaluation-score graph generated.
- [ ] Different board sizes tested.
- [ ] Experimental results recorded.
- [ ] Random-Restart Hill-Climbing implemented.
- [ ] Random-Restart graph generated.
- [ ] Both algorithms compared.
- [ ] All 10 lab questions answered.
- [ ] Extension task for N = 20 completed.
- [ ] Final conclusion written.
