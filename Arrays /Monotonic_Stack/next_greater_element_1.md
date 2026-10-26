# LeetCode 496 — Next Greater Element I

**Pattern:** Monotonic Decreasing Stack + HashMap  
**Difficulty:** Easy  
**Time Complexity:** O(n1 + n2) Average  
**Space Complexity:** O(n2)

---

## 1. Problem Understanding

Given two integer arrays `nums1` and `nums2`, where `nums1` is a subset of `nums2` and all elements in `nums2` are distinct.

For every element in `nums1`, find its Next Greater Element in `nums2`.

The Next Greater Element of an element `x` is the first element to its right that is strictly greater than `x`.

If no greater element exists, return `-1`.

### Example

```cpp
nums1 = {4, 1, 2}
nums2 = {1, 3, 4, 2}
```

Output:

```cpp
{-1, 3, -1}
```

Explanation:

- For `4`, no greater element exists on its right → `-1`.
- For `1`, the first greater element is `3`.
- For `2`, no greater element exists on its right → `-1`.

---

## 2. Brute Force Approach

For every element of `nums1`:

1. Find its position in `nums2`.
2. Traverse the elements to its right.
3. Find the first element greater than it.
4. If no greater element exists, return `-1`.

### Complexity

Searching for the position and then finding NGE can take O(n2) per element.

Overall:

- Time: O(n1 × n2)
- Auxiliary Space: O(1), excluding output.

We can optimize this using Monotonic Stack + HashMap.

---

## 3. Core Concept — Next Greater Element

For every element `nums[i]`, we need:

$$
NGE[i] = \text{First } nums[j] > nums[i]
$$

subject to:

$$
j > i
$$

There are two important conditions:

1. The element must be on the right side.
2. It must be strictly greater, not equal.

Example:

```cpp
nums = {2, 2, 3}
```

For the first `2`, the next greater element is `3`, not the second `2`.

---

## 4. Why Monotonic Stack?

A normal stack does not maintain any ordering.

A Monotonic Stack maintains elements in a particular order.

For Next Greater Element, we use a **Monotonic Decreasing Stack**.

In this implementation:

- Stack stores indices, not values.
- Values corresponding to those indices remain strictly decreasing from bottom to top.
- We traverse the array from right to left.

### Why decreasing?

Suppose:

```cpp
nums = {1, 3, 4, 2}
```

When processing an element, we remove all elements from the stack that are smaller than or equal to the current element.

These removed elements cannot be the Next Greater Element of the current element.

After removing them, the top of the stack is the nearest valid greater candidate.

---

## 5. The Most Important Logic

```cpp
while(!st.empty() && nums[i] >= nums[st.top()]) {
    st.pop();
}
```

This is the heart of the algorithm.

Why do we pop?

Suppose:

```cpp
nums = {1, 3, 4, 2}
```

We are processing `3`.

The stack's top represents `2`.

Since:

$$
3 \geq 2
$$

`2` cannot be the Next Greater Element of `3`.

Therefore, pop it.

The same logic applies to equal values because we need a strictly greater element.

After popping:

- If the stack is empty, no greater element exists.
- Otherwise, the top represents the nearest greater element to the right.

---

## 6. Why Traverse From Right to Left?

We need the Next Greater Element on the right.

When traversing from right to left, the stack contains candidates from the already processed right side.

This means we can answer each position using previously processed elements.

For every index:

1. Remove invalid candidates.
2. If stack is not empty, top is the answer.
3. Push the current index.

---

## 7. Complete NGE Function — Line-by-Line

```cpp
vector<int> NGE(vector<int>& nums) {

    int n = nums.size();

    vector<int> NGE(n, -1);

    stack<int> st;

    st.push(n - 1);

    for(int i = n - 2; i >= 0; i--) {

        while(!st.empty() && nums[i] >= nums[st.top()]) {
            st.pop();
        }

        if(!st.empty()) {
            NGE[i] = nums[st.top()];
        }

        st.push(i);
    }

    return NGE;
}
```

### Explanation

**Step 1: Initialize answer**

```cpp
vector<int> NGE(n, -1);
```

Initially, every element has answer `-1`.

This is useful because some elements genuinely have no greater element to their right.

