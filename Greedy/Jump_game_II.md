# Jump Game II — LeetCode 45

**Pattern:** Greedy + Range Expansion + Minimum Jumps  
**Difficulty:** Medium  
**Core Concept:** Expand the reachable range and take a jump only when the current range is exhausted.

## 1. Problem Statement

You are given a 0-indexed array `nums` of length `n`.

You start at index `0`.

Each element `nums[i]` represents the maximum jump length from index `i`.

You can jump from index `i` to any index in the range:

`[i + 1, i + nums[i]]`

Return the minimum number of jumps required to reach the last index.

**Important:** The problem guarantees that the last index is reachable.

### Example 1

```cpp
nums = [2,3,1,1,4]
Output = 2
```

One optimal path:

`0 → 1 → 4`

### Example 2

```cpp
nums = [2,3,0,1,4]
Output = 2
```

One optimal path:

`0 → 1 → 4`

---

## 2. Greedy Intuition

The main objective is not to find the maximum jump at every index.

Our objective is to reach the destination using the minimum number of jumps.

Think of the array as a series of reachable ranges.

Suppose we are currently at a range of positions that can be reached using the same number of jumps.

We examine every position in this range and calculate how far we can reach using one more jump.

We maintain the farthest reachable position.

When we reach the end of the current range, we must take another jump.

This is the core idea:

**Explore the entire current range → Find maximum next coverage → Take one jump → Expand the range.**

## 3. Three Important Variables

This solution depends on three variables.

### A. total_jumps

```cpp
int total_jumps = 0;
```

Stores the minimum number of jumps taken so far.

We increment it only when we finish exploring the current range.

### B. last_jump_idx

```cpp
int last_jump_idx = 0;
```

Represents the endpoint of the current reachable range.

When we reach this index, we have explored every possible position available within the current jump count.

We then move this boundary forward.

### C. coverage

```cpp
int coverage = 0;
```

Stores the farthest index reachable from any position explored so far in the current range.

For every index:

```cpp
coverage = max(coverage, i + nums[i]);
```

This ensures that we never lose a better reachable position.

### Most Important Difference

| Variable | Meaning |
|---|---|
| `last_jump_idx` | End of the current range |
| `coverage` | Farthest endpoint achievable from the current range |
| `total_jumps` | Number of jumps needed to reach the current range |

**Remember:** `last_jump_idx` tells us WHEN to jump. `coverage` tells us WHERE the next range can end.

---

## 4. Optimized Greedy Approach

### C++ Implementation

```cpp
class Solution {
public:
    int jump(vector<int>& nums) {

        int n = nums.size();

        int last_destination = n - 1;
        int total_jumps = 0;
        int last_jump_idx = 0;
        int coverage = 0;

        if(n == 1) {
            return 0;
        }

        for(int i = 0; i < n - 1; i++) {

            coverage = max(coverage, i + nums[i]);

            if(i == last_jump_idx) {

                last_jump_idx = coverage;
                total_jumps++;

                if(coverage >= last_destination) {
                    return total_jumps;
                }
            }
        }

        return total_jumps;
    }
};
```

---

## 5. Step-by-Step Algorithm

1. Calculate the destination index: `n - 1`.
2. Initialize `total_jumps = 0`.
3. Initialize `last_jump_idx = 0`.
4. Initialize `coverage = 0`.
5. Traverse the array from left to right, excluding the last index.
6. At every index, calculate the maximum reachable position:

   `coverage = max(coverage, i + nums[i])`

7. If the current index reaches the end of the current range:

   `if(i == last_jump_idx)`

   - Update `last_jump_idx = coverage`.
   - Increment `total_jumps`.
   - If coverage reaches the destination, return the answer.

8. Return the total number of jumps.

---

## 6. Dry Run — Example 1

Input:

```cpp
nums = [2,3,1,1,4]
```

Initial values:

```cpp
total_jumps = 0
last_jump_idx = 0
coverage = 0
last_destination = 4
```

| i | nums[i] | i + nums[i] | coverage | last_jump_idx | total_jumps |
|---:|---:|---:|---:|---:|---:|
| 0 | 2 | 2 | 2 | 2 | 1 |
| 1 | 3 | 4 | 4 | 2 | 1 |
| 2 | 1 | 3 | 4 | 4 | 2 |

At `i = 0`:

- Current range ends at index 0.
- We calculate coverage = 2.
- We take our first jump.
- New range endpoint = 2.

At `i = 1`:

- We are exploring the current range `[1, 2]`.
- Index 1 can reach index 4.
- Coverage becomes 4.

At `i = 2`:

- We have reached the end of the current range.
- We take our second jump.
- Coverage = 4, which reaches the destination.

**Answer = 2**

### Range Visualization

```text
Indices:       0   1   2   3   4
Array:         2   3   1   1   4

After Jump 1:  [-------]
Range:         0       2

After Jump 2:      [-----------]
Range:             1           4
```

---

## 7. Dry Run — Example 2

Input:

```cpp
nums = [2,3,0,1,4]
```

Initial values:

```cpp
total_jumps = 0
last_jump_idx = 0
coverage = 0
last_destination = 4
```

