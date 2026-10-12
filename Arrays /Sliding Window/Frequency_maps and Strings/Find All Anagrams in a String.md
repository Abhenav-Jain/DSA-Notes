# Find All Anagrams in a String — LeetCode 438

**Difficulty:** Medium  
**Pattern:** Fixed-Size Sliding Window + Frequency Counting  
**Primary approach:** `unordered_map`  
**Optimized approach:** Frequency array  
**Time complexity:** `O(m + n)` average for the map approach with bounded character set; `O(m + n)` for the array approach  
**Space complexity:** `O(Σ)` for maps/arrays, where `Σ` is the character set size

---

## 1. Problem Intuition

Given two strings `s` and `p`, find all starting indices of substrings in `s` that are anagrams of `p`.

Anagrams contain exactly the same characters with the same frequencies, but their order can differ.

### Example

```text
s = "cbaebabacd"
p = "abc"

Output = [0, 6]
```

Explanation:

- `s.substr(0, 3) = "cba"` → anagram of `"abc"`.
- `s.substr(6, 3) = "bac"` → anagram of `"abc"`.

Both substrings contain `a`, `b`, and `c` exactly once.

**Important:** Return the starting indices, not the substrings themselves.

---

## 2. Core Idea — Fixed-Size Sliding Window

Let:

- `n = p.length()` — required window size.
- `m = s.length()` — length of the main string.
- `mp` — character frequencies in `p`.
- `cp` — character frequencies in the current window of `s`.
- `i` — left pointer.
- `j` — right pointer.
- `ans` — stores the starting indices of all anagrams.

Since every anagram of `p` has exactly `p.length()` characters, our window must have a fixed size of `n`.

### Key condition

```cpp
j - i + 1 == n
```

When the window reaches the required size, compare the character frequencies.

If `cp == mp`, the current window is an anagram, so add `i` to the answer.

---

## 3. Approach 1 — Your Original Solution (`unordered_map`)

```cpp
class Solution {
public:
    vector<int> findAnagrams(string s, string p) {
        unordered_map<char,int> mp;

        for(char c : p) {
            mp[c]++;
        }

        int i = 0;
        int j = 0;
        int n = p.length();
        int m = s.length();

        vector<int> ans;
        unordered_map<char,int> cp;

        while(j < m) {
            cp[s[j]]++;

            while((j - i + 1) > n) {
                cp[s[i]]--;

                if(cp[s[i]] == 0) {
                    cp.erase(s[i]);
                }

                i++;
            }

            if(cp == mp) {
                ans.push_back(i);
            }

            j++;
        }

        return ans;
    }
};
```

### Line-by-Line Explanation

**Step 1 — Build the pattern frequency map**

```cpp
for(char c : p) {
    mp[c]++;
}
```

For `p = "abc"`:

```text
mp = {a:1, b:1, c:1}
```

**Step 2 — Expand the window**

```cpp
cp[s[j]]++;
```

Add the current character to the window's frequency map.

**Step 3 — Shrink if the window is too large**

```cpp
while((j - i + 1) > n)
```

If the window exceeds the length of `p`, remove characters from the left until its size is at most `n`.

**Step 4 — Update the outgoing character's frequency**

```cpp
cp[s[i]]--;
```

The character at index `i` is leaving the window, so decrease its frequency.

**Step 5 — Erase zero-frequency keys**

```cpp
if(cp[s[i]] == 0) {
    cp.erase(s[i]);
}
```

This matters because map equality compares keys as well as their values. A key with frequency zero can make the maps unequal even when their positive frequencies otherwise match.

**Step 6 — Check whether the window is an anagram**

```cpp
if(cp == mp) {
    ans.push_back(i);
}
```

If the maps are equal, the window contains exactly the same character frequencies as `p`. Store its starting index.

### Small Improvements to Your Code

Add this early return:

```cpp
if(p.length() > s.length()) {
    return {};
}
```

No anagram is possible if `p` is longer than `s`.

Also, you can make the window-size condition explicit:

```cpp
if(j - i + 1 == n && cp == mp) {
    ans.push_back(i);
}
```

Your original code is already logically correct: the map equality can only hold when the current window contains the same total number of characters as `p`, and the shrinking loop ensures the window never exceeds `n`.

