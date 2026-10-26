# LeetCode 1475 — Final Prices With a Special Discount in a Shop

**Pattern:** Monotonic Stack  
**Sub-pattern:** Next Smaller or Equal Element (NSE / NSEE)  
**Difficulty:** Easy  
**Related Problems:** LC 496, LC 739, LC 503

---

## 1. Problem Statement

You are given an integer array `prices`, where `prices[i]` represents the price of the `i-th` item in a shop.

There is a special discount for items in the shop:

For each item, you find the first item to its right whose price is less than or equal to the current item's price.

The discount is equal to that item's price.

If no such item exists, no discount is applied.

Return an array containing the final prices after applying the discounts.

### Example 1

```cpp
Input:  prices = [8,4,6,2,3]
Output: [4,2,4,2,3]
```

Explanation:

- Item at index 0: Price = 8, discount = 4 → Final price = 4
- Item at index 1: Price = 4, discount = 2 → Final price = 2
- Item at index 2: Price = 6, discount = 2 → Final price = 4
- Item at index 3: Price = 2, no discount → Final price = 2
- Item at index 4: Price = 3, no discount → Final price = 3

### Example 2

```cpp
Input:  prices = [1,2,3,4,5]
Output: [1,2,3,4,5]
```

No element has a smaller or equal element to its right.

---

## 2. Identify the Actual Pattern

For every index `i`, we need to find:

**The first element on the right such that:**

$$prices[j] \leq prices[i]$$

where:

$$j > i$$

This is not a normal Next Smaller Element problem.

It is specifically:

**Next Smaller or Equal Element (NSEE).**

Why?

Because the question explicitly allows the discount item to have a price equal to the current item's price.

Example:

```cpp
prices = [10,10,10]
```

For the first `10`, the next valid discount is also `10`.

Final result:

```cpp
[0,0,10]
```

This equality condition is the most important part of this problem.

---

## 3. Brute Force Approach

For every price:

1. Traverse all elements towards the right.
2. Find the first element satisfying `prices[j] <= prices[i]`.
3. Subtract that value from the current price.
4. If no such element exists, keep the original price.

### Code

```cpp
class Solution {
public:
    vector<int> finalPrices(vector<int>& prices) {

        int n = prices.size();
        vector<int> ans(n);

        for(int i = 0; i < n; i++) {

            ans[i] = prices[i];

            for(int j = i + 1; j < n; j++) {

                if(prices[j] <= prices[i]) {
                    ans[i] = prices[i] - prices[j];
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

For each element, in the worst case, we may traverse the entire remaining array.

We can optimize this using a monotonic stack.

---

## 4. Optimal Approach — Monotonic Stack

### Core intuition

Instead of checking the right side repeatedly for every element, we can process the array from right to left.

While traversing from right to left:

- The stack contains potential discount candidates from the already processed right side.
- We remove elements that cannot be valid discounts.
- The top of the stack becomes the nearest valid discount candidate.

### Step 1: Traverse from right to left

```cpp
for(int i = n-1; i >= 0; i--)
```

Why right to left?

Because for the current element, all elements to its right have already been processed.

Therefore, the stack can help us find the next valid element.

### Step 2: Remove invalid candidates

```cpp
while(!st.empty() && nums[st.top()] > nums[i]) {
    st.pop();
}
```

This is the most important line.

We need:

$$nums[st.top()] \leq nums[i]$$

Therefore, elements strictly greater than the current element are invalid.

We remove them.

Notice that we use `>` and not `>=`.

**Why?**

Because equal values are valid discounts.

Example:

```cpp
prices = [10,10]
```

If we use:

```cpp
nums[st.top()] >= nums[i]
```

The equal `10` would get removed.

But the problem allows equal prices to be discounts.

Hence:

```cpp
nums[st.top()] > nums[i]
```

is correct.

### Step 3: Find the discount

```cpp
if(!st.empty()) {
    ans[i] = nums[st.top()];
}
```

After popping invalid candidates:

- If stack is non-empty, its top is the next smaller or equal element.
- If stack is empty, no valid discount exists.

### Step 4: Push current element

```cpp
st.push(i);
```

We store the index because the stack needs to identify elements in the original array.

We could also store values in this particular problem, provided we only need the discount value. Both approaches work.

---

## 5. Your Accepted Implementation

Your implementation stores indices in the stack and stores the corresponding values in the NSE result.

```cpp
class Solution {
public:

    vector<int> NSE(vector<int>& nums) {

        int n = nums.size();

        stack<int> st;
        vector<int> ans(n,-1);

        st.push(n-1);

        for(int i = n-2; i >= 0; i--) {

            while(!st.empty() && nums[st.top()] > nums[i]) {
                st.pop();
            }

            if(!st.empty()) {
                ans[i] = nums[st.top()];
            }

            st.push(i);
        }

        return ans;
    }

