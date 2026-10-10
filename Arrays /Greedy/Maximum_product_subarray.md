# Maximum Product Subarray — LeetCode 152

**Pattern:** Prefix & Suffix / Subarray Products  
**Difficulty:** Medium

## 1. Problem

Given an integer array `nums`, find the contiguous subarray having the largest product and return that product.

**Example:**
```cpp
Input:  [2, 3, -2, 4]
Output: 6
```

Maximum product subarray: `[2, 3]` → Product = `6`.

## 2. Approach 1 — Brute Force

Generate every possible subarray and calculate its product using a third loop.

```cpp
int maxProduct(vector<int>& nums) {
    int n = nums.size();
    int maxi = INT_MIN;

    for (int i = 0; i < n; i++) {
        for (int j = i; j < n; j++) {
            int prod = 1;

            for (int k = i; k <= j; k++) {
                prod *= nums[k];
            }

            maxi = max(maxi, prod);
        }
    }

    return maxi;
}
```

**Time:** `O(n³)`  
**Space:** `O(1)`

## 3. Approach 2 — Optimized Brute Force

Instead of recalculating each subarray product, maintain a running product while extending the subarray.

```cpp
int maxProduct(vector<int>& nums) {
    int n = nums.size();
    int maxi = INT_MIN;

    for (int i = 0; i < n; i++) {
        int prod = 1;

        for (int j = i; j < n; j++) {
            prod *= nums[j];
            maxi = max(maxi, prod);
        }
    }

    return maxi;
}
```

**Time:** `O(n²)`  
**Space:** `O(1)`

**Improvement:** Removed the third loop by calculating products incrementally.

## 4. Approach 3 — Prefix & Suffix (Optimal)

### Intuition

Negative numbers make this problem different from Maximum Subarray:

- Positive × positive can increase the product.
- Negative × negative becomes positive.
- Zero breaks a product sequence.

Calculate products from both directions:

- `prefix`: Product from left to right.
- `suffix`: Product from right to left.
- Reset a running product to `1` when it becomes `0`.
- Track the maximum product found.

### C++ Code

```cpp
class Solution {
public:
    int maxProduct(vector<int>& nums) {
        int n = nums.size();
        int prefix = 1, suffix = 1;
        int maxi = INT_MIN;

        for (int i = 0; i < n; i++) {
            if (prefix == 0) prefix = 1;
            if (suffix == 0) suffix = 1;

            prefix *= nums[i];
            suffix *= nums[n - 1 - i];

            maxi = max(maxi, max(prefix, suffix));
        }

        return maxi;
    }
};
```

**Time:** `O(n)`  
**Space:** `O(1)`

## 5. Dry Run — Prefix & Suffix

`nums = [2, -3, -2, 4]`

| `i` | Prefix | Suffix | Maximum so far |
|---:|---:|---:|---:|
| 0 | 2 | 4 | 4 |
| 1 | -6 | -8 | 4 |
| 2 | 12 | 16 | 16 |
| 3 | 48 | 2 | 48 |

**Answer: `48`**

## 6. Important Edge Cases

- `[5]` → `5`
- `[-2, 0, -1]` → `0`
- `[-2, 3, -4]` → `24`
- `[-2, -3, -4]` → `12`
- `[-5, -2, -8]` → `40`

## 7. Comparison

| Approach | Time | Extra Space |
|---|---|---|
| Brute force | `O(n³)` | `O(1)` |
| Optimized brute force | `O(n²)` | `O(1)` |
| Prefix & suffix | `O(n)` | `O(1)` |

## 8. Interview Takeaways

- Maximum product requires handling both positive and negative products.
- The prefix-suffix approach handles zeros by resetting the running product.
- Unlike the sum-based Kadane's Algorithm, maximum product may depend on the minimum negative product becoming positive.
- The prefix-suffix method achieves linear time without extra arrays.

**Revision mantra:** Extend the product → scan both directions → reset at zero → update maximum.
