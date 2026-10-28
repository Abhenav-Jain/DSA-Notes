# LeetCode 84 — Largest Rectangle in Histogram

**Difficulty:** Hard  
**Pattern:** Monotonic Stack  
**Core Concepts:** Previous Smaller Element (PSE), Next Smaller Element (NSE)  
**Primary Approach:** PSE + NSE  
**Optimized Approach:** Single-Pass Monotonic Stack

---

# 1. Problem Statement

You are given an array `heights` representing the heights of bars in a histogram.

Each bar has a width of `1`.

Your task is to find the area of the largest rectangle that can be formed within the histogram.

### Example 1

```cpp
Input:
heights = [2, 1, 5, 6, 2, 3]

Output:
10
```

### Explanation

The largest rectangle is formed using bars with heights `5` and `6`.

The limiting height is `5`, and the width is `2`.

$$Area = Height \times Width$$

$$Area = 5 \times 2 = 10$$

### Example 2

```cpp
Input:
heights = [2, 4]

Output:
4
```

---

# 2. Core Observation — Think About Every Bar

The most important observation:

**Consider every bar as the minimum-height bar of a rectangle.**

For a particular bar `i`, we want to extend the rectangle as far as possible:

- Towards the left.
- Towards the right.

But we cannot cross a bar whose height is smaller than `heights[i]`.

Therefore, we need:

1. Previous Smaller Element (PSE).
2. Next Smaller Element (NSE).

These two elements determine the maximum possible width for a rectangle having `heights[i]` as its height.

---

# 3. Previous Smaller Element (PSE)

PSE is the nearest element on the left whose height is strictly smaller than the current element.

Example:

```cpp
heights = [2, 1, 5, 6, 2, 3]
```

PSE indices:

```cpp
PSE = [-1, -1, 1, 2, 1, 4]
```

Here:

- `PSE[0] = -1` because no smaller element exists on the left.
- `PSE[2] = 1` because index 1 has height 1, which is smaller than 5.
- `PSE[3] = 2` because index 2 has height 5, which is smaller than 6.

We use `-1` when no previous smaller element exists.

---

# 4. Next Smaller Element (NSE)

NSE is the nearest element on the right whose height is strictly smaller than the current element.

For the same input:

```cpp
heights = [2, 1, 5, 6, 2, 3]
```

NSE indices:

```cpp
NSE = [1, 6, 4, 4, 6, 6]
```

Here:

- `NSE[0] = 1` because index 1 has height 1.
- `NSE[2] = 4` because index 4 has height 2, which is smaller than 5.
- `NSE[5] = 6` because no smaller element exists on the right.

We use `n` when no next smaller element exists.

---

# 5. Deriving the Rectangle Width Formula

Suppose:

```cpp
heights[i] = 5
PSE[i] = 1
NSE[i] = 4
```

The rectangle can extend from index 2 to index 3.

The PSE and NSE themselves cannot be included because their heights are smaller.

Therefore:

$$Width = NSE[i] - PSE[i] - 1$$

Substituting:

$$Width = 4 - 1 - 1 = 2$$

Area:

$$Area = heights[i] \times Width$$

$$Area = 5 \times 2 = 10$$

### Final formula

$$Area[i] = heights[i] \times (NSE[i] - PSE[i] - 1)$$

Calculate this for every index and return the maximum area.

---

# 6. Approach 1 — PSE + NSE Using Monotonic Stack

This is your implemented approach.

We calculate PSE and NSE separately, then calculate the maximum rectangle area.

## A. NSE Function

```cpp
vector<int> NSE(vector<int>& heights) {
    int n = heights.size();

    vector<int> result(n, n);
    stack<int> st;

    for(int i = n - 1; i >= 0; i--) {

        while(!st.empty() &&
              heights[i] <= heights[st.top()]) {
            st.pop();
        }

        if(!st.empty()) {
            result[i] = st.top();
        }

        st.push(i);
    }

    return result;
}
```

### Explanation

- Traverse from right to left.
- Remove elements greater than or equal to the current height.
- The remaining stack top is the nearest strictly smaller element.
- If the stack is empty, the default answer remains `n`.

## B. PSE Function

```cpp
vector<int> PSE(vector<int>& heights) {
    int n = heights.size();

    vector<int> result(n, -1);
    stack<int> st;

    for(int i = 0; i < n; i++) {

        while(!st.empty() &&
              heights[i] <= heights[st.top()]) {
            st.pop();
        }

        if(!st.empty()) {
            result[i] = st.top();
        }

        st.push(i);
    }

    return result;
}
```

### Explanation

- Traverse from left to right.
- Remove elements greater than or equal to the current height.
- The remaining stack top is the nearest strictly smaller element.
- If the stack is empty, the default answer remains `-1`.

## C. Complete Solution

