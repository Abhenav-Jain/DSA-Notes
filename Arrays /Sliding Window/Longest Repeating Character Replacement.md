# Longest Repeating Character Replacement — LeetCode 424

## Pattern
Sliding Window + Frequency Map

## Idea
Window ke andar ek character ko maximum frequency wala character maan lo.
Baaki characters ko replace karna hai.

Condition:
window_size - max_frequency <= k
→ window valid hai.

Agar condition fail ho:
left ko move karke window shrink karo.

## Key Formula
replacements_needed = (right - left + 1) - max_freq

Agar replacements_needed > k:
    left++

## Approach
1. right pointer se window expand karo.
2. freq[s[right]]++.
3. max_freq maintain karo.
4. Agar `(window_size - max_freq) > k`, left se shrink karo.
5. Har valid window ka maximum length answer hai.

## Code
class Solution {
public:
    int characterReplacement(string s, int k) {
        unordered_map<char,int> freq;
        int left = 0;
        int max_freq = 0;
        int ans = 0;

        for(int right = 0; right < s.length(); right++) {
            freq[s[right]]++;
            max_freq = max(max_freq, freq[s[right]]);

            while((right - left + 1) - max_freq > k) {
                freq[s[left]]--;
                left++;
            }

            ans = max(ans, right - left + 1);
        }

        return ans;
    }
};

## Complexity
Time: O(n)
Space: O(1)  // only 26 characters

## Remember
"Window Size - Most Frequent Character = Required Replacements"

If replacements > k → shrink window.