**Step 2: Create stack**

```cpp
stack<int> st;
```

We store indices so that we can access the corresponding values using `nums[st.top()]`.

**Step 3: Push last index**

```cpp
st.push(n - 1);
```

The last element has no element to its right, so its answer remains `-1`.

It becomes a candidate for elements to its left.

**Step 4: Traverse right to left**

```cpp
for(int i = n - 2; i >= 0; i--)
```

Every iteration processes one element.

**Step 5: Remove invalid candidates**

```cpp
while(!st.empty() && nums[i] >= nums[st.top()]) {
    st.pop();
}
```

Remove smaller or equal elements.

**Step 6: Find answer**

```cpp
if(!st.empty()) {
    NGE[i] = nums[st.top()];
}
```

The stack top is the nearest greater element to the right.

**Step 7: Push current index**

```cpp
st.push(i);
```

The current element can be a greater element for some future iteration.

---

## 8. Complete Dry Run

Input:

```cpp
nums = {1, 3, 4, 2}
```

We process from right to left.

| Index | Current Value | Stack Before Processing | Operation | NGE |
|---:|---:|---|---|---:|
| 3 | 2 | Empty | Push index 3 | -1 |
| 2 | 4 | [2] | Pop 2, push 4 | -1 |
| 1 | 3 | [4] | Top is 4, push 3 | 4 |
| 0 | 1 | [4, 3] | Top is 3, push 1 | 3 |

Final answer:

```cpp
NGE = {3, 4, -1, -1}
```

### Stack representation

The stack stores indices.

After processing index 2:

```cpp
Stack = {2}
```

After processing index 1:

```cpp
Stack = {2, 1}
```

After processing index 0:

```cpp
Stack = {2, 1, 0}
```

Corresponding values from bottom to top:

```cpp
{4, 3, 1}
```

They are strictly decreasing.

---

## 9. Why Is Every Element Pushed and Popped at Most Once?

This is the key to understanding the O(n) complexity.

Every index is:

- Pushed into the stack exactly once.
- Popped from the stack at most once.

Even though we have a `while` loop inside a `for` loop, the total number of pop operations across all iterations cannot exceed `n`.

Therefore:

$$
Total\ Operations = O(n)
$$

This is called **Amortized Analysis**.

The algorithm is not O(n²).

---

## 10. Complete LC 496 Solution

```cpp
class Solution {
public:

    vector<int> NGE(vector<int>& nums) {

        int n = nums.size();

        vector<int> NGE(n, -1);

        stack<int> st;

        st.push(n - 1);

        for(int i = n - 2; i >= 0; i--) {

            while(!st.empty() && nums[i] >= nums[st.top()]) {
                st.pop();
            }

            if(!st.empty()) {
                NGE[i] = nums[st.top()];
            }

            st.push(i);
        }

        return NGE;
    }

    vector<int> nextGreaterElement(vector<int>& nums1, vector<int>& nums2) {

        int n1 = nums1.size();
        int n2 = nums2.size();

        vector<int> temp_result = NGE(nums2);

        unordered_map<int, int> mp;

        for(int i = 0; i < n2; i++) {
            mp[nums2[i]] = temp_result[i];
        }

        vector<int> result;

        for(int i = 0; i < n1; i++) {
            result.push_back(mp[nums1[i]]);
        }

        return result;
    }
};
```

---

## 11. Why HashMap?

We have calculated the Next Greater Element for every position in `nums2`.

But the final answer is required only for elements in `nums1`.

We need a fast way to retrieve the corresponding NGE.

Therefore, we create:

```cpp
unordered_map<int, int> mp;
```

Mapping:

$$
Element \rightarrow NextGreaterElement
$$

Example:

```cpp
nums2 = {1, 3, 4, 2}
```

NGE:

```cpp
{3, 4, -1, -1}
```

HashMap:

| Key | Value |
|---:|---:|
| 1 | 3 |
| 3 | 4 |
| 4 | -1 |
| 2 | -1 |

Now for:

```cpp
nums1 = {4, 1, 2}
```

We retrieve:

```cpp
mp[4] = -1
mp[1] = 3
mp[2] = -1
```

Final result:

```cpp
{-1, 3, -1}
```

