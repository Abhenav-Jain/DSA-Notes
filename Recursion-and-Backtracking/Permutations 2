# Permutations II

**LeetCode:** 47 — Permutations II  
**Concept:** Recursion + Backtracking + Duplicate Handling  
**Difficulty:** Medium

---

## 1. Problem

Given a collection of numbers `nums` that might contain duplicates, return all possible unique permutations.

The answer must not contain duplicate permutations.

### Example

**Input:**
```text
[1,1,2]
```

**Output:**
```text
[
    [1,1,2],
    [1,2,1],
    [2,1,1]
]
```

Notice that although there are `3! = 6` possible arrangements considering positions, only `3` of them are unique because two elements are equal.

---

## 2. Main Difference From Permutations I

In **LeetCode 46 — Permutations**, all elements are distinct.

Example:

```text
[1,2,3]
```

Every permutation is automatically unique.

But here:

```text
[1,1,2]
```

there are duplicate elements.

If we blindly use the same approach as Permutations I, duplicate permutations will be generated.

For example:

```text
[1,1,2]
[1,2,1]
[1,1,2]   ← duplicate
[1,2,1]   ← duplicate
[2,1,1]
[2,1,1]   ← duplicate
```

Therefore, we need a way to avoid choosing the same value more than once at the same recursion level.

---

## 3. Core Idea

The basic permutation logic remains exactly the same:

```text
Fix current position
        ↓
Try every remaining element
        ↓
Swap
        ↓
Recursive call
        ↓
Swap back
```

The only new thing is:

> At every recursion level, do not choose the same value more than once.

We achieve this using:

```cpp
set<int> used;
```

---

## 4. Why Do We Need `used`?

Suppose:

```text
nums = [1,1,2]
idx = 0
```

At position `0`, we have three choices:

```text
1
1
2
```

But choosing the first `1` and choosing the second `1` at this same level produces the same set of possibilities.

So we only need:

```text
Choose 1
Choose 2
```

The second `1` should be skipped.

This is exactly what:

```cpp
if(used.count(nums[i]))
    continue;
```

does.

---

## 5. Meaning of `used`

```cpp
set<int> used;
```

stores the values that have already been chosen **at the current recursion level**.

For example:

```text
idx = 0

nums = [1,1,2]

used = {}
```

Choose first `1`:

```text
used = {1}
```

Now we encounter another `1`.

Since:

```cpp
used.count(1)
```

is true, we skip it.

Then we can choose:

```text
2
```

So the choices at this level become:

```text
1
2
```

instead of:

```text
1
1
2
```

---

## 6. Very Important: `used` Is Per Recursion Level

This is one of the most important concepts in this problem.

The set:

```cpp
set<int> used;
```

is declared **inside `solve()`**:

```cpp
void solve(...) {

    set<int> used;

    ...
}
```

Therefore, every recursive call gets its own separate `used` set.

For example:

```text
Level 0
used = {}

Level 1
used = {}

Level 2
used = {}
```

Each level independently decides which values have already been used at that level.

---

## 7. Why Can't We Use One Global `set`?

Because we are not trying to prevent an element from being used in the entire permutation.

We only want to prevent:

> Choosing the same value multiple times at the same recursion level.

For example:

```text
[1,1,2]
```

The final answer:

```text
[1,1,2]
```

contains two `1`s.

So duplicate values are allowed in the permutation.

We only avoid generating the same permutation multiple times.

---

## 8. Complete Code

```cpp
class Solution {
public:

    void solve(vector<int>& nums,
               vector<vector<int>>& ans,
               int idx) {

        // Base Case
        if(idx == nums.size()) {
            ans.push_back(nums);
            return;
        }

        // Store values already used at this level
        set<int> used;

        // Try every remaining element
        for(int i = idx; i < nums.size(); i++) {

            // Skip duplicate choice at this level
            if(used.count(nums[i])) {
                continue;
            }

            used.insert(nums[i]);

            // Choose
            swap(nums[i], nums[idx]);

            // Explore
            solve(nums, ans, idx + 1);

            // Undo / Backtrack
            swap(nums[i], nums[idx]);
        }
    }

    vector<vector<int>> permuteUnique(vector<int>& nums) {

        vector<vector<int>> ans;

        solve(nums, ans, 0);

        return ans;
    }
};
```

---

## 9. Understanding the Code Step-by-Step

### Step 1: Base Case

```cpp
if(idx == nums.size()) {
    ans.push_back(nums);
    return;
}
```

When:

```text
idx == n
```

all positions have been fixed.

Therefore, the current arrangement is a complete permutation.

---

### Step 2: Create `used`

```cpp
set<int> used;
```

This tracks which values have already been selected at the current recursion level.

---

