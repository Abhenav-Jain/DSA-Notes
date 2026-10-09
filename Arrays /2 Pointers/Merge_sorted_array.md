# Merge Sorted Array — LeetCode 88

**Pattern:** Two Pointers  
**Difficulty:** Easy

## Approach
- `i = m - 1`: Last valid element of `nums1`.
- `j = n - 1`: Last element of `nums2`.
- `k = m + n - 1`: Last index of `nums1`.
- Compare `nums1[i]` and `nums2[j]`, and place the larger element at `nums1[k]`.
- Move pointers backward to avoid overwriting valid elements.
- Once the main loop ends, copy remaining elements of `nums2`. Remaining elements of `nums1` are already in the correct positions.

## C++ Solution

```cpp
class Solution {
public:
    void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
        int i = m - 1;
        int j = n - 1;
        int k = m + n - 1;

        while (i >= 0 && j >= 0) {
            if (nums1[i] >= nums2[j]) {
                nums1[k--] = nums1[i--];
            } else {
                nums1[k--] = nums2[j--];
            }
        }

        while (j >= 0) {
            nums1[k--] = nums2[j--];
        }
    }
};
```

## Complexity
- **Time:** `O(m + n)`
- **Extra Space:** `O(1)`

## Key Insight
Merge from the **end** because `nums1` has extra space. This prevents overwriting its original elements.
