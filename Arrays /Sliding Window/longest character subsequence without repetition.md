# LeetCode 3 — Longest Substring Without Repeating Characters

**Pattern:** Sliding Window + HashMap  
**Difficulty:** Medium

## Approach

Maintain a window `[left ... right]` containing **unique characters**.

- `right` → expands the window
- If `s[right]` already exists in the window:
  - Remove characters from the left until duplicate is removed.
  - Move `left`.
- Calculate window length:
  `right - left + 1`
- Keep maximum length.

## Code

class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        unordered_map<char,int> mp;

        int left = 0;
        int max_length = 0;

        for(int right = 0; right < s.length(); right++) {

            while(mp.find(s[right]) != mp.end()) {
                mp[s[left]]--;

                if(mp[s[left]] == 0) {
                    mp.erase(s[left]);
                }

                left++;
            }

            max_length = max(max_length, right - left + 1);
            mp[s[right]]++;
        }

        return max_length;
    }
};

## Example

s = "abcabcbb"

Window progression:

    "abc" → length 3
    duplicate 'a' → move left
    "bca" → length 3
    duplicate 'b' → move left

Answer = **3**

## Complexity

Time: O(n)  
Space: O(k)  → k = number of distinct characters

## Key Takeaway

**Sliding Window:** right pointer expands, and when a duplicate appears, move left until the window becomes unique again.
