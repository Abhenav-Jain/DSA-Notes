# Fruit Into Baskets — LeetCode 904

**Difficulty:** Medium  
**Pattern:** Variable Sliding Window + Hash Map  
**Time Complexity:** `O(n)` average  
**Space Complexity:** `O(1)` — at most 2 distinct fruit types in the valid window

## 1. Problem Intuition

Given an array `fruits`, find the maximum number of fruits we can collect from a contiguous subarray, subject to these rules:

- We have only **2 baskets**.
- Each basket can hold only one type of fruit.
- We can collect as many fruits of that type as we want.
- We must collect fruits from consecutive trees.

In simple terms:

**Find the longest subarray containing at most 2 distinct elements.**

### Example

```text
fruits = [1, 2, 1, 2, 3]

Output: 4
```

The longest valid subarray is `[1, 2, 1, 2]`, which contains 4 fruits and only 2 distinct types.

## 2. Core Idea — Sliding Window

We maintain a window using two pointers:

- `i`: left boundary of the window.
- `j`: right boundary of the window.
- `mp`: frequency of each fruit type inside the window.
- `max_size`: maximum valid window length found so far.

### Window validity condition

```cpp
mp.size() <= 2
```

The map stores the distinct fruit types currently present in the window.

If `mp.size() > 2`, the window becomes invalid. We shrink it from the left until at most 2 distinct types remain.

## 3. C++ Implementation

```cpp
class Solution {
public:
    int totalFruit(vector<int>& fruits) {
        int i = 0;
        int j = 0;
        int max_size = 0;
        int n = fruits.size();

        unordered_map<int, int> mp;

        while (j < n) {
            mp[fruits[j]]++;

            while (mp.size() > 2) {
                mp[fruits[i]]--;

                if (mp[fruits[i]] == 0) {
                    mp.erase(fruits[i]);
                }

                i++;
            }

            max_size = max(max_size, j - i + 1);
            j++;
        }

        return max_size;
    }
};
```

## 4. Dry Run

```text
fruits = [1, 2, 1, 2, 3]
```

| `j` | Added fruit | Current window after shrinking | Distinct types | `max_size` |
|---:|---:|---|---:|---:|
| 0 | 1 | `[1]` | 1 | 1 |
| 1 | 2 | `[1, 2]` | 2 | 2 |
| 2 | 1 | `[1, 2, 1]` | 2 | 3 |
| 3 | 2 | `[1, 2, 1, 2]` | 2 | 4 |
| 4 | 3 | `[2, 1, 2, 3]` → shrink to `[2, 3]` | 2 | 4 |

**Final answer: `4`**

When fruit `3` is added, there are 3 distinct types. The left pointer moves forward, removing fruit `1` completely from the map when its frequency reaches zero. The window becomes valid again at `[2, 3]`.

## 5. Line-by-Line Explanation

### Step 1: Add the current fruit

```cpp
mp[fruits[j]]++;
```

Increase the frequency of the fruit at index `j`.

### Step 2: Check whether the window is invalid

```cpp
while (mp.size() > 2)
```

More than 2 distinct fruit types means we need to remove fruits from the left.

### Step 3: Decrease the outgoing fruit's frequency

```cpp
mp[fruits[i]]--;
```

The fruit at the left boundary is leaving the window.

### Step 4: Remove a fruit type when its frequency becomes zero

```cpp
if (mp[fruits[i]] == 0) {
    mp.erase(fruits[i]);
}
```

**Important:** `mp.size()` counts keys, not total fruit occurrences.

For example:

```text
mp = {1: 3, 2: 1}
```

There are 4 fruits in the window but only 2 distinct types. If type `1` reaches frequency zero, its key must be erased so that `mp.size()` reflects the actual number of distinct types.

### Step 5: Update the maximum length

```cpp
max_size = max(max_size, j - i + 1);
```

After shrinking, the window is valid. Calculate its length and update the answer.

## 6. Why Do We Use `while`, Not `if`?

Suppose the window has 3 distinct fruit types. Removing just one fruit may not eliminate an entire type because that fruit could appear multiple times.

Therefore, we continue shrinking until the map contains at most 2 keys.

```cpp
while (mp.size() > 2)
```

This guarantees that the window is valid before calculating its length.

## 7. Complexity Analysis

**Time: `O(n)` average**

- The right pointer visits each element once.
- The left pointer only moves forward and visits each element at most once.
- Hash map operations are `O(1)` on average.

**Auxiliary space: `O(1)`**

After shrinking, the map contains at most 2 distinct types. During expansion, it temporarily holds at most 3 before shrinking begins.

## 8. Edge Cases

| Input | `k` equivalent | Expected output |
|---|---:|---:|
| `[1, 1, 1]` | 2 types allowed | 3 |
| `[1, 2, 3]` | 2 types allowed | 2 |
| `[1, 2, 1, 2]` | 2 types allowed | 4 |
| `[5]` | 2 types allowed | 1 |
| `[]` | 2 types allowed | 0 |

## 9. Generalized Pattern — At Most K Distinct Elements

This problem is a special case of finding the longest subarray with at most `K` distinct elements.

The general pattern is:

```cpp
while (mp.size() > k) {
    mp[arr[i]]--;

    if (mp[arr[i]] == 0) {
        mp.erase(arr[i]);
    }

    i++;
}

ans = max(ans, j - i + 1);
```

For Fruit Into Baskets, `k = 2`.

This pattern also connects to **Longest Substring with At Most K Distinct Characters** and **Subarrays with K Different Integers**.

## 10. Common Mistakes

1. Forgetting to erase a key when its frequency becomes zero.
2. Using `if (mp.size() > 2)` instead of `while`.
3. Updating the answer before the window becomes valid.
4. Confusing total fruit count with the number of distinct fruit types.
5. Moving the left pointer without decrementing the outgoing fruit's frequency.

## One-Line Revision

**Expand right, count frequencies, shrink while distinct types exceed 2, erase zero-frequency keys, and update the maximum valid window length.**
