# Find Peak Element — LeetCode 162

## 1. Problem Statement
Given an array `nums`, find a peak element and return its index.

A peak element is an element strictly greater than its adjacent elements.

- `nums[i] > nums[i-1]` (if left neighbor exists)
- `nums[i] > nums[i+1]` (if right neighbor exists)

For boundary elements, the missing neighbor is treated as negative infinity.

**Important:** The array is not necessarily sorted. Any valid peak index can be returned.

## 2. My Accepted C++ Solution

```cpp
class Solution {
public:
    int findPeakElement(vector<int>& nums) {
        int low = 0;
        int high = nums.size()-1;

        if(nums.size() == 1){
            return 0;
        }

        while(low < high){
            int mid = low + (high-low)/2;

            if(nums[mid] < nums[mid+1]){
                low = mid + 1;
            }
            else{
                high = mid;
            }
        }

        return low;
    }
};
```

## 3. Core Intuition — Binary Search on Slope

We do not search for a particular value. Instead, we examine the **direction of the slope** between `nums[mid]` and `nums[mid+1]`.

There are two cases:

### Case 1: Increasing slope
`nums[mid] < nums[mid+1]`

The next element is greater, so a peak must exist somewhere to the right.

```cpp
low = mid + 1;
```

### Case 2: Decreasing slope
`nums[mid] > nums[mid+1]`

A peak exists at `mid` or somewhere to its left.

```cpp
high = mid;
```

**Why `high = mid`, not `mid - 1`?**

Because `mid` itself may be the peak. Removing it could discard a valid answer.

Under LeetCode 162's constraints, adjacent elements are unequal, so the two cases cover every comparison.

## 4. Dry Run

Input:

```cpp
nums = {1, 2, 1, 3, 5, 6, 4};
```

| low | high | mid | nums[mid] | nums[mid+1] | Decision |
|---:|---:|---:|---:|---:|---|
| 0 | 6 | 3 | 3 | 5 | `low = 4` |
| 4 | 6 | 5 | 6 | 4 | `high = 5` |
| 4 | 5 | 4 | 5 | 6 | `low = 5` |

Now `low == high == 5`.

Return index `5`, where `nums[5] = 6`.

Since `6 > 5` and `6 > 4`, it is a valid peak.

**Note:** Another valid peak may exist at index `1`, but returning any one valid peak is acceptable.

## 5. Why Does Binary Search Work?

We maintain the invariant that **at least one peak exists in the current search interval**.

- If `nums[mid] < nums[mid+1]`, the slope rises to the right. Continuing right must eventually lead to a peak, including a possible boundary peak.
- Otherwise, the slope falls to the right. A peak exists at `mid` or to its left, so we retain the left half including `mid`.

Each iteration reduces the search interval. Eventually, `low == high`, leaving one index that must be a peak.

This is called **binary search on a monotonic property / slope direction**, not ordinary binary search on a sorted array.

## 6. Edge Cases

| Input | Possible output | Reason |
|---|---:|---|
| `[1]` | `0` | The only element is a peak |
| `[1, 2]` | `1` | Last element is greater |
| `[2, 1]` | `0` | First element is greater |
| `[1, 2, 3, 4]` | `3` | Increasing array; peak at right boundary |
| `[4, 3, 2, 1]` | `0` | Decreasing array; peak at left boundary |
| `[1, 3, 2]` | `1` | Middle element is a peak |

The separate `nums.size() == 1` check is correct, but optional: the binary search already handles a single-element array.

## 7. Complexity Analysis

- **Time:** `O(log n)` — the search interval is approximately halved each iteration.
- **Auxiliary Space:** `O(1)` — only a constant number of variables are used.

## 8. Common Mistakes

1. **Returning `mid` whenever `nums[mid] > nums[mid+1]`.**  
   This is incorrect because the peak might be further left. Example: `[1, 5, 4, 3, 2, 1, 0, -1]`. At `mid = 3`, the current element is greater than its right neighbor but is not a peak.

2. **Using `high = mid - 1`.**  
   This can discard `mid`, even though `mid` may be the peak.

3. **Using `while(low <= high)` without changing the logic.**  
   This implementation is designed around `while(low < high)` and the convergence of the two boundaries.

4. **Assuming the array must be sorted.**  
   It does not need to be sorted; the slope comparison is sufficient.

5. **Checking both neighbors explicitly at every midpoint.**  
   This is unnecessary for the binary-search solution.

## 9. Interview Follow-ups

**Q1. Why can binary search be applied to an unsorted array?**

Because the decision is based on the local slope, not on the array being globally sorted. The slope tells us which half is guaranteed to contain a peak.

**Q2. Why does `high = mid` preserve correctness?**

The descending-slope case guarantees that a peak exists at `mid` or to its left. Therefore, `mid` must remain in the search interval.

**Q3. Can the answer be unique?**

No. Multiple peaks can exist, and the problem accepts any valid peak index.

**Q4. What if adjacent elements can be equal?**

The standard LeetCode 162 constraints guarantee `nums[i] != nums[i+1]`. If equal adjacent values were allowed, this exact two-case slope argument would need reconsideration.

## 10. Pattern Recognition

Recognize this pattern when:
- The problem asks for a peak or local maximum.
- The array is not globally sorted.
- A comparison between neighboring elements reveals which direction to search.
- The problem guarantees that a valid peak exists.

**Pattern:** Binary Search on Slope / Local Structure

**One-line revision:**

If `nums[mid] < nums[mid+1]`, move right; otherwise, keep `mid` and move the right boundary to it. Return `low` when both boundaries converge.
