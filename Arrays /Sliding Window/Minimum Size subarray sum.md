# LeetCode 209 — Minimum Size Subarray Sum

**Pattern:** Variable-Size Sliding Window  
**Difficulty:** Medium

## Approach

Array mein **positive integers** hain, isliye Sliding Window use kar sakte hain.

- `right` se window expand karo → `sum += nums[right]`
- Jab `sum >= target` ho:
  - Current window ki minimum length update karo.
  - Left element remove karo.
  - `left++`
- `while` isliye use hota hai kyunki ek hi window se multiple smaller valid subarrays mil sakte hain.

## Code

class Solution {
public:
    int minSubArrayLen(int target, vector<int>& nums) {
        int left = 0;
        int sum = 0;
        int min_length = INT_MAX;

        for(int right = 0; right < nums.size(); right++) {
            sum += nums[right];

            while(sum >= target) {
                min_length = min(min_length, right - left + 1);
                sum -= nums[left];
                left++;
            }
        }

        return min_length == INT_MAX ? 0 : min_length;
    }
};

## Example

target = 7  
nums = [2,3,1,2,4,3]

Valid windows:
[2,3,1,2] → length 4  
[3,1,2,4] → length 4  
[4,3] → **length 2**

Answer = **2**

## Complexity

Time: O(n)  
Space: O(1)

## Key Takeaway

**Variable Sliding Window:** `sum >= target` → answer update → left ko move karke window shrink karo.

**Important:** Ye approach positive integers ke case mein work karti hai.
