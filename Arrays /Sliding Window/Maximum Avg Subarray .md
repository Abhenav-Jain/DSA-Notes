# LeetCode 643 — Maximum Average Subarray I

**Pattern:** Fixed Size Sliding Window  
**Difficulty:** Easy

## Approach

Hume exactly `k` size ka subarray chahiye jiska average maximum ho.

- First `k` elements ka sum calculate karo.
- Window ko right se expand karo.
- Har step:
  - New element add → `sum += nums[right]`
  - Leftmost element remove → `sum -= nums[left]`
  - `left++`
- Har window ka average check karke maximum store karo.

## Code

class Solution {
public:
    double findMaxAverage(vector<int>& nums, int k) {
        int left = 0;
        double sum = 0;
        double max_average = -1e9;

        for(int right = 0; right < nums.size(); right++) {
            sum += nums[right];

            if(right - left + 1 == k) {
                max_average = max(max_average, sum / k);
                sum -= nums[left];
                left++;
            }
        }

        return max_average;
    }
};

## Example

nums = [1,12,-5,-6,50,3], k = 4

Windows:
[1,12,-5,-6] → 0.5  
[12,-5,-6,50] → **12.75**  
[-5,-6,50,3] → 10.5

Answer = **12.75**

## Complexity

Time: O(n)  
Space: O(1)

## Key Takeaway

**Fixed-size window:** `right - left + 1 == k` → answer calculate → left element remove → `left++`.
