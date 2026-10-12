# Permutation in String — LeetCode 567

**Difficulty:** Medium  
**Pattern:** Fixed-Size Sliding Window + Character Frequency  
**Approaches:** Hash Maps → Frequency Arrays  
**Goal:** Check whether `s2` contains any permutation of `s1`.

---

## 1. Problem Intuition

Given two strings `s1` and `s2`, return `true` if `s2` contains a substring that is a permutation of `s1`; otherwise, return `false`.

A permutation contains exactly the same characters with the same frequencies, but the order can differ.

### Example

```text
s1 = "ab"
s2 = "eidbaooo"

Output: true
```

The substring `"ba"` is a permutation of `"ab"`.

```text
s1 frequency: {a: 1, b: 1}
"ba" frequency: {b: 1, a: 1}
```

Both frequency counts match, so the answer is `true`.

**Important:** We need a contiguous substring of `s2`, not a subsequence.

---

## 2. Core Idea — Fixed-Size Sliding Window

Let:

- `m = s1.length()`
- `n = s2.length()`
- `mp`: character frequencies in `s1`
- `mp2`: character frequencies in the current window of `s2`
- `i`: left pointer
- `j`: right pointer

A permutation of `s1` must have exactly `m` characters.

Therefore, maintain a window of size `m` and compare its character frequencies with `s1`.

### Window condition

```cpp
j - i + 1 == m
```

If both frequency maps are equal, a permutation exists.

### Why does frequency comparison work?

For example:

```text
s1 = "aabc"
window = "abac"

s1:     a → 2, b → 1, c → 1
window: a → 2, b → 1, c → 1
```

The frequencies are identical, so the window is a permutation of `s1`.

---

## 3. Approach 1 — Your Original Code (Hash Maps)

```cpp
class Solution {
public:
    bool checkInclusion(string s1, string s2) {
        unordered_map<char,int> mp;

        for (char c : s1) {
            mp[c]++;
        }

        int i = 0;
        int j = 0;
        int n = s2.length();
        int m = s1.length();

        unordered_map<char,int> mp2;

        while (j < n) {
            mp2[s2[j]]++;

            while (j - i + 1 > m) {
                mp2[s2[i]]--;

                if (mp2[s2[i]] == 0) {
                    mp2.erase(s2[i]);
                }

                i++;
            }

            if (mp2 == mp) {
                return true;
            }

            j++;
        }

        return false;
    }
};
```

### How your code works

**Step 1 — Build the target frequency map**

```cpp
for (char c : s1) {
    mp[c]++;
}
```

Store the frequency of every character in `s1`.

**Step 2 — Expand the window**

```cpp
mp2[s2[j]]++;
```

Add the current character of `s2` to the window.

**Step 3 — Shrink if the window is too large**

```cpp
while (j - i + 1 > m)
```

The window must not exceed the length of `s1`. If it does, remove characters from the left.

**Step 4 — Remove zero-frequency keys**

```cpp
mp2[s2[i]]--;

if (mp2[s2[i]] == 0) {
    mp2.erase(s2[i]);
}
```

This is important because `unordered_map` equality compares keys and values. A key with frequency zero can make the maps unequal even if all positive frequencies match.

**Step 5 — Compare the maps**

```cpp
if (mp2 == mp) {
    return true;
}
```

If all character frequencies match, the current window is a permutation of `s1`.

### One improvement to your code

Add this check at the beginning:

```cpp
if (s1.length() > s2.length()) {
    return false;
}
```

If `s1` is longer than `s2`, a matching substring is impossible.

Also, explicitly compare only when the window size equals `m`. Your original code is logically correct without this extra condition because an equal frequency map necessarily has the same total character count, but the explicit condition makes the fixed-window logic clearer.

---

## 4. Dry Run

```text
s1 = "ab"
s2 = "eidbaooo"
```

Target map:

```text
mp = {a: 1, b: 1}
```

| Current window | Window frequencies | Match? |
|---|---|---|
| `"ei"` | `{e:1, i:1}` | No |
| `"id"` | `{i:1, d:1}` | No |
| `"db"` | `{d:1, b:1}` | No |
| `"ba"` | `{b:1, a:1}` | Yes |

When the window becomes `"ba"`, the frequencies match.

**Output: `true`**

---

