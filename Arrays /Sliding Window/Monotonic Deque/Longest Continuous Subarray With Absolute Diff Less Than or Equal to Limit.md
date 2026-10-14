# 1438. Longest Continuous Subarray With Absolute Diff Less Than or Equal to Limit

**Difficulty:** Medium  
**Pattern:** Sliding Window + Two Monotonic Deques  
**Core Concept:** Maintain the maximum and minimum of a window in `O(1)` amortized time.

## 1. Problem Statement

Given an integer array `nums` and an integer `limit`, find the length of the longest non-empty contiguous subarray such that the absolute difference between every pair of elements is at most `limit`.

### Example

```text
Input:  nums = [8,2,4,7], limit = 4
Output: 2
```

Explanation:

- `[8]` → valid, length `1`
- `[2,4]` → valid because `|2 - 4| = 2`, length `2`
- `[4,7]` → valid because `|4 - 7| = 3`, length `2`
- `[8,2]` → invalid because `|8 - 2| = 6 > 4`

The longest valid subarray has length `2`.

## 2. 💡 Core Intuition

The key observation is:

**A subarray is valid if and only if its maximum element minus its minimum element is at most `limit`.**

\[
\boxed{\max(\text{window})-\min(\text{window})\leq limit}
\]

Why?

Every element lies between the minimum and maximum. Therefore, no pair can have an absolute difference greater than the difference between these two extremes.

Instead of checking every pair, we only need to maintain the window's maximum and minimum efficiently.

### Why two deques?

- `maxi`: A decreasing monotonic deque that maintains candidate indexes for the maximum.
- `mini`: An increasing monotonic deque that maintains candidate indexes for the minimum.

Both deques store **indexes, not values**, so we can remove elements that leave the window.

## 3. 💻 C++ Solution — My Approach

```cpp
class Solution {
public:
    int longestSubarray(vector<int>& nums, int limit) {
        deque<int> maxi;
        deque<int> mini;

        int i = 0, j = 0;
        int n = nums.size();
        int max_len = 0;

        while (j < n) {
            // 1. Maintain decreasing deque for maximum
            while (!maxi.empty() &&
                   nums[j] > nums[maxi.back()]) {
                maxi.pop_back();
            }

            // 2. Maintain increasing deque for minimum
            while (!mini.empty() &&
                   nums[j] < nums[mini.back()]) {
                mini.pop_back();
            }

            // 3. Insert current index
            maxi.push_back(j);
            mini.push_back(j);

            // 4. Shrink while the window is invalid
            while (nums[maxi.front()] - nums[mini.front()] > limit) {
                i++;

                if (!maxi.empty() && maxi.front() < i) {
                    maxi.pop_front();
                }

                if (!mini.empty() && mini.front() < i) {
                    mini.pop_front();
                }
            }

            // 5. Update maximum valid window length
            max_len = max(max_len, j - i + 1);

            j++;
        }

        return max_len;
    }
};
```

**Note:** This preserves your original algorithm. The `abs()` call is unnecessary because the maximum is always at least the minimum. For the standard LeetCode constraints, this implementation is correct.

## 4. ⚙️ How the Monotonic Deques Work

### A. Maximum deque — `maxi`

Maintain indexes in decreasing order of their corresponding values.

```cpp
while (!maxi.empty() &&
       nums[j] > nums[maxi.back()]) {
    maxi.pop_back();
}
maxi.push_back(j);
```

Whenever a new element is greater than an element at the back, that older element is removed.

Why? The new element is greater and occurs later, so it will remain in the window at least as long as the older element. The older element can never become the maximum while the new element remains.

The front therefore gives the maximum candidate.

### B. Minimum deque — `mini`

Maintain indexes in increasing order of their corresponding values.

```cpp
while (!mini.empty() &&
       nums[j] < nums[mini.back()]) {
    mini.pop_back();
}
mini.push_back(j);
```

A smaller incoming element dominates larger elements at the back because it occurs later and remains in the window longer.

The front therefore gives the minimum candidate.

### C. Expired indexes

When the left pointer moves, any index smaller than `i` is outside the current window.

```cpp
if (!maxi.empty() && maxi.front() < i)
    maxi.pop_front();

if (!mini.empty() && mini.front() < i)
    mini.pop_front();
```

Only the front needs to be checked because the deque indexes are maintained in increasing index order.

## 5. 🔍 Dry Run

**Input:** `nums = [8,2,4,7]`, `limit = 4`

| `j` | Current value | Window after shrinking | Maximum | Minimum | `max - min` | `max_len` |
|---:|---:|---|---:|---:|---:|---:|
| 0 | 8 | `[8]` | 8 | 8 | 0 | 1 |
| 1 | 2 | `[2]` | 2 | 2 | 0 | 1 |
| 2 | 4 | `[2,4]` | 4 | 2 | 2 | 2 |
| 3 | 7 | `[2,4,7]` → `[4,7]` | 7 | 4 | 3 | 2 |

At `j = 1`, the window `[8,2]` is invalid because:

\[
8-2=6>4
\]

Move `i` forward, removing index `0` from the maximum deque. The window becomes `[2]`, which is valid.

At `j = 3`, `[2,4,7]` is valid because:

\[
7-2=5>4
\]

Move `i` forward once more. The window becomes `[4,7]`, where:

\[
7-4=3\leq4
\]

The maximum valid length is **2**.

## 6. 🎯 Why the Sliding Window Is Correct

The algorithm follows three important properties:

1. The deques give the exact maximum and minimum of the current window because they retain the relevant candidate indexes.
2. If `max - min > limit`, the window is invalid, so the left pointer moves forward until the window becomes valid.
3. After shrinking, `j - i + 1` is the length of a valid window ending at `j`. Taking the maximum over all right endpoints gives the longest valid subarray.

The left pointer never moves backwards, so the algorithm avoids repeatedly processing earlier elements.

## 7. ⚠️ Common Mistakes

- **Storing values instead of indexes:** You cannot correctly identify which occurrence has left the window.
- **Using one deque only:** Maintaining both maximum and minimum efficiently requires two deques.
- **Removing the wrong end:** Remove dominated candidates from the back; remove expired candidates from the front.
- **Checking only one pair:** The maximum-minus-minimum condition automatically handles every pair.
- **Updating the answer before shrinking:** That could record an invalid window.
- **Forgetting empty checks:** Never access `front()` or `back()` without ensuring the deque is non-empty.
- **Using `<=` instead of `<` when removing expired indexes:** Index `i` is still inside the current window.

## 8. ⏱️ Complexity Analysis

**Time: `O(n)`**

Each index is inserted into each deque once and removed at most once. Although there are nested `while` loops, the total number of deque operations is linear.

**Auxiliary space: `O(n)` worst case**

Each deque can contain up to `n` indexes. Together, they still use `O(n)` space.

## 9. 🔥 Interview Takeaways

- Use **two monotonic deques** when a sliding-window problem needs both the maximum and minimum.
- Convert an all-pairs absolute-difference condition into `max - min <= limit`.
- Expand the window with `j`; shrink it with `i` whenever the condition fails.
- The maximum and minimum can be obtained in `O(1)` from the deque fronts.
- The overall algorithm is `O(n)`, compared with a straightforward approach that may take `O(n²)` or more.

### Pattern Connection

| Problem | Data structure |
|---|---|
| LC 239 — Sliding Window Maximum | One monotonic deque |
| LC 1438 — Longest Continuous Subarray | Two monotonic deques |
| LC 76 — Minimum Window Substring | Sliding window + frequency map |

**Remember:** When a window's validity depends on its maximum and minimum, think **Sliding Window + Two Monotonic Deques**.