### Step 3: Try Every Remaining Element

```cpp
for(int i = idx; i < nums.size(); i++)
```

We try every element from the current position onward.

---

### Step 4: Skip Duplicate Choices

```cpp
if(used.count(nums[i])) {
    continue;
}
```

If this value has already been selected at this level, skip it.

---

### Step 5: Mark the Value

```cpp
used.insert(nums[i]);
```

Now this value has been used at the current level.

---

### Step 6: Choose

```cpp
swap(nums[i], nums[idx]);
```

Place the selected element at the current position.

---

### Step 7: Explore

```cpp
solve(nums, ans, idx + 1);
```

Move to the next position.

---

### Step 8: Undo

```cpp
swap(nums[i], nums[idx]);
```

Restore the array so that the next choice can be explored.

---

# 10. Dry Run

Consider:

```text
nums = [1,1,2]
```

Initially:

```text
idx = 0
```

At level `0`:

```text
used = {}
```

---

### Choice 1: First `1`

```text
used = {1}
```

Swap:

```text
[1,1,2]
```

Now:

```text
idx = 1
```

At level `1`:

```text
used = {}
```

---

### Level 1 — Choose `1`

```text
used = {1}
```

Array:

```text
[1,1,2]
```

Move to:

```text
idx = 2
```

Choose `2`:

```text
[1,1,2]
```

Complete permutation:

```text
[1,1,2]
```

---

### Backtrack

Return to:

```text
idx = 1
```

Array restored.

Now try `2`.

```text
used = {1,2}
```

Array becomes:

```text
[1,2,1]
```

Complete permutation:

```text
[1,2,1]
```

---

### Back to Level 0

Now at level `0`:

```text
used = {1}
```

We encounter the second `1`.

```cpp
if(used.count(1))
```

is true.

Therefore:

```cpp
continue;
```

The second `1` is skipped.

This prevents duplicate permutations.

---

### Choice 2: `2`

Now:

```text
used = {1,2}
```

Choose `2`.

Array becomes:

```text
[2,1,1]
```

The remaining positions can only contain:

```text
1,1
```

So we get:

```text
[2,1,1]
```

Final answer:

```text
[
    [1,1,2],
    [1,2,1],
    [2,1,1]
]
```

---

# 11. Recursion Tree

For:

```text
[1,1,2]
```

At the first level:

```text
              []
            /    \
           1      2
         /   \     \
        1     2     1
        |     |     |
        2     1     1
```

Result:

```text
[1,1,2]
[1,2,1]
[2,1,1]
```

The second `1` at the first level is skipped because:

```text
1 is already present in used
```

---

# 12. Why `used` Must Be Inside the Loop's Recursion Level

Correct:

```cpp
void solve(...) {

    set<int> used;

    for(...) {
        ...
    }
}
```

This means:

```text
Every recursion level has its own used set.
```

Suppose:

```text
Level 0:
used = {1,2}

Level 1:
used = {1,2}
```

These are two different sets.

This is necessary because the same value may be used at different positions.

---

# 13. Difference Between `used` and `visited`

Do not confuse this with the `visited[]` approach.

### `used`

Used here to prevent:

```text
Duplicate choices at the same recursion level
```

Example:

```text
[1,1,2]
```

At one level:

```text
1 ← choose
1 ← skip
2 ← choose
```

---

### `visited`

Usually used to track:

```text
Which element is currently present in the permutation
```

For example:

```cpp
visited[i] = true;
```

means that particular index is already included in the current permutation.

So:

```text
used
```

and:

```text
visited
```

solve different problems.

---

# 14. Permutations I vs Permutations II

## Permutations I — LeetCode 46

Input:

```text
[1,2,3]
```

All elements are distinct.

Therefore:

```cpp
for(int i = idx; i < nums.size(); i++) {
    swap(nums[i], nums[idx]);
    solve(nums, idx + 1, ans);
    swap(nums[i], nums[idx]);
}
```

No duplicate handling is required.

---

## Permutations II — LeetCode 47

Input:

```text
[1,1,2]
```

Duplicates exist.

Therefore, we add:

```cpp
set<int> used;
```

and:

```cpp
if(used.count(nums[i]))
    continue;

used.insert(nums[i]);
```

Everything else remains almost the same.

---

# 15. The Key Modification

### LeetCode 46

```cpp
for(int i = idx; i < nums.size(); i++) {

    swap(nums[i], nums[idx]);

    solve(nums, idx + 1, ans);

    swap(nums[i], nums[idx]);
}
```

### LeetCode 47

```cpp
set<int> used;

for(int i = idx; i < nums.size(); i++) {

    if(used.count(nums[i]))
        continue;

    used.insert(nums[i]);

    swap(nums[i], nums[idx]);

    solve(nums, idx + 1, ans);

    swap(nums[i], nums[idx]);
}
```

