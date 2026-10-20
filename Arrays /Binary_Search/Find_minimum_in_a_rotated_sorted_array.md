# 🔍 Find Minimum in Rotated Sorted Array — LeetCode 153

## 1. Problem Statement

A sorted array of **distinct integers** is rotated between 1 and `n` times. Find and return the minimum element in `O(log n)` time.

### Example 1
```text
Input:  nums = [3,4,5,1,2]
Output: 1
```

### Example 2
```text
Input:  nums = [4,5,6,7,0,1,2]
Output: 0
```

### Example 3
```text
Input:  nums = [11,13,15,17]
Output: 11
```

## 2. Core Intuition

The array was originally sorted, but rotation creates a point where the order drops.

Example:
```text
[4, 5, 6, 7, 0, 1, 2]
             ^
          Minimum
```

The key observation is that **at least one side of the current search range is sorted**.

We use binary search to identify which side may contain the minimum and discard the other side.

There are two useful approaches:

1. **Maintain an answer variable:** Track the best minimum found so far.
2. **Use binary search boundaries directly:** Narrow the range until `low == high`; that index contains the minimum.

---

## 3. Approach 1 — Original Code: Maintain `ans`

### C++ Solution

```cpp
class Solution {
public:
    int findMin(vector<int>& nums) {
        int low = 0;
        int high = nums.size() - 1;
        int ans = INT_MAX;

        while (low <= high) {
            int mid = low + (high - low) / 2;

            if (nums[mid] >= nums[low]) {
                ans = min(ans, nums[low]);
                low = mid + 1;
            }
            else {
                ans = min(ans, nums[mid]);
                high = mid - 1;
            }
        }

        return ans;
    }
};
```

### How it works

**Step 1: Initialize the search range**
```cpp
int low = 0;
int high = nums.size() - 1;
int ans = INT_MAX;
```

- `low` and `high` represent the current search boundaries.
- `ans` stores the smallest candidate found so far.
- `INT_MAX` is the initial value because any valid array element is smaller.

**Step 2: Calculate the middle**
```cpp
int mid = low + (high - low) / 2;
```

This avoids the potential integer overflow of `(low + high) / 2`.

**Step 3: Identify the sorted left half**
```cpp
if (nums[mid] >= nums[low]) {
    ans = min(ans, nums[low]);
    low = mid + 1;
}
```

If `nums[mid] >= nums[low]`, the range from `low` to `mid` is sorted.

Its minimum is `nums[low]`, so we record that candidate. We then search to the right because the rotation point—and therefore the global minimum—may lie there.

**Step 4: Handle the other case**
```cpp
else {
    ans = min(ans, nums[mid]);
    high = mid - 1;
}
```

If the left half is not sorted, the minimum lies between `low` and `mid`.

We record `nums[mid]` as a candidate and continue searching to the left.

**Step 5: Return the best candidate**
```cpp
return ans;
```

The minimum candidate encountered during the search is returned.

### Dry Run

Input:
```text
nums = [4,5,6,7,0,1,2]
```

| `low` | `high` | `mid` | `nums[mid]` | `ans` | Action |
|---:|---:|---:|---:|---:|---|
| 0 | 6 | 3 | 7 | 4 | Left half sorted; record `4`, move `low` to 4 |
| 4 | 6 | 5 | 1 | 1 | Left half not sorted; record `1`, move `high` to 4 |
| 4 | 4 | 4 | 0 | 0 | Left half sorted; record `0`, move `low` to 5 |

The loop terminates and returns `0`.

### Complexity

- Time: `O(log n)`
- Auxiliary space: `O(1)`

**Note:** This approach is correct for the standard problem constraints: a non-empty rotated sorted array with distinct elements.

---

## 4. Approach 2 — Optimized Code: Search Boundaries Only

### C++ Solution

```cpp
class Solution {
public:
    int findMin(vector<int>& nums) {
        int low = 0;
        int high = nums.size() - 1;

        while (low < high) {

            // Current range is already sorted
            if (nums[low] < nums[high]) {
                return nums[low];
            }

            int mid = low + (high - low) / 2;

            if (nums[mid] >= nums[low]) {
                low = mid + 1;
            }
            else {
                high = mid;
            }
        }

        return nums[low];
    }
};
```

### Core Difference

The optimized version does not maintain a separate `ans` variable. Instead, it preserves a search range that contains the minimum until only one candidate remains.

### Step-by-step explanation

**Step 1: Initialize boundaries**
```cpp
int low = 0;
int high = nums.size() - 1;
```

Initially, the entire array is the search range.

**Step 2: Continue while multiple candidates remain**
```cpp
while (low < high)
```

When `low == high`, exactly one candidate remains, so the loop can stop.

**Step 3: Check whether the current range is sorted**
```cpp
if (nums[low] < nums[high]) {
    return nums[low];
}
```

If the first element is smaller than the last element, the current range is strictly increasing. Its first element is the minimum, so we can return immediately.

This is an important optimization: it avoids further binary-search iterations when the remaining range is already sorted.