```cpp
class Solution {
public:

    vector<int> NSE(vector<int>& heights) {
        int n = heights.size();
        vector<int> result(n, n);
        stack<int> st;

        for(int i = n - 1; i >= 0; i--) {

            while(!st.empty() &&
                  heights[i] <= heights[st.top()]) {
                st.pop();
            }

            if(!st.empty()) {
                result[i] = st.top();
            }

            st.push(i);
        }

        return result;
    }

    vector<int> PSE(vector<int>& heights) {
        int n = heights.size();
        vector<int> result(n, -1);
        stack<int> st;

        for(int i = 0; i < n; i++) {

            while(!st.empty() &&
                  heights[i] <= heights[st.top()]) {
                st.pop();
            }

            if(!st.empty()) {
                result[i] = st.top();
            }

            st.push(i);
        }

        return result;
    }

    int largestRectangleArea(vector<int>& heights) {

        int max_area = 0;
        int n = heights.size();

        vector<int> pse = PSE(heights);
        vector<int> nse = NSE(heights);

        for(int i = 0; i < n; i++) {

            int area = heights[i] *
                       (nse[i] - pse[i] - 1);

            max_area = max(max_area, area);
        }

        return max_area;
    }
};
```

---

# 7. Complete Dry Run — Approach 1

Input:

```cpp
heights = [2, 1, 5, 6, 2, 3]
```

### Step 1: Calculate PSE

```cpp
PSE = [-1, -1, 1, 2, 1, 4]
```

### Step 2: Calculate NSE

```cpp
NSE = [1, 6, 4, 4, 6, 6]
```

### Step 3: Calculate Width and Area

| Index | Height | PSE | NSE | Width | Area |
|---:|---:|---:|---:|---:|---:|
| 0 | 2 | -1 | 1 | 1 | 2 |
| 1 | 1 | -1 | 6 | 6 | 6 |
| 2 | 5 | 1 | 4 | 2 | 10 |
| 3 | 6 | 2 | 4 | 1 | 6 |
| 4 | 2 | 1 | 6 | 4 | 8 |
| 5 | 3 | 4 | 6 | 1 | 3 |

Final answer:

```cpp
10
```

---

# 8. Why Do We Pop Greater Than OR Equal Elements?

In both PSE and NSE, we use:

```cpp
heights[i] <= heights[st.top()]
```

Our requirement is a **strictly smaller element**.

Suppose:

```cpp
heights = [2, 2, 2, 2]
```

If we do not remove equal heights, we may incorrectly treat an equal-height bar as the smaller boundary.

By removing equal heights, each bar gets the nearest strictly smaller boundary.

For this input:

```cpp
PSE = [-1, -1, -1, -1]
NSE = [4, 4, 4, 4]
```

Each bar can extend across the entire histogram.

Maximum area:

$$2 \times 4 = 8$$

---

# 9. Approach 2 — Single-Pass Monotonic Stack

Now let's optimize the implementation.

In Approach 1:

1. We calculate PSE.
2. We calculate NSE.
3. We calculate maximum area.

But notice something:

**When we encounter a smaller bar, we have discovered the NSE of the previous taller bars.**

At that moment, we can calculate their areas immediately.

We do not need to store separate PSE and NSE arrays.

## Core intuition

Maintain a monotonically increasing stack of indices.

When the current height is smaller than the height at the stack top:

1. Pop the top index.
2. The current index is its NSE.
3. The new stack top is its PSE.
4. Calculate the rectangle area.

### C++ Code

```cpp
class Solution {
public:
    int largestRectangleArea(vector<int>& heights) {

        int n = heights.size();
        stack<int> st;
        int max_area = 0;

        for(int i = 0; i <= n; i++) {

            int currHeight = (i == n) ? 0 : heights[i];

            while(!st.empty() &&
                  currHeight < heights[st.top()]) {

                int height = heights[st.top()];
                st.pop();

                int pse = st.empty() ? -1 : st.top();
                int nse = i;

                int width = nse - pse - 1;

                int area = height * width;

                max_area = max(max_area, area);
            }

            st.push(i);
        }

        return max_area;
    }
};
```

### Why `i <= n`?

We process one extra position after the last histogram bar.

At `i == n`, we use a virtual height of zero.

This forces all remaining positive-height bars to be popped and their areas calculated.

### Why does the new stack top become PSE?

Suppose we pop index `j`.

Before popping, all stack indices are in increasing order and their corresponding heights are increasing.

After popping, the new stack top is the nearest remaining index to the left with a strictly smaller height.

Therefore:

```cpp
int pse = st.empty() ? -1 : st.top();
```

### Why is `i` the NSE?

The current bar is smaller than the popped bar.

Because we are processing from left to right, it is the first smaller bar encountered on the right.

Therefore:

```cpp
int nse = i;
```

### Important implementation detail

The virtual index `n` is pushed after processing the final position. This is safe because the function returns immediately and never accesses `heights[n]`.

---

# 10. Dry Run — Single-Pass Approach

Input:

```cpp
heights = [2, 1, 5, 6, 2, 3]
```

We process the array with an additional virtual zero.

| Current index | Current height | Popped bar | PSE | NSE | Calculated area |
|---:|---:|---:|---:|---:|---:|
| 0 | 2 | — | — | — | — |
| 1 | 1 | 2 | -1 | 1 | 2 |
| 2 | 5 | — | — | — | — |
| 3 | 6 | — | — | — | — |
| 4 | 2 | 6 | 2 | 4 | 6 |
| 4 | 2 | 5 | 1 | 4 | 10 |
| 5 | 3 | — | — | — | — |
| 6 | 0 | 3 | 4 | 6 | 3 |
| 6 | 0 | 2 | 1 | 6 | 8 |
| 6 | 0 | 1 | -1 | 6 | 6 |

