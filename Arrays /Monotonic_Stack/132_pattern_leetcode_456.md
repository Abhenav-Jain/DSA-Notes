# LeetCode 456 — 132 Pattern
## Monotonic Stack | Prefix Minimum | Pattern Recognition

**Difficulty:** Medium  
**Topic:** Arrays, Stack, Monotonic Stack  
**LeetCode:** https://leetcode.com/problems/132-pattern/  
**Target Complexity:** O(n) Time | O(n) Space

---

## 1. Problem Statement

Given an array of integers `nums`, return `true` if there exists a 132 pattern in the array.

A 132 pattern is a subsequence of three integers:

- `nums[i]` = 1
- `nums[j]` = 3
- `nums[k]` = 2

Such that:

$$i < j < k$$

And:

$$nums[i] < nums[k] < nums[j]$$

**Important:** The three elements do not need to be contiguous. Only their relative indices and values matter.

### Example 1

```cpp
nums = {1, 2, 3, 4};
Output = false;
```

No three elements satisfy the 132 relationship.

### Example 2

```cpp
nums = {3, 1, 4, 2};
Output = true;
```

Choose:

```cpp
nums[i] = 1;
nums[j] = 4;
nums[k] = 2;
```

Therefore:

$$1 < 2 < 4$$

And:

$$i < j < k$$

Hence, a valid 132 pattern exists.

### Example 3

```cpp
nums = {-1, 3, 2, 0};
Output = true;
```

One valid combination is:

```cpp
-1, 3, 2
```

---

# 2. Understanding the Mathematical Relationship

The 132 pattern can be represented as:

$$nums[i] < nums[k] < nums[j]$$

With:

$$i < j < k$$

Think of the elements as:

| Element | Role | Requirement |
|---|---|---|
| `nums[i]` | 1 | Smallest |
| `nums[j]` | 3 | Largest |
| `nums[k]` | 2 | Between 1 and 3 |

The most important observation:

**For every possible middle element (3), we need:**

1. A smaller element on its left.
2. An element on its right that is smaller than the middle element.
3. That right-side element must be greater than the left-side element.

---

# 3. Approach 1 — Brute Force

### Intuition

Try every possible combination of three indices:

- Select `i`.
- Select `j` after `i`.
- Select `k` after `j`.
- Check whether the 132 condition is satisfied.

### Code

```cpp
class Solution {
public:
    bool find132pattern(vector<int>& nums) {
        int n = nums.size();

        for(int i = 0; i < n; i++) {
            for(int j = i + 1; j < n; j++) {

                if(nums[j] > nums[i]) {

                    for(int k = j + 1; k < n; k++) {

                        if(nums[k] > nums[i] &&
                           nums[k] < nums[j]) {
                            return true;
                        }
                    }
                }
            }
        }

        return false;
    }
};
```

### Complexity

- Time: O(n³)
- Auxiliary Space: O(1)

### Limitation

For every element, we potentially explore all pairs of elements to its right. This becomes expensive for large arrays.

---

# 4. Approach 2 — Prefix Minimum + Nested Loop

### Optimization Observation

In the brute-force solution, we repeatedly search for a suitable `nums[i]`.

But for a fixed middle index `j`, we don't need to examine every previous element.

We only need the minimum element present before `j`.

Define:

$$min_i = \min(nums[0], nums[1], \ldots, nums[j])$$

Since `min_i` comes from the prefix ending at `j`, and the inner loop only considers indices after `j`, the required index ordering is preserved.

For each `j`, check whether there exists a `k > j` such that:

$$min_i < nums[k] < nums[j]$$

### Code

```cpp
class Solution {
public:
    bool find132pattern(vector<int>& nums) {
        int n = nums.size();

        int min_i = nums[0];

        for(int j = 1; j < n; j++) {

            min_i = min(min_i, nums[j]);

            for(int k = j + 1; k < n; k++) {

                if(nums[k] > min_i &&
                   nums[k] < nums[j]) {
                    return true;
                }
            }
        }

        return false;
    }
};
```

### Complexity

- Time: O(n²)
- Auxiliary Space: O(1)

### Why is this better?