**Important:** This direct value-to-answer mapping works because `nums2` contains distinct elements and `nums1` is its subset.

---

## 12. Complexity Analysis

Let:

- `n1` = size of `nums1`
- `n2` = size of `nums2`

### NGE Calculation

Each index is pushed once and popped at most once.

$$
O(n_2)
$$

### HashMap Construction

We insert all elements of `nums2`.

$$
O(n_2) \text{ average}
$$

### Result Construction

We retrieve one answer for every element of `nums1`.

$$
O(n_1) \text{ average}
$$

### Total Time

$$
O(n_1+n_2) \text{ average}
$$

### Total Auxiliary Space

Stack + NGE vector + HashMap:

$$
O(n_2)
$$

The returned result vector requires an additional O(n1) output space.

---

## 13. Common Mistakes

### Mistake 1: Using `>` instead of `>=`

Incorrect:

```cpp
while(!st.empty() && nums[i] > nums[st.top()])
```

This leaves equal elements in the stack.

But Next Greater Element requires a strictly greater value.

Correct:

```cpp
while(!st.empty() && nums[i] >= nums[st.top()])
```

### Mistake 2: Storing Values Instead of Indices

Storing values can work for some basic NGE implementations, but indices provide more flexibility for distance-based problems.

For example, Daily Temperatures requires:

$$
Answer[i] = NextGreaterIndex-i
$$

Therefore, storing indices is a strong reusable habit.

### Mistake 3: Forgetting to Push Current Index

After calculating the answer:

```cpp
st.push(i);
```

The current element must become a candidate for elements to its left.

### Mistake 4: Updating the Answer Before Removing Invalid Elements

Always remove smaller or equal candidates first.

Only then can the stack top be trusted as the nearest greater candidate.

---

## 14. Connection With Other Monotonic Stack Problems

| Problem | Stack Pattern | What We Store |
|---|---|---|
| LC 496 — Next Greater Element I | Decreasing | Indices |
| LC 739 — Daily Temperatures | Decreasing | Indices |
| LC 503 — Next Greater Element II | Decreasing | Indices |
| LC 901 — Online Stock Span | Decreasing | Price and span / index |
| LC 84 — Largest Rectangle in Histogram | Increasing | Indices |
| LC 907 — Sum of Subarray Minimums | Increasing | Indices |

### Pattern Recognition

Whenever a problem asks for:

- Next greater element
- Next smaller element
- Previous greater element
- Previous smaller element
- Nearest element satisfying a comparison

Think about Monotonic Stack.

---

## 15. Interview Follow-Up Questions

**Q1. Why is the stack decreasing?**

Because smaller or equal candidates are removed while processing each current element from right to left. The remaining candidate values decrease from bottom to top.

**Q2. Why do we traverse from right to left?**

Because the required greater element must be on the right, and the stack maintains candidates from that already processed region.

**Q3. Why do we pop equal elements?**

Because the problem requires strictly greater elements.

**Q4. Why is the algorithm O(n) despite nested loops?**

Every element is pushed once and popped at most once. Total stack operations are linear.

**Q5. Why use a HashMap after calculating NGE?**

To retrieve the precomputed answer for each element of `nums1` in O(1) average time.

---

## 16. Final Revision Cheat Sheet

```cpp
vector<int> NGE(vector<int>& nums) {

    int n = nums.size();

    vector<int> ans(n, -1);

    stack<int> st;

    for(int i = n - 1; i >= 0; i--) {

        while(!st.empty() && nums[i] >= nums[st.top()]) {
            st.pop();
        }

        if(!st.empty()) {
            ans[i] = nums[st.top()];
        }

        st.push(i);
    }

    return ans;
}
```

**Remember:**

1. Next Greater Element → Monotonic Decreasing Stack.
2. Traverse right to left.
3. Store indices.
4. Pop smaller or equal elements.
5. Stack top gives the nearest greater element.
6. Push current index after calculating the answer.
7. Each element is pushed once and popped at most once.

**Core Pattern:**

`Monotonic Decreasing Stack + HashMap`

**One-line intuition:**

For every element, eliminate all smaller or equal candidates from the right; the remaining stack top is the nearest strictly greater element.
