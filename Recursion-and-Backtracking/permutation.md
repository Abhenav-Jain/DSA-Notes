# Permutations

**LeetCode:** 46 — Permutations  
**Concept:** Recursion + Backtracking  
**Difficulty:** Medium

---

## 1. Problem

Given an array `nums` containing distinct integers, return all possible permutations.

### Example

**Input:**
```text
[1,2,3]
```

**Output:**
```text
[
    [1,2,3],
    [1,3,2],
    [2,1,3],
    [2,3,1],
    [3,1,2],
    [3,2,1]
]
```

For `n` distinct elements:

```text
Number of permutations = n!
```

For example:

```text
3! = 3 × 2 × 1 = 6
```

---

## 2. What is a Permutation?

A permutation means arranging all elements in different possible orders.

For:

```text
[1,2,3]
```

Possible permutations are:

```text
[1,2,3]
[1,3,2]
[2,1,3]
[2,3,1]
[3,1,2]
[3,2,1]
```

### Important

- Every element is used exactly once.
- Only the order changes.
- For `n` distinct elements, total permutations are `n!`.

---

## 3. Core Idea

The main idea is:

> Fix one position and try every remaining element at that position.

Initially:

```text
[_,_,_]
 ↑
idx = 0
```

For position `0`, we can choose:

```text
1
2
3
```

If we choose `1`:

```text
[1,_,_]
```

Now position `1` can be filled using the remaining elements:

```text
2
3
```

This gives:

```text
[1,2,_]
[1,3,_]
```

Finally:

```text
[1,2,3]
[1,3,2]
```

The same process is repeated for `2` and `3`.

---

## 4. Backtracking Pattern

The entire solution follows:

```text
Choose
   ↓
Explore
   ↓
Undo
```

In our code:

```cpp
swap(nums[i], nums[idx]);   // Choose

solve(nums, idx + 1, ans);  // Explore

swap(nums[i], nums[idx]);   // Undo
```

The second `swap()` is the backtracking step.

---

## 5. Meaning of `idx`

`idx` represents the current position that we are trying to fix.

For example:

```text
idx = 0
```

means:

```text
[_,_,_]
 ↑
current position
```

After fixing position `0`:

```text
idx = 1
```

means:

```text
[1,_,_]
   ↑
current position
```

Then:

```text
idx = 2
```

means:

```text
[1,2,_]
     ↑
current position
```

So:

> `idx` tells us which position is currently being fixed.

---

## 6. Why Do We Use a `for` Loop?

At every position, we need to try every remaining element.

```cpp
for(int i = idx; i < nums.size(); i++)
```

Suppose:

```text
nums = [1,2,3]
idx = 0
```

The loop tries:

```text
i = 0 → choose 1
i = 1 → choose 2
i = 2 → choose 3
```

Therefore, every possible choice for the current position is explored.

---

## 7. Why Does the Loop Start From `idx`?

Suppose:

```text
nums = [1,2,3]
idx = 1
```

This means position `0` is already fixed:

```text
[1,_,_]
```

Only the remaining elements should be considered:

```text
2,3
```

Therefore:

```cpp
for(int i = idx; i < nums.size(); i++)
```

starts from `idx`.

The elements before `idx` are already fixed and should not be changed.

---

## 8. Why Do We Use `swap()`?

Suppose:

```text
nums = [1,2,3]
idx = 0
i = 2
```

We want to put `3` at position `0`.

So:

```cpp
swap(nums[i], nums[idx]);
```

changes:

```text
[1,2,3]
```

into:

```text
[3,2,1]
 ↑
fixed position
```

Now `3` is fixed at position `0`.

Then recursion handles the remaining positions.

---

## 9. Fixed Part and Remaining Part

We can think of the array as:

```text
Fixed Part | Remaining Part
```

For example:

```text
[2 | 1,3]
```

Here:

```text
2
↑
fixed
```

and:

```text
1,3
↑
remaining
```

The recursion works on the remaining part.

---

## 10. Why Do We Call `idx + 1`?

After fixing the current position, we move to the next position.

```cpp
solve(nums, idx + 1, ans);
```

Flow:

```text
idx = 0
   ↓
fix position 0
   ↓
idx = 1
   ↓
fix position 1
   ↓
idx = 2
   ↓
fix position 2
   ↓
complete permutation
```

