# Binary Subarrays With Sum — LeetCode 930

**Difficulty:** Medium  
**Pattern:** Sliding Window + At Most K Technique  
**Approach:** `AtMost(goal) - AtMost(goal - 1)`  
**Time Complexity:** `O(n)`  
**Auxiliary Space:** `O(1)`

---

## 1. Problem Intuition

Given a binary array `nums` containing only `0` and `1`, and an integer `goal`, count the number of non-empty contiguous subarrays whose sum is **exactly equal to `goal`**.

### Example

```text
nums = [1, 0, 1, 0, 1]
goal = 2

Output = 4
```

Valid subarrays:

```text
[1, 0, 1]          // indices 0–2
[1, 0, 1, 0]       // indices 0–3
[0, 1, 0, 1]       // indices 1–4
[1, 0, 1]          // indices 2–4
```

All four have sum exactly `2`.

**Important:** The subarray must be contiguous, and the sum must equal `goal`, not merely be less than or equal to it.

---

## 2. Core Observation — Exactly K = At Most K − At Most K−1

A direct sliding window for exactly `goal` is tricky because zeros can create multiple valid subarrays with the same sum.

Instead, calculate two easier quantities:

- `atMost(goal)`: number of subarrays whose sum is at most `goal`.
- `atMost(goal - 1)`: number of subarrays whose sum is at most `goal - 1`.

Subtracting them leaves only subarrays whose sum is exactly `goal`.

\[
\boxed{\text{Exactly}(k)=\text{AtMost}(k)-\text{AtMost}(k-1)}
\]

### Why does this work?

Every subarray with sum at most `goal` belongs to one of these groups:

1. Sum strictly less than `goal`.
2. Sum exactly equal to `goal`.

`atMost(goal - 1)` counts the first group. Subtracting it removes those subarrays and leaves only the second group.

---

## 3. Your C++ Solution

```cpp
class Solution {
public:
    int subarrays(vector<int>& nums, int goal) {
        int n = nums.size();
        int i = 0;
        int j = 0;
        int cnt = 0;
        int sum = 0;

        if (goal < 0) {
            return 0;
        }

        while (j < n) {
            sum += nums[j];

            while (sum > goal) {
                sum -= nums[i];
                i++;
            }

            cnt += j - i + 1;
            j++;
        }

        return cnt;
    }

    int numSubarraysWithSum(vector<int>& nums, int goal) {
        return subarrays(nums, goal)
             - subarrays(nums, goal - 1);
    }
};
```

**Your implementation is correct** for the problem's binary-array constraints.

---

## 4. Line-by-Line Explanation

### Step 1 — Handle negative goals

```cpp
if (goal < 0) {
    return 0;
}
```

A binary array contains only non-negative elements. No subarray can have a negative sum.

This check is essential when `goal == 0`, because the second call evaluates `subarrays(nums, -1)`.

### Step 2 — Expand the window

```cpp
sum += nums[j];
```

Include the rightmost element in the current window.

### Step 3 — Shrink while the sum exceeds the goal

```cpp
while (sum > goal) {
    sum -= nums[i];
    i++;
}
```

Remove elements from the left until the window sum is at most `goal`.

Since the elements are non-negative, removing elements cannot increase the sum.

### Step 4 — Count valid subarrays ending at `j`

```cpp
cnt += j - i + 1;
```

Once the window `[i, j]` is valid, all of these subarrays are valid:

```text
nums[j]
nums[j-1 ... j]
nums[j-2 ... j]
...
nums[i ... j]
```

There are exactly `j - i + 1` such subarrays.

### Step 5 — Calculate exactly `goal`

```cpp
return subarrays(nums, goal)
     - subarrays(nums, goal - 1);
```

This removes all subarrays whose sums are smaller than `goal`, leaving exactly those whose sums equal it.

---

## 5. Dry Run — `atMost(goal)`

```text
nums = [1, 0, 1, 0, 1]
goal = 2
```