| i | nums[i] | i + nums[i] | coverage | last_jump_idx | total_jumps |
|---:|---:|---:|---:|---:|---:|
| 0 | 2 | 2 | 2 | 2 | 1 |
| 1 | 3 | 4 | 4 | 2 | 1 |
| 2 | 0 | 2 | 4 | 4 | 2 |

At index 0, we take our first jump and can reach index 2.

While exploring indices 1 and 2, index 1 provides coverage up to index 4.

When `i == 2`, we take our second jump and reach the destination.

**Answer = 2**

---

## 8. Why Does This Greedy Work?

This is the most important conceptual part.

Suppose we can reach indices from `L` to `R` using exactly `k` jumps.

Now, from every index in this range, we calculate the farthest position reachable with one additional jump.

The maximum of these positions gives us the farthest endpoint achievable using at most `k + 1` jumps.

Therefore:

- We explore the complete current range.
- We choose the maximum possible next coverage.
- We increment the jump count only after exhausting the current range.

Why is choosing maximum coverage optimal?

Because reaching a farther index cannot reduce the set of positions we can potentially reach afterward. It preserves at least as many future options as any shorter reach.

Thus, choosing the farthest coverage at every range boundary cannot increase the minimum number of jumps.

This is a greedy range-expansion strategy.

---

## 9. Complexity Analysis

Let `N` be the size of the array.

| Complexity | Value | Explanation |
|---|---|---|
| Time | O(N) | Every index is visited at most once |
| Auxiliary Space | O(1) | Only constant extra variables |

This is optimal in time complexity for the general problem.

---

## 10. Edge Cases

| Input | Output | Explanation |
|---|---:|---|
| `[0]` | 0 | Already at destination |
| `[1,2]` | 1 | One jump |
| `[2,3,1,1,4]` | 2 | Standard case |
| `[2,3,0,1,4]` | 2 | Zero does not block the optimal path |
| `[1,1,1,1]` | 3 | One step per jump |
| `[4,1,1,1,1]` | 1 | Direct jump to destination |

---

## 11. Common Mistakes

**1. Incrementing jumps at every index**

Wrong:

```cpp
total_jumps++;
```

inside every loop iteration.

We increment only when `i == last_jump_idx`.

**2. Updating coverage incorrectly**

Wrong:

```cpp
coverage = i + nums[i];
```

This may overwrite a previously discovered farther reach.

Correct:

```cpp
coverage = max(coverage, i + nums[i]);
```

**3. Confusing coverage with last_jump_idx**

They are not interchangeable.

- `coverage` continuously tracks the best next endpoint.
- `last_jump_idx` marks the current range boundary.

**4. Jumping immediately to the maximum reachable index**

We do not need to simulate the actual path. We only need the minimum jump count.

**5. Including the last index in the loop**

We use:

```cpp
i < n - 1
```

because reaching the last index completes the task. We do not need to take another jump from it.

---

## 12. Jump Game I vs Jump Game II

| Feature | Jump Game (LC 55) | Jump Game II (LC 45) |
|---|---|---|
| Goal | Determine reachability | Find minimum jumps |
| Approach | Backward Greedy or Forward Greedy | Forward Greedy |
| Main variable | Target / maxReach | coverage |
| Additional variable | — | last_jump_idx |
| Result | Boolean | Integer |
| Time | O(N) | O(N) |
| Space | O(1) | O(1) |

**Connection:** Both problems use reachability, but Jump Game II adds range boundaries to count the minimum number of jumps.

---

## 13. Interview Follow-up Questions

**Q1. Why do we use `max()` for coverage?**

Because we want to retain the farthest reachable position discovered anywhere in the current range.

**Q2. Why do we increment jumps only at `i == last_jump_idx`?**

Because all positions in the current range have been explored. We now need another jump to move into the next range.

**Q3. Why do we need two boundaries?**

One marks the current range endpoint; the other tracks the farthest endpoint achievable from that range.

**Q4. Can this problem be solved using BFS?**

Yes. Consider each reachable range as a BFS level. However, explicitly constructing and traversing a graph is unnecessary. Greedy range expansion achieves O(N) time and O(1) auxiliary space.

**Q5. Does this algorithm find the actual path?**

No. It returns only the minimum number of jumps. Reconstructing an actual path would require tracking additional information.

---

## 14. Pattern Recognition

Recognize this pattern when:

- You need the minimum number of jumps or moves.
- Each position offers a range of possible next positions.
- You can group positions by the number of moves required to reach them.
- You need to expand the reachable frontier efficiently.

Think:

**Current Range → Explore Every Position → Calculate Maximum Coverage → Finish Current Range → Increment Jumps.**

---

## 15. Quick Revision Cheat Sheet

```cpp
int jumps = 0;
int currentEnd = 0;
int farthest = 0;

for(int i = 0; i < n - 1; i++) {

    farthest = max(farthest, i + nums[i]);

    if(i == currentEnd) {
        jumps++;
        currentEnd = farthest;

        if(currentEnd >= n - 1)
            return jumps;
    }
}
```

### One-line intuition

Explore all positions reachable with the current number of jumps, find the farthest next reach, and increase the jump count only when the current range ends.

**Final takeaway:**

`last_jump_idx` tells us when to take the next jump, while `coverage` tells us how far that next jump range can extend.

**GitHub:** Definitely add this problem. It is one of the most important Greedy range-expansion problems for coding interviews.
