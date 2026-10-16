# First Missing Positive — LeetCode 41

**Difficulty:** Hard  
**Pattern:** In-place Array Marking  
**Time Complexity:** `O(n)`  
**Auxiliary Space:** `O(1)`

## 1. Problem Statement

Given an unsorted integer array `nums`, return the **smallest missing positive integer**.

The solution must run in `O(n)` time and use `O(1)` auxiliary space.

### Examples

```text
Input:  [1, 2, 0]
Output: 3
```

```text
Input:  [3, 4, -1, 1]
Output: 2
```

```text
Input:  [7, 8, 9, 11, 12]
Output: 1
```

## 2. Core Intuition

For an array of size `n`, the first missing positive must lie in the range **`[1, n + 1]`**.

Why?

- If any number from `1` to `n` is missing, the answer lies within that range.
- If all numbers from `1` to `n` are present, the answer is `n + 1`.

Therefore, we only need to track the presence of numbers from `1` to `n`.

**Key observation:** The array itself can act as a presence-tracking structure by using negative signs as markers.

For every number `x` in the valid range `[1, n]`, mark index `x - 1` as negative.

After marking:
- Negative value at index `i` → `i + 1` is present.
- Non-negative value at index `i` → `i + 1` is missing.

This avoids using an extra hash set or frequency array.

## 3. Algorithm — Three Passes

### Pass 1: Clean the array

```cpp
int mark = n + 10;

for (int i = 0; i < n; i++) {
    if (nums[i] <= 0 || nums[i] > n) {
        nums[i] = mark;
    }
}
```

Replace all non-positive numbers and numbers greater than `n` with a positive marker `n + 10`.

These values cannot affect the answer because only numbers in `[1, n]` matter.

**Why use `n + 10`?**

It is guaranteed to be greater than `n`, so it will not be treated as a valid number during marking.

### Pass 2: Mark the presence of each number

```cpp
for (int i = 0; i < n; i++) {
    int val = abs(nums[i]);

    if (val > n) continue;

    int idx = val - 1;

    if (nums[idx] > 0) {
        nums[idx] = -nums[idx];
    }
}
```

For each element:

1. Use `abs(nums[i])` to recover the original positive value, even if the current element has already been marked negative.
2. Skip it if `val > n`.
3. Calculate its corresponding index: `idx = val - 1`.
4. If `nums[idx]` is positive, negate it to mark that `val` exists.

**Why `abs()`?**

Earlier iterations may have made an element negative. We still need its original magnitude to identify the correct index.

**Why check `nums[idx] > 0`?**

This ensures we only negate a positive value. Already-negative entries stay marked, and zero does not need to be negated.

### Pass 3: Find the first missing positive

```cpp
for (int i = 0; i < n; i++) {
    if (nums[i] >= 0) {
        return i + 1;
    }
}

return n + 1;
```

Scan from left to right:

- The first non-negative entry identifies the smallest missing positive.
- If every entry is negative, all numbers from `1` to `n` exist, so return `n + 1`.

## 4. Complete C++ Solution

```cpp
class Solution {
public:
    int firstMissingPositive(vector<int>& nums) {
        int n = nums.size();
        int mark = n + 10;

        // Pass 1: Replace irrelevant values
        for (int i = 0; i < n; i++) {
            if (nums[i] <= 0 || nums[i] > n) {
                nums[i] = mark;
            }
        }

        // Pass 2: Mark the presence of numbers
        for (int i = 0; i < n; i++) {
            int val = abs(nums[i]);

            if (val > n) continue;

            int idx = val - 1;

            if (nums[idx] > 0) {
                nums[idx] = -nums[idx];
            }
        }

        // Pass 3: Find the first missing positive
        for (int i = 0; i < n; i++) {
            if (nums[i] >= 0) {
                return i + 1;
            }
        }

        return n + 1;
    }
};
```

## 5. Detailed Dry Run

```text
Input: [3, 4, -1, 1]
n = 4
mark = n + 10 = 14
```

### Pass 1 — Clean

Replace `-1` with `14` because it is non-positive.

```text
[3, 4, 14, 1]
```

### Pass 2 — Negative marking

| `i` | `val = abs(nums[i])` | Target index | Action |
|---:|---:|---:|---|
| 0 | 3 | 2 | Mark index 2 negative |
| 1 | 4 | 3 | Mark index 3 negative |
| 2 | 14 | — | Skip because `14 > n` |
| 3 | 1 | 0 | Mark index 0 negative |

Final array:

```text
[-3, 4, -14, -1]
```

Index `1` remains positive, meaning the number `2` was never found.

### Pass 3 — Find the answer

```text
Index:  0   1    2   3
Array: -3   4  -14  -1
```

The first non-negative element occurs at index `1`.

Therefore:

```text
Answer = 1 + 1 = 2
```

## 6. Edge Cases

| Input | Output | Reason |
|---|---:|---|
| `[]` | 1 | Empty array has no positive integers |
| `[1]` | 2 | The number `1` exists |
| `[2]` | 1 | The number `1` is missing |
| `[1, 2, 3]` | 4 | All numbers from `1` to `n` exist |
| `[-1, -2, 0]` | 1 | No positive number exists |
| `[3, 4, -1, 1]` | 2 | The number `2` is missing |
| `[1, 1, 2, 2]` | 3 | Duplicates do not change presence |
| `[7, 8, 9, 11, 12]` | 1 | The number `1` is missing |

**Empty-array behavior:** All loops execute zero times, and the final `return n + 1` correctly returns `1`.

## 7. Complexity Analysis

**Time: `O(n)`**

- Cleaning pass: `O(n)`
- Marking pass: `O(n)`
- Final scan: `O(n)`

Total: `O(3n) = O(n)`.

**Auxiliary space: `O(1)`**

Only a constant number of variables are used. The input array is modified in-place; no additional data structure proportional to `n` is created.

## 8. Common Mistakes

1. **Ignoring values greater than `n`:** They cannot be the first missing positive.
2. **Not replacing zero or negative values:** They can interfere with sign-based marking.
3. **Forgetting `abs()`:** Previously marked elements may already be negative.
4. **Using `idx = val` instead of `val - 1`:** Number `1` corresponds to index `0`.
5. **Negating repeatedly without checking:** Duplicate values should not undo an existing marker.
6. **Returning `n` instead of `n + 1`:** If all numbers from `1` to `n` exist, the answer is `n + 1`.

## 9. Pattern Recognition

**Clean → Mark → Scan**

This technique is useful when:
- The answer is bounded by the array length.
- Values can be mapped to array indices.
- The input array may be modified.
- `O(1)` auxiliary space is required.

### Alternative Approach

Cyclic placement is another `O(n)` time, `O(1)` auxiliary-space solution. It places each valid value `x` at index `x - 1`, then finds the first index where `nums[i] != i + 1`.

The sign-marking approach used here is equally optimal and avoids repeatedly swapping elements.

**Final takeaway:** For First Missing Positive, remember that the answer is at most `n + 1`, and use the array's signs to record which positive integers are present.