So:

> Once the current position is fixed, recursion moves to the next position.

---

## 11. Base Case

```cpp
if(idx == nums.size())
```

This means all positions have been fixed.

For example:

```text
nums = [2,3,1]
idx = 3
```

Since:

```text
idx == nums.size()
```

we have a complete permutation.

Therefore:

```cpp
ans.push_back(nums);
return;
```

---

## 12. Why Do We Swap Back?

This is the most important part of backtracking.

Suppose the original array is:

```text
[1,2,3]
```

We choose `2` for the first position:

```text
[2,1,3]
```

We recursively generate all permutations starting with `2`.

After completing this branch, we need to restore the array:

```text
[1,2,3]
```

so that we can try `3` at the first position.

Therefore:

```cpp
swap(nums[i], nums[idx]);
```

is performed again.

The complete pattern is:

```cpp
swap(nums[i], nums[idx]);   // Choose

solve(nums, idx + 1, ans);  // Explore

swap(nums[i], nums[idx]);   // Undo
```

This is exactly what backtracking means.

---

## 13. Recursion Tree

For:

```text
[1,2,3]
```

the recursion tree is:

```text
                         [1,2,3]
                       /    |    \
                      1     2     3
                    /  \   / \   / \
                   2    3 1   3 1   2
                   |    | |   | |   |
                  123  132 213 231 312 321
```

The leaf nodes are the final permutations:

```text
[1,2,3]
[1,3,2]
[2,1,3]
[2,3,1]
[3,1,2]
[3,2,1]
```

---

## 14. Dry Run

Consider:

```text
nums = [1,2,3]
```

Initially:

```text
idx = 0
```

### Branch 1: Choose `1`

```cpp
swap(nums[0], nums[0]);
```

Array remains:

```text
[1,2,3]
```

Now:

```text
idx = 1
```

At position `1`, try:

```text
2
3
```

This generates:

```text
[1,2,3]
[1,3,2]
```

After completing this branch, we backtrack.

---

### Branch 2: Choose `2`

Now:

```cpp
swap(nums[1], nums[0]);
```

Array becomes:

```text
[2,1,3]
```

Position `0` is fixed:

```text
2 | 1,3
```

Now we generate:

```text
[2,1,3]
[2,3,1]
```

After finishing, swap back again.

---

### Branch 3: Choose `3`

Now:

```cpp
swap(nums[2], nums[0]);
```

Array becomes:

```text
[3,2,1]
```

Position `0` is fixed:

```text
3 | 2,1
```

Remaining permutations:

```text
[3,2,1]
[3,1,2]
```

Therefore, all `3! = 6` permutations are generated.

---

## 15. Complete Code

```cpp
class Solution {
public:

    void solve(vector<int>& nums,
               int idx,
               vector<vector<int>>& ans) {

        // Base Case
        if(idx == nums.size()) {
            ans.push_back(nums);
            return;
        }

        // Try every remaining element
        for(int i = idx; i < nums.size(); i++) {

            // Choose
            swap(nums[i], nums[idx]);

            // Explore
            solve(nums, idx + 1, ans);

            // Undo / Backtrack
            swap(nums[i], nums[idx]);
        }
    }

    vector<vector<int>> permute(vector<int>& nums) {

        vector<vector<int>> ans;

        solve(nums, 0, ans);

        return ans;
    }
};
```

---

## 16. Why This Code Works

At every recursive level:

```text
idx = current position
```

The loop:

```cpp
for(int i = idx; i < nums.size(); i++)
```

tries every remaining element.

Then:

```cpp
swap(nums[i], nums[idx]);
```

places the selected element at the current position.

Then:

```cpp
solve(nums, idx + 1, ans);
```

solves the next position.

Finally:

```cpp
swap(nums[i], nums[idx]);
```

restores the previous state.

Therefore:

```text
Choose → Explore → Undo
```

is repeated for every possible choice.

---

## 17. Why We Don't Need `temp`

In many backtracking problems we use:

```cpp
temp.push_back(x);
solve(...);
temp.pop_back();
```

But here we directly modify `nums`.

So instead of:

```text
push → recurse → pop
```

we use:

```text
swap → recurse → swap back
```

Both are backtracking techniques.

---

## 18. Alternative Approach Using `visited[]`

