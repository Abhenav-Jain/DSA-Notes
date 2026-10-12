# Longest Repeating Character Replacement — LeetCode 424

**Difficulty:** Medium  
**Pattern:** Variable Sliding Window + Frequency Map  
**Time Complexity:** `O(n)`  
**Space Complexity:** `O(1)` — uppercase English letters ke liye at most 26 characters

## 1. Problem Intuition

Given a string `s` and an integer `k`, find the length of the longest substring that can be converted into a string containing the **same character throughout**, using at most `k` replacements.

**Example:**

```text
s = "AABABBA", k = 1

Output: 4
```

Substring `"AABA"` ko `"AAAA"` banane ke liye sirf ek `B` replace karna padega.

## 2. Core Idea

Ek sliding window maintain karenge using two pointers:

- `i`: window ka starting index.
- `j`: window ka ending index.
- `mp`: current window mein har character ki frequency.
- `max_freq`: window mein kisi bhi character ki maximum frequency.
- `ans`: maximum valid window length.

### Key Formula

```text
window_length = j - i + 1

replacements_needed = window_length - max_freq
```

**Why?**

Window ke most frequent character ko unchanged rakhenge. Baaki sab characters ko us character mein replace karna padega.

For example:

```text
Window = "AABAB"
Length = 5

Frequency:
A = 3
B = 2

max_freq = 3
replacements_needed = 5 - 3 = 2
```

Is window ko `"AAAAA"` banane ke liye 2 replacements chahiye.

### Window Validity Condition

```cpp
(j - i + 1) - max_freq <= k
```

Agar replacements required `k` se zyada ho jaayein, window invalid hai. Left pointer ko aage move karke window shrink karenge.

## 3. C++ Implementation

```cpp
class Solution {
public:
    int characterReplacement(string s, int k) {
        int i = 0;
        int j = 0;
        int n = s.length();
        int max_freq = 0;
        int ans = 0;

        unordered_map<char, int> mp;

        while (j < n) {
            mp[s[j]]++;

            max_freq = max(max_freq, mp[s[j]]);

            while ((j - i + 1) - max_freq > k) {
                mp[s[i]]--;
                i++;
            }

            ans = max(ans, j - i + 1);
            j++;
        }

        return ans;
    }
};
```

## 4. Dry Run

```text
s = "AABABBA", k = 1
```

| Window | Length | Max Frequency | Replacements | Action |
|---|---:|---:|---:|---|
| `A` | 1 | 1 | 0 | Valid |
| `AA` | 2 | 2 | 0 | Valid |
| `AAB` | 3 | 2 | 1 | Valid |
| `AABA` | 4 | 3 | 1 | Valid; answer = 4 |
| `AABAB` | 5 | 3 | 2 | Invalid; shrink |
| `ABAB` | 4 | 2 | 2 | Invalid; shrink again |
| `BAB` | 3 | 2 | 1 | Valid |
| `ABBA` | 4 | 2 | 2 | Invalid; shrink |

Final answer: `4`

*Note:* Table mein invalid window ke baad shrinking tab tak continue hoti hai jab tak window valid na ho jaaye. `max_freq` stale reh sakta hai; uska reason neeche explained hai.

## 5. Line-by-Line Logic

### Step 1: Add the right character

```cpp
mp[s[j]]++;
```

Current window mein `s[j]` ki frequency increase hoti hai.

### Step 2: Update maximum frequency

```cpp
max_freq = max(max_freq, mp[s[j]]);
```

Naya character add karne ke baad uski frequency current `max_freq` se compare karte hain.

### Step 3: Shrink invalid windows

```cpp
while ((j - i + 1) - max_freq > k) {
    mp[s[i]]--;
    i++;
}
```

Agar required replacements `k` se zyada hain, leftmost character remove karte hain aur window shrink karte hain.

### Step 4: Update the answer

```cpp
ans = max(ans, j - i + 1);
```

Valid window ki length ko maximum answer ke saath compare karte hain.

## 6. Important Interview Insight: Why Doesn't `max_freq` Decrease?

Jab left pointer move karta hai, toh theoretically `max_freq` ko recalculate karna chahiye. Lekin is optimized solution mein hum use decrease nahi karte.

**Reason:** `max_freq` ko historical maximum frequency ke roop mein use karte hain. Ye value current window ki exact maximum frequency se badi ho sakti hai.

Isse kuch intermediate windows actual mein valid hone ke bawajood temporarily consider ho sakti hain. Lekin algorithm ka maximum answer incorrect nahi hota: stale `max_freq` se window ka size badhne par naya, larger answer sirf tab mil sakta hai jab us length ka corresponding valid window pehle hi establish ho chuka ho. Shrinking still prevents the window from growing arbitrarily.

**Interview takeaway:** Is problem ke standard optimized solution mein `max_freq` ko har shrink par recalculate karna zaroori nahi hai.

## 7. Complexity Analysis

**Time: `O(n)` average**

- Right pointer har character ko ek baar add karta hai.
- Left pointer bhi maximum `n` baar move karta hai.
- Har pointer total linear movements karta hai.

**Space: `O(1)`**

Uppercase English letters ke liye frequency map mein maximum 26 distinct characters ho sakte hain. Isliye auxiliary space constant hai.

## 8. Common Mistakes

1. `j - i + 1` mein `+1` bhool jaana.
2. Invalid window par `if` use karna, jabki yahan `while` se window valid hone tak shrink karna hai.
3. `max_freq` ko current window ki exact frequency samajhna, jabki optimized version mein ye historical maximum hota hai.
4. Answer ko shrink karne se pehle update karna, bina validity ka dhyan rakhe.
5. `k = 0` case bhool jaana. Is case mein answer longest substring of identical characters hona chahiye.

## 9. Pattern Recognition

Jab problem mein ye clues milen, sliding window consider karo:

- Longest substring.
- At most `k` replacements/changes.
- Window ke andar frequency count.
- Window valid hai ya nahi, ye ek condition se check ho sakta hai.

**Pattern:** Expand right → update frequency → shrink while invalid → update maximum length.

## 10. One-Line Revision

`Longest window length = window size`, provided `window size - maximum character frequency <= k`; otherwise shrink from the left.

**Related Problems:** LC 3 — Longest Substring Without Repeating Characters; LC 1004 — Max Consecutive Ones III; LC 904 — Fruit Into Baskets.
