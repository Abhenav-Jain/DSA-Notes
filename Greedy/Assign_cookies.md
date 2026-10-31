# Assign Cookies — LeetCode 455

**Pattern:** Greedy + Sorting + Two Pointers  
**Difficulty:** Easy  
**Core Idea:** Assign the smallest sufficient cookie to the least greedy unsatisfied child.

## 1. Problem Understanding

We are given two arrays:

- `g[i]`: Minimum cookie size required by child `i` to be content.
- `s[j]`: Size of cookie `j`.

A child becomes content if:

\[
s[j] \geq g[i]
\]

Each child can receive at most one cookie, and each cookie can be assigned to at most one child.

**Goal:** Maximize the number of content children.

## 2. Greedy Intuition

Consider:

```cpp
g = [1, 2, 3]
s = [1, 1]
```

The child with greed `1` can be satisfied by a cookie of size `1`.

But a child with greed `2` cannot be satisfied by a cookie of size `1`.

Therefore, we should:
1. Consider children in increasing order of greed.
2. Consider cookies in increasing order of size.
3. Assign the smallest sufficient cookie to the current child.
4. Skip a cookie if it is too small.

**Greedy Strategy:**

> Always satisfy the least greedy unsatisfied child using the smallest available cookie that can satisfy them.

Why? Because saving a larger cookie for a child with higher greed preserves flexibility.

## 3. Optimal Approach — Sorting + Two Pointers

### Step 1: Sort both arrays

```cpp
sort(g.begin(), g.end());
sort(s.begin(), s.end());
```

Both arrays are sorted in ascending order.

### Step 2: Initialize two pointers

```cpp
int l = 0; // Children pointer
int r = 0; // Cookies pointer
```

### Step 3: Compare greed and cookie size

There are two cases:

**Case 1: Cookie can satisfy the child**

```cpp
if(g[l] <= s[r]){
    l++;
    r++;
}
```

The child is satisfied, so move both pointers forward.

**Case 2: Cookie is too small**

```cpp
if(g[l] > s[r]){
    r++;
}
```

Discard the current cookie and try the next larger one.

Continue until either all children or all cookies have been considered.

## 4. C++ Implementation

```cpp
class Solution {
public:
    int findContentChildren(vector<int>& g, vector<int>& s) {
        int n = g.size();
        int m = s.size();

        int l = 0;
        int r = 0;

        sort(g.begin(), g.end());
        sort(s.begin(), s.end());

        while(l < n && r < m) {
            if(g[l] <= s[r]) {
                l++;
                r++;
            }

            while(l < n && r < m && g[l] > s[r]) {
                r++;
            }
        }

        return l;
    }
};
```

**Note:** The inner `while` skips all cookies that are too small for the current child. It is correct because both arrays are sorted.

An equivalent implementation uses a single `if-else` inside the main loop:

```cpp
while(l < n && r < m) {
    if(g[l] <= s[r]) {
        l++;
    }
    r++;
}
```

Both implementations produce the same result.

## 5. Dry Run

```cpp
g = [1, 2, 3]
s = [1, 1]
```

Arrays are already sorted.

| Step | l | r | g[l] | s[r] | Decision |
|---|---:|---:|---:|---:|---|
| 1 | 0 | 0 | 1 | 1 | Match |
| 2 | 1 | 1 | 2 | 1 | Skip cookie |
| 3 | 1 | 2 | — | — | Cookies exhausted |

Only one child is satisfied.

**Output: `1`**

## 6. Why Is This Greedy Approach Correct?

We can justify the strategy using an exchange argument.

Suppose we are considering the least greedy remaining child and the smallest remaining cookie.

- If the cookie is too small, it cannot satisfy this child.
- Since all remaining children have greed at least as large, this cookie cannot satisfy any of them either. So discarding it is safe.
- If the cookie is sufficient, assigning it to the least greedy child is safe: any larger cookie that could satisfy this child can also be saved for a child with a higher greed requirement.

Thus, these decisions never reduce the maximum possible number of satisfied children.

## 7. Complexity Analysis

Let:
- `n` = Number of children
- `m` = Number of cookies

| Operation | Time Complexity |
|---|---|
| Sort children | O(n log n) |
| Sort cookies | O(m log m) |
| Two-pointer traversal | O(n + m) |

**Total Time Complexity:**

\[
O(n\log n + m\log m)
\]

**Auxiliary Space:** O(1) for the traversal variables; sorting may use implementation-dependent extra space.

The two-pointer traversal is linear because both pointers only move forward. Even though there is a nested `while`, the cookie pointer advances at most `m` times overall.

## 8. Important Edge Cases

| Input | Output | Reason |
|---|---:|---|
| `g = [1,2,3], s = [1,1]` | 1 | Only one child can be satisfied |
| `g = [1,2], s = [1,2,3]` | 2 | Both children can be satisfied |
| `g = [5], s = [1,2,3]` | 0 | No sufficient cookie |
| `g = [1,2,3], s = []` | 0 | No cookies available |
| `g = [], s = [1,2]` | 0 | No children |
| `g = [1,1,2], s = [1,1]` | 2 | Two children can be satisfied |

## 9. Common Mistakes

1. **Not sorting the arrays:** The greedy allocation order may fail.
2. **Using a large cookie for a low-greed child unnecessarily:** This can waste resources needed by more demanding children.
3. **Incrementing only the child pointer after a successful match:** The cookie must also be consumed.
4. **Not skipping insufficient cookies:** They cannot satisfy the current or any later child.
5. **Assuming nested loops mean O(n × m):** The inner loop advances the same cookie pointer monotonically, so total traversal remains O(n + m).

## 10. Pattern Recognition — When Should You Think of This Greedy Strategy?

Look for situations where:

- You have two groups of elements.
- One group has requirements and the other has resources.
- Each resource can be used only once.
- A resource can satisfy a requirement if it meets a threshold.
- You want to maximize the number of successful assignments.

Examples:
- Assigning jobs to workers based on minimum skill.
- Matching tasks with machines based on capacity.
- Allocating minimum-sized resources to requirements.

## 11. Interview Follow-up Questions

**Q1. Why do we sort both arrays?**

To process the least demanding child and smallest available cookie first, making greedy allocation possible.

**Q2. Why can we discard an insufficient cookie?**

Because every remaining child has an equal or higher greed requirement.

**Q3. Why do we assign the smallest sufficient cookie?**

It preserves larger cookies for children who may need them.

**Q4. Is the solution optimal?**

Yes. It achieves the maximum number of satisfied children with the standard comparison-based sorting and linear traversal approach.

**Q5. Why is the two-pointer traversal O(n + m)?**

Each pointer moves only forward and never exceeds its array's length.

## 12. Quick Revision Cheat Sheet

| Concept | Remember |
|---|---|
| Pattern | Greedy + Sorting + Two Pointers |
| Sort order | Ascending |
| Child pointer | `l` |
| Cookie pointer | `r` |
| Successful match | `g[l] <= s[r]` |
| Insufficient cookie | Skip it |
| Return value | `l` |
| Time | O(n log n + m log m) |
| Main intuition | Smallest sufficient resource first |

**One-line takeaway:**

> Sort requirements and resources, then greedily assign the smallest sufficient resource to each least-demanding unsatisfied requirement.
