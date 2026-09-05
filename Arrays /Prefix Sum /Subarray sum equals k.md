# Subarray Sum Equals K — LeetCode 560

## Pattern
Prefix Sum + HashMap

## Idea
Har index tak ka cumulative sum `sum` maintain karo.

Agar current prefix sum `sum` hai, aur hume subarray ka sum `k` chahiye:

    previous_sum = sum - k

Agar `sum - k` pehle aa chuka hai, to us prefix sum ke baad se current
index tak ke subarrays ka sum `k` hoga.

HashMap me:
    mp[prefix_sum] = kitni baar ye prefix sum aaya hai

Initially:
    mp[0] = 1

## Algorithm
1. `sum = 0`, `count = 0`
2. Har element ke liye:
   - `sum += nums[i]`
   - Agar `sum-k` map me hai → `count += mp[sum-k]`
   - `mp[sum]++`
3. `count` return karo.

## Code
class Solution {
public:
    int subarraySum(vector<int>& nums, int k) {
        unordered_map<int,int> mp;
        int sum = 0;
        int count = 0;

        mp[0] = 1;

        for(int i = 0; i < nums.size(); i++) {
            sum += nums[i];

            if(mp.find(sum - k) != mp.end()) {
                count += mp[sum - k];
            }

            mp[sum]++;
        }

        return count;
    }
};

## Example
nums = [1,2,3], k = 3

Prefix sums:
1 → 3 → 6

At sum = 3:
    sum-k = 0
    mp[0] = 1 → [1,2]

At sum = 6:
    sum-k = 3
    mp[3] = 1 → [3]

Answer = 2

## Complexity
Time  : O(n)
Space : O(n)

## Key Takeaway
"Current prefix sum - required sum = previous prefix sum"

⚠️ `mp[0] = 1` bhoolna nahi — ye starting se aane wale subarrays
ko count karta hai.
