# Subarray Product Less Than K — LeetCode 713

**Difficulty:** Medium  
**Pattern:** Variable Sliding Window + Running Product  
**Approach:** Two Pointers  
**Time Complexity:** `O(n)`  
**Auxiliary Space:** `O(1)`

---

## 1. Problem Intuition

Given an array of positive integers `nums` and an integer `k`, count the number of **contiguous, non-empty subarrays whose product is strictly less than `k`**.

### Example

```text
nums = [10, 5, 2, 6]
k = 100

Output = 8
```

Valid subarrays:

```text
[10]
[5]
[2]
[6]
[10, 5]
[5, 2]
[2, 6]
[5, 2, 6]
```

Their products are all strictly less than `100`.

**Important:** Product equal to `k` is not valid.

---

## 2. Core Idea — Variable Sliding Window

Maintain a window `[i, j]` and its product.

- `i`: left pointer.
- `j`: right pointer.
- `prod`: product of all elements inside the current window.
- `cnt`: total number of valid subarrays found.

As we move `j` forward, we multiply the product by `nums[j]`.

If the product becomes greater than or equal to `k`, shrink the window from the left until the product is strictly less than `k`.

### Window validity

```cpp
prod < k
```

Because all numbers are positive integers, shrinking the window by removing leftmost elements reduces the product or leaves it unchanged when removing a factor of `1`. The shrinking loop continues until the window is valid.

---

## 3. Your C++ Solution

```cpp
class Solution {
public:
    int numSubarrayProductLessThanK(vector<int>& nums, int k) {
        int prod = 1;
        int i = 0;
        int j = 0;
        int n = nums.size();
        int cnt = 0;

        if (k <= 1) {
            return 0;
        }

        while (j < n) {
            prod *= nums[j];

            while (prod >= k) {
                prod /= nums[i];
                i++;
            }

            cnt += (j - i + 1);
            j++;
        }

        return cnt;
    }
};
```

**Your implementation is correct for the problem's positive-integer constraints.**

---

## 4. Line-by-Line Explanation

### Step 1 — Initialize the product

```cpp
int prod = 1;
```

Multiplication ka identity element `1` hai, isliye initial product `1` rakha hai.

### Step 2 — Handle the `k <= 1` edge case

```cpp
if (k <= 1) {
    return 0;
}
```

Every element is a positive integer, so every non-empty subarray has product at least `1`.

If `k <= 1`, no product can be strictly less than `k`.

### Step 3 — Expand the window

```cpp
prod *= nums[j];
```

Add the rightmost element to the current window's product.

### Step 4 — Shrink the invalid window

```cpp
while (prod >= k) {
    prod /= nums[i];
    i++;
}
```

While the product is too large, remove the leftmost element from the window.

Because the array contains positive integers, division by the outgoing element correctly updates the product.

### Step 5 — Count newly valid subarrays

```cpp
cnt += (j - i + 1);
```

**This is the most important line in the problem.**

Once the window `[i, j]` is valid, every subarray ending at `j` and starting anywhere from `i` through `j` is also valid.

Those subarrays are:

```text
nums[j]
nums[j-1 ... j]
nums[j-2 ... j]
...
nums[i ... j]
```

Their count is:

\[
j-i+1
\]

Add this count to `cnt`.

### Step 6 — Move to the next element

```cpp
j++;
```

Expand the window and repeat.

---

## 5. The Key Mathematical Observation

Suppose the current valid window has length \(L\).

The total number of non-empty contiguous subarrays **inside that window** is:

\[
\frac{L(L+1)}{2}
\]

However, in this sliding-window algorithm, we must **not** add that formula at every iteration.

Why?

Because subarrays ending at earlier indices have already been counted.

Instead, when the right pointer reaches index `j`, count only the newly considered subarrays that end at `j`:

\[
\boxed{\text{New subarrays}=j-i+1}
\]

This ensures that every valid subarray is counted exactly once.

### Example

```text
nums = [1, 2, 3]
k = 10
```

All subarrays are valid.

| `j` | Current window | Length | New subarrays | Running count |
|---:|---|---:|---:|---:|
| 0 | `[1]` | 1 | 1 | 1 |
| 1 | `[1, 2]` | 2 | 2 | 3 |
| 2 | `[1, 2, 3]` | 3 | 3 | 6 |

Total:

\[
1+2+3=6
\]

Using \(L(L+1)/2\) at every step would give \(1+3+6=10\), incorrectly counting some subarrays more than once.

---

## 6. Complete Dry Run

```text
nums = [10, 5, 2, 6]
k = 100
```

Initially:

```text
i = 0
prod = 1
cnt = 0
```

| `j` | Added element | Product before shrinking | Window after shrinking | Added count | Total |
|---:|---:|---:|---|---:|---:|
| 0 | 10 | 10 | `[10]` | 1 | 1 |
| 1 | 5 | 50 | `[10, 5]` | 2 | 3 |
| 2 | 2 | 100 | `[5, 2]` | 2 | 5 |
| 3 | 6 | 60 | `[5, 2, 6]` | 3 | 8 |

At `j = 2`, the product reaches `100`, which is **not valid** because the condition is strictly less than `k`.

The algorithm removes `10`:

```text
prod = 100 / 10 = 10
i = 1
```

Now the product is `10`, and the window `[5, 2]` is valid.

At `j = 3`, the window `[5, 2, 6]` has product `60`, so it contributes three new valid subarrays.

**Final answer: `8`**

---

## 7. Why Does This Algorithm Count Every Valid Subarray?

After shrinking, `[i, j]` is the longest valid window ending at `j`.

Since every number is positive, any suffix of this window has product no greater than the full window's product.

Therefore, these are all valid:

```text
[i ... j]
[i+1 ... j]
[i+2 ... j]
...
[j ... j]
```

There are exactly `j - i + 1` such subarrays.

Any subarray ending at `j` that starts before `i` is invalid; otherwise, the window could have extended farther left.

Thus, adding `j - i + 1` counts all valid subarrays ending at `j`, without duplication.

---

## 8. Complexity Analysis

**Time: `O(n)`**

- The right pointer visits every element once.
- The left pointer only moves forward and visits each element at most once.
- Although there is a nested `while` loop, the total pointer movement is linear.

**Auxiliary space: `O(1)`**

Only a few variables are used; no extra array or map is required.

---

## 9. Common Mistakes

1. Using `prod > k` instead of `prod >= k`.
2. Forgetting that the product must be **strictly less than** `k`.
3. Forgetting the `k <= 1` early return.
4. Adding `L(L+1)/2` at every iteration, causing double-counting.
5. Forgetting to divide the product by `nums[i]` when shrinking.
6. Using this product-based sliding-window approach with negative numbers, where the monotonicity assumption fails.
7. Using a data type that is too small for intermediate products under unusually large constraints. The official constraints determine whether `int` is sufficient.

---

## 10. Pattern Recognition

Look for these clues:

- Count all contiguous subarrays.
- The array contains positive integers.
- A product threshold determines validity.
- Once a window is valid, all its suffixes ending at the right pointer are also valid.

### Related Problems

- LC 209 — Minimum Size Subarray Sum
- LC 1004 — Max Consecutive Ones III
- LC 930 — Binary Subarrays With Sum
- LC 992 — Subarrays with K Different Integers

---

## One-Line Revision

Expand the right pointer, shrink while `prod >= k`, then add `j - i + 1` because every suffix of the valid window ending at `j` is a newly counted valid subarray.
