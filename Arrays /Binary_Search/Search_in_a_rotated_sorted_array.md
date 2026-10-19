# Search in Rotated Sorted Array — LeetCode 33

## 1. Problem Statement
Given a sorted array that has been rotated at an unknown pivot, search for a target and return its index. If the target does not exist, return `-1`.

**Constraints:** All elements are distinct. The solution must run in `O(log n)` time.

Example:
```text
Input: nums = [4,5,6,7,0,1,2], target = 0
Output: 4
```

## 2. Core Intuition

A rotated sorted array is not entirely sorted, but **at least one half of the current search range is always sorted** when elements are distinct.

At every iteration:
1. Calculate `mid`.
2. If `nums[mid] == target`, return `mid`.
3. Identify the sorted half.
4. Check whether the target lies inside that half.
5. Discard the half that cannot contain the target.

This eliminates half of the remaining search space in every iteration.

## 3. Algorithm

1. Initialize `i = 0` and `j = n - 1`.
2. Continue while `i <= j`.
3. Calculate `mid = i + (j - i) / 2`.
4. If the middle element is the target, return `mid`.
5. If `nums[i] <= nums[mid]`, the left half is sorted:
   - If `nums[i] <= target < nums[mid]`, search left.
   - Otherwise, search right.
6. Otherwise, the right half is sorted:
   - If `nums[mid] < target <= nums[j]`, search right.
   - Otherwise, search left.
7. If the loop finishes, return `-1`.

## 4. C++ Solution

```cpp
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int i = 0;
        int j = nums.size() - 1;

        while (i <= j) {
            int mid = i + (j - i) / 2;

            if (nums[mid] == target) {
                return mid;
            }
            else {
                // Left half is sorted
                if (nums[i] <= nums[mid]) {
                    if (target >= nums[i] &&
                        target < nums[mid]) {
                        j = mid - 1;
                    }
                    else {
                        i = mid + 1;
                    }
                }
                // Right half is sorted
                else {
                    if (target > nums[mid] &&
                        target <= nums[j]) {
                        i = mid + 1;
                    }
                    else {
                        j = mid - 1;
                    }
                }
            }
        }

        return -1;
    }
};
```

## 5. Dry Run

Input:
```text
nums = [4, 5, 6, 7, 0, 1, 2]
target = 0
```

| Step | `i` | `j` | `mid` | `nums[mid]` | Decision |
|---|---:|---:|---:|---:|---|
| 1 | 0 | 6 | 3 | 7 | Left half sorted; target is outside it. Set `i = 4`. |
| 2 | 4 | 6 | 5 | 1 | Right half sorted; target lies in it. Set `j = 4`. |
| 3 | 4 | 4 | 4 | 0 | Target found; return `4`. |

## 6. Understanding the Conditions

### Left half is sorted
```cpp
if (nums[i] <= nums[mid])
```
The elements from index `i` through `mid` are in sorted order.

```cpp
if (target >= nums[i] && target < nums[mid])
```
The target must be in the interval `[nums[i], nums[mid])` to search left. The middle element is excluded because it has already been checked.

### Right half is sorted
```cpp
else
```
The left half is not sorted, so the right half from `mid` through `j` is sorted.

```cpp
if (target > nums[mid] && target <= nums[j])
```
The target must be in `(nums[mid], nums[j]]` to search right.

**Remember:** The target range checks use inclusive/exclusive boundaries deliberately.

## 7. Edge Cases

| Case | Input | Target | Expected output |
|---|---|---:|---:|
| Single element, found | `[1]` | `1` | `0` |
| Single element, absent | `[1]` | `0` | `-1` |
| No rotation | `[1,2,3,4,5]` | `4` | `3` |
| Target at beginning | `[4,5,6,7,0,1,2]` | `4` | `0` |
| Target at end | `[4,5,6,7,0,1,2]` | `2` | `6` |
| Target absent | `[4,5,6,7,0,1,2]` | `3` | `-1` |

For the standard LeetCode constraints, the input array is non-empty. If adapting this code to arbitrary input, the empty-array case can be handled explicitly.

## 8. Complexity Analysis

- **Time Complexity:** `O(log n)` — the search interval is halved in each iteration.
- **Auxiliary Space:** `O(1)` — only a constant number of variables are used.

## 9. Common Mistakes

1. **Using ordinary binary search:** The entire current interval may not be sorted.
2. **Incorrect sorted-half detection:** Use `nums[i] <= nums[mid]` for the left half.
3. **Wrong boundaries:** The left search condition excludes `mid`; the right search condition also excludes `mid`.
4. **Forgetting to move the pointers:** Update either `i` or `j` so the search progresses.
5. **Assuming duplicates are supported:** This implementation relies on distinct elements.

## 10. Interview Follow-ups

**Q1. Why does this run in `O(log n)`?**

Because each iteration identifies a half that cannot contain the target and removes it from the search space.

**Q2. Why is at least one half sorted?**

The rotation creates a break in the original sorted order. A midpoint divides the current interval into two halves; with distinct elements, at least one half remains sorted.

**Q3. What changes if duplicates are allowed?**

If `nums[i] == nums[mid] == nums[j]`, it may be impossible to identify the sorted half confidently. One approach is to shrink the boundaries, but the worst-case time complexity can become `O(n)`.

**Q4. How is this different from ordinary binary search?**

Ordinary binary search assumes the entire array is sorted. This algorithm first identifies a sorted half, then determines whether the target belongs to it.

## 11. Pattern to Remember

**Identify the sorted half → check target range → discard the impossible half.**

This pattern is useful for rotated-array search problems and variations involving finding the minimum element in a rotated sorted array.

## 12. Final Revision Checklist

- [ ] Identify which half is sorted.
- [ ] Check whether the target lies within that half's boundaries.
- [ ] Update the search boundaries correctly.
- [ ] Handle target-not-found using `return -1`.
- [ ] State the distinct-elements assumption.
- [ ] Achieve `O(log n)` time and `O(1)` auxiliary space.
