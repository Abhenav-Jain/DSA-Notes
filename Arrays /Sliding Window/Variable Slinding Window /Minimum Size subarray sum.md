# Minimum Size Subarray Sum — LC 209

**Pattern:** Variable Sliding Window  
**Difficulty:** Medium

## Intuition
Find the minimum length of a contiguous subarray whose sum is `>= target`.

Expand the window by adding `nums[j]`. Whenever `sum >= target`, update the minimum length and shrink from the left to find a smaller valid window.

## Core Logic
```cpp
sum += nums[j];

while (sum >= target) {
    min_size = min(min_size, j - i + 1);
    sum -= nums[i];
    i++;
}
```

- `j` expands the window.
- `i` shrinks the window.
- `j - i + 1` gives the current window length.
- Use `while`, not `if`, because multiple left-side removals may keep the sum `>= target`.

## C++ Code
```cpp
class Solution {
public:
    int minSubArrayLen(int target, vector<int>& nums) {
        int sum = 0;
        int i = 0, j = 0;
        int n = nums.size();
        int min_size = INT_MAX;

        while (j < n) {
            sum += nums[j];

            while (sum >= target) {
                min_size = min(min_size, j - i + 1);
                sum -= nums[i];
                i++;
            }

            j++;
        }

        return min_size == INT_MAX ? 0 : min_size;
    }
};
```

## Dry Run
`target = 7`, `nums = [2, 3, 1, 2, 4, 3]`

| Window | Sum | Action |
|---|---:|---|
| `[2,3,1,2]` | 8 | Valid; minimum length = 4 |
| `[3,1,2]` | 6 | Shrinking stops |
| `[3,1,2,4]` | 10 | Valid; minimum length = 4 |
| `[1,2,4]` | 7 | Valid; minimum length = 3 |
| `[2,4,3]` | 9 | Valid; minimum length = 3 |
| `[4,3]` | 7 | Valid; minimum length = 2 |

**Answer: `2`**, from subarray `[4,3]`.

## Complexity
- **Time:** O(n) — each pointer moves forward at most `n` times.
- **Space:** O(1) — only a few variables are used.

## Important Notes
- `min_size` starts at `INT_MAX` because no valid subarray has been found yet.
- Return `0` if no valid subarray exists.
- This sliding-window approach requires **positive integers** (as guaranteed by LC 209). With negative numbers, shrinking the window does not reliably decrease the sum.
- Update the minimum length **before** removing `nums[i]`; otherwise, you may miss a valid window.

**Key takeaway:** Expand until the sum reaches the target, then shrink as much as possible while maintaining validity.
