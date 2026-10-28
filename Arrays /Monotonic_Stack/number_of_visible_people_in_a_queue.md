# LeetCode 1944 — Number of Visible People in a Queue

**Difficulty:** Hard  
**Pattern:** Monotonic Stack  
**Core Concept:** Visibility Counting + Next Greater Element  
**Traversal:** Left to Right  
**Stack Stores:** Indices  
**Primary Approach:** Monotonic Decreasing Stack

---

# 1. Problem Statement

There are `n` people standing in a queue. Each person has a unique height.

A person can see another person to their right if everyone standing between them is shorter than both of them.

In other words, person `i` can see person `j` if:

1. `i < j`
2. `heights[i] > heights[j]`
3. Every person between `i` and `j` is shorter than `heights[j]`.

**Task:** Return an array where `answer[i]` represents the number of people that person `i` can see to their right.

## Example

```cpp
Input:
heights = [10, 6, 8, 5, 11, 9]

Output:
[3, 1, 2, 1, 1, 0]
```

### Visual understanding

```text
Person:    0   1   2   3   4   5
Height:   10   6   8   5  11   9
```

- Person 0 (height 10) sees 6, 8 and 11.
- Person 1 (height 6) sees 8.
- Person 2 (height 8) sees 5 and 11.
- Person 3 (height 5) sees 11.
- Person 4 (height 11) sees 9.
- Person 5 sees nobody.

Final answer:

```cpp
[3, 1, 2, 1, 1, 0]
```

**Important:** A taller person blocks everyone standing behind them from the perspective of the person before them.

---

# 2. Core Observation — Visibility and Next Greater Element

The most important observation is:

For every person, we need to count visible people on their right until the first person taller than them is encountered.

There are two types of people who can be visible:

1. People shorter than the current person, provided they are not blocked by another person.
2. The first person taller than the current person, if one exists.

The first taller person is visible, but people behind that person are blocked.

### Example

```text
heights = [10, 6, 8, 5, 11]
```

For person 0 (height 10):

- Height 6 is visible.
- Height 8 is visible.
- Height 5 is blocked by height 8.
- Height 11 is visible.
- Anyone after 11 is blocked.

Therefore, person 0 sees 3 people.

This is closely related to the **Next Greater Element (NGE)** pattern.

---

# 3. Brute Force Approach

For every person `i`, traverse all people to their right.

Check whether each person is visible by maintaining the maximum height seen between `i` and the current person.

A person `j` is visible when their height is greater than every person between `i` and `j`, and the person at `i` is taller than `j`.

### C++ Code

```cpp
class Solution {
public:
    vector<int> canSeePersonsCount(vector<int>& heights) {
        int n = heights.size();
        vector<int> ans(n, 0);

        for(int i = 0; i < n; i++) {
            int maxHeight = 0;

            for(int j = i + 1; j < n; j++) {
                if(heights[j] > maxHeight) {
                    ans[i]++;
                    maxHeight = heights[j];
                }

                if(heights[j] > heights[i]) {
                    break;
                }
            }
        }

        return ans;
    }
};
```

### Complexity

- Time: O(n²) worst case.
- Auxiliary space: O(1), excluding the output array.

Why O(n²)?

In a decreasing array, each person may scan many people to their right.

---

# 4. Optimized Approach — Monotonic Stack

We can solve the problem in O(n) time using a monotonic stack.

## Stack intuition

We traverse the array from left to right.

The stack stores indices of people whose visibility counts may still be affected by future people.

We maintain a strictly decreasing sequence of heights from bottom to top.

For every current person `i`:

### Step 1: Pop shorter or equal people

```cpp
while(!st.empty() && heights[i] >= heights[st.top()])
```

Every popped person can see the current person.

Therefore:

```cpp
result[st.top()] += 1;
```

Then remove that person from the stack.

Why?

The current person is taller than or equal to the popped person and blocks their view of people standing further right.

### Step 2: Count the remaining stack-top person

```cpp
if(!st.empty()) {
    result[st.top()] += 1;
}
```

If a person remains on the stack, that person is taller than the current person.

The remaining stack-top person can see the current person.

Only the nearest remaining taller person is visible from that position.

### Step 3: Push the current index

```cpp
st.push(i);
```

The current person may be visible to someone standing further left.

---

# 5. Optimized C++ Solution — Your Approach

```cpp
class Solution {
public:
    vector<int> canSeePersonsCount(vector<int>& heights) {
        int n = heights.size();

        stack<int> st;
        vector<int> result(n, 0);

        for(int i = 0; i < n; i++) {

            while(!st.empty() &&
                  heights[i] >= heights[st.top()]) {

                result[st.top()] += 1;
                st.pop();
            }

            if(!st.empty()) {
                result[st.top()] += 1;
            }

            st.push(i);
        }

        return result;
    }
};
```

---

# 6. Line-by-Line Explanation

### Initialization

```cpp
int n = heights.size();
stack<int> st;
vector<int> result(n, 0);
```

- `n`: Number of people.
- `st`: Stores indices of people.
- `result`: Stores the number of visible people for every index.

### Traversal

```cpp
for(int i = 0; i < n; i++)
```

We process people from left to right.

### Pop smaller or equal people

```cpp
while(!st.empty() && heights[i] >= heights[st.top()])
```

The current person is taller than or equal to the stack-top person.

The stack-top person can see the current person.

```cpp
result[st.top()] += 1;
st.pop();
```

Increment the visibility count and remove the blocked person.

### Count the nearest remaining taller person

```cpp
if(!st.empty()) {
    result[st.top()] += 1;
}
```

The remaining stack-top person is taller than the current person.

That person can also see the current person.

### Push current person

```cpp
st.push(i);
```

Store the current person's index for future processing.

---

# 7. Complete Dry Run

