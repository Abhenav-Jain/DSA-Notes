# LeetCode 739 — Daily Temperatures

**Pattern:** Monotonic Decreasing Stack  
**Difficulty:** Medium  
**Time Complexity:** O(n) Amortized  
**Space Complexity:** O(n)

---

## 1. Problem Understanding

Given an array `temperatures`, where `temperatures[i]` represents the temperature on the ith day.

For every day, find how many days we have to wait until a warmer temperature occurs.

If there is no future day with a warmer temperature, store `0`.

### Example

```cpp
temperatures = {73, 74, 75, 71, 69, 72, 76, 73}
```

Output:

```cpp
{1, 1, 4, 2, 1, 1, 0, 0}
```

Explanation:

- Day 0: 73 → 74 occurs after 1 day.
- Day 1: 74 → 75 occurs after 1 day.
- Day 2: 75 → 76 occurs after 4 days.
- Day 3: 71 → 72 occurs after 2 days.
- Day 4: 69 → 72 occurs after 1 day.
- Day 5: 72 → 76 occurs after 1 day.
- Day 6: 76 → No warmer temperature → 0.
- Day 7: 73 → No future day → 0.

---

## 2. Brute Force Approach

For every day `i`:

1. Start traversing from `i + 1`.
2. Find the first temperature greater than `temperatures[i]`.
3. Calculate the difference between the two indices.
4. If no greater temperature exists, store `0`.

### Brute Force Code

```cpp
class Solution {
public:
    vector<int> dailyTemperatures(vector<int>& temperatures) {

        int n = temperatures.size();
        vector<int> ans(n, 0);

        for(int i = 0; i < n; i++) {

            for(int j = i + 1; j < n; j++) {

                if(temperatures[j] > temperatures[i]) {
                    ans[i] = j - i;
                    break;
                }
            }
        }

        return ans;
    }
};
```

### Complexity

- Time: O(n²)
- Auxiliary Space: O(1), excluding output.

We can optimize this using Monotonic Stack.

---

## 3. Connection With LC 496 — Next Greater Element I

This problem is a direct extension of Next Greater Element I.

| LC 496 | LC 739 |
|---|---|
| Find next greater element | Find next warmer temperature |
| Return greater element's value | Return index difference |
| Store indices in stack | Store indices in stack |
| Monotonic decreasing stack | Monotonic decreasing stack |
| Default answer = -1 | Default answer = 0 |

The fundamental logic remains the same.

**Only the required output changes.**

In LC 496:

```cpp
ans[i] = nums[st.top()];
```

In LC 739:

```cpp
ans[i] = st.top() - i;
```

---

## 4. Core Concept — Next Greater Element

For every index `i`, we need to find the first index `j` satisfying:

$$
j > i
$$

and

$$
temperatures[j] > temperatures[i]
$$

The required answer is:

$$
ans[i] = j-i
$$

If no such index exists:

$$
ans[i] = 0
$$

This is exactly a Next Greater Element problem where we need the distance instead of the value.

---

## 5. Why Monotonic Decreasing Stack?

We use a Monotonic Decreasing Stack.

In this implementation:

- Stack stores indices.
- Corresponding temperatures decrease from bottom to top.
- We traverse the array from right to left.

Why decreasing?

Suppose:

```cpp
temperatures = {73, 74, 75, 71, 69, 72, 76, 73}
```

While processing the current temperature, all smaller or equal temperatures on the stack are removed.

They cannot be the next warmer day for the current temperature.

After removing invalid candidates:

- If the stack is not empty, its top is the nearest warmer day.
- If the stack is empty, no warmer day exists.

---

## 6. Most Important Logic

```cpp
while(!st.empty() && temperatures[i] >= temperatures[st.top()]) {
    st.pop();
}
```

Why do we pop smaller or equal temperatures?

Because we need a strictly warmer temperature.

Consider:

```cpp
temperatures = {70, 70, 75}
```

For the first `70`, the next warmer temperature is `75`, not the second `70`.

