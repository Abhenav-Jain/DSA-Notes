# Move Zeroes — LeetCode 283

## 1. Problem Statement

Given an integer array `nums`, move all `0`s to the end of the array while maintaining the relative order of all non-zero elements.

The operation must be performed **in-place**, without making a copy of the array.

**Example 1:**

```text
Input:  nums = [0,1,0,3,12]
Output: [1,3,12,0,0]
```

**Example 2:**

```text
Input:  nums = [0]
Output: [0]
```

### Requirements

1. All non-zero elements must appear before zeroes.
2. Relative order of non-zero elements must remain unchanged.
3. Modify the original array.
4. Use O(1) auxiliary space.

---

## 2. Pattern Identification

**Pattern: Two Pointers + In-place Array Modification**

We use two pointers:

- `i`: Position where the next non-zero element should be placed.
- `j`: Traversal pointer that explores the complete array.

The objective is to compact all non-zero elements towards the beginning of the array.

This is also known as the **Stable Compaction Pattern**.

Why stable?

Because the relative order of non-zero elements remains unchanged.

For example:

```text
Before: [0, 5, 0, 2, 8, 0, 3]
After:  [5, 2, 8, 3, 0, 0, 0]
```

The order `5 → 2 → 8 → 3` is preserved.

---

## 3. Optimized Approach — Two Pointers

### Pointer initialization

```cpp
int i = 0;
int j = 0;
```

- `i` represents the next available position for a non-zero element.
- `j` traverses the array from left to right.

### Algorithm

1. Start both pointers at index `0`.
2. Traverse the array using `j`.
3. If `nums[j] == 0`, simply move `j` forward.
4. If `nums[j] != 0`:
   - Swap `nums[i]` and `nums[j]`.
   - Increment `i`.
5. Continue until `j` reaches the end.

### C++ Code

```cpp
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        int i = 0;
        int j = 0;
        int n = nums.size();

        while(j < n) {
            if(nums[j] != 0) {
                swap(nums[i], nums[j]);
                i++;
            }
            j++;
        }
    }
};
```

---

## 4. Detailed Dry Run

Input:

```text
nums = [0, 1, 0, 3, 12]
```

Initially:

```text
i = 0
j = 0
```

| Iteration | i | j | Condition | Action | Array |
|---|---:|---:|---|---|---|
| 1 | 0 | 0 | nums[j] == 0 | Skip | [0,1,0,3,12] |
| 2 | 0 | 1 | nums[j] != 0 | Swap(0,1) | [1,0,0,3,12] |
| 3 | 1 | 2 | nums[j] == 0 | Skip | [1,0,0,3,12] |
| 4 | 1 | 3 | nums[j] != 0 | Swap(1,3) | [1,3,0,0,12] |
| 5 | 2 | 4 | nums[j] != 0 | Swap(2,4) | [1,3,12,0,0] |

Final output:

```text
[1, 3, 12, 0, 0]
```

---

## 5. Understanding the Core Logic

Consider:

```text
[0, 1, 0, 3, 12]
```

At every iteration, the array can be divided into three regions:

```text
[ Processed Non-zero | Processed Zeroes | Unprocessed ]
```

More precisely:

- `[0, i-1]`: All non-zero elements encountered so far, in their original relative order.
- `[i, j-1]`: All zeroes encountered so far.
- `[j, n-1]`: Elements yet to be processed.

When `nums[j]` is non-zero, swapping it with `nums[i]` places it immediately after the previously processed non-zero elements.

When it is zero, we simply extend the processed-zero region by advancing `j`.

This is why the algorithm works.

---

## 6. Loop Invariant — Correctness Proof

At the beginning of every iteration:

1. All elements in the range `[0, i-1]` are non-zero.
2. All elements in the range `[i, j-1]` are zero.
3. The relative order of processed non-zero elements is preserved.
4. No unprocessed element has been modified.

### Case 1: `nums[j] == 0`

We only increment `j`.

The zero region expands, and the invariant remains true.

### Case 2: `nums[j] != 0`

We swap `nums[i]` and `nums[j]`.

- The current non-zero element moves to the next available position.
- If `i < j`, `nums[i]` was zero and moves to position `j`.
- If `i == j`, it is simply a self-swap.

