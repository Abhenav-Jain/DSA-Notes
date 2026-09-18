# 76. Minimum Window Substring

**Difficulty:** Hard
**Pattern:** Sliding Window (variable size) + Frequency Counting
**Tags:** String, Hash Map, Two Pointers, Sliding Window

---

## Problem

Given strings `s` and `t`, find the **smallest substring of `s`** that contains **every character of `t`**, including duplicates (i.e. contains at least as many of each character as `t` requires). Return `""` if no such window exists.

### Examples
```
s = "ADOBECODEBANC", t = "ABC"  -> "BANC"
s = "a", t = "a"                -> "a"
s = "a", t = "aa"                -> ""   (s doesn't have two 'a's)
```

---

## Core Idea

Classic **variable-size sliding window**, expand-then-shrink:

1. **`need`** — frequency map of what `t` requires (built once, upfront).
2. **`have`** — frequency map of what the current window `[left, right]` actually contains, but only for characters that appear in `need` (irrelevant characters aren't tracked here).
3. **`cnt`** — a running count of "how many required occurrences are currently satisfied." Every time a character is added to the window and it's still *needed* (`have[ch] <= need[ch]`), `cnt++`. This is the key trick: `cnt` counts satisfied **occurrences**, not just satisfied **distinct characters** — so `cnt == t.length()` is exactly the condition "window contains everything `t` needs, with correct multiplicities."
4. Whenever `cnt == requirement` (window is valid), **shrink from the left as far as possible** while staying valid — drop characters not in `need` outright, and drop *excess* copies of characters that are in `need` (`have[ch] > need[ch]`) since those are surplus. Stop the moment you'd remove a character you still need (`have[ch] == need[ch]` exactly) — that's the tightest the window can get for this `right`.
5. Record the window if it beats the best found so far.
6. Advance `right` and repeat.

Both pointers only move forward — `right` scans the string once, and `left` across the whole run also only ever advances (it never resets backward), giving **O(n)** total work despite the nested-looking `while` inside the `while`.

---

## Verified Working Code (C++)

```cpp
class Solution {
public:
    string minWindow(string s, string t) {
        unordered_map<char,int> need;
        unordered_map<char,int> have;
        int left = 0, right = 0;
        int min_len = INT_MAX;
        int requirement = t.length();   // total required *occurrences*, not distinct chars
        int cnt = 0;
        string ans = "";

        for (char ch : t) need[ch]++;   // build requirement map once

        while (right < s.length()) {
            if (need.find(s[right]) != need.end()) {
                have[s[right]]++;
                if (have[s[right]] <= need[s[right]]) {
                    cnt++;              // only count up to what's actually needed
                }
            }

            if (cnt == requirement) {
                // shrink as far as possible while window stays valid
                while (left <= right) {
                    if (need.find(s[left]) == need.end()) {
                        left++;                          // irrelevant char, always drop
                    } else if (have[s[left]] > need[s[left]]) {
                        have[s[left]]--;
                        left++;                           // surplus copy, drop it
                    } else {
                        break;                             // exactly needed — stop shrinking
                    }
                }
                int len = right - left + 1;
                if (len < min_len) {
                    min_len = len;
                    ans = s.substr(left, min_len);
                }
            }
            right++;
        }
        return ans;
    }
};
```

Verified against LeetCode's official example plus edge cases: `s="a",t="aa"` → `""`, `s="ab",t="b"` → `"b"`, `s="aa",t="aa"` → `"aa"`, `s="bba",t="ab"` → `"ba"`, `t` requiring characters in different order than `s` (`s="abc",t="cba"` → `"abc"`) — all pass.

---

## Dry Run

`s = "ADOBECODEBANC"`, `t = "ABC"` (`need = {A:1, B:1, C:1}`, `requirement = 3`) — traced from the actual running code:

| right | char added | cnt | window becomes valid? | shrink result | window | len | best? |
|---|---|---|---|---|---|---|---|
| 0–4 | A,D,O,B,E | 1→1→1→2→2 | not yet (cnt<3) | – | – | – | – |
| 5 | C | 3 | ✅ | left stays 0 (A,B,C all exactly needed) | `ADOBEC` | 6 | **new best (6)** |
| 6–9 | O,D,E,B | 3 | ✅ (still) | left stays 0 (extra B is surplus... but wait, see note below) | grows to `ADOBECODEB` | 10 | no |
| 10 | A | 3 | ✅ | shrinks hard: drops `A,D,O,B,E` (surplus/irrelevant) until left=5 | `CODEBA` | 6 | no (ties, not strictly less) |
| 11 | N | 3 | ✅ | left stays 5 | `CODEBAN` | 7 | no |
| 12 | C | 3 | ✅ | shrinks: drops `C,O,D,E` (surplus C, irrelevant O/D/E) until left=9 | `BANC` | 4 | **new best (4)** |

Final answer: **`"BANC"`** ✅ matches expected output.

> Note on right=6–9: the shrink loop *does* run every time (since `cnt` stays `== requirement`), but it exits immediately without moving `left`, because `s[left]` is `'A'` at index 0, and `have['A'] == need['A'] == 1` exactly — that's the "exactly needed, stop shrinking" branch. The surplus second `B` (at index 9) doesn't get evicted until `left` actually walks up to it later (at right=10), because the shrink loop always checks from the *current* `left` outward — it can't skip ahead to evict a surplus character sitting in the middle of the window while an exactly-needed character still blocks the front.

---

## Complexity

- **Time:** O(|s| + |t|) — building `need` is O(|t|); the main loop is O(|s|) because `right` advances |s| times and `left` advances at most |s| times total across the whole run (amortized, not per-iteration).
- **Space:** O(k) where `k` = number of distinct characters in `t` (bounded by alphabet size, so effectively O(1) for fixed character sets like ASCII).

---

## Edge Cases / Gotchas

- **`t` longer than `s`, or `t` has more of a character than `s` can ever supply** → `cnt` never reaches `requirement` → loop ends with `ans == ""`. No special-casing needed, falls out naturally.
- **Duplicate characters in `t`** (e.g. `t = "aa"`) — this is exactly why `cnt` tracks *occurrences* via `have[ch] <= need[ch]`, not just "have I seen this character at all." A naive "distinct characters satisfied" check would wrongly consider `s = "a"` sufficient for `t = "aa"`.
- **The `have[ch] <= need[ch]` guard when incrementing `cnt`** is what prevents overcounting: if a character keeps appearing beyond what's needed, `cnt` correctly stops incrementing for it, so surplus copies don't fake a "more satisfied" state.
- **Shrink loop's three-way branch matters**: dropping irrelevant characters (`left++` with no `have` change) must be separate from dropping surplus needed characters (`have[ch]--` then `left++`) — conflating them (e.g. decrementing `have` for a character not even in `need`) would corrupt the map.
- **Why the shrink loop is inside the `if(cnt == requirement)` block and not a separate pass**: shrinking must happen immediately every time the window becomes valid at the current `right`, so that `len` is measured on the *tightest* valid window for that `right` — not deferred to later.
- **Ties on `len`**: code only updates on strictly `len < min_len`, so the *first* minimal window found is kept if a later window of equal length appears — doesn't affect correctness (any minimal window is an acceptable answer) but worth knowing if the problem ever asked for something order-dependent.

---

## Related / Same-Pattern Problems

- **76** (this one) — minimum window containing all of `t` (with multiplicities).
- **3. Longest Substring Without Repeating Characters** — same two-pointer skeleton, but shrink condition is "duplicate found" instead of a frequency-satisfaction count.
- **438. Find All Anagrams in a String** / **567. Permutation in String** — fixed-size window version of the same `need`/`have` frequency-matching idea (window size is `t.length()`, not variable).
- **General template:** expand `right` unconditionally, maintain a `have` map + a satisfied-count variable, shrink `left` only while the window stays "valid" by some frequency condition, record the extremal window whenever valid. This exact skeleton solves the large majority of "smallest/largest substring/subarray satisfying a character/frequency condition" problems.

---

## One-Line Recap (for quick revision)

> Expand `right`, track needed-vs-have counts with `cnt` counting *satisfied occurrences* (not just distinct chars); the moment `cnt == len(t)`, greedily shrink `left` past irrelevant and surplus characters until you'd break validity, and record that window if it's the smallest seen.
