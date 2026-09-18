# 567. Permutation in String

**Difficulty:** Medium
**Pattern:** Sliding Window (**fixed size**) + Frequency Array
**Tags:** String, Hash Table, Two Pointers, Sliding Window

---

## Problem

Given two strings `s1` and `s2`, return `true` if `s2` contains a **permutation of `s1`** as a contiguous substring — i.e. some window of `s2` with length `s1.length()` that has exactly the same character multiset as `s1` (any order).

### Examples
```
s1 = "ab", s2 = "eidbaooo"  -> true   (window "ba" at index 3-4 is a permutation of "ab")
s1 = "ab", s2 = "eidboaoo"  -> false  (no window is an anagram of "ab")
```

---

## Core Idea

This is the **fixed-size** sibling of the Minimum Window Substring pattern — instead of growing/shrinking to find the *smallest* valid window, the window size is locked to `m = s1.length()` from the start, and you slide it one step at a time across `s2`, checking equality at every position.

1. `need` — frequency array (size 26, since lowercase-only) of `s1`.
2. `window` — frequency array of the current `m`-length slice of `s2`.
3. Expand `right`, add the new character into `window`.
4. If the window has grown past size `m`, shrink from `left` (remove exactly one character) — this keeps the window size clamped to **at most `m`**, and since we add exactly one and remove at most one per step, it settles at **exactly `m`** for every `right >= m-1`.
5. Once the window is exactly size `m`, compare `window == need` (full 26-length array comparison). If they match, `s2`'s substring at `[left, right]` is an anagram of `s1` — return `true` immediately.
6. If `right` reaches the end with no match, return `false`.

The key simplification versus problem 76 (Minimum Window Substring): there's no "have I satisfied enough occurrences" counter needed, because the window size is fixed — you just directly compare the full frequency arrays. This trades a bit of per-step work (`vector<int> == vector<int>` is O(26), i.e. O(1) since the alphabet is fixed) for much simpler logic.

---

## Verified Working Code (C++)

```cpp
class Solution {
public:
    bool checkInclusion(string s1, string s2) {
        int m = s1.length();
        int n = s2.length();
        if (m > n) return false;   // s1 can't possibly fit in s2

        vector<int> need(26, 0);
        vector<int> window(26, 0);
        for (char c : s1) need[c - 'a']++;

        int left = 0, right = 0;
        while (right < n) {
            window[s2[right] - 'a']++;

            // keep window size clamped to at most m
            while ((right - left + 1) > m) {
                window[s2[left] - 'a']--;
                left++;
            }

            if ((right - left + 1) == m) {
                if (window == need) {
                    return true;
                }
            }
            right++;
        }
        return false;
    }
};
```

Verified against LeetCode's two official examples plus edge cases: `s1` longer than `s2` → `false`, `s1 == s2` (single matching window) → `true`, single-character `s1` present/absent in `s2`, and a case with no match despite same character *set* but wrong *multiset* (`s1="aa", s2="ab"`) → `false`.

---

## Dry Run

`s1 = "ab"` (`need`: a→1, b→1, `m = 2`), `s2 = "eidbaooo"` — traced from the actual running code:

| right | char added | char dropped | left | current window | size==m? | matches `need`? |
|---|---|---|---|---|---|---|
| 0 | `e` | – | 0 | `"e"` | ❌ (size 1) | – |
| 1 | `i` | – | 0 | `"ei"` | ✅ | ❌ |
| 2 | `d` | `e` | 1 | `"id"` | ✅ | ❌ |
| 3 | `b` | `i` | 2 | `"db"` | ✅ | ❌ |
| 4 | `a` | `d` | 3 | `"ba"` | ✅ | ✅ **→ return true** |

Notice the window **always has exactly one character enter and at most one leave per step** once `right >= m-1` — that's what makes it "slide" rather than "grow/shrink" like problem 76's variable window.

---

## Complexity

- **Time:** O(n × 26) ≈ **O(n)**, where `n = s2.length()`. Each position does O(1) array updates plus an O(26) array comparison (`window == need`) — since 26 is a constant, this whole term is O(n) overall, not O(26n) in any meaningful asymptotic sense.
- **Space:** O(26) = **O(1)** — two fixed-size frequency arrays, independent of input size.

> This is strictly better than using two `unordered_map<char,int>` for `need`/`window` (as problem 76's solution does) — since the alphabet is known and small (lowercase a-z), a `vector<int>(26)` avoids hashing overhead and gives you a trivial O(1) equality check via `operator==` on the vectors.

---

## Edge Cases / Gotchas

- **`m > n` guard at the top is essential** — without it, the window would never reach size `m`, and the loop would just run to completion returning `false` anyway... but it's still worth keeping as an explicit early-exit for clarity (and to avoid wasted iterations on clearly-impossible inputs).
- **`window == need` compares full 26-length vectors** — this correctly handles characters *not* in `s1` too: if `s2`'s window contains a character absent from `s1`, that character's slot in `window` will be nonzero while `need`'s slot is `0`, so the vectors won't match. No separate "extra character" check needed.
- **Fixed-size window vs. variable-size window (76) — don't confuse the two patterns:**
  - *Fixed size* (this problem, also 438 Find All Anagrams): window size is known upfront; slide by adding one + removing one every step past the initial fill.
  - *Variable size* (76 Minimum Window Substring): window grows until valid, then shrinks as far as possible; size isn't fixed.
- **Multiset, not set** — `s1 = "aa"` requires *two* `'a'`s in the window, not just "contains an `'a'`." The full frequency-array comparison naturally enforces multiplicity; a distinct-character-set check would be wrong here (this is the same trap as in problem 76).
- **Early return on first match** — the function returns `true` the moment any valid window matches; it doesn't need to check all windows, unlike problems that ask you to *count* all matches (e.g. 438 needs to continue scanning and collect every matching start index instead of returning early).

---

## Related / Same-Pattern Problems

- **567** (this one) — does *any* fixed-size window equal a target frequency profile (return `true`/`false` on first match).
- **438. Find All Anagrams in a String** — nearly identical code, but instead of returning `true` on match, record `left` into a result list and keep scanning to the end.
- **76. Minimum Window Substring** — the variable-size version of this family; compare directly with these notes to see how the "satisfied count" trick there replaces the direct array-equality check used here (equality check works here specifically because the window size is fixed, so the *only* way to match is exact multiset equality; for a variable window that isn't true).
- **General fixed-window template:** maintain a frequency array of the target, maintain a frequency array of the current window, add-one/remove-one per step once the window reaches full size, and compare (or diff-count) the two arrays each step.

---

## One-Line Recap (for quick revision)

> Lock the window to `s1.length()`, slide it one character at a time across `s2` (add the new char, drop the oldest once size exceeds `m`), and compare the window's 26-length frequency array against `s1`'s — a match means `s2` contains a permutation of `s1` ending here.
