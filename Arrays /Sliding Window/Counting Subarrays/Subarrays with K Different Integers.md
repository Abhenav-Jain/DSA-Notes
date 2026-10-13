# 992. Subarrays with K Different Integers

**Difficulty:** Hard  
**Pattern:** Sliding Window + Hash Map  
**Core Concept:** Exactly K = AtMost(K) − AtMost(K − 1)

## 💡 Problem Intuition

Given an integer array `nums` and an integer `k`, count the number of contiguous subarrays containing **exactly `k` distinct integers**.

Instead of directly counting subarrays with exactly `k` distinct integers, calculate:

- `atMost(k)`: Number of subarrays containing at most `k` distinct integers.
- `atMost(k - 1)`: Number of subarrays containing at most `k - 1` distinct integers.

Subtract them to obtain the exact count.

\[
\boxed{\text{Exactly}(k)=\text{AtMost}(k)-\text{AtMost}(k-1)}
\]

## 💻 C++ Solution — Sliding Window

```cpp
class Solution {
public:
    int count(vector<int>& nums, int k) {
        unordered_map<int, int> mp;

        int i = 0, j = 0;
        int n = nums.size();
        int ans = 0;

        while (j < n) {
            mp[nums[j]]++;

            while (mp.size() > k) {
                mp[nums[i]]--;

                if (mp[nums[i]] == 0) {
                    mp.erase(nums[i]);
                }

                i++;
            }

            ans += j - i + 1;
            j++;
        }

        return ans;
    }

    int subarraysWithKDistinct(vector<int>& nums, int k) {
        return count(nums, k) - count(nums, k - 1);
    }
};
```

## 🔍 Dry Run

**Input:** `nums = [1, 2, 1, 2, 3]`, `k = 2`

We want subarrays containing exactly two distinct integers.

| Right index `j` | Current window | Distinct count | Valid subarrays added |
|---:|---|---:|---:|
| 0 | `[1]` | 1 | 1 |
| 1 | `[1,2]` | 2 | 2 |
| 2 | `[1,2,1]` | 2 | 3 |
| 3 | `[1,2,1,2]` | 2 | 4 |
| 4 | `[3]` after shrinking | 1 | 1 |

Therefore:

- `atMost(2) = 12`
- `atMost(1) = 5`

\[
12-5=\boxed{7}
\]

The seven subarrays containing exactly two distinct integers are:

`[1,2]`, `[2,1]`, `[1,2,1]`, `[2,1,2]`, `[1,2,1,2]`, `[2,1,2]`, `[1,2]`.

## 🧠 Why `ans += j - i + 1`?

After shrinking the window until `mp.size() <= k`, every subarray ending at `j` and starting from any index between `i` and `j` has at most `k` distinct integers.

The number of such subarrays is:

\[
j-i+1
\]

For example, if the current valid window is `[1,2,1]`, its valid subarrays ending at the last index are:

- `[1]`
- `[2,1]`
- `[1,2,1]`

Hence, add `3`.

## ⚙️ How the Hash Map Works

- `mp[nums[j]]++` adds the incoming element's frequency.
- `mp.size()` gives the number of distinct elements because each distinct element has one map entry.
- If `mp.size() > k`, shrink the window from the left.
- Decrement the outgoing element's frequency.
- Erase its key when the frequency reaches zero; otherwise, the distinct count would be incorrect.
- Add `j - i + 1` to count valid subarrays.

## ⚠️ Important Edge Case

If `k <= 0`, the helper should return `0` for an array of positive length.

```cpp
if (k <= 0) return 0;
```

This prevents invalid shrinking when the helper is called with `k - 1` and the original `k` is `0` or less.

For the standard LeetCode constraints, `k >= 1`, so the original solution works as intended.

## ⏱️ Complexity Analysis

- **Expected time:** `O(n)` per helper call, assuming average constant-time hash-map operations.
- **Auxiliary space:** `O(k)` for the frequency map in a valid window, bounded by `O(n)`.

The complete solution makes two helper calls, so its expected time complexity remains `O(n)`.

## 🎯 Interview Takeaways

1. Whenever a problem asks for **exactly K distinct elements**, consider the AtMost technique.
2. Use a frequency map to maintain the number of distinct elements.
3. Shrink the window while `mp.size() > k`.
4. Erase elements whose frequency becomes zero.
5. Count valid subarrays using `j - i + 1`.

## 🔥 Pattern Recognition

| Problem | What is being counted? | Formula |
|---|---|---|
| LC 930 | Binary subarrays with sum `goal` | `AtMost(goal) - AtMost(goal - 1)` |
| LC 1248 | Subarrays with exactly `k` odd numbers | `AtMost(k) - AtMost(k - 1)` |
| LC 992 | Subarrays with exactly `k` distinct integers | `AtMost(k) - AtMost(k - 1)` |

**Remember:** Exactly K is often easier to solve as the difference between two AtMost counts.

**Pattern unlocked:** Sliding Window + Frequency Map + Exactly K.