Therefore, equal temperatures must also be removed.

Once invalid candidates are removed:

```cpp
if(!st.empty()) {
    ans[i] = st.top() - i;
}
```

The stack top gives the nearest greater index.

The difference gives the number of days to wait.

---

## 7. Why Traverse From Right to Left?

We need the next warmer temperature on the right.

When traversing from right to left:

- The right side has already been processed.
- The stack maintains useful candidate indices.
- We can eliminate candidates that cannot be the answer.
- The remaining top is the nearest valid greater index.

This avoids repeatedly scanning the right side.

---

## 8. Complete Optimized C++ Code

```cpp
class Solution {
public:

    vector<int> NGE(vector<int>& nums) {

        int n = nums.size();

        stack<int> st;

        vector<int> ans(n, 0);

        for(int i = n - 1; i >= 0; i--) {

            while(!st.empty() && nums[i] >= nums[st.top()]) {
                st.pop();
            }

            if(!st.empty()) {
                ans[i] = st.top() - i;
            }

            st.push(i);
        }

        return ans;
    }

    vector<int> dailyTemperatures(vector<int>& temperatures) {

        vector<int> result = NGE(temperatures);

        return result;
    }
};
```

---

## 9. Line-by-Line Explanation

### Step 1: Initialize answer

```cpp
vector<int> ans(n, 0);
```

Initially, every answer is zero.

This is important because if no warmer day exists, the required output is `0`.

### Step 2: Create stack

```cpp
stack<int> st;
```

We store indices rather than temperatures.

Why?

Because we need the index difference to calculate the number of waiting days.

### Step 3: Traverse right to left

```cpp
for(int i = n - 1; i >= 0; i--)
```

We process the array from the last day to the first day.

### Step 4: Remove invalid candidates

```cpp
while(!st.empty() && nums[i] >= nums[st.top()]) {
    st.pop();
}
```

Remove all temperatures that are smaller than or equal to the current temperature.

### Step 5: Calculate waiting days

```cpp
if(!st.empty()) {
    ans[i] = st.top() - i;
}
```

The top contains the index of the nearest warmer day.

Therefore:

$$
WaitingDays = NextWarmerIndex - CurrentIndex
$$

### Step 6: Push current index

```cpp
st.push(i);
```

The current index may become the next warmer day for an element further to the left.

---

## 10. Complete Dry Run

Input:

```cpp
temperatures = {73, 74, 75, 71, 69, 72, 76, 73}
```

We traverse from right to left.

| Index | Temperature | Stack Operation | Answer |
|---:|---:|---|---:|
| 7 | 73 | Push 7 | 0 |
| 6 | 76 | Pop 7, push 6 | 0 |
| 5 | 72 | Push 5 | 1 |
| 4 | 69 | Push 4 | 1 |
| 3 | 71 | Pop 4, push 3 | 2 |
| 2 | 75 | Pop 3, 5, 6; push 2 | 4 |
| 1 | 74 | Push 1 | 1 |
| 0 | 73 | Push 0 | 1 |

Final answer:

```cpp
{1, 1, 4, 2, 1, 1, 0, 0}
```

### Detailed Example: Index 2

Current temperature:

```cpp
temperatures[2] = 75;
```

At this point, the stack contains indices corresponding to:

```cpp
{76, 72, 71, 73}
```

From the top, we encounter:

- 71 → Pop.
- 72 → Pop.
- 73 → Pop.
- 76 → Remains.

Now the stack top is index `6`.

Therefore:

$$
ans[2] = 6-2=4
$$

The next warmer day occurs after 4 days.

---

## 11. Why Is the Complexity O(n)?

Although there is a nested `while` loop inside the `for` loop, the algorithm is not O(n²).

Every index:

- Enters the stack exactly once.
- Leaves the stack at most once.

Therefore, total pushes are at most `n` and total pops are at most `n`.

$$
Total\ Stack\ Operations = O(n)
$$

This is called Amortized Analysis.

