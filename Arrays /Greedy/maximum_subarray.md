# Maximum Subarray — LeetCode 53

**Pattern:** Kadane's Algorithm (Greedy)  
**Difficulty:** Medium

## 1. Problem
Given an integer array `nums`, find the **contiguous subarray** with the largest sum and return that sum.

**Example:**
```cpp
Input:  [-2,1,-3,4,-1,2,1,-5,4]
Output: 6
```
Maximum subarray: `[4,-1,2,1]` → Sum = `6`.

## 2. Approach — Kadane's Algorithm

Maintain two variables:

- `sum`: Sum of the current subarray.
- `max_sum`: Maximum subarray sum found so far.

For every element:
1. Add the current element to `sum`.
2. Update `max_sum`.
3. If `sum < 0`, reset it to `0`.

**Why reset when `sum < 0`?**

A negative running sum only reduces the sum of any subarray extended from it. Starting fresh from the next element is better.

## 3. C++ Code

```cpp
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int sum = 0;
        int max_sum = INT_MIN;

        for (int i = 0; i < nums.size(); i++) {
            sum += nums[i];

            max_sum = max(max_sum, sum);

            if (sum < 0) {
                sum = 0;
            }
        }

        return max_sum;
    }
};
```

## 4. Dry Run

`nums = [-2,1,-3,4,-1,2,1,-5,4]`

| Element | Running sum | Maximum sum | Action |
|---:|---:|---:|---|
| -2 | -2 | -2 | Reset to 0 |
| 1 | 1 | 1 | Continue |
| -3 | -2 | 1 | Reset to 0 |
| 4 | 4 | 4 | Continue |
| -1 | 3 | 4 | Continue |
| 2 | 5 | 5 | Continue |
| 1 | 6 | 6 | Continue |
| -5 | 1 | 6 | Continue |
| 4 | 5 | 6 | End |

**Answer: `6`**

## 5. Important Edge Cases

**All negative numbers**
```cpp
nums = [-5, -2, -8]
Output: -2
```
`max_sum = INT_MIN` ensures the largest negative element is returned instead of `0`.

**Single element**
```cpp
nums = [7]
Output: 7
```

**All positive numbers**
```cpp
nums = [1, 2, 3, 4]
Output: 10
```

## 6. Complexity

- **Time:** `O(n)` — one traversal.
- **Auxiliary Space:** `O(1)` — only two variables.

## 7. Common Mistakes

- Initializing `max_sum = 0` incorrectly returns `0` for an all-negative array.
- Resetting `sum` before updating `max_sum` can lose the best negative subarray.
- The problem requires a **contiguous** subarray, not an arbitrary subsequence.

## 8. Interview Takeaways

- Kadane's Algorithm optimizes the brute-force approach to `O(n)`.
- A negative running sum is discarded because it cannot improve a future subarray.
- Update the maximum **before** resetting the running sum.
- The algorithm returns the maximum sum, not the subarray itself.

**Revision mantra:** Add → Update maximum → Reset if negative.
