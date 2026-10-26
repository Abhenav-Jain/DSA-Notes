# LeetCode 503 — Next Greater Element II

**Pattern:** Monotonic Stack + Circular Array  
**Difficulty:** Medium  
**Related Problems:** LC 496 — Next Greater Element I, LC 739 — Daily Temperatures

---

## 1. Problem Statement

Given a circular integer array `nums`, return the Next Greater Number for every element.

The Next Greater Number of an element `x` is the first number greater than `x` while traversing the array in the forward direction.

Since the array is circular, after reaching the last element, we continue from the beginning.

If no greater element exists, return `-1`.

### Example 1

```cpp
Input:  nums = [1,2,1]
Output: [2,-1,2]
```

Explanation:

- For `1` at index 0, next greater element is `2`.
- For `2`, no greater element exists.
- For `1` at index 2, circular traversal reaches `2`.

### Example 2

```cpp
Input:  nums = [3,6,5,4,2]
Output: [6,-1,6,6,3]
```

---

## 2. Understand the Core Difference

In normal Next Greater Element:

We search only towards the right side of the current index.

In circular Next Greater Element:

We search towards the right side, and if we reach the end, we continue searching from index `0`.

Example:

```cpp
nums = [3,6,5,4,2]
```

For element `2`:

- Right side mein koi element nahi hai.
- Array ke beginning se traversal continue hoga.
- `3` is greater than `2`.

Therefore, NGE of `2` = `3`.

**Key observation:** We need to simulate a circular traversal without physically creating another array.

---

## 3. Brute Force Approach

For every index `i`:

1. Start checking from the next index.
2. Traverse at most `n-1` elements.
3. Use modulo to simulate circular movement.
4. Find the first element greater than `nums[i]`.
5. If no greater element exists, keep `-1`.

### Code

```cpp
class Solution {
public:
    vector<int> nextGreaterElements(vector<int>& nums) {
        int n = nums.size();
        vector<int> ans(n, -1);

        for(int i = 0; i < n; i++) {
            for(int j = 1; j < n; j++) {
                int idx = (i + j) % n;

                if(nums[idx] > nums[i]) {
                    ans[i] = nums[idx];
                    break;
                }
            }
        }

        return ans;
    }
};
```

### Complexity

- Time: $O(n^2)$
- Auxiliary Space: $O(1)$
- Output Space: $O(n)$

Why O(n²)?

For each of the `n` elements, we may check up to `n-1` other elements.

We need a more optimized approach.

---

## 4. Optimal Approach — Monotonic Stack

### Step 1: Think about the circular array

Suppose:

```cpp
nums = [3,6,5,4,2]
```

We can logically imagine the array as:

```cpp
[3,6,5,4,2,3,6,5,4,2]
```

But creating a separate doubled array is unnecessary.

We can simulate this using:

```cpp
nums[i % n]
```

The modulo operation maps the doubled traversal back to the original array.

For example, when `n = 5`:

| i | i % n | nums[i % n] |
|---:|---:|---:|
| 9 | 4 | 2 |
| 8 | 3 | 4 |
| 7 | 2 | 5 |
| 6 | 1 | 6 |
| 5 | 0 | 3 |
| 4 | 4 | 2 |

### Step 2: Why traverse from right to left?

We are solving Next Greater Element.

When moving from right to left, the stack contains potential greater elements that occur later in the circular traversal.

This is the same principle used in LC 496 and LC 739.

### Step 3: Why use 2*n iterations?

We need to consider elements from the end and also simulate the circular continuation.

We traverse:

```cpp
for(int i = 2*n - 1; i >= 0; i--)
```

- First traversal: prepares the stack with possible candidates.
- Second traversal: calculates answers for actual array indices.

Only when `i < n` do we store the answer.

### Step 4: Why pop elements?

```cpp
while(!st.empty() && nums[i%n] >= st.top()) {
    st.pop();
}
```

We need the first element strictly greater than the current element.

Therefore:

- Smaller elements cannot be answers.
- Equal elements cannot be answers.
- Both must be removed.

After popping, if the stack is non-empty, its top is the next greater element.

### Step 5: Store the answer

```cpp
if(i < n) {
    if(st.empty()) {
        ans[i] = -1;
    }
    else {
        ans[i] = st.top();
    }
}
```

If the stack is empty, no greater candidate exists.

Otherwise, the top contains the required greater value.

### Step 6: Push the current element

```cpp
st.push(nums[i%n]);
```

The current element becomes a candidate for elements that will be processed later in this right-to-left traversal.

---

## 5. Optimal C++ Code

This is the same implementation you solved.

```cpp
class Solution {
public:
    vector<int> nextGreaterElements(vector<int>& nums) {

        int n = nums.size();

        stack<int> st;
        vector<int> ans(n, -1);

        for(int i = 2*n - 1; i >= 0; i--) {

            while(!st.empty() && nums[i%n] >= st.top()) {
                st.pop();
            }

            if(i < n) {
                if(st.empty()) {
                    ans[i] = -1;
                }
                else {
                    ans[i] = st.top();
                }
            }

            st.push(nums[i%n]);
        }

        return ans;
    }
};
```

---

## 6. Detailed Dry Run

Input:

```cpp
nums = [3,6,5,4,2]
n = 5
```

Initial state:

```cpp
st = {}
ans = [-1,-1,-1,-1,-1]
```

We process indices from `9` to `0`.

| i | nums[i%n] | Stack after popping | Answer |
|---:|---:|---|---:|
| 9 | 2 | [2] | Not calculated |
| 8 | 4 | [2,4] | Not calculated |
| 7 | 5 | [2,4,5] | Not calculated |
| 6 | 6 | [2,4,5,6] | Not calculated |
| 5 | 3 | [2,4,5,6,3] | Not calculated |
| 4 | 2 | [2,4,5,6,3,2] | 3 |
| 3 | 4 | [2,4,5,6,4] | 6 |
| 2 | 5 | [2,4,5,6,5] | 6 |
| 1 | 6 | [2,4,5,6] | -1 |
| 0 | 3 | [2,4,5,6,3] | 6 |

