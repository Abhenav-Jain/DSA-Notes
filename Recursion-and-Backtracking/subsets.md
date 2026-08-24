# Subsets

**LeetCode:** 78 — Subsets
**Concept:** Recursion + Backtracking
**Difficulty:** Medium

---

## 1. Problem

Given an integer array `nums` containing unique elements, return **all possible subsets** (the power set).

The solution must not contain duplicate subsets.

### Example

```text
Input:
nums = [1,2,3]

Output:
[
    [],
    [1],
    [2],
    [1,2],
    [3],
    [1,3],
    [2,3],
    [1,2,3]
]
```

---

# 2. Core Idea

For every element, we have exactly **2 choices**:

```text
1. Include the element
2. Exclude the element
```

For example:

```text
nums = [1,2]
```

For `1`:

```text
       ""
      /  \
     1    ""
```

Then for `2`:

```text
         ""
       /    \
      1      ""
     / \    / \
   1,2  1  2   ""
```

Therefore, every element creates **2 branches**.

This is the classic:

> **Include / Exclude Pattern**

---

# 3. Intuition

Suppose:

```text
nums = [1,2,3]
```

At index `0`, we are looking at `1`.

We ask:

```text
Should I take 1?
```

Two possibilities:

### Choice 1 — Include `1`

```text
temp = [1]
```

Then move to the next element.

### Choice 2 — Exclude `1`

```text
temp = []
```

Then move to the next element.

We repeat this for every element.

When we reach the end of the array, whatever is inside `temp` is one valid subset.

---

# 4. Recursion State

Our recursive function contains:

```cpp
solve(nums, ans, idx, temp)
```

### `idx`

Represents the current element we are deciding about.

```text
idx = 0 → nums[0]
idx = 1 → nums[1]
idx = 2 → nums[2]
```

### `temp`

Stores the current subset being constructed.

Example:

```text
temp = [1,3]
```

means that we selected `1` and `3`.

### `ans`

Stores all completed subsets.

---

# 5. Base Case

When:

```cpp
idxx == nums.size()
```

we have processed every element.

Therefore:

```cpp
ans.push_back(temp);
return;
```

For example:

```text
nums = [1,2,3]

idx = 3
temp = [1,3]
```

Then `[1,3]` is a complete subset, so we add it to `ans`.

---

# 6. Include / Exclude Pattern

This is the most important part of the question.

For every element:

```cpp
temp.push_back(nums[idx]);
solve(...);
temp.pop_back();

solve(...);
```

### First branch — Include

```cpp
temp.push_back(nums[idx]);
solve(idx + 1);
```

We include the current element.

### Undo

```cpp
temp.pop_back();
```

We remove the element so that the next branch starts with the original state.

### Second branch — Exclude

```cpp
solve(idx + 1);
```

We simply skip the current element.

So the pattern is:

```text
             Current Element
                  |
          ┌───────┴───────┐
          ↓               ↓
       INCLUDE          EXCLUDE
          ↓               ↓
      recursion        recursion
          ↓               ↓
        undo
```

---

# 7. Why `pop_back()` Is Necessary

Suppose:

```text
nums = [1,2]
```

Initially:

```text
temp = []
```

We include `1`:

```text
temp = [1]
```

Then include `2`:

```text
temp = [1,2]
```

After this branch is complete, we need to explore:

```text
[1]
```

where `2` is excluded.

Therefore:

```cpp
temp.pop_back();
```

changes:

```text
[1,2] → [1]
```

Now we can explore the exclude branch.

---

# 8. Dry Run

Consider:

```text
nums = [1,2]
```

Start:

```text
idx = 0
temp = []
```

---

### Element `1`

Two choices.

### Include `1`

```text
temp = [1]
```

Move to index `1`.

---

### Element `2`

Again two choices.

#### Include `2`

```text
temp = [1,2]
```

Reach the end.

Add:

```text
[1,2]
```

---

#### Exclude `2`

First:

```text
temp.pop_back()
```

Now:

```text
temp = [1]
```

Reach the end.

Add:

```text
[1]
```

---

### Back to element `1`

Undo `1`:

```text
temp.pop_back()
```

Now:

```text
temp = []
```

Choose to exclude `1`.

Move to index `1`.

---

### Element `2`

#### Include `2`

```text
temp = [2]
```

Add:

```text
[2]
```

#### Exclude `2`

```text
temp = []
```

Add:

```text
[]
```

Final result:

```text
[
    [1,2],
    [1],
    [2],
    []
]
```

The order doesn't matter.

---

# 9. Recursion Tree

For:

```text
nums = [1,2,3]
```

The recursion tree is:

```text
                              []
                         /          \
                    Include 1     Exclude 1
                       [1]            []
                     /    \         /    \
                +2 [1,2]  -2 [1] +2 [2]  -2 []
                  / \       / \    / \      / \
                +3  -3    +3 -3  +3 -3    +3 -3
                /     \    /   \   /  \    /   \
           [1,2,3] [1,2] [1,3] [1] [2,3] [2] [3] []
```