| `j` | Added element | Valid window after shrinking | `sum` | Added subarrays | Running count |
|---:|---:|---|---:|---:|---:|
| 0 | 1 | `[1]` | 1 | 1 | 1 |
| 1 | 0 | `[1,0]` | 1 | 2 | 3 |
| 2 | 1 | `[1,0,1]` | 2 | 3 | 6 |
| 3 | 0 | `[1,0,1,0]` | 2 | 4 | 10 |
| 4 | 1 | `[0,1,0,1]` | 2 | 4 | 14 |

Therefore:

```text
atMost(2) = 14
```

For the same array:

```text
atMost(1) = 10
```

So:

\[
\text{Exactly}(2)=14-10=\boxed{4}
\]

**Final answer: `4`**

---

## 6. Why Does `j - i + 1` Work Even When Zeros Exist?

Suppose the current valid window is:

```text
[1, 0, 1, 0]
```

Its sum is `2`.

All subarrays ending at the current right pointer have sums no greater than `2`, because the array contains only non-negative values.

For example:

```text
[0]
[1, 0]
[0, 1, 0]
[1, 0, 1, 0]
```

Each is valid for `atMost(2)`.

Zeros may produce several subarrays with the same sum, but `j - i + 1` counts every possible starting position exactly once.

---

## 7. Why Not Count `sum == goal` Directly?

A condition such as:

```cpp
if (sum == goal) {
    cnt++;
}
```

does not count all valid subarrays.

For example:

```text
nums = [1, 0, 1]
goal = 1
```

The valid subarrays are:

```text
[1]       // index 0
[1, 0]    // indices 0–1
[0, 1]    // indices 1–2
[1]       // index 2
```

There are four valid subarrays. A single equality check per right endpoint can miss the additional subarrays created by zeros.

The at-most technique handles these overlapping windows systematically.

---

## 8. Complexity Analysis

Let `n = nums.size()`.

**Time: `O(n)`**

Each `subarrays()` call is linear:

- `j` moves forward at most `n` times.
- `i` also moves forward at most `n` times.
- The two calls together still take `O(n)` time.

**Auxiliary Space: `O(1)`**

Only pointers, counters, and a running sum are used. No extra data structure is required.

---

## 9. Edge Cases

| Input | Goal | Expected output |
|---|---:|---:|
| `[1,0,1,0,1]` | 2 | 4 |
| `[0,0,0,0]` | 0 | 10 |
| `[1,1,1]` | 0 | 0 |
| `[1,1,1]` | 2 | 2 |
| `[0,1,0]` | 1 | 4 |

For `[0,0,0,0]` and `goal = 0`, every non-empty subarray is valid:

\[
\frac{4(4+1)}{2}=10
\]

The `goal < 0` base case ensures that `atMost(-1)` returns `0`.

---

## 10. Common Mistakes

1. Returning `atMost(goal)` directly instead of subtracting `atMost(goal - 1)`.
2. Forgetting the negative-goal check.
3. Using `if (sum > goal)` instead of `while`, which may leave the window invalid.
4. Counting only one subarray when `sum == goal`.
5. Forgetting that zeros can create multiple valid subarrays with the same sum.
6. Using this sum-based sliding-window technique unchanged with negative array elements; the required monotonicity no longer holds.

---

## 11. Pattern Recognition

Look for this technique when:

- The problem asks for the number of subarrays with sum exactly `k`.
- The array contains non-negative elements.
- Counting exactly `k` directly is awkward.
- You can efficiently count subarrays with sum at most a threshold.

### Related Problems

- LC 713 — Subarray Product Less Than K
- LC 992 — Subarrays with K Different Integers
- LC 1248 — Count Number of Nice Subarrays

The same general principle appears in many problems:

\[
\boxed{\text{Exactly}(k)=\text{AtMost}(k)-\text{AtMost}(k-1)}
\]

---

## One-Line Revision

Count subarrays with sum at most `goal`, subtract those with sum at most `goal - 1`, and the difference gives the number whose sum is exactly `goal`.