Another way to solve permutations is by maintaining a `visited` array.

Concept:

```text
Choose an unused element
        ↓
Mark it visited
        ↓
Recurse
        ↓
Unmark it
```

Example:

```cpp
vector<bool> visited(n, false);
```

However, the current solution uses swapping, so a separate `visited[]` array is not required.

### Swap Approach

```text
Choose → Swap → Recurse → Swap Back
```

### Visited Approach

```text
Choose → Mark → Recurse → Unmark
```

Both use the same fundamental backtracking idea.

---

## 19. Subsets vs Permutations

### Subsets

For every element:

```text
Include
   OR
Exclude
```

For `n` elements:

```text
Number of subsets = 2^n
```

For example:

```text
[1,2,3]

2^3 = 8
```

---

### Permutations

For every position:

```text
Try every remaining element
```

For `n` distinct elements:

```text
Number of permutations = n!
```

For example:

```text
3! = 6
```

---

## 20. Combination Sum vs Permutations

### Combination Sum

We have two choices:

```text
Include current element
Exclude current element
```

When including the element, we may stay at the same index because the element can be reused.

```cpp
solve(..., idx);
```

When excluding:

```cpp
solve(..., idx + 1);
```

---

### Permutations

There is no include/exclude decision.

Instead:

```text
Try every remaining element
```

using:

```cpp
for(int i = idx; i < n; i++)
```

Then:

```text
Swap
 ↓
Recursive Call
 ↓
Swap Back
```

---

## 21. Important Backtracking Template

For permutation problems, remember this template:

```cpp
for(int i = idx; i < n; i++) {

    // Choose
    swap(nums[i], nums[idx]);

    // Explore
    solve(nums, idx + 1, ans);

    // Undo
    swap(nums[i], nums[idx]);
}
```

This is one of the most important Recursion + Backtracking patterns.

---

## 22. Complexity

For `n` distinct elements:

```text
Number of permutations = n!
```

Each permutation contains `n` elements.

### Time Complexity

```text
O(n × n!)
```

The `n` factor comes from copying each generated permutation into `ans`.

### Auxiliary Space

Recursion depth:

```text
O(n)
```

### Output Space

There are `n!` permutations and each contains `n` elements:

```text
O(n × n!)
```

---

## 23. Common Mistakes

### Mistake 1: Starting the loop from `0`

Incorrect:

```cpp
for(int i = 0; i < n; i++)
```

Correct:

```cpp
for(int i = idx; i < n; i++)
```

Because positions before `idx` are already fixed.

---

### Mistake 2: Forgetting the second swap

Incorrect:

```cpp
swap(nums[i], nums[idx]);
solve(nums, idx + 1, ans);
```

Correct:

```cpp
swap(nums[i], nums[idx]);

solve(nums, idx + 1, ans);

swap(nums[i], nums[idx]);
```

Without the second swap, the next branch will start from the wrong state.

---

### Mistake 3: Not increasing `idx`

Incorrect:

```cpp
solve(nums, idx, ans);
```

Correct:

```cpp
solve(nums, idx + 1, ans);
```

The current position has already been fixed.

---

### Mistake 4: Wrong Base Case

Correct:

```cpp
if(idx == nums.size())
```

At this point, every position has been fixed.

---

## 24. Pattern Recognition

Whenever a question asks:

> Generate all possible arrangements/orders of elements.

Think:

```text
Permutation
    ↓
Fix current position
    ↓
Try every remaining element
    ↓
Swap
    ↓
Recursion
    ↓
Swap Back
```

---

## 25. Key Takeaways

### `idx`

Current position that needs to be fixed.

### `i`

Element that we are trying to place at the current position.

### First Swap

```cpp
swap(nums[i], nums[idx]);
```

Means:

> Choose this element for the current position.

### Recursive Call

```cpp
solve(nums, idx + 1, ans);
```

Means:

> Explore the remaining positions.

### Second Swap

```cpp
swap(nums[i], nums[idx]);
```

Means:

> Undo the choice and restore the previous state.

### Base Case

```cpp
idx == nums.size()
```

Means:

> A complete permutation has been generated.

---

## 26. One-Line Revision

> **Permutation = Fix the current position, try every remaining element there, recursively solve the next position, and swap back to restore the array.**