---

## 4. Dry Run

```text
s = "cbaebabacd"
p = "abc"
```

Target frequency map:

```text
mp = {a:1, b:1, c:1}
```

| Window | Frequency match? | Action |
|---|---|---|
| `"cba"` | Yes | Store index `0` |
| `"bae"` | No | Continue |
| `"aeb"` | No | Continue |
| `"eba"` | No | Continue |
| `"bab"` | No | Continue |
| `"aba"` | No | Continue |
| `"bac"` | Yes | Store index `6` |
| `"acd"` | No | Continue |

Final answer:

```text
[0, 6]
```

---

## 5. Complexity of Your Map-Based Solution

Let `m = s.length()`, `n = p.length()`, and `Σ` be the character set size.

- **Time:** `O(n + m × Σ)` when map equality compares all relevant keys at each window.
- **Space:** `O(Σ)` for the two frequency maps, excluding the output vector.

For lowercase English letters, `Σ ≤ 26`, so the map comparison is bounded by a small constant in practice. The output vector can contain `O(m)` indices.

---

## 6. Approach 2 — Optimized Frequency Arrays

For lowercase English letters, we don't need hash maps. We can use arrays of size `26`.

Instead of maintaining keys and values, each array position represents a character:

```text
'a' → index 0
'b' → index 1
...
'z' → index 25
```

### C++ Code

```cpp
class Solution {
public:
    vector<int> findAnagrams(string s, string p) {
        int m = s.size();
        int n = p.size();

        vector<int> ans;

        if(n > m) return ans;

        vector<int> need(26, 0);
        vector<int> window(26, 0);

        for(char c : p) {
            need[c - 'a']++;
        }

        for(int j = 0; j < m; j++) {
            window[s[j] - 'a']++;

            if(j >= n) {
                window[s[j - n] - 'a']--;
            }

            if(j >= n - 1 && window == need) {
                ans.push_back(j - n + 1);
            }
        }

        return ans;
    }
};
```

### How It Works

1. Count the characters in `p`.
2. Expand the window by adding `s[j]`.
3. If the window exceeds size `n`, remove the character at `s[j - n]`.
4. Once the window has size `n`, compare both frequency arrays.
5. If they match, the starting index is `j - n + 1`.

### Why `j - n + 1`?

The window ends at `j` and contains `n` characters.

```text
start = end - length + 1
      = j - n + 1
```

### Complexity

- **Time:** `O(m + n)` because each window comparison checks exactly 26 entries.
- **Auxiliary space:** `O(1)` because both frequency arrays always contain 26 integers.
- **Output space:** Up to `O(m)` for the result indices.

This solution assumes both strings contain lowercase English letters.

---

## 7. Alternative Optimization — Single Frequency Array

We can also maintain one frequency-difference array and count how many character frequencies differ from the target.

Conceptually:

```text
diff[c] = frequency in p - frequency in current window
```

When every entry is zero, the window is an anagram.

This approach avoids comparing all 26 entries for every window. It is a useful advanced technique, but the two-array version is usually simpler to explain and already runs in linear time.

---

## 8. Common Mistakes

1. Using a variable-size window when the required size is fixed.
2. Forgetting to remove the outgoing character from the frequency count.
3. Leaving zero-frequency keys in a map before comparing maps.
4. Returning the ending index instead of the starting index.
5. Forgetting to check whether `p` is longer than `s`.
6. Comparing frequencies before the window reaches size `p.length()`.
7. Thinking anagrams must have the same order of characters.

---

## 9. Pattern Recognition

Use fixed-size sliding window + frequency counting when:

- You need to find all anagrams or permutations.
- Every valid substring has a known fixed length.
- Character frequencies must match exactly.
- You need all matching starting indices.

### Related Problems

- LC 567 — Permutation in String
- LC 76 — Minimum Window Substring
- LC 3 — Longest Substring Without Repeating Characters

---

## One-Line Revision

Maintain a window of size `p.length()`, update its character frequencies as it slides, and record the starting index whenever the window's frequencies match `p`.

**Interview takeaway:** Your map-based solution is correct. For lowercase English letters, the frequency-array version is a cleaner optimization with `O(m + n)` time and `O(1)` auxiliary space.