Instead of searching for the left-side minimum repeatedly, we maintain it in constant time.

**Optimization achieved:** O(n³) → O(n²).

---

# 5. Approach 3 — Optimized Monotonic Stack

<badge>Optimal Approach</badge>

### Target Complexity

- Time: O(n)
- Auxiliary Space: O(n)

### Step 1: Think from Right to Left

The 132 relationship is:

$$nums[i] < nums[k] < nums[j]$$

We want to identify a potential `2` element efficiently.

If we traverse from right to left:

- Current element can act as a potential `1`.
- A larger element to its right can act as `3`.
- A smaller element to the right, which was popped from the stack, can act as `2`.

This leads to a powerful monotonic stack approach.

### Step 2: Understand the Two Variables

We maintain:

**1. Stack (`st`)**

Stores potential candidates for the `3` element.

The stack maintains a decreasing order from bottom to top.

**2. `num_z`**

Stores a potential `2` element.

Initially:

```cpp
int num_z = INT_MIN;
```

Why?

Because no valid `2` candidate has been discovered yet.

### Step 3: The Most Important Observation

Consider:

```cpp
while(!st.empty() && nums[i] > st.top()) {
    num_z = st.top();
    st.pop();
}
```

Suppose:

```cpp
Current element = 4
Stack top = 2
```

Since:

$$4 > 2$$

We have found a pair:

```text
2 = potential 2
4 = potential 3
```

Therefore, we save:

```cpp
num_z = 2;
```

Now we search for an element smaller than `2` while traversing leftward.

```cpp
if(nums[i] < num_z) {
    return true;
}
```

If this condition becomes true:

$$nums[i] < num_z < nums[j]$$

The 132 pattern is found.

### Step 4: Complete Optimized Code

```cpp
class Solution {
public:
    bool find132pattern(vector<int>& nums) {

        int n = nums.size();

        stack<int> st;

        int num_z = INT_MIN;

        for(int i = n - 1; i >= 0; i--) {

            // Check whether current element
            // can act as the '1' in 132.
            if(nums[i] < num_z) {
                return true;
            }

            // Find potential '2' candidates.
            while(!st.empty() && nums[i] > st.top()) {

                num_z = st.top();

                st.pop();
            }

            // Current element becomes a candidate for '3'.
            st.push(nums[i]);
        }

        return false;
    }
};
```

---

# 6. Detailed Dry Run

Input:

```cpp
nums = {3, 1, 4, 2};
```

Initial state:

```text
Stack = []
num_z = INT_MIN
```

We traverse from right to left.

| Index | Current | Stack before | Operation | num_z |
|---|---:|---|---|---:|
| 3 | 2 | [] | Push 2 | INT_MIN |
| 2 | 4 | [2] | Pop 2, push 4 | 2 |
| 1 | 1 | [4] | Check 1 < 2 | 2 |

At index 1:

```cpp
nums[1] < num_z

1 < 2
```

Condition is true.

Therefore:

```cpp
return true;
```

132 Pattern:

```text
1 → 4 → 2
```

---

# 7. Why Does This Algorithm Work?

Let's understand the correctness through three observations.

### Observation 1 — Right-to-Left Traversal

When we process index `i`, all elements to its right have already been processed.

Therefore, any candidate `2` or `3` discovered from the right automatically has an index greater than `i`.

### Observation 2 — Stack Popping Finds a Valid Pair

Whenever:

```cpp
nums[i] > st.top()
```

The popped element is smaller than the current element.

The current element is a possible `3`, and the popped element is a possible `2`.

We store the popped value in `num_z`.

### Observation 3 — The Final Comparison

When:

```cpp
nums[i] < num_z
```

We have:

- A current element smaller than `num_z`.
- A `num_z` candidate that was previously popped by a larger element to its right.

Thus, the required relationship exists:

$$nums[i] < num_z < nums[j]$$

And the index order is:

$$i < j < k$$

Therefore, a valid 132 pattern exists.

---

# 8. Why Do We Keep Only One `num_z`?

This is one of the most important ideas.

Suppose we discover multiple potential `2` candidates.

We want to retain the **largest valid `2` candidate** encountered so far.