    vector<int> finalPrices(vector<int>& prices) {

        int n = prices.size();

        vector<int> check = NSE(prices);
        vector<int> ans;

        for(int i = 0; i < n; i++) {

            if(check[i] == -1) {
                ans.push_back(prices[i]);
            }
            else {
                ans.push_back(prices[i] - check[i]);
            }
        }

        return ans;
    }
};
```

**Verdict:** Correct for the problem constraints.

---

## 6. Detailed Dry Run

Input:

```cpp
prices = [8,4,6,2,3]
```

Initial state:

```cpp
st = {}
NSE = [-1,-1,-1,-1,-1]
```

We process from right to left.

| i | Current price | Popping condition | Stack after processing | NSE |
|---:|---:|---|---|---:|
| 4 | 3 | Nothing to pop | [4] | -1 |
| 3 | 2 | Pop 3 | [3] | -1 |
| 2 | 6 | Nothing to pop | [3,2] | 2 |
| 1 | 4 | Nothing to pop | [3,2,1] | 2 |
| 0 | 8 | Nothing to pop | [3,2,1,0] | 4 |

Stack indices are shown from bottom to top.

Final NSE array:

```cpp
[4,2,2,-1,-1]
```

Now apply the discounts:

```cpp
prices = [8,4,6,2,3]
NSE    = [4,2,2,-1,-1]
```

Final calculation:

```cpp
8 - 4 = 4
4 - 2 = 2
6 - 2 = 4
2      = 2
3      = 3
```

Final answer:

```cpp
[4,2,4,2,3]
```

---

## 7. Why Does the Monotonic Stack Work?

Consider the current element `prices[i]`.

We want the first element to its right that satisfies:

$$prices[j] \leq prices[i]$$

The stack represents possible candidates from the right side.

Suppose a candidate `x` is greater than the current value.

Then:

$$x > prices[i]$$

It cannot be the required discount.

Moreover, because we process from right to left, the current element is closer to every earlier position than `x` is.

Therefore, `x` can also be eliminated as a candidate for those earlier positions.

After removing all invalid candidates:

- The remaining stack top is the nearest valid candidate.
- If no candidate remains, no discount exists.

This is why we can solve the problem in linear time.

---

## 8. Complexity Analysis

Let `n` be the number of prices.

**Time Complexity: O(n)**

Although we have a loop and a while loop, the while loop does not execute `n` times for every element.

Each index:

- Is pushed onto the stack once.
- Is popped from the stack at most once.

Therefore, total stack operations are O(n).

**Auxiliary Space: O(n)**

The stack can contain up to `n` indices.

The `NSE` result and final answer each require O(n) output/intermediate storage.

---

## 9. The Most Important Comparison — NGE vs NSE vs NSEE

This is the key revision table for monotonic stack.

| Required element | Invalid candidates to pop | Condition |
|---|---|---|
| Next Greater (strictly greater) | Smaller or equal | `nums[top] <= nums[i]` |
| Next Greater or Equal | Smaller | `nums[top] < nums[i]` |
| Next Smaller (strictly smaller) | Greater or equal | `nums[top] >= nums[i]` |
| Next Smaller or Equal | Greater | `nums[top] > nums[i]` |

For LC 1475:

```cpp
while(!st.empty() && nums[st.top()] > nums[i]) {
    st.pop();
}
```

This is Next Smaller or Equal.

**Remember:** The comparison operator depends on whether equality is allowed in the answer.

---

## 10. Important Edge Cases

### Case 1: Equal prices

```cpp
prices = [10,10,10]
Output  = [0,0,10]
```

Equal prices are valid discounts.

### Case 2: Increasing array

```cpp
prices = [1,2,3,4,5]
Output  = [1,2,3,4,5]
```

No smaller or equal element exists to the right.

### Case 3: Decreasing array

```cpp
prices = [5,4,3,2,1]
Output  = [1,1,1,1,1]
```

Every element except the last gets the next element as its discount.

### Case 4: Single element

```cpp
prices = [7]
Output  = [7]
```

No discount exists.

---

## 11. Interview Questions

**Q1. Why do we traverse from right to left?**

Because we need the next valid element on the right. Processing from right to left allows the stack to maintain candidates from the already processed portion.

**Q2. Why do we pop only strictly greater elements?**

Because equal prices are valid discounts.

**Q3. What happens if we use `>=` instead of `>`?**

Equal prices would be removed, producing incorrect results.

**Q4. Why is the algorithm O(n), despite the nested while loop?**

Every element is pushed once and popped at most once. Total stack operations are linear.

**Q5. Can we solve this using an index-based stack?**

Yes. In fact, your implementation uses indices. We can also store values because the problem only requires the discount value.

**Q6. Is this a Next Smaller Element or Next Smaller or Equal Element problem?**

It is Next Smaller or Equal Element because the condition is:

$$prices[j] \leq prices[i]$$

---

## 12. Pattern Recognition

Whenever a problem asks:

- Find the first smaller element on the right.
- Find the first greater element on the right.
- Find the nearest element satisfying a comparison.
- Find a discount based on the next eligible price.

Think about monotonic stack.

### Your Monotonic Stack Progress

| Problem | Pattern |
|---|---|
| LC 496 | Next Greater Element |
| LC 739 | Next Greater Element + Distance |
| LC 503 | Circular Next Greater Element |
| LC 1475 | Next Smaller or Equal Element |

---

## 13. Final Revision Cheat Sheet

```cpp
// Next Smaller or Equal Element
// Traverse right to left
// Store indices in stack

while(!st.empty() && nums[st.top()] > nums[i]) {
    st.pop();
}

if(!st.empty()) {
    ans[i] = nums[st.top()];
}

st.push(i);
```

**Three things to remember:**

1. `>` means equal elements remain valid.
2. Stack top gives the nearest smaller-or-equal candidate.
3. Each element is pushed and popped at most once.

**Final complexity: O(n) Time | O(n) Auxiliary Space.**

**Core takeaway:** LC 1475 teaches you that a small change in the comparison operator can change the entire monotonic stack pattern. Always derive the pop condition from the exact inequality in the problem statement.