Then we increment `i` and `j`.

The invariant remains true.

After the loop, every element has been processed. Therefore, all non-zero elements are at the beginning, all zeroes are at the end, and the relative order is preserved.

---

## 7. Complexity Analysis

Let N be the size of the array.

| Complexity | Analysis |
|---|---|
| Time | O(N) |
| Auxiliary Space | O(1) |

**Time Complexity: O(N)**

The pointer `j` traverses the array exactly once. Every iteration performs constant-time work.

**Space Complexity: O(1)**

Only two pointers and a few variables are used. The original array is modified directly.

---

## 8. Edge Cases

| Input | Expected Output |
|---|---|
| `[0]` | `[0]` |
| `[1]` | `[1]` |
| `[0,0,0]` | `[0,0,0]` |
| `[1,2,3]` | `[1,2,3]` |
| `[0,0,1,2]` | `[1,2,0,0]` |
| `[1,2,0,0,3]` | `[1,2,3,0,0]` |
| `[0,1,0,3,12]` | `[1,3,12,0,0]` |

---

## 9. Common Mistakes

### Mistake 1: Incrementing `i` even when the current element is zero

Incorrect:

```cpp
if(nums[j] == 0) {
    i++;
}
```

`i` should only advance when a non-zero element is placed.

### Mistake 2: Overwriting instead of swapping

Simply assigning:

```cpp
nums[i] = nums[j];
```

can destroy an existing value at `nums[i]`.

Swapping preserves all elements.

### Mistake 3: Losing relative order

An approach that moves zeroes by swapping arbitrary elements from the end can disturb the relative order of non-zero elements.

### Mistake 4: Using an extra array unnecessarily

The problem explicitly asks for in-place modification with O(1) auxiliary space.

---

## 10. Optimization — Avoid Unnecessary Self-Swaps

The current solution is already optimal in time and auxiliary space.

However, when `i == j`, swapping an element with itself is unnecessary.

We can write:

```cpp
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        int i = 0;

        for(int j = 0; j < nums.size(); j++) {
            if(nums[j] != 0) {
                if(i != j) {
                    swap(nums[i], nums[j]);
                }
                i++;
            }
        }
    }
};
```

Both implementations have O(N) time and O(1) auxiliary space.

The second version avoids self-swaps. This is a minor refinement, not a complexity improvement.

---

## 11. Interview Follow-up Questions

**Q1. Why do we need two pointers?**

One pointer tracks the next placement position, while the other explores the array.

**Q2. Why does the relative order remain unchanged?**

The traversal pointer processes non-zero elements from left to right and places each at the next available position.

**Q3. Can this be solved using one pointer?**

Yes. A single traversal pointer can be used along with a placement index. Conceptually, these are still two indices serving different purposes.

**Q4. Why is the algorithm O(1) space?**

It modifies the input array directly and does not allocate another array proportional to N.

**Q5. What happens when there are no zeroes?**

Every element is swapped with itself in the original implementation. The optimized version skips these unnecessary swaps.

---

## 12. Pattern Recognition — When to Use This Technique?

Look for this pattern when a problem asks you to:

- Move certain elements to one side of an array.
- Remove or filter elements in-place.
- Preserve the relative order of remaining elements.
- Compact valid elements towards the beginning.
- Avoid using an additional array.

Related problems:

| Problem | LeetCode |
|---|---:|
| Remove Element | 27 |
| Move Zeroes | 283 |
| Remove Duplicates from Sorted Array | 26 |
| Remove Duplicates from Sorted Array II | 80 |
| Sort Colors | 75 |

These problems share the broad idea of in-place array modification, although their exact pointer strategies differ.

---

## 13. Final Revision Cheat Sheet

```text
PROBLEM: Move Zeroes (LC 283)

PATTERN:
Two Pointers + Stable Compaction

i = Next position for a non-zero element
j = Traversal pointer

RULE:
If nums[j] != 0:
    swap(nums[i], nums[j])
    i++

Always:
    j++

TIME: O(N)
SPACE: O(1)

KEY INSIGHT:
Place every encountered non-zero element at the
next available position while preserving its order.
```

**Final takeaway:** This problem teaches an important Two Pointers technique: maintaining a boundary between processed valid elements and the remaining array while modifying the array in-place.