Why?

Because we are searching for:

$$nums[i] < num_z$$

A larger `num_z` makes it easier for an element on the left to satisfy this condition.

The algorithm updates `num_z` whenever another stack element is popped.

This maintains the most useful candidate for future comparisons.

---

# 9. Edge Cases

| Input | Expected | Reason |
|---|---|---|
| `{1,2,3,4}` | false | Strictly increasing |
| `{4,3,2,1}` | false | Strictly decreasing |
| `{3,1,4,2}` | true | Valid 1, 3, 2 |
| `{-1,3,2,0}` | true | Negative values supported |
| `{1,0,1,-4,-3}` | false | No valid pattern |
| `{1,2,1}` | false | No valid 132 triplet |
| `{3,5,0,3,4}` | true | 0, 5, 4 |

**Important:** The comparison must remain strict. Equal values do not form a valid 132 pattern.

---

# 10. Common Mistakes

### Mistake 1 — Using the Wrong Traversal Direction

The standard optimized approach traverses from right to left.

This allows the stack to maintain candidates that occur after the current index.

### Mistake 2 — Using `>=` Instead of `>`

Correct:

```cpp
nums[i] > st.top()
```

The problem requires a strict inequality.

### Mistake 3 — Checking the Wrong Comparison

Correct:

```cpp
if(nums[i] < num_z)
```

We need the current left-side element to be smaller than the stored `2` candidate.

### Mistake 4 — Confusing Stack and `num_z`

- Stack: potential `3` candidates.
- `num_z`: potential `2` candidate.

They have different responsibilities.

### Mistake 5 — Thinking the Stack Stores Indices

This implementation stores values, not indices.

The right-to-left traversal and the order in which values are popped establish the necessary positional relationship.

---

# 11. Complexity Analysis

| Approach | Time | Auxiliary Space |
|---|---:|---:|
| Brute Force | O(n³) | O(1) |
| Prefix Minimum | O(n²) | O(1) |
| Monotonic Stack | O(n) | O(n) |

### Why is the stack approach O(n)?

Every element:

- Enters the stack at most once.
- Leaves the stack at most once.

Therefore, the total number of stack operations is linear.

$$O(n) + O(n) = O(n)$$

---

# 12. Interview Follow-up Questions

**Q1. Why do we traverse from right to left?**

Because we want to identify potential `2` and `3` elements before checking whether a smaller `1` exists to their left.

**Q2. What does `num_z` represent?**

It represents the largest useful candidate for the middle-right `2` value discovered so far.

**Q3. Why is a monotonic stack suitable here?**

Because we need to efficiently maintain and eliminate candidates based on comparisons with the current element.

**Q4. Can the algorithm handle negative numbers?**

Yes. It uses comparisons rather than assumptions about positive values.

**Q5. What is the optimal complexity?**

O(n) time and O(n) auxiliary space.

---

# 13. Pattern Recognition — When Should You Think of This Approach?

Look for these signals:

- A subsequence of three elements.
- A strict relationship between their values.
- Their relative index order matters.
- You need to identify a smaller element to the left and two related elements to the right.
- A right-to-left traversal can maintain useful candidates.

This is a classic example of using a monotonic stack to solve a non-obvious subsequence problem.

---

# 14. Final Revision Cheat Sheet

```text
Problem: LC 456 — 132 Pattern

Required:
i < j < k

nums[i] < nums[k] < nums[j]

Approach:
Traverse Right to Left

Stack:
Potential 3 candidates

num_z:
Potential 2 candidate

If nums[i] < num_z:
    Pattern Found

While stack not empty AND nums[i] > stack.top():
    num_z = stack.top()
    stack.pop()

Push current element.

TC: O(n)
SC: O(n)
```

## Key Takeaway

The biggest learning from this problem is not just the monotonic stack implementation.

It is understanding how to transform a three-element subsequence condition into a right-to-left candidate-maintenance problem.

**Your progression:**

- Brute Force: O(n³)
- Prefix Minimum: O(n²)
- Monotonic Stack: O(n)

You independently derived all three approaches. That's exactly the kind of progression that builds strong DSA problem-solving skills.