So the main new concept is:

```text
Duplicate handling at each recursion level.
```

---

# 16. Why Sorting Is Not Necessary in This Approach

A common duplicate-handling technique is:

```cpp
sort(nums.begin(), nums.end());
```

and then skipping duplicates using:

```cpp
if(i > idx && nums[i] == nums[i-1])
    continue;
```

However, in this solution we use:

```cpp
set<int> used;
```

Therefore, sorting is not necessary.

The set directly tells us:

```cpp
if(used.count(nums[i]))
```

whether this value has already been chosen at the current level.

---

# 17. `set` vs `unordered_set`

Current code:

```cpp
set<int> used;
```

works correctly.

We could also use:

```cpp
unordered_set<int> used;
```

because we only need:

```cpp
insert()
count()
```

and ordering is not important.

Conceptually:

```text
set
→ stores unique values
→ count() checks whether value exists
```

---

# 18. Backtracking Pattern

The complete pattern is:

```text
At every level:

Create used set

        ↓

Try every remaining element

        ↓

If value already used at this level
        ↓
      Skip

        ↓

Mark value as used

        ↓

Swap

        ↓

Recursive call

        ↓

Swap back
```

In code:

```cpp
set<int> used;

for(int i = idx; i < nums.size(); i++) {

    if(used.count(nums[i]))
        continue;

    used.insert(nums[i]);

    swap(nums[i], nums[idx]);

    solve(nums, ans, idx + 1);

    swap(nums[i], nums[idx]);
}
```

---

# 19. Complexity

There can be up to:

```text
n!
```

permutations.

For each complete permutation, we store `n` elements.

Therefore, the output itself can require:

```text
O(n × n!)
```

space.

The recursion depth is:

```text
O(n)
```

The `set` at each recursion level can contain at most `n` different values.

The overall complexity depends on the number of unique permutations generated.

For the worst case where all elements are distinct:

```text
Time: O(n × n!)
Space: O(n × n!)   // including output
```

---

# 20. Common Mistakes

### Mistake 1: Using `used` globally

Incorrect concept:

```cpp
set<int> used;
```

outside `solve()`.

We need a separate set for every recursion level.

Correct:

```cpp
void solve(...) {

    set<int> used;

    ...
}
```

---

### Mistake 2: Forgetting `used.insert()`

Need:

```cpp
if(used.count(nums[i]))
    continue;

used.insert(nums[i]);
```

Otherwise duplicates will not be detected.

---

### Mistake 3: Forgetting Backtracking

Need both:

```cpp
swap(nums[i], nums[idx]);
```

before recursion and:

```cpp
swap(nums[i], nums[idx]);
```

after recursion.

---

### Mistake 4: Using `visited` instead of level-based duplicate handling

`visited` and `used` have different purposes.

Here:

```text
used = avoid duplicate choices at current level
```

---

# 21. Pattern Recognition

Whenever a problem says:

> Generate all unique permutations and the input may contain duplicate elements.

Think:

```text
Permutation
    ↓
Backtracking
    ↓
Duplicate elements?
    ↓
Use level-wise used set
```

Template:

```cpp
set<int> used;

for(int i = idx; i < n; i++) {

    if(used.count(nums[i]))
        continue;

    used.insert(nums[i]);

    swap(nums[i], nums[idx]);

    solve(nums, idx + 1, ans);

    swap(nums[i], nums[idx]);
}
```

---

# 22. Important Concept

The most important line in this problem is:

```cpp
if(used.count(nums[i]))
    continue;
```

This does **not** mean:

> This number cannot be used again in the entire permutation.

It means:

> This value has already been chosen for the current position from this recursion level, so choosing the same value again would generate duplicate branches.

This distinction is extremely important.

---

# 23. One-Line Revision

> **Permutations II = Permutations I + a `used` set at every recursion level to avoid choosing the same value twice at the same level.**

---

# 24. Quick Comparison

| Problem | Duplicate Elements | Duplicate Handling |
|---|---|---|
| LeetCode 46 — Permutations | No | Not required |
| LeetCode 47 — Permutations II | Yes | `set` at each recursion level |

---

# 25. Final Mental Model

Remember this:

```text
                Current Position
                       ↓
             Try every remaining value
                       ↓
             Already used at level?
                  /         \
                YES          NO
                 ↓            ↓
               Skip      Mark as used
                              ↓
                            Swap
                              ↓
                           Recurse
                              ↓
                         Swap Back
```

### Golden Rule

> **Duplicate values are allowed in the final permutation, but the same value should not be selected more than once at the same recursion level.**

That is the entire core of **LeetCode 47 — Permutations II**.