Final answer:

```cpp
[6,-1,6,6,3]
```

**Important:** Stack table mein bottom-to-top order diya gaya hai. Top is the rightmost element.

---

## 7. Why Does This Algorithm Work?

There are three important observations.

**Observation 1 — Elimination**

If an element is smaller than or equal to the current element, it cannot be the Next Greater Element for the current element.

Hence, we remove it.

**Observation 2 — Nearest valid candidate**

After removing all elements that are smaller than or equal to the current element, the top of the stack is the nearest remaining greater candidate in the traversal order.

**Observation 3 — Circular simulation**

By processing `2*n` positions with modulo indexing, we include the elements from the beginning when searching for a greater element after the end of the array.

Together, these observations ensure that every answer is the first strictly greater element in circular forward order.

---

## 8. Why Do We Store Values Instead of Indices?

In this problem:

```cpp
stack<int> st;
st.push(nums[i%n]);
```

We store values because the output requires the Next Greater Element's **value**.

Compare:

| Problem | Required answer | Stack stores |
|---|---|---|
| LC 496 — NGE I | Greater value | Indices or values |
| LC 739 — Daily Temperatures | Number of days | Indices |
| LC 503 — NGE II | Greater value | Indices or values |

In LC 739, we need:

```cpp
st.top() - i
```

Therefore, indices are required.

In LC 503, `st.top()` itself is the answer, so values work perfectly.

---

## 9. Complexity Analysis

Let `n` be the size of the input array.

**Time Complexity: O(n)**

Although the loop executes `2*n` times:

- Every element is pushed at most twice in the simulated traversal.
- Every pushed element can be popped at most once.
- Each stack operation takes O(1) time.

Therefore:

$$O(2n)=O(n)$$

**Auxiliary Space: O(n)**

The stack can hold O(n) elements.

**Output Space: O(n)**

The answer vector contains `n` elements.

---

## 10. Important Edge Cases

| Input | Output | Reason |
|---|---|---|
| `[1]` | `[-1]` | No other element |
| `[5,4,3,2,1]` | `[-1,5,5,5,5]` | Circular traversal |
| `[1,2,3,4]` | `[2,3,4,-1]` | Increasing array |
| `[4,4,4]` | `[-1,-1,-1]` | Equal is not greater |
| `[3,6,5,4,2]` | `[6,-1,6,6,3]` | Circular NGE |

---

## 11. Common Mistakes

**Mistake 1: Using `>` instead of `>=`**

Incorrect:

```cpp
while(!st.empty() && nums[i%n] > st.top())
```

Why?

Equal elements would remain in the stack, even though the question requires a strictly greater element.

Correct:

```cpp
while(!st.empty() && nums[i%n] >= st.top())
```

**Mistake 2: Traversing only `n` elements**

That would miss elements from the beginning when searching circularly.

**Mistake 3: Using `nums[i]` instead of `nums[i%n]`**

When `i >= n`, accessing `nums[i]` goes out of bounds.

**Mistake 4: Calculating answers in both traversals**

We only need to fill the answer for original indices:

```cpp
if(i < n)
```

**Mistake 5: Returning the nearest element without checking strict inequality**

Always remove smaller and equal elements before reading the stack top.

---

## 12. Interview Follow-up Questions

**Q1. Why do we traverse 2*n times?**

To simulate circular traversal and make elements from the beginning available as candidates for elements near the end.

**Q2. Why is modulo used?**

To map virtual indices from the doubled traversal back to valid indices in the original array.

**Q3. Why do we traverse from right to left?**

Because the stack maintains possible next-greater candidates from the already processed portion of the circular traversal.

**Q4. Why do we pop equal elements?**

Because the question asks for a strictly greater element, not greater than or equal to.

**Q5. Can we solve it without creating a doubled array?**

Yes. `nums[i%n]` simulates the doubled array without extra array storage.

**Q6. Why is the time complexity linear even though we have 2*n iterations?**

Because 2 is a constant, and each stack element is pushed and popped only a constant number of times.

---

## 13. Pattern Recognition — When Should You Use This?

Look for these signals:

- Next Greater Element
- Next Smaller Element
- Circular Array
- First greater element on the right
- Previous greater/smaller element
- Nearest element satisfying a comparison condition

These problems often lead to a Monotonic Stack.

### Your Monotonic Stack Progress

| Problem | Pattern learned |
|---|---|
| LC 496 — Next Greater Element I | Basic NGE |
| LC 739 — Daily Temperatures | NGE index and distance |
| LC 503 — Next Greater Element II | Circular NGE |

---

## 14. Final Revision Cheat Sheet

```cpp
// Circular Next Greater Element
// Right-to-left traversal
// Stack stores values
// Strictly greater element required

for(int i = 2*n - 1; i >= 0; i--) {

    while(!st.empty() && nums[i%n] >= st.top()) {
        st.pop();
    }

    if(i < n) {
        ans[i] = st.empty() ? -1 : st.top();
    }

    st.push(nums[i%n]);
}
```

**Remember these three things:**

1. `2*n` → simulate circular traversal.
2. `i%n` → access original array safely.
3. `>=` → remove smaller and equal elements because we need strictly greater.

**Final complexity:** O(n) Time | O(n) Auxiliary Space.

**Core takeaway:** LC 503 is not a completely new monotonic stack pattern. It is the same NGE pattern from LC 496, with circular traversal added using `2*n` and modulo.
