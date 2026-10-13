# 1248. Count Number of Nice Subarrays

**Difficulty:** Medium  
**Pattern:** Sliding Window + At Most K  
**Approach:** `Exactly(K) = AtMost(K) - AtMost(K - 1)`

## 💡 Intuition

A *nice subarray* is a contiguous subarray containing exactly `k` odd numbers.

Instead of directly counting subarrays with exactly `k` odd numbers, count:

- Subarrays containing **at most `k` odd numbers**
- Subarrays containing **at most `k - 1` odd numbers**

Subtracting these counts gives the number of subarrays containing exactly `k` odd numbers.

\[
\boxed{\text{Exactly}(k)=\text{AtMost}(k)-\text{AtMost}(k-1)}
\]

## 💻 C++ Solution

```cpp
class Solution {
public:
    bool is_odd(int el) {
        return el % 2 != 0;
    }

    int atMost(vector<int>& nums, int k) {
        if (k < 0) return 0;

        int i = 0, j = 0;
        int cnt = 0;
        int ans = 0;
        int n = nums.size();

        while (j < n) {
            if (is_odd(nums[j])) {
                cnt++;
            }

            while (cnt > k) {
                if (is_odd(nums[i])) {
                    cnt--;
                }
                i++;
            }

            ans += j - i + 1;
            j++;
        }

        return ans;
    }

    int numberOfSubarrays(vector<int>& nums, int k) {
        return atMost(nums, k) - atMost(nums, k - 1);
    }
};
```

## 🔍 Dry Run

**Input:** `nums = [1,1,2,1,1], k = 3`

We need subarrays containing exactly 3 odd numbers.

| Subarray | Odd count | Nice? |
|---|---:|---|
| `[1,1,2,1]` | 3 | Yes |
| `[1,2,1,1]` | 3 | Yes |
| `[1,1,2,1,1]` | 4 | No |

The answer is **2**.

Using the formula:

- `atMost(3) = 14`
- `atMost(2) = 12`

\[
14-12=\boxed{2}
\]

## 🧠 Why `ans += j - i + 1`?

After shrinking the window until it contains at most `k` odd numbers, every subarray ending at index `j` and starting between `i` and `j` is valid.

The number of such subarrays is:

\[
j-i+1
\]

For example, if the valid window is `[2, 1, 1]`, the subarrays ending at its last index are:

- `[1]`
- `[1, 1]`
- `[2, 1, 1]`

So we add `3`, not the total number of subarrays inside the window.

## ⚠️ Important Edge Case

When `k = 0`, the formula calls `atMost(-1)`.

Without a guard, the shrinking loop may move `i` out of bounds.

```cpp
if (k < 0) return 0;
```

This must be the first condition inside `atMost()`.

## ⏱️ Complexity Analysis

- **Time:** `O(n)` — each pointer moves forward at most `n` times per helper call.
- **Auxiliary space:** `O(1)` — only a constant number of variables are used.

## 🎯 Interview Takeaways

1. Recognize the **exactly K** counting pattern.
2. Transform the problem into counting subarrays with **at most K** odd numbers.
3. Use a sliding window to count valid subarrays ending at each right pointer.
4. Handle `k < 0` to avoid invalid windows and out-of-bounds access.

**Pattern to remember:**

`Exactly(K) = AtMost(K) - AtMost(K - 1)`

This same pattern applies to problems involving exactly `K` distinct integers, exactly `K` odd numbers, and binary subarrays with a given sum.
