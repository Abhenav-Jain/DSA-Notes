# Maximum Erasure Value — LeetCode 1695

**Difficulty:** Medium  
**Pattern:** Variable Sliding Window + Hashing + Running Sum  
**Time Complexity:** `O(n)` average  
**Space Complexity:** `O(n)`

## 1. Problem Intuition

Given an integer array `nums`, find the maximum possible sum of a **non-empty subarray containing only unique elements**.

A subarray means a contiguous portion of the array.

### Example

```text
nums = [4, 2, 4, 5, 6]

Output: 17
```

The optimal subarray is `[2, 4, 5, 6]`.

```text
Score = 2 + 4 + 5 + 6 = 17
```

We need to maximize the sum while ensuring that no element repeats.

## 2. Core Idea

Maintain a sliding window `[i, j]` containing unique elements.

- `i`: left boundary.
- `j`: right boundary.
- `mp`: frequency of elements in the current window.
- `score`: sum of the current window.
- `max_score`: maximum valid sum found so far.

### Window Validity

Every element inside the window must appear exactly once.

```cpp
while (mp[nums[j]] > 1)
```

If adding `nums[j]` creates a duplicate, remove elements from the left until the duplicate disappears.

At every step, update the maximum score.

## 3. C++ Solution — Your Map-Based Approach

```cpp
class Solution {
public:
    int maximumUniqueSubarray(vector<int>& nums) {
        int score = 0;
        int max_score = 0;
        int i = 0;
        int j = 0;

        unordered_map<int, int> mp;
        int n = nums.size();

        while (j < n) {
            mp[nums[j]]++;
            score += nums[j];

            while (mp[nums[j]] > 1) {
                mp[nums[i]]--;
                score -= nums[i];
                i++;
            }

            max_score = max(max_score, score);
            j++;
        }

        return max_score;
    }
};
```

**Why it works:** After the inner `while` loop, the current window contains unique elements. Therefore, its sum is a valid candidate for the maximum score.

## 4. Dry Run

```text
nums = [4, 2, 4, 5, 6]
```

| `j` | Added element | Window after shrinking | `score` | `max_score` |
|---:|---:|---|---:|---:|
| 0 | 4 | `[4]` | 4 | 4 |
| 1 | 2 | `[4, 2]` | 6 | 6 |
| 2 | 4 | `[2, 4]` | 6 | 6 |
| 3 | 5 | `[2, 4, 5]` | 11 | 11 |
| 4 | 6 | `[2, 4, 5, 6]` | 17 | 17 |

At index `2`, the new `4` creates a duplicate. We remove the first `4` from the left, so the window becomes `[2, 4]`.

**Final answer: `17`**

## 5. Important Optimization — `unordered_set`

In this problem, we only need to know whether an element already exists in the window. We don't actually need its frequency.

So, a set is a simpler alternative:

```cpp
class Solution {
public:
    int maximumUniqueSubarray(vector<int>& nums) {
        unordered_set<int> st;

        int i = 0;
        int score = 0;
        int max_score = 0;

        for (int j = 0; j < nums.size(); j++) {
            while (st.count(nums[j])) {
                st.erase(nums[i]);
                score -= nums[i];
                i++;
            }

            st.insert(nums[j]);
            score += nums[j];

            max_score = max(max_score, score);
        }

        return max_score;
    }
};
```

### Map vs Set

| Requirement | Best fit |
|---|---|
| Check whether an element exists | `unordered_set` |
| Count element frequencies | `unordered_map` |
| Maintain a running sum | Either works |

Both approaches have **`O(n)` average time and `O(n)` auxiliary space**.

## 6. Why Maintain `score` Instead of Recalculating the Sum?

A brute-force approach might repeatedly calculate the sum of every candidate subarray. That can take `O(n²)` or more, depending on the implementation.

Instead, maintain a running sum:

```cpp
score += nums[j];  // Add right element
score -= nums[i];  // Remove left element
```

Each element is added and removed at most once, keeping the algorithm linear on average.

## 7. Why Does the Inner Loop Use `while`?

Consider:

```text
nums = [1, 2, 3, 2, 4]
```

When the second `2` arrives, removing just `1` is not enough to remove the duplicate. We must also remove the first `2`.

Therefore, we keep shrinking until the duplicate is gone:

```cpp
while (mp[nums[j]] > 1)
```

A single `if` would not guarantee a valid window.

## 8. Complexity Analysis

**Time: `O(n)` average**

- The right pointer visits each element once.
- The left pointer only moves forward.
- Each element enters and leaves the window at most once.
- Hash operations are `O(1)` on average.

**Space: `O(n)`**

In the worst case, all `n` elements can be distinct, so the map or set may store up to `n` elements.

## 9. Common Mistakes

1. Forgetting to subtract `nums[i]` from the running sum while shrinking.
2. Decreasing the frequency without advancing `i`.
3. Using `if` instead of `while` when multiple elements may need to be removed.
4. Updating the answer before the window becomes valid.
5. Recalculating the entire window sum unnecessarily.
6. Assuming `unordered_set` has better Big-O space complexity than `unordered_map`. Both are `O(n)` here, although their actual memory usage may differ.

## 10. Pattern Recognition

Look for these clues:

- A contiguous subarray is required.
- Elements must be unique.
- We need the maximum sum.
- The window can be repaired by moving the left pointer.

**Pattern:** Expand right → detect duplicate → shrink from left while updating the running sum → update maximum score.

### Related Problems

- LC 3 — Longest Substring Without Repeating Characters
- LC 904 — Fruit Into Baskets
- LC 424 — Longest Repeating Character Replacement

## One-Line Revision

Maintain a unique-element window and its running sum; remove elements from the left until duplicates disappear, then maximize the sum.
