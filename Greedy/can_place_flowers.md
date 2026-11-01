# Can Place Flowers — LeetCode 605

**Pattern:** Greedy + Array  
**Difficulty:** Easy  
**Core Concepts:** Greedy Placement, Local Decisions, Array Modification

---

## 1. Problem Understanding

We are given an array `flowerbed` consisting of:

- `0` → Empty plot.
- `1` → Plot already contains a flower.

We are also given an integer `n`, representing the number of new flowers we want to plant.

**Condition:** We cannot plant flowers in adjacent plots.

**Goal:** Return `true` if we can plant all `n` flowers without violating the adjacency condition. Otherwise, return `false`.

### Example 1

```cpp
flowerbed = [1,0,0,0,1]
n = 1

Output: true
```

We can plant one flower at index 2.

### Example 2

```cpp
flowerbed = [1,0,0,0,1]
n = 2

Output: false
```

Only one additional flower can be planted.

---

## 2. Greedy Intuition

The key observation is:

**If the current position is empty and both its neighbours are empty, we can safely plant a flower there.**

For a position `i`, we need:

1. `flowerbed[i] == 0`
2. Left neighbour is empty or does not exist.
3. Right neighbour is empty or does not exist.

In other words:

\[
flowerbed[i]=0
\]

\[
LeftEmpty \land RightEmpty
\]

Once we plant a flower, we update:

```cpp
flowerbed[i] = 1;
```

This is important because future positions must consider the newly planted flower.

### Why Left-to-Right Greedy?

We scan the array from left to right.

Whenever we find the earliest valid empty position, we plant immediately.

This creates a valid arrangement while preserving the maximum number of possible future placements.

---

## 3. Greedy Strategy

For every index `i`:

**Case 1: Current position already occupied**

```cpp
if(flowerbed[i] == 1){
    continue;
}
```

We cannot plant here.

**Case 2: Current position is empty**

Check whether its neighbours are also empty.

- If both neighbours are empty, plant.
- If either neighbour contains a flower, skip.

**Case 3: Update the count**

```cpp
flowerbed[i] = 1;
cnt++;
```

If `cnt == n`, return `true`.

After traversing the complete array, if we cannot plant enough flowers, return `false`.

---

## 4. Approach 1 — Explicit Boundary Checking

This is the independently derived approach.

We separately handle:

- Middle positions.
- First position.
- Last position.
- Single-element flowerbed.

### C++ Implementation

```cpp
class Solution {
public:
    bool canPlaceFlowers(vector<int>& flowerbed, int n) {

        int cnt = 0;
        int m = flowerbed.size();

        if(n == 0){
            return true;
        }

        if(n > m){
            return false;
        }

        if(m == 1 && n == 1 && flowerbed[0] == 0){
            return true;
        }

        for(int i = 0; i < m; i++){

            if(flowerbed[i] == 0){

                // Middle position
                if(i-1 >= 0 && i+1 < m &&
                   flowerbed[i-1] == 0 &&
                   flowerbed[i+1] == 0){

                    flowerbed[i] = 1;
                    cnt++;
                }

                // First position
                else if(i == 0){

                    if(i+1 < m && flowerbed[i+1] == 0){
                        flowerbed[i] = 1;
                        cnt++;
                    }
                }

                // Last position
                else if(i-1 >= 0 && i+1 == m &&
                        flowerbed[i-1] == 0){

                    flowerbed[i] = 1;
                    cnt++;
                }

                if(cnt == n){
                    return true;
                }
            }
        }

        return false;
    }
};
```

### Important Observation

Your implementation modifies the original array.

For example:

```cpp
flowerbed = [0,0,0,0,0]
```

After greedy placements:

```cpp
[1,0,1,0,1]
```

This ensures that subsequent decisions account for previously planted flowers.

---

## 5. Approach 2 — Cleaner Greedy Implementation

We can simplify the boundary conditions by treating positions outside the array as empty.

For any index `i`:

- If `i == 0`, consider the left neighbour empty.
- If `i == m-1`, consider the right neighbour empty.

### C++ Code

```cpp
class Solution {
public:
    bool canPlaceFlowers(vector<int>& flowerbed, int n) {

        int m = flowerbed.size();

        for(int i = 0; i < m; i++){

            if(flowerbed[i] == 0){

                bool leftEmpty = (i == 0 || flowerbed[i-1] == 0);

                bool rightEmpty = (i == m-1 || flowerbed[i+1] == 0);

                if(leftEmpty && rightEmpty){

                    flowerbed[i] = 1;
                    n--;

                    if(n == 0){
                        return true;
                    }
                }
            }
        }

        return n == 0;
    }
};
```

**Why is this cleaner?**

We do not need separate branches for the first, middle and last positions.

The boundary conditions are handled directly through logical OR.

---

## 6. Dry Run — Basic Example

```cpp
flowerbed = [1,0,0,0,1]
n = 1
```

| Index | Current Value | Left | Right | Decision |
|---|---:|---:|---:|---|
| 0 | 1 | — | 0 | Already occupied |
| 1 | 0 | 1 | 0 | Skip |
| 2 | 0 | 0 | 0 | Plant |
| 3 | 1 | — | — | Already occupied |
| 4 | 1 | — | — | Already occupied |

Updated array:

```cpp
[1,0,1,0,1]
```