## 5. Complexity of Your Map-Based Approach

Let `n = s2.length()` and `m = s1.length()`.

- **Time:** Average-case \(O(n+m)\) for map construction and window movement, but map equality comparisons can cost up to \(O(|\Sigma|)\) per window. Thus, a more precise bound is \(O(m+n|\Sigma|)\), where \(\Sigma\) is the character set represented in the maps.
- **Space:** \(O(|\Sigma|)\) for the two maps, where \(\Sigma\) is the number of distinct characters stored.

For a fixed character set such as lowercase English letters, the map size is bounded by 26, so this behaves linearly in practice.

---

## 6. Approach 2 — Optimized Solution (Frequency Array)

Because LeetCode 567 uses English letters, we can use two arrays of size 26 instead of hash maps.

```cpp
class Solution {
public:
    bool checkInclusion(string s1, string s2) {
        int m = s1.length();
        int n = s2.length();

        if (m > n) return false;

        vector<int> a(26, 0), b(26, 0);

        for (int i = 0; i < m; i++) {
            a[s1[i] - 'a']++;
            b[s2[i] - 'a']++;
        }

        if (a == b) return true;

        for (int j = m; j < n; j++) {
            b[s2[j] - 'a']++;
            b[s2[j - m] - 'a']--;

            if (a == b) return true;
        }

        return false;
    }
};
```

### How the optimized version works

1. Build the frequency array for `s1`.
2. Build the first window of length `m` in `s2`.
3. Compare both arrays.
4. Slide the window by one character:
   - Add the incoming character.
   - Remove the outgoing character.
5. Compare the arrays again.

There is no need to rebuild the entire window at every step.

### Complexity

- **Time:** \(O(n+m)\), because array comparison takes at most 26 checks per window.
- **Auxiliary space:** \(O(1)\), because both arrays always have exactly 26 entries.

**Important:** This version assumes lowercase English letters (`'a'` to `'z'`).

---

## 7. Further Optimization — Single Frequency Array

We can use one frequency array and track how many characters are currently different from the target frequencies. This avoids comparing all 26 entries at each step.

```cpp
class Solution {
public:
    bool checkInclusion(string s1, string s2) {
        int m = s1.size(), n = s2.size();
        if (m > n) return false;

        vector<int> freq(26, 0);

        for (char c : s1) {
            freq[c - 'a']++;
        }

        int diff = 0;
        for (int x : freq) {
            if (x != 0) diff++;
        }

        auto update = [&](char c, int delta) {
            int idx = c - 'a';

            if (freq[idx] == 0) diff++;
            freq[idx] += delta;
            if (freq[idx] == 0) diff--;
        };

        for (int j = 0; j < n; j++) {
            update(s2[j], -1);

            if (j >= m) {
                update(s2[j - m], 1);
            }

            if (j >= m - 1 && diff == 0) {
                return true;
            }
        }

        return false;
    }
};
```

**Intuition:** `freq[c]` represents the target frequency minus the current window frequency. When every entry is zero, the current window is a permutation.

- **Time:** \(O(n+m)\)
- **Auxiliary space:** \(O(1)\)

This version is more advanced. For interviews, the two-array version is often easier to explain and is already efficient.

---

## 8. Common Mistakes

1. Using a variable-size window instead of maintaining exactly `m` characters.
2. Forgetting to remove the outgoing character from the frequency count.
3. Leaving zero-frequency keys in a map and then comparing maps directly.
4. Comparing frequencies before the window has reached size `m`.
5. Forgetting the early return when `m > n`.
6. Assuming a permutation must preserve character order. It does not.

---

## 9. Pattern Recognition

Recognize **fixed-size sliding window + frequency count** when:

- A permutation or anagram is required.
- The substring length is fixed by another string.
- Character counts must match exactly.

### Related Problems

- LC 438 — Find All Anagrams in a String
- LC 3 — Longest Substring Without Repeating Characters
- LC 76 — Minimum Window Substring

---

## One-Line Revision

Maintain a window of length `s1.length()` in `s2`; if its character frequencies match those of `s1`, return `true`.

**Interview takeaway:** Your map solution is a valid baseline. A 26-element frequency array reduces overhead and provides \(O(n+m)\) time with \(O(1)\) auxiliary space for lowercase English letters.