Maximum area:

```cpp
10
```

---

# 11. Comparison Between Both Approaches

| Feature | PSE + NSE | Single Pass |
|---|---|---|
| Traversals | 3 | 1 |
| PSE array | Required | Not required |
| NSE array | Required | Not required |
| Stack | Used separately | One stack |
| Time | O(n) | O(n) |
| Auxiliary space | O(n) | O(n) |
| Understanding | Easier initially | More advanced |
| Implementation | Modular | Compact |

**Important:** Both approaches have the same asymptotic time and space complexity. The single-pass approach reduces separate arrays and traversals, but does not change the Big-O complexity.

---

# 12. Edge Cases

### Case 1 — Increasing heights

```cpp
heights = [1, 2, 3, 4, 5]
```

Maximum area:

```cpp
9
```

Explanation:

Height 3 can extend across three bars.

### Case 2 — Decreasing heights

```cpp
heights = [5, 4, 3, 2, 1]
```

Maximum area:

```cpp
9
```

The rectangle with height 3 and width 3 has area 9.

### Case 3 — All equal heights

```cpp
heights = [2, 2, 2, 2]
```

Maximum area:

```cpp
8
```

### Case 4 — Single bar

```cpp
heights = [7]
```

Maximum area:

```cpp
7
```

### Case 5 — Zero heights

```cpp
heights = [0, 0, 0]
```

Maximum area:

```cpp
0
```

### Case 6 — Mixed heights

```cpp
heights = [2, 1, 2]
```

Maximum area:

```cpp
3
```

---

# 13. Common Mistakes

| Mistake | Why it is wrong |
|---|---|
| Using NSE - PSE as width | Both boundary bars must be excluded |
| Forgetting `-1` in width | Produces an off-by-one error |
| Finding Next Greater instead of Next Smaller | Histogram boundaries are determined by shorter bars |
| Keeping equal elements in PSE/NSE stack | We need strictly smaller boundaries |
| Calculating area using the boundary height | Rectangle height is the popped/current bar's height |
| Forgetting the final flush in single-pass | Remaining bars never get their maximum area calculated |
| Using `int` without considering constraints | Check the problem's constraints and use a sufficiently wide integer type if needed |

---

# 14. Interview Follow-Up Questions

**Q1. Why do we need both PSE and NSE?**

They determine the maximum range over which a rectangle of a particular bar's height can extend.

**Q2. Why is the width `NSE - PSE - 1`?**

The PSE and NSE are smaller boundary bars and cannot be included in the rectangle.

**Q3. Why is a monotonic increasing stack used?**

It allows us to efficiently identify previous and next smaller boundaries.

**Q4. What is the role of the virtual zero in the single-pass solution?**

It forces all remaining bars to be popped so their maximum possible areas can be evaluated.

**Q5. Can this problem be solved using a segment tree or divide and conquer?**

Yes. Other approaches exist, but the monotonic stack solution is the standard efficient solution.

**Q6. Why is the overall complexity O(n), despite the while loop inside the for loop?**

Each index enters the stack once and leaves it at most once. Therefore, total stack operations are linear.

---

# 15. Connection With Other Monotonic Stack Problems

| Problem | Pattern |
|---|---|
| LC 496 — Next Greater Element I | NGE |
| LC 739 — Daily Temperatures | NGE |
| LC 503 — Next Greater Element II | Circular NGE |
| LC 1475 — Final Prices | NSE |
| LC 901 — Online Stock Span | PGE |
| LC 1944 — Number of Visible People | Visibility Counting |
| LC 84 — Largest Rectangle in Histogram | PSE + NSE |

### Pattern Recognition

Whenever a problem asks for the largest rectangle or maximum width under a height constraint, think about:

- Previous Smaller Element.
- Next Smaller Element.
- Monotonic Increasing Stack.
- Width between two boundaries.

---

# 16. Final Revision Cheat Sheet

```text
Problem:
LC 84 — Largest Rectangle in Histogram

Pattern:
Monotonic Stack

Primary approach:
PSE + NSE

PSE:
Nearest strictly smaller element on left

NSE:
Nearest strictly smaller element on right

Pop condition:
heights[i] <= heights[st.top()]

Width:
NSE[i] - PSE[i] - 1

Area:
heights[i] * width

Optimized approach:
Single-Pass Monotonic Stack

Single-pass idea:
When a smaller bar arrives:
    Pop previous taller bar
    Current index = NSE
    New stack top = PSE
    Calculate area

Time:
O(n)

Space:
O(n)
```

## Final Takeaway

**Largest Rectangle in Histogram = Every bar acts as a possible minimum height of a rectangle.**

Find its nearest strictly smaller boundary on both sides, calculate the maximum width, and multiply by its height.

The most important formula to remember:

$$\boxed{Area[i] = heights[i] \times (NSE[i] - PSE[i] - 1)}$$