### Time Complexity

$$
O(n)
$$

### Auxiliary Space Complexity

$$
O(n)
$$

The stack can contain up to `n` indices.

The answer vector separately requires O(n) output space.

---

## 12. Important Edge Cases

| Input | Expected Output | Reason |
|---|---|---|
| `{30}` | `{0}` | No future day |
| `{30,40}` | `{1,0}` | Next day is warmer |
| `{40,30}` | `{0,0}` | No warmer future day |
| `{30,30,40}` | `{2,1,0}` | Equal temperature is not warmer |
| `{40,35,30}` | `{0,0,0}` | Decreasing temperatures |
| `{30,40,50,60}` | `{1,1,1,0}` | Increasing temperatures |

---

## 13. Common Mistakes

### Mistake 1: Using `>` instead of `>=`

Incorrect:

```cpp
while(!st.empty() && nums[i] > nums[st.top()])
```

Equal temperatures must also be removed because the question asks for a strictly warmer day.

Correct:

```cpp
while(!st.empty() && nums[i] >= nums[st.top()])
```

### Mistake 2: Returning the Temperature Instead of the Distance

Incorrect:

```cpp
ans[i] = nums[st.top()];
```

Correct:

```cpp
ans[i] = st.top() - i;
```

### Mistake 3: Storing Temperatures Instead of Indices

The output requires the number of days, so we need the indices.

### Mistake 4: Initializing the Answer With -1

The problem requires `0` when no warmer temperature exists.

Correct:

```cpp
vector<int> ans(n, 0);
```

### Mistake 5: Forgetting to Push the Current Index

```cpp
st.push(i);
```

The current index must become a candidate for earlier days.

---

## 14. Connection With Other Monotonic Stack Problems

| Problem | LeetCode | Main Idea |
|---|---:|---|
| Next Greater Element I | 496 | Next greater value |
| Daily Temperatures | 739 | Next greater index distance |
| Next Greater Element II | 503 | Circular next greater element |
| Online Stock Span | 901 | Previous greater element |
| Largest Rectangle in Histogram | 84 | Nearest smaller boundaries |
| Sum of Subarray Minimums | 907 | Element contribution |

### Pattern Recognition

Whenever a question asks:

- How many days until a greater value?
- Distance to the next greater element?
- Nearest greater element on the right?
- First future element satisfying a comparison?

Think about Monotonic Stack.

---

## 15. Interview-Level Questions

**Q1. Why do we store indices instead of temperatures?**

Because the answer requires the difference between the current day and the next warmer day.

**Q2. Why do we traverse from right to left?**

Because we need the next greater element on the right, and previously processed elements are maintained in the stack.

**Q3. Why do we pop equal temperatures?**

Because the problem requires a strictly greater temperature.

**Q4. Why is the algorithm O(n) despite a nested loop?**

Each index is pushed once and popped at most once.

**Q5. What does an empty stack indicate after popping?**

No warmer future day exists for the current index, so its answer remains zero.

---

## 16. Final Revision Cheat Sheet

```cpp
vector<int> dailyTemperatures(vector<int>& nums) {

    int n = nums.size();

    stack<int> st;

    vector<int> ans(n, 0);

    for(int i = n - 1; i >= 0; i--) {

        while(!st.empty() && nums[i] >= nums[st.top()]) {
            st.pop();
        }

        if(!st.empty()) {
            ans[i] = st.top() - i;
        }

        st.push(i);
    }

    return ans;
}
```

### Remember These 6 Points

1. Next warmer day = Next Greater Element.
2. Use a Monotonic Decreasing Stack.
3. Traverse from right to left.
4. Store indices, not values.
5. Pop smaller or equal temperatures.
6. Calculate `st.top() - i`.

**Core Pattern:**

`Monotonic Decreasing Stack + Index Difference`

**One-line intuition:**

Remove all smaller or equal future temperatures; the remaining stack top gives the nearest warmer day, and its index difference gives the waiting time.