Input:

```cpp
heights = [10, 6, 8, 5, 11, 9]
```

Stack stores indices.

| Current index | Height | Popped indices | Remaining stack before push | Result updated |
|---|---:|---|---|---|
| 0 | 10 | None | [] | — |
| 1 | 6 | None | [0] | result[0] += 1 |
| 2 | 8 | [1] | [0] | result[1] += 1, result[0] += 1 |
| 3 | 5 | None | [0,2] | result[2] += 1 |
| 4 | 11 | [3,2,0] | [] | result[3], result[2], result[0] increment |
| 5 | 9 | None | [4] | result[4] += 1 |

Final output:

```cpp
[3, 1, 2, 1, 1, 0]
```

---

# 8. Why Does the Algorithm Work?

Consider the stack before processing a new person.

The stack contains people with strictly decreasing heights.

When the current person arrives:

**Case 1: Current person is taller than or equal to stack top**

The stack-top person can see the current person. No person between them exists in the queue that could block visibility because the stack-top person is the most recent unresolved candidate.

After counting, the current person blocks the popped person's view of anyone further right.

**Case 2: Stack is not empty after popping**

The remaining top is taller than the current person.

It is the nearest taller person whose view can reach the current person.

Therefore, it can see the current person.

**Case 3: Stack is empty**

No unresolved person remains to the left who can see the current person.

Push the current person and continue.

This ensures every visible pair is counted exactly once.

---

# 9. Why Do We Use `>=` Instead of `>`?

This is a critical detail.

The problem guarantees that all heights are unique.

Therefore, equal heights do not occur in valid input.

Even so, the `>=` condition is harmless and expresses the intended stack maintenance.

The important semantic rule is that a person must be taller than the person they can see. An equal-height person cannot see the current person under the problem's definition.

Since heights are unique, `>` and `>=` behave identically on valid input.

**Interview note:** Do not confuse this problem's visibility definition with LC 901, where equal prices are included in stock span.

---

# 10. Alternative Approach — Right to Left

We can also process the array from right to left.

For each person, maintain a decreasing stack of heights/indices representing people who may be visible to someone on their left.

For current index `i`:

1. While the stack is not empty and `heights[i] > heights[st.top()]`, pop and count each popped person.
2. If the stack is still non-empty, count the nearest taller person.
3. Push current index.

### C++ Code

```cpp
class Solution {
public:
    vector<int> canSeePersonsCount(vector<int>& heights) {
        int n = heights.size();
        vector<int> ans(n, 0);
        stack<int> st;

        for(int i = n - 1; i >= 0; i--) {

            while(!st.empty() &&
                  heights[i] > heights[st.top()]) {
                ans[i]++;
                st.pop();
            }

            if(!st.empty()) {
                ans[i]++;
            }

            st.push(i);
        }

        return ans;
    }
};
```

### Complexity

- Time: O(n)
- Space: O(n)

Both approaches are valid.

**Difference:** In the left-to-right approach, we update the answer of previous people. In the right-to-left approach, we directly calculate the answer for the current person.

---

# 11. Complexity Analysis

Let `n` be the number of people.

### Time Complexity

Every index:

- Is pushed exactly once.
- Can be popped at most once.

Therefore:

$$O(n)$$

### Auxiliary Space Complexity

The stack can contain up to `n` indices.

$$O(n)$$

The output vector also requires O(n) space.

---

# 12. Common Mistakes

| Mistake | Explanation |
|---|---|
| Storing heights instead of indices in the primary solution | We need to update `result[st.top()]` |
| Forgetting to increment result before popping | The popped person can see the current person |
| Removing the final `if(!st.empty())` | The nearest taller person is also visible |
| Incrementing result for every element in the stack | Not every person in the stack can see the current person |
| Confusing visibility with NGE | A person can see multiple shorter people before the first taller person |
| Using nested loops in the final solution | Can lead to O(n²) time |

---

# 13. Connection With Other Monotonic Stack Problems

| LeetCode | Problem | Main Concept |
|---|---|---|
| 496 | Next Greater Element I | NGE |
| 739 | Daily Temperatures | NGE Index |
| 503 | Next Greater Element II | Circular NGE |
| 1475 | Final Prices | NSE |
| 901 | Online Stock Span | PGE |
| 1944 | Number of Visible People | Visibility + Monotonic Stack |
| 84 | Largest Rectangle in Histogram | Previous/Next Smaller Element |

---

# 14. Interview Questions

**Q1. Why is a monotonic stack useful here?**

It maintains the relevant candidates for visibility and removes people who are blocked by a newly encountered taller person.

**Q2. Why do we store indices?**

Because we need to update the visibility count of a specific person in the answer array.

**Q3. Why can each person be popped only once?**

Once removed, that index never enters the stack again. Every index is pushed exactly once.

**Q4. Why is the nearest taller person also visible?**

Because all people between the current person and that nearest taller person are shorter than the current person and cannot block the view.

**Q5. Can this problem be solved from right to left?**

Yes. The answer can be calculated directly for each current index by popping shorter visible people and counting the nearest taller person.

---

# 15. Final Revision Cheat Sheet

```text
Problem:
LC 1944 — Number of Visible People in a Queue

Pattern:
Monotonic Stack

Primary traversal:
Left to Right

Stack stores:
Indices

Stack property:
Strictly decreasing heights (bottom to top)

Pop condition:
heights[i] >= heights[st.top()]

On every pop:
result[st.top()]++

After popping:
If stack is not empty:
result[st.top()]++

Finally:
st.push(i)

Time:
O(n)

Space:
O(n)
```

## Core Takeaway

**Every person can see all the shorter people encountered before the first taller person, plus that first taller person if it exists.**

The monotonic stack helps count these visible pairs efficiently without checking every pair individually.
