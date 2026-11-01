# Minimum Subsequence in Non-Increasing Order — LeetCode 1403

**Pattern:** Greedy + Sorting  
**Difficulty:** Easy  
**Core Concepts:** Greedy Selection, Descending Sorting, Minimum Cardinality Selection

---

## 1. Problem Understanding

We are given an integer array `nums`.

We need to find a subsequence such that:

1. The sum of selected elements is strictly greater than the sum of all remaining elements.
2. The number of selected elements should be minimum.
3. If multiple answers exist, any valid answer is acceptable.
4. The returned subsequence must be in non-increasing order.

### Example 1

```cpp
nums = [4,3,10,9,8]
```

One valid answer:

```cpp
[10,9]
```

Selected sum:

\[
10+9=19
\]

Remaining sum:

\[
8+4+3=15
\]

Since:

\[
19>15
\]

The condition is satisfied.

### Example 2

```cpp
nums = [4,4,7,6,7]
```

Output:

```cpp
[7,7,6]
```

Selected sum = 20

Remaining sum = 11

Since 20 > 11, the condition is satisfied.

---

## 2. Greedy Intuition

The main question is:

**How can we select the minimum number of elements while making their sum greater than the remaining sum?**

Consider:

```cpp
nums = [4,3,10,9,8]
```

Total sum:

\[
S=4+3+10+9+8=34
\]

We need:

\[
SelectedSum>RemainingSum
\]

Since:

\[
RemainingSum=S-SelectedSum
\]

Therefore:

\[
SelectedSum>S-SelectedSum
\]

Rearranging:

\[
2\times SelectedSum>S
\]

Finally:

\[
\boxed{SelectedSum>\frac{S}{2}}
\]

**Important Observation:**

We only need to select elements whose sum becomes strictly greater than half of the total sum.

### Why choose the largest elements first?

Suppose we need to reach a target sum greater than 17.

Available elements:

```cpp
[10,9,8,4,3]
```

Selecting largest elements:

- Select 10 → Sum = 10
- Select 9 → Sum = 19

We reach the target using only two elements.

Selecting smaller elements would require more elements.

**Greedy Strategy:**

> Always select the largest available element first and stop as soon as the selected sum becomes greater than half of the total sum.

---

## 3. Optimal Approach — Sorting + Greedy Selection

### Step 1: Calculate the total sum

```cpp
int sum = 0;

for(int el : nums){
    sum += el;
}
```

We need the total sum to calculate the remaining sum.

### Step 2: Sort in descending order

```cpp
sort(nums.begin(), nums.end(), greater<int>());
```

Now the largest elements appear first.

### Step 3: Initialize variables

```cpp
vector<int> ans;
int curr_sum = 0;
```

- `ans` stores the selected subsequence.
- `curr_sum` stores the sum of selected elements.

### Step 4: Select elements greedily

```cpp
for(int i = 0; i < n; i++){

    curr_sum += nums[i];
    ans.push_back(nums[i]);

    if(curr_sum > sum - curr_sum){
        break;
    }
}
```

At each iteration:

1. Select the current largest remaining element.
2. Add it to `curr_sum`.
3. Compare selected sum with remaining sum.
4. Stop immediately when the condition becomes true.

### Step 5: Return the answer

```cpp
return ans;
```

Because we sorted in descending order, the returned subsequence is already in non-increasing order.

---

## 4. C++ Implementation — Independently Derived Solution

```cpp
class Solution {
public:
    vector<int> minSubsequence(vector<int>& nums) {

        int n = nums.size();
        int sum = 0;

        // Calculate total sum
        for(int el : nums){
            sum += el;
        }

        // Sort in descending order
        sort(nums.begin(), nums.end(), greater<int>());

        vector<int> ans;
        int curr_sum = 0;

        // Greedy selection
        for(int i = 0; i < n; i++){

            curr_sum += nums[i];
            ans.push_back(nums[i]);

            // Stop when selected sum > remaining sum
            if(curr_sum > sum - curr_sum){
                break;
            }
        }

        return ans;
    }
};
```

---

## 5. Dry Run

Consider:

```cpp
nums = [4,3,10,9,8]
```

### Step 1: Calculate total sum

\[
S=34
\]

### Step 2: Sort descending

```cpp
[10,9,8,4,3]
```

### Step 3: Greedy selection

| Step | Selected Element | Current Sum | Remaining Sum | Condition |
|---|---:|---:|---:|---|
| 1 | 10 | 10 | 24 | False |
| 2 | 9 | 19 | 15 | True |

At step 2:

\[
19>15
\]

We stop immediately.

Final answer:

```cpp
[10,9]
```

**Output: `[10,9]`**

---

## 6. Why Is This Greedy Strategy Correct?

We need to establish two things:

1. Selecting the largest elements first is optimal.
2. Stopping at the first valid selection gives minimum length.

### Observation 1: Largest elements maximize the sum for a fixed selection size

Suppose we want to select exactly `k` elements.

The maximum possible sum of any `k` elements is obtained by choosing the `k` largest elements.

Therefore, if any selection of `k` elements can satisfy:

\[
SelectedSum>\frac{S}{2}
\]

then selecting the `k` largest elements will also satisfy it.

### Observation 2: First successful selection has minimum length

Our algorithm checks:

- First largest element.
- Two largest elements.
- Three largest elements.
- And so on.

Suppose the condition first becomes true after selecting `k` elements.

Since the `k-1` largest elements did not satisfy the condition, no other selection of `k-1` elements can satisfy it either.

This is because no other selection of `k-1` elements can have a greater sum than the `k-1` largest elements.

Therefore, `k` is the minimum possible number of selected elements.

**Conclusion:** Descending sorting followed by greedy selection produces a minimum-length valid subsequence.

---

## 7. Alternative Mathematical Interpretation

The stopping condition in the code is:

```cpp
if(curr_sum > sum - curr_sum)
```

Rearranging:

\[
curr\_sum>sum-curr\_sum
\]

\[
2(curr\_sum)>sum
\]

\[
curr\_sum>\frac{sum}{2}
\]

All three conditions are mathematically equivalent.

You can also write:

```cpp
if(2 * curr_sum > sum){
    break;
}
```

However, the original comparison avoids unnecessary multiplication.

**Important:** The inequality must be strict. Equality is not sufficient.

---

## 8. Complexity Analysis

Let `n` be the number of elements.

| Operation | Time Complexity |
|---|---|
| Calculate total sum | O(n) |
| Sort descending | O(n log n) |
| Greedy selection | O(n) |
| Total | O(n log n) |

### Auxiliary Space

- Answer vector: O(k), where `k` is the number of selected elements.
- Sorting may use implementation-dependent auxiliary space.

The dominant time complexity is:

\[
\boxed{O(n\log n)}
\]

---

## 9. Important Edge Cases

| Input | Output | Explanation |
|---|---|---|
| `[4,3,10,9,8]` | `[10,9]` | Two largest elements are sufficient |
| `[4,4,7,6,7]` | `[7,7,6]` | Three elements required |
| `[1]` | `[1]` | Single element |
| `[10,1,1]` | `[10]` | Largest element exceeds remaining sum |
| `[1,2,3]` | `[3,2]` | 3 is not strictly greater than 3 |
| `[5,5,5,5]` | `[5,5,5]` | Strict inequality requires three elements |

---

## 10. Common Mistakes

### Mistake 1: Sorting in ascending order

Incorrect:

```cpp
sort(nums.begin(), nums.end());
```

We need the largest elements first to minimize the number of selected elements.

### Mistake 2: Using `>=` instead of `>`

Incorrect:

```cpp
if(curr_sum >= sum - curr_sum)
```

The problem requires selected sum to be strictly greater than remaining sum.

### Mistake 3: Selecting all elements unnecessarily

We must stop as soon as the condition is satisfied.

### Mistake 4: Forgetting the remaining sum

The comparison is not against the original total sum.

Correct:

```cpp
curr_sum > sum - curr_sum
```

### Mistake 5: Confusing subsequence with subarray

A subsequence does not require elements to be contiguous.

Sorting and selecting elements is allowed here.

---

## 11. Pattern Recognition — When to Think of This Greedy Strategy

Look for problems where:

1. You need to select a minimum number of elements.
2. The selected elements must satisfy a sum or threshold condition.
3. Larger elements contribute more toward satisfying the condition.
4. The order of original elements does not need to be preserved.
5. You can stop as soon as the required condition is reached.

Think:

**Sort by contribution → Select the most valuable elements → Stop at the first valid solution.**

Similar problem themes:
- Minimum number of elements to reach a target.
- Selecting maximum-value resources first.
- Minimum items required to cross a threshold.
- Greedy selection under sum constraints.

---

## 12. Interview Follow-up Questions

**Q1. Why do we sort in descending order?**

Because the largest elements help us reach the required sum using fewer elements.

**Q2. Why do we stop when selected sum exceeds remaining sum?**

Because the problem requires the selected sum to be strictly greater than the remaining sum.

**Q3. Why is the first valid selection guaranteed to have minimum length?**

Because the selected elements are the largest possible elements for every given selection size.

**Q4. Why is this not a subarray problem?**

Because a subsequence does not require contiguous elements.

**Q5. Can we solve this without sorting?**

A selection-based approach such as repeatedly finding the maximum could work, but would generally be less efficient than sorting.

**Q6. Why is `>` necessary instead of `>=`?**

Because equality does not satisfy the problem's strict inequality condition.

---

## 13. Quick Revision Cheat Sheet

| Concept | Remember |
|---|---|
| Pattern | Greedy + Sorting |
| Sorting order | Descending |
| Total sum | Sum of all elements |
| Selection | Largest elements first |
| Stopping condition | `curr_sum > sum - curr_sum` |
| Mathematical condition | `2 * curr_sum > sum` |
| Objective | Minimum selected elements |
| Output order | Non-increasing |
| Time | O(n log n) |

### One-Line Takeaway

**When you need the minimum number of elements to satisfy a sum threshold, and larger elements contribute more, sort in descending order and select greedily until the condition is satisfied.**