**Output: `true`**

---

## 7. Dry Run — Maximum Greedy Placement

Consider:

```cpp
flowerbed = [0,0,0,0,0]
n = 3
```

Initially:

```cpp
cnt = 0;
```

| Index | Decision | Updated Flowerbed | Count |
|---|---|---|---:|
| 0 | Plant | `[1,0,0,0,0]` | 1 |
| 1 | Skip | `[1,0,0,0,0]` | 1 |
| 2 | Plant | `[1,0,1,0,0]` | 2 |
| 3 | Skip | `[1,0,1,0,0]` | 2 |
| 4 | Plant | `[1,0,1,0,1]` | 3 |

**Output: `true`**

We successfully plant three flowers.

---

## 8. Why Is This Greedy Strategy Correct?

We can understand correctness through an exchange argument.

Suppose we are scanning from left to right and the current position `i` is empty, with both neighbours empty (or outside the array).

Our greedy strategy plants a flower at `i`.

Could skipping `i` allow us to plant more flowers later?

Consider any optimal arrangement that does not plant at `i`.

- If it plants at `i+1`, that flower can be shifted to `i`. This does not conflict with the previous position because the left neighbour is empty, and it frees `i+1`.
- If it does not plant at `i+1`, planting at `i` cannot invalidate any later placement because the only position directly affected on the right is `i+1`.

Thus, we can transform an optimal arrangement to include the greedy placement without reducing the total number of flowers.

Repeating this argument at each position proves that left-to-right greedy placement achieves the maximum possible number of additional flowers.

---

## 9. Complexity Analysis

Let:

\[
m = flowerbed.size()
\]

### Time Complexity

We traverse the flowerbed once.

Each index is processed in constant time.

\[
T(m)=O(m)
\]

### Auxiliary Space Complexity

We use only a few variables.

\[
S(m)=O(1)
\]

**Both approaches are optimal.**

---

## 10. Important Edge Cases

| Input | Output | Explanation |
|---|---:|---|
| `[1,0,0,0,1], n=1` | true | One middle position available |
| `[1,0,0,0,1], n=2` | false | Only one position available |
| `[0], n=1` | true | Single empty plot |
| `[1], n=1` | false | Single plot already occupied |
| `[0,0,0,0,0], n=3` | true | Three alternating positions |
| `[0,0,0,0,0], n=2` | true | Two flowers can be planted |
| `[1,0,1], n=1` | false | No valid position |
| `[1,1,1], n=0` | true | No new flower required |
| `[0,0], n=2` | false | Adjacent flowers are prohibited |

---

## 11. Common Mistakes

### Mistake 1: Checking only the current position

Incorrect:

```cpp
if(flowerbed[i] == 0){
    flowerbed[i] = 1;
}
```

We must also check both neighbours.

### Mistake 2: Not modifying the array

If we increment the count without updating `flowerbed[i]`, the next position might incorrectly be considered available.

### Mistake 3: Incorrect boundary handling

The first and last positions have only one existing neighbour.

We should not access invalid indices such as `flowerbed[-1]`.

### Mistake 4: Forgetting `n == 0`

If no flowers need to be planted, the answer is immediately `true`.

### Mistake 5: Assuming every empty plot is usable

An empty plot is usable only if both neighbours are empty or outside the array.

---

## 12. Pattern Recognition — When to Think of Greedy Placement

Look for problems where:

1. You need to place objects in an array or sequence.
2. Placement at one position affects neighbouring positions.
3. You have local restrictions.
4. You want to maximize the number of placements.
5. An earliest valid placement may preserve future opportunities.

Examples of similar problem themes:

- Placing items with minimum distance.
- Scheduling tasks with cooldown constraints.
- Selecting non-adjacent elements.
- Maximizing placements under spacing restrictions.

---

## 13. Interview Follow-up Questions

**Q1. Why do we scan from left to right?**

Because choosing the earliest valid position can be shown to preserve an optimal arrangement.

**Q2. Why do we modify the original array?**

To ensure that future placement decisions account for newly planted flowers.

**Q3. Can we solve it without modifying the original array?**

Yes. We can use a temporary variable to track the previous position's state or create a copy of the array.

**Q4. Why is the algorithm O(m)?**

Every index is visited once, with constant work per position.

**Q5. What is the maximum number of flowers that can be planted in an initially empty flowerbed of length m?**

\[
\left\lceil\frac{m}{2}\right\rceil
\]

For example, for five empty plots:

```cpp
[1,0,1,0,1]
```

Maximum = 3.

**Q6. Is the greedy algorithm guaranteed to maximize the number of flowers?**

Yes. The earliest-valid-placement exchange argument establishes its optimality.

---

## 14. Quick Revision Cheat Sheet

| Concept | Remember |
|---|---|
| Pattern | Greedy Placement |
| Traversal | Left to Right |
| Current condition | `flowerbed[i] == 0` |
| Left condition | `i == 0 || flowerbed[i-1] == 0` |
| Right condition | `i == m-1 || flowerbed[i+1] == 0` |
| Placement | Set current position to 1 |
| Update | Decrease remaining flowers |
| Time | O(m) |
| Auxiliary Space | O(1) |

### One-Line Takeaway

**Always plant at the earliest valid empty position; updating the array ensures every future decision respects previous placements.**
