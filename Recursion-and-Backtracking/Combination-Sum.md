# Combination Sum

**LeetCode:** 39 — Combination Sum
**Concept:** Recursion + Backtracking
**Difficulty:** Medium

---

# 1. Problem

Given an array of distinct integers `candidates` and an integer `target`, return all unique combinations of candidates where the chosen numbers sum to `target`.

The **same number can be chosen unlimited times**.

### Example

```text
Input:
candidates = [2,3,6,7]
target = 7

Output:
[
    [2,2,3],
    [7]
]
```

---

# 2. Core Concept

This problem uses:

```text
Recursion + Backtracking
```

The important idea is:

> At every index, we have two choices:
>
> 1. Include the current number.
> 2. Exclude the current number.

But there is one special thing:

### If we include the current number

We are allowed to use it **again**.

Therefore:

```cpp
solve(..., idx)
```

instead of:

```cpp
solve(..., idx + 1)
```

This is the key difference from a normal Include/Exclude problem.

---

# 3. Intuition

Consider:

```text
candidates = [2,3,6,7]
target = 7
```

Start with:

```text
idx = 0
candidate = 2
target = 7
```

We have two choices.

### Choice 1 — Include `2`

```text
temp = [2]
target = 5
```

Can we use `2` again?

Yes.

So we stay at:

```text
idx = 0
```

and choose `2` again:

```text
temp = [2,2]
target = 3
```

Again:

```text
idx = 0
```

Now if we include `2`:

```text
target = 1
```

Cannot use `2`.

So eventually we move to `3`:

```text
[2,2,3]
```

Sum becomes:

```text
2 + 2 + 3 = 7
```

Valid combination.

---

# 4. Two Important Choices

For every candidate:

## Include

```cpp
temp.push_back(candidates[idx]);

solve(
    candidates,
    target - candidates[idx],
    ans,
    temp,
    idx
);

temp.pop_back();
```

Notice:

```cpp
idx
```

does **not change**.

Why?

Because the same candidate can be used again.

---

## Exclude

```cpp
solve(
    candidates,
    target,
    ans,
    temp,
    idx + 1
);
```

Here we don't use the current candidate.

Therefore we move to the next candidate.

---

# 5. The Most Important Difference

Compare:

## Subsets

For each element:

```text
Include → idx + 1
Exclude → idx + 1
```

because every element can be selected at most once.

---

## Combination Sum

For each candidate:

```text
Include → SAME idx
Exclude → idx + 1
```

because the current number can be used unlimited times.

This is the most important concept of this problem.

---

# 6. Recursive State

Our function:

```cpp
solve(
    candidates,
    target,
    ans,
    temp,
    idx
);
```

contains:

### `idx`

Current candidate we are deciding about.

### `target`

Remaining sum required.

### `temp`

Current combination.

### `ans`

All valid combinations found so far.

---

# 7. Base Cases

There are two important base cases.

## Case 1 — Target becomes zero

```cpp
if(target == 0) {
    ans.push_back(temp);
    return;
}
```

This means:

```text
Current combination has exactly the required sum.
```

Example:

```text
temp = [2,2,3]
target = 0
```

Therefore:

```text
2 + 2 + 3 = 7
```

We store it.

---

## Case 2 — No candidates left

```cpp
if(idx == candidates.size()) {
    return;
}
```

This means we have considered every candidate but haven't reached target `0`.

So this path is invalid.

---

# 8. Why `target >= candidates[idx]`?

Suppose:

```text
target = 3
candidates[idx] = 5
```

We cannot include `5` because it would make the remaining target negative.

Therefore:

```cpp
if(target >= candidates[idx])
```

ensures that we only take a candidate when it can fit inside the remaining target.

---

# 9. Why Sort the Array?

You used:

```cpp
sort(candidates.begin(), candidates.end());
```

This is a good idea.

Example:

```text
candidates = [2,3,6,7]
```

Now candidates are in increasing order.

This allows us to reason:

```text
If candidates[idx] > target
```

then every candidate after it will also be greater than target.

Therefore we can stop exploring that branch.

A further optimization can be:

```cpp
if(candidates[idx] > target)
    return;
```

because the array is sorted.

---

# 10. Dry Run

Consider:

```text
candidates = [2,3]
target = 7
```

Start:

```text
idx = 0
target = 7
temp = []
```

Current candidate:

```text
2
```

---

## Include `2`

```text
temp = [2]
target = 5
idx = 0
```

Again include `2`:

```text
temp = [2,2]
target = 3
idx = 0
```

Again include `2`:

```text
temp = [2,2,2]
target = 1
```

Can't include another `2`.

So exclude `2`:

```text
idx = 1
target = 1
```

Candidate `3` is too large.

This path fails.

---

## Backtrack

We remove the last `2`:

```text
temp = [2,2]
```

Then try candidate `3`:

```text
temp = [2,2,3]
target = 0
```

Base case:

```text
ans = [[2,2,3]]
```

---

# 11. Recursion Tree

For:

```text
candidates = [2,3]
target = 7
```

Conceptually:

```text
                         []
                     target = 7
                       /     \
                 Include 2   Exclude 2
                    /            \
                  [2]            []
                target 5        target 7
                 /   \
           Include 2  Exclude 2
              /          \
           [2,2]          [2,3...]
          target 3
```

The important part is:

```text
Include 2
   ↓
idx stays same
   ↓
Can choose 2 again
```

while:

```text
Exclude 2
   ↓
idx increases
   ↓
Move to 3
```

---

# 12. Backtracking Pattern

The general structure is:

```cpp
// Choose
temp.push_back(candidates[idx]);

// Explore
solve(...);

// Undo
temp.pop_back();

// Skip
solve(..., idx + 1);
```

So remember:

```text
        Current Candidate
              |
       ┌──────┴──────┐
       ↓             ↓
    INCLUDE        EXCLUDE
       ↓             ↓
   same idx       idx + 1
       ↓
   recurse
       ↓
   pop_back()
```

---

# 13. Why `pop_back()` Is Necessary

Suppose:

```text
temp = [2,2]
```

We choose `3`:

```text
temp = [2,2,3]
```

After recursion returns, we need to explore another branch from:

```text
[2,2]
```

Therefore:

```cpp
temp.pop_back();
```

changes:

```text
[2,2,3]
```

back to:

```text
[2,2]
```

This allows us to try other possibilities.

---

# 14. Code

```cpp
class Solution {
public:
    void solve(vector<int>& candidates,
               int target,
               vector<vector<int>>& ans,
               vector<int>& temp,
               int idx) {

        if(target == 0) {
            ans.push_back(temp);
            return;
        }

        if(idx == candidates.size()) {
            return;
        }

        // Include current candidate
        if(target >= candidates[idx]) {
            temp.push_back(candidates[idx]);

            // Same idx because we can reuse the candidate
            solve(
                candidates,
                target - candidates[idx],
                ans,
                temp,
                idx
            );

            temp.pop_back();
        }

        // Exclude current candidate
        solve(
            candidates,
            target,
            ans,
            temp,
            idx + 1
        );
    }

    vector<vector<int>> combinationSum(
        vector<int>& candidates,
        int target
    ) {

        vector<vector<int>> ans;
        vector<int> temp;

        sort(candidates.begin(), candidates.end());

        solve(
            candidates,
            target,
            ans,
            temp,
            0
        );

        return ans;
    }
};
```

---

# 15. Important Optimization

Because we sorted:

```cpp
sort(candidates.begin(), candidates.end());
```

we can replace:

```cpp
if(target >= candidates[idx])
```

with a stronger pruning condition.

If:

```text
candidates[idx] > target
```

then every later candidate will also be too large.

So we can stop:

```cpp
if(candidates[idx] > target)
    return;
```

Then the recursive structure becomes cleaner.

---

# 16. Pattern Recognition

When you see:

> Find all combinations whose sum equals a target.

Immediately ask:

### Can an element be reused?

If **yes**:

```text
Include → same idx
Exclude → idx + 1
```

If **no**:

```text
Include → idx + 1
Exclude → idx + 1
```

This distinction is extremely important.

---

# 17. Comparison With Subsets

### Subsets

Question:

```text
Should I take this element?
```

Choices:

```text
Include
Exclude
```

Both move forward:

```text
Include → idx + 1
Exclude → idx + 1
```

---

### Combination Sum

Question:

```text
Should I take this candidate?
```

But the candidate can be reused.

Therefore:

```text
Include → idx
Exclude → idx + 1
```

---

# 18. Comparison With Letter Combinations

### Letter Combinations

For one digit:

```text
2 → a,b,c
```

We loop through all choices:

```cpp
for(char ch : letters)
```

### Combination Sum

For one candidate:

```text
2
```

we have two decisions:

```text
Take 2
Don't take 2
```

So the recursion structures are slightly different.

---

# 19. Mistakes / Important Learning

### 🔑 Mistake 1 — Moving `idx` after including

If we write:

```cpp
solve(..., idx + 1);
```

after including the candidate, we won't be able to reuse it.

For example:

```text
[2,2,3]
```

would become impossible.

Since reuse is allowed:

```cpp
solve(..., idx);
```

must be used.

---

### 🔑 Mistake 2 — Forgetting `pop_back()`

After exploring an included candidate, remove it before exploring the exclude branch.

```cpp
temp.push_back(...);
solve(...);
temp.pop_back();
```

---

### 🔑 Mistake 3 — Not sorting

Sorting isn't strictly required for your basic solution, but it allows useful pruning:

```text
If current candidate > target,
all later candidates are also > target.
```

---

# 20. Key Takeaways

### 1.

**Unlimited reuse** is the defining feature of Combination Sum.

Therefore:

```text
Include → same idx
```

### 2.

When skipping a candidate:

```text
Exclude → idx + 1
```

### 3.

Backtracking follows:

```text
Choose
↓
Explore
↓
Undo
```

### 4.

`target == 0` means we found a valid combination.

### 5.

Sorting enables pruning and makes the recursion easier to reason about.

---

# 21. Complexity

Let:

```text
n = number of candidates
T = target
```

The exact number of recursive states depends on the candidate values, so the complexity is not simply `O(2^n)`.

A commonly used upper-bound style estimate is exponential in the target/candidate values. If `m` is the smallest candidate, the recursion depth can be at most:

```text
T / m
```

and the search tree can be exponential in that depth.

The important practical point is:

```text
Backtracking → exponential search
```

with additional output cost for storing all valid combinations.

---

# ⭐ One-Line Revision

> **Combination Sum = Include the candidate and stay at the same index because reuse is allowed; or exclude it and move to the next index.**

---

# 🔗 Similar Questions

After this, practice:

1. **Combination Sum II** — LeetCode 40
2. **Combination Sum III** — LeetCode 216
3. **Subsets** — LeetCode 78
4. **Subsets II** — LeetCode 90
5. **Permutations** — LeetCode 46
6. **Combination Sum IV** — LeetCode 377

The most important next one is **Combination Sum II**, because there the reuse rule changes and duplicate handling is introduced.