There are:

```text
2 × 2 × 2 = 2³ = 8
```

subsets.

---

# 10. Why Are There `2^n` Subsets?

Every element has exactly two choices:

```text
Include
Exclude
```

For `n` elements:

```text
2 × 2 × 2 × ... × 2
```

`n` times.

Therefore:

```text
Number of subsets = 2^n
```

For example:

```text
n = 1 → 2 subsets
n = 2 → 4 subsets
n = 3 → 8 subsets
n = 4 → 16 subsets
```

---

# 11. Code

```cpp
class Solution {
public:
    void solve(vector<int>& nums,
               vector<vector<int>>& ans,
               int idx,
               vector<int>& temp) {

        if(idx == nums.size()) {
            ans.push_back(temp);
            return;
        }

        // Include current element
        temp.push_back(nums[idx]);
        solve(nums, ans, idx + 1, temp);

        // Undo inclusion
        temp.pop_back();

        // Exclude current element
        solve(nums, ans, idx + 1, temp);
    }

    vector<vector<int>> subsets(vector<int>& nums) {

        vector<vector<int>> ans;
        vector<int> temp;

        solve(nums, ans, 0, temp);

        return ans;
    }
};
```

---

# 12. Important Pattern

This question teaches one of the most important recursion patterns:

## Include / Exclude

```cpp
// Include
temp.push_back(nums[idx]);
solve(idx + 1);
temp.pop_back();

// Exclude
solve(idx + 1);
```

Whenever a question asks us to consider whether an element should be selected or not, think:

```text
Include
   |
Exclude
```

---

# 13. Pattern Recognition

When should I think of this approach?

If the question asks for:

* All subsets
* All subsequences
* All combinations where each element can be selected/not selected
* Generate all possible choices
* Pick or skip each element

Then immediately think:

```text
Recursion
+
Include / Exclude
```

---

# 14. Connection With Previous Question

This question and **Letter Combinations of a Phone Number** use the same fundamental idea, but the branching is different.

### Letter Combinations

For each digit:

```text
2 → a,b,c
```

We loop through all available choices:

```cpp
for(char ch : letters)
```

Pattern:

```text
For each choice:
    choose
    recurse
    undo
```

---

### Subsets

For each number:

```text
Include
Exclude
```

So there are exactly two branches:

```cpp
// Include
temp.push_back(nums[idx]);
solve(...);
temp.pop_back();

// Exclude
solve(...);
```

### Common Pattern

Both are:

```text
Choose
  ↓
Recursive call
  ↓
Undo
  ↓
Next choice
```

The difference is simply **how many choices each element provides**.

---

# 15. Mistakes / Things to Remember

### Mistake 1 — Forgetting `pop_back()`

If we include an element and don't remove it before the exclude branch, the next branch will have an incorrect `temp`.

Always remember:

```text
push
 ↓
recurse
 ↓
pop
 ↓
next branch
```

---

### Mistake 2 — Missing the empty subset

The empty subset:

```text
[]
```

is also a valid subset.

Our recursion naturally generates it when we exclude every element.

For:

```text
nums = [1,2,3]
```

one path is:

```text
exclude 1
   ↓
exclude 2
   ↓
exclude 3
   ↓
[]
```

---

# 16. Complexity

There are:

```text
2^n
```

subsets.

Each subset can contain up to `n` elements.

Therefore:

### Time Complexity

```text
O(n × 2^n)
```

### Auxiliary Space

Recursion depth:

```text
O(n)
```

Current subset:

```text
O(n)
```

So auxiliary space is:

```text
O(n)
```

### Output Space

The answer contains:

```text
2^n
```

subsets, each potentially containing `n` elements.

Therefore:

```text
O(n × 2^n)
```

---

# 17. Key Takeaways

### 🔑 1.

Every element has two decisions:

```text
Include
Exclude
```

### 🔑 2.

The recursion tree has:

```text
2 branches per element
```

Therefore:

```text
2^n leaves
```

### 🔑 3.

`temp` represents the current path/subset.

### 🔑 4.

`ans` stores all completed subsets.

### 🔑 5.

The fundamental backtracking pattern is:

```cpp
choose
recurse
undo
```

### 🔑 6.

The **Include / Exclude** pattern is extremely important for:

```text
Subsets
Subsequences
Combination problems
0/1 selection problems
Many backtracking problems
```

---

# ⭐ One-Line Revision

> **For every element, make two choices — include it or exclude it — recursively explore both choices, and backtrack after inclusion.**

---

# 🔗 Similar Questions

After Subsets, good questions to practice are:

1. **Subsets II** — LeetCode 90
2. **Permutations** — LeetCode 46
3. **Combination Sum** — LeetCode 39
4. **Combination Sum II** — LeetCode 40
5. **Letter Case Permutation** — LeetCode 784
6. **Palindrome Partitioning** — LeetCode 131
7. **Generate Parentheses** — LeetCode 22
