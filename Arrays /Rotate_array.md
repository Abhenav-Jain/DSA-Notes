# Rotate Array — LeetCode 189

**Difficulty:** Medium  
**Pattern:** Array · Reversal Algorithm  
**Goal:** Rotate the array to the right by `k` positions.

---

## 1. Problem Understanding

Given an integer array `nums`, rotate it to the right by `k` positions.

**Example:**

```text
Input:  nums = [1,2,3,4,5,6,7], k = 3
Output: [5,6,7,1,2,3,4]
```

The last `k` elements move to the beginning while preserving their relative order.

### Important Observation

If `n = nums.size()`, then:

```cpp
k = k % n;
```

Why? Rotating an array `n` times brings it back to its original state.

For example, if `n = 7` and `k = 10`, rotating by `10` is equivalent to rotating by `3`.

**Edge case:** Check whether `n == 0` before calculating `k % n`.

---

## 2. Approach 1 — Brute Force Using an Extra Array

### Intuition

For every destination index `i`, calculate the original index from which its element should come:

```cpp
mark = (n - k + i) % n;
```

Then copy `nums[mark]` into a temporary array.

### C++ Solution

```cpp
class Solution {
public:
    void rotate(vector<int>& nums, int k) {
        int n = nums.size();

        if (n == 0) return;

        k = k % n;
        vector<int> ans;

        for (int i = 0; i < n; i++) {
            int mark = (n - k + i) % n;
            ans.push_back(nums[mark]);
        }

        nums = ans;
    }
};
```

### Dry Run

```text
nums = [1,2,3,4,5,6,7]
n = 7, k = 3

i = 0 → mark = (7 - 3 + 0) % 7 = 4 → 5
i = 1 → mark = (7 - 3 + 1) % 7 = 5 → 6
i = 2 → mark = (7 - 3 + 2) % 7 = 6 → 7
i = 3 → mark = (7 - 3 + 3) % 7 = 0 → 1
i = 4 → mark = (7 - 3 + 4) % 7 = 1 → 2
i = 5 → mark = (7 - 3 + 5) % 7 = 2 → 3
i = 6 → mark = (7 - 3 + 6) % 7 = 3 → 4

Output = [5,6,7,1,2,3,4]
```

### Complexity

- **Time:** `O(n)`
- **Extra Space:** `O(n)`

### Limitations

- Requires a temporary array.
- Additional memory allocation and copying are needed.
- Does not achieve the optimal `O(1)` auxiliary-space requirement.

---

## 3. Approach 2 — Optimal Reversal Algorithm

### Core Intuition

Instead of moving elements individually, reverse the array in three steps:

1. Reverse the entire array.
2. Reverse the first `k` elements.
3. Reverse the remaining `n-k` elements.

This rotates the array in-place without using an extra array.

### C++ Solution

```cpp
class Solution {
public:
    void rotate(vector<int>& nums, int k) {
        int n = nums.size();

        if (n == 0) return;

        k = k % n;

        reverse(nums.begin(), nums.end());
        reverse(nums.begin(), nums.begin() + k);
        reverse(nums.begin() + k, nums.end());
    }
};
```

### Dry Run

```text
Initial:
[1,2,3,4,5,6,7]

Step 1: Reverse the entire array
[7,6,5,4,3,2,1]

Step 2: Reverse the first k = 3 elements
[5,6,7,4,3,2,1]

Step 3: Reverse the remaining n-k = 4 elements
[5,6,7,1,2,3,4]

Final Output:
[5,6,7,1,2,3,4]
```

### Why Does It Work?

Split the original array into two parts:

```text
A = [1,2,3,4]
B = [5,6,7]

Original: A + B
Required: B + A
```

Reversing the complete array reverses both parts and swaps their order:

```text
reverse(A + B) = reverse(B) + reverse(A)
```

Reversing each part separately restores their internal order:

```text
B + A
```

Therefore, the final array is correctly rotated.

### Complexity

- **Time:** `O(n)` — three linear-time reversals.
- **Auxiliary Space:** `O(1)` — in-place modification.

---

## 4. Brute Force vs Optimal

| Criteria | Extra Array | Reversal Algorithm |
|---|---|---|
| Time Complexity | `O(n)` | `O(n)` |
| Extra Space | `O(n)` | `O(1)` |
| In-place | No | Yes |
| Implementation | Index mapping | Three reversals |
| Best Use | Understanding index mapping | Interview and optimal solution |

**Preferred solution:** Reversal Algorithm.

---

## 5. Edge Cases to Remember

| Input | Expected Output |
|---|---|
| `nums = [1], k = 3` | `[1]` |
| `nums = [1,2,3], k = 0` | `[1,2,3]` |
| `nums = [1,2,3], k = 3` | `[1,2,3]` |
| `nums = [1,2,3], k = 4` | `[3,1,2]` |
| `nums = [], k = 2` | `[]` |

Always normalize `k` using `k % n`, after handling the empty-array case.

---

## 6. Common Mistakes

1. **Modulo by zero:** `k % n` is invalid when `n == 0`.
2. **Wrong rotation direction:** This problem asks for right rotation, not left rotation.
3. **Wrong reversal boundaries:** `reverse(begin, end)` excludes the `end` iterator.
4. **Forgetting modulo:** `k` may be greater than the array length.
5. **Unnecessary temporary storage:** The reversal algorithm achieves `O(1)` auxiliary space.

---

## 7. Interview Explanation

"I first solved the problem using an extra array. For each destination index, I calculated its source index using `(n - k + i) % n`. This takes `O(n)` time and `O(n)` extra space.

To optimize the space complexity, I used the reversal algorithm. I reverse the entire array, then reverse the first `k` elements and the remaining elements separately. This preserves the correct relative order and achieves `O(n)` time with `O(1)` auxiliary space."

---

## Final Revision Shortcut

**Right Rotate by `k`:**

```text
1. Handle n == 0
2. k = k % n
3. Reverse all
4. Reverse first k
5. Reverse remaining n-k
```

**Remember:** Same `O(n)` time, but the reversal algorithm improves auxiliary space from `O(n)` to `O(1)`.