**Step 4: Identify the relevant half**
```cpp
if (nums[mid] >= nums[low]) {
    low = mid + 1;
}
else {
    high = mid;
}
```

- If the left portion is sorted, its minimum is at `low`. Because the current range was not already fully sorted, the rotation point is to the right, so set `low = mid + 1`.
- Otherwise, the minimum lies in the left portion, including `mid`, so set `high = mid`.

**Step 5: Return the remaining candidate**
```cpp
return nums[low];
```

At termination, `low == high`, and that position contains the minimum.

---

## 5. Dry Run — Optimized Approach

Input:
```text
nums = [4,5,6,7,0,1,2]
```

| Step | `low` | `high` | `mid` | `nums[mid]` | Decision |
|---|---:|---:|---:|---:|---|
| 1 | 0 | 6 | 3 | 7 | Left half sorted; `low = 4` |
| 2 | 4 | 6 | 5 | 1 | `nums[4] > nums[5]`; set `high = 5` |
| 3 | 4 | 5 | 4 | 0 | Set `high = 4` |
| End | 4 | 4 | — | 0 | Return `nums[4] = 0` |

The minimum is `0`.

### Why is `high = mid` important?

```cpp
high = mid;
```

The middle element might itself be the minimum, so we must retain it as a candidate.

If we used `high = mid - 1`, we could discard the actual minimum.

By contrast, in your original code, the middle candidate is recorded in `ans` before setting `high = mid - 1`, so that update is safe there.

**This distinction is crucial:** the original code stores candidates separately; the optimized code keeps the answer inside the search range.

---

## 6. Comparison: Original vs Optimized

| Feature | Original approach | Optimized approach |
|---|---|---|
| Answer storage | Uses `ans` | No separate `ans` |
| Loop condition | `low <= high` | `low < high` |
| Search update | `high = mid - 1` in the left-side case | `high = mid` |
| Early return for sorted range | No | Yes |
| Time complexity | `O(log n)` | `O(log n)` |
| Auxiliary space | `O(1)` | `O(1)` |
| Main idea | Track minimum candidates | Maintain a range containing the minimum |

Both approaches are optimal in asymptotic time complexity. The second is more compact and can terminate early if the current range is already sorted.

---

## 7. Edge Cases

| Input | Expected output | Reason |
|---|---:|---|
| `[1]` | `1` | Only one element |
| `[3,4,5,1,2]` | `1` | Minimum near the middle |
| `[4,5,6,7,0,1,2]` | `0` | Rotation point is near the middle |
| `[11,13,15,17]` | `11` | Already sorted array |
| `[2,1]` | `1` | Minimum at the end |
| `[3,1,2]` | `1` | Minimum near the beginning |

The standard LeetCode problem guarantees a non-empty array, so neither implementation needs an empty-array guard under those constraints. For reusable production code, validate that the input is non-empty.

---

## 8. Common Interview Mistakes

1. **Using `high = mid - 1` in the optimized approach:** This may discard the minimum.
2. **Forgetting the sorted-range shortcut:** If `nums[low] < nums[high]`, the minimum is `nums[low]`.
3. **Confusing the two approaches:** The original records candidates in `ans`; the optimized approach keeps the minimum inside the search range.
4. **Using ordinary binary search:** The whole array may not be sorted after rotation.
5. **Ignoring the distinct-elements constraint:** The standard algorithm relies on distinct values. Duplicates can make the sorted-half decision ambiguous.
6. **Using an unsafe midpoint formula:** Prefer `low + (high - low) / 2`.

---

## 9. Interview Follow-up Questions

**Q1. Why does binary search work here?**

The rotation creates a boundary between two sorted portions. Comparing values at the boundaries and midpoint tells us which portion can contain the minimum.

**Q2. Why can the optimized approach return `nums[low]` when `nums[low] < nums[high]`?**

The current range is strictly increasing, so its first element is the smallest in that range.

**Q3. Why does the optimized approach use `while (low < high)`?**

It stops when one candidate remains. At that point, `low == high`, and that element is the answer.

**Q4. What happens if duplicates are allowed?**

The sorted-half detection can become ambiguous. For example, if `nums[low] == nums[mid] == nums[high]`, comparisons alone may not reveal which side contains the rotation point. A duplicate-aware solution may need to shrink a boundary, giving `O(n)` worst-case time.

**Q5. What is the time and space complexity?**

Both solutions run in `O(log n)` time and use `O(1)` auxiliary space.

---

## 10. Pattern to Remember

**For the answer-variable approach:**

Identify a sorted portion → record its minimum candidate → discard the irrelevant portion → return the best candidate.

**For the boundary-only approach:**

Check whether the current range is sorted → keep the half that contains the minimum → stop when `low == high`.

### Final Revision Checklist

- [ ] Understand why a rotated array has sorted portions.
- [ ] Know when to update `ans`.
- [ ] Understand why optimized code uses `high = mid`.
- [ ] Remember the early return for an already sorted range.
- [ ] Explain why both solutions are `O(log n)` time and `O(1)` space.
- [ ] State that the standard problem assumes distinct elements.
