# Next Permutation — LeetCode 31

**Difficulty:** Medium  
**Pattern:** Greedy + Two Pointers  
**Time Complexity:** `O(n)`  
**Space Complexity:** `O(1)`

## 1. Problem Intuition

Given an array of integers, rearrange it into the **next lexicographically greater permutation**.

- If a greater permutation exists, find the immediate next one.
- If the array is already the largest permutation, rearrange it into the smallest permutation (ascending order).
- Modify the array in-place.

**Example:**
```text
Input:  [1, 2, 3]
Output: [1, 3, 2]
```

```text
Input:  [3, 2, 1]
Output: [1, 2, 3]
```

```text
Input:  [1, 1, 5]
Output: [1, 5, 1]
```

## 2. Core Intuition

To get the *immediate* next permutation, we should make the **smallest possible increase from the right side**.

We do this in three main steps:

1. Find the **pivot**: the first index from the right where `nums[i-1] < nums[i]`.
2. Find the **successor**: the rightmost element greater than the pivot, then swap them.
3. Reverse the suffix after the pivot to make it as small as possible.

If no pivot exists, the array is in descending order—the largest permutation. Reverse the entire array.

## 3. Algorithm

### Step 1 — Find the pivot

Traverse from right to left:

```cpp
for (int i = n - 1; i > 0; i--) {
    if (nums[i - 1] < nums[i]) {
        gola_index = i - 1;
        break;
    }
}
```

The rightmost position satisfying this condition is the pivot.

Why? The suffix to its right is non-increasing, meaning it is already in its largest possible order. We must increase an element just before that suffix to get the next permutation.

### Step 2 — Find the successor and swap

```cpp
for (int i = n - 1; i >= gola_index + 1; i--) {
    if (nums[i] > nums[gola_index]) {
        swap_index = i;
        break;
    }
}
swap(nums[gola_index], nums[swap_index]);
```

Search from the right and choose the first element greater than the pivot. Because the suffix is non-increasing, this is the **smallest element in the suffix that is greater than the pivot**.

### Step 3 — Reverse the suffix

```cpp
reverse(nums.begin() + gola_index + 1, nums.end());
```

After the swap, reverse the suffix to put it in ascending order, producing the smallest possible suffix.

**Important edge case:** If `gola_index == -1`, then `gola_index + 1 == 0`. Reversing from `begin()` reverses the entire array, correctly handling a descending input.

## 4. Dry Run

```text
Input: [1, 3, 5, 4, 2]
```

**Step 1: Find pivot**

From the right:

- `4 < 2` → false
- `5 < 4` → false
- `3 < 5` → true

Therefore, `pivot index = 1`, and `pivot = 3`.

```text
[1, 3, 5, 4, 2]
    ↑
  pivot
```

**Step 2: Find successor**

Search from the right for the first element greater than `3`.

- `2 > 3` → false
- `4 > 3` → true

Swap `3` and `4`:

```text
[1, 4, 5, 3, 2]
```

**Step 3: Reverse the suffix after the pivot**

Reverse `[5, 3, 2]`:

```text
[1, 4, 2, 3, 5]
```

**Output:** `[1, 4, 2, 3, 5]`

This is the immediate next lexicographically greater permutation.

## 5. C++ Solution

```cpp
class Solution {
public:
    void nextPermutation(vector<int>& nums) {
        int n = nums.size();
        int gola_index = -1;

        // Step 1: Find the pivot
        for (int i = n - 1; i > 0; i--) {
            if (nums[i - 1] < nums[i]) {
                gola_index = i - 1;
                break;
            }
        }

        // Step 2: Find successor and swap
        int swap_index = gola_index;

        if (gola_index != -1) {
            for (int i = n - 1; i >= gola_index + 1; i--) {
                if (nums[i] > nums[gola_index]) {
                    swap_index = i;
                    break;
                }
            }

            swap(nums[gola_index], nums[swap_index]);
        }

        // Step 3: Reverse the suffix
        reverse(nums.begin() + gola_index + 1, nums.end());
    }
};
```

## 6. Edge Cases

| Input | Output | Reason |
|---|---|---|
| `[1,2,3]` | `[1,3,2]` | Normal next permutation |
| `[1,3,2]` | `[2,1,3]` | Swap pivot with successor, reverse suffix |
| `[3,2,1]` | `[1,2,3]` | No pivot; reverse entire array |
| `[1,1,5]` | `[1,5,1]` | Handles duplicate values |
| `[1]` | `[1]` | Single element remains unchanged |

## 7. Complexity Analysis

- **Time: `O(n)`**
  - Pivot search: `O(n)` worst case.
  - Successor search: `O(n)` worst case.
  - Suffix reversal: `O(n)` worst case.
  - Total remains `O(n)` because these operations run sequentially, not nested over the whole array.

- **Auxiliary Space: `O(1)`**
  - Only a constant number of integer variables are used.
  - Swapping and reversing are performed in-place.

## 8. Common Mistakes

1. **Finding the pivot from left to right:** The required pivot is the rightmost index satisfying `nums[i-1] < nums[i]`.
2. **Choosing the wrong successor:** Choose the rightmost element strictly greater than the pivot.
3. **Sorting the suffix unnecessarily:** Reversing it is sufficient because the suffix is non-increasing before the swap.
4. **Forgetting the no-pivot case:** A descending array must become ascending.
5. **Using `<=` instead of `<` for the pivot:** Equal adjacent values do not indicate an increase.

## 9. Pattern to Remember

**Find pivot → Find successor → Swap → Reverse suffix**

The key greedy idea is to increase the rightmost possible position by the smallest amount, then minimize everything to its right.

**When to recall this pattern:** Problems involving lexicographical ordering, permutations, or finding the immediate next arrangement.
