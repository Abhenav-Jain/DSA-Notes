# 1004. Max Consecutive Ones III

**Difficulty:** Medium
**Pattern:** Sliding Window (variable size, "budget"-constrained)
**Tags:** Array, Binary Search, Sliding Window

---

## Problem

Given a binary array `nums` and an integer `k`, return the length of the **longest contiguous subarray** containing only `1`s, if you're allowed to **flip at most `k` zeros to `1`s**.

### Examples
```
nums = [1,1,1,0,0,0,1,1,1,1,0], k = 2  -> 6   (flip the two 0s in "0011110" region -> "111111")
nums = [0,0,1,1,0,0,1,1,1,0,1,1,0,0,0,1,1,1,1], k = 3 -> 10
```

---

## Core Idea

This is the simplest member of the variable-size sliding-window family — even simpler than Minimum Window Substring (76) or Permutation in String (567), because there's only **one thing to track**: a running count of zeros in the current window.

1. Expand `right` every iteration, unconditionally. If the new element is `0`, increment `zeros`.
2. **Invariant to maintain:** the window can contain **at most `k` zeros** (since each zero "costs" one flip, and you only have `k` flips). Whenever `zeros > k`, the window is invalid — shrink from the `left` until it's valid again (removing a `0` decrements `zeros`; removing a `1` doesn't).
3. After the shrink step, the window `[left, right]` is *guaranteed* valid (`zeros <= k`) — record its length against `max_len`.
4. Advance `right`.

The elegant part: **you never need to explicitly shrink back down after growing past the best answer** — the window's size is naturally non-decreasing in the *best* sense, because once you've found a window of some length, `left` never moves backward, so the window can only get better or stay the same size as `right` advances (a classic property of this style of "at most K constraint violations" sliding window). That's why there's no `if` gate before shrinking — the `while(zeros > k)` loop just runs whenever needed, and `max_len` update happens unconditionally every iteration (it just won't produce a new best when the window had to shrink).

---

## Verified Working Code (C++)

```cpp
class Solution {
public:
    int longestOnes(vector<int>& nums, int k) {
        int zeros = 0;
        int left = 0, right = 0;
        int max_len = 0;

        while (right < nums.size()) {
            if (nums[right] == 0) {
                zeros++;
            }

            // shrink while the window has more zeros than flips allowed
            while (zeros > k) {
                if (nums[left] == 0) {
                    zeros--;
                }
                left++;
            }

            max_len = max(max_len, right - left + 1);
            right++;
        }
        return max_len;
    }
};
```

Verified against both LeetCode official examples plus edge cases: all-zero array with `k=0` → `0`, all-ones array with `k=0` → full length, single `0` with `k=1` → `1`, empty array → `0`, and an array where `k` is large enough to cover every zero → whole array length.

---

## Dry Run

`nums = [1,1,1,0,0,0,1,1,1,1,0]`, `k = 2` — traced from the actual running code:

| right | nums[right] | zeros (after add) | left (after shrink) | window length | new best? |
|---|---|---|---|---|---|
| 0 | 1 | 0 | 0 | 1 | ✅ (1) |
| 1 | 1 | 0 | 0 | 2 | ✅ (2) |
| 2 | 1 | 0 | 0 | 3 | ✅ (3) |
| 3 | 0 | 1 | 0 | 4 | ✅ (4) |
| 4 | 0 | 2 | 0 | 5 | ✅ (5) |
| 5 | 0 | 2 (shrunk: dropped `nums[0..3]`=1,1,1,0 until zeros back ≤2) | 4 | 2 | no |
| 6 | 1 | 2 | 4 | 3 | no |
| 7 | 1 | 2 | 4 | 4 | no |
| 8 | 1 | 2 | 4 | 5 | no |
| 9 | 1 | 2 | 4 | 6 | ✅ **(6, final answer)** |
| 10 | 0 | 2 (shrunk: dropped `nums[4]`=0) | 5 | 6 | no (tied, not exceeded) |

Final `max_len = 6` ✅ matches expected output — the winning window is at `right=9`, `left=4`, i.e. indices `[4,9]` = `nums[4],nums[5],nums[6],nums[7],nums[8],nums[9]` = `[0,0,1,1,1,1]`, which has exactly 2 zeros (flip both → six 1s in a row). Matches `zeros=2 <= k=2`. ✅

---

## Complexity

- **Time:** O(n) — `right` scans the array once; `left` only ever moves forward and advances at most `n` times total across the entire run (amortized O(1) per step, not counted per inner-loop iteration).
- **Space:** O(1) — just a handful of counters, no auxiliary arrays or maps needed (unlike 76/567, there's no character/value frequency to track — just a single zero-count).

---

## Edge Cases / Gotchas

- **`k >= total zeros in array`** — the window never needs to shrink at all; `right` just runs to the end and `max_len` ends up as `nums.size()`. Falls out naturally, no special-casing.
- **`k == 0`** — this degrades to plain "longest run of consecutive 1s, no flips allowed" (LeetCode 485-style); the shrink loop kicks in the instant any `0` is encountered, correctly forcing `left` past it.
- **Empty array** — loop body never executes, `max_len` stays `0`. Correct.
- **Why the `max_len` update has no `if` guard before it** (unlike problems 76/567, which only record a candidate when the window is "valid" under some *different* condition): here, **every iteration's window (after shrinking) is automatically valid** — the shrink loop's job is precisely to restore the "at most k zeros" invariant every single time before measuring. There's no separate notion of "valid" vs "current" window; they're the same thing after the `while` loop runs.
- **Don't confuse this with a fixed-size window** — the window size is *not* predetermined here (unlike 567); it grows freely and only contracts when the zero-budget is exceeded, which is the "variable size, constraint-bounded" flavor of sliding window (same family as 76, different constraint).

---

## Related / Same-Pattern Problems

- **1004** (this one) — longest subarray with at most `k` zeros (i.e., at most `k` allowed "violations").
- **487. Max Consecutive Ones II** — same problem with `k` hardcoded to `1`; can literally reuse this exact code.
- **340 / 159. Longest Substring with At Most K Distinct Characters** — same skeleton, but the constraint is "at most K distinct characters" (needs a frequency map to track distinct count) instead of "at most K zeros" (a single counter suffices).
- **General template — "longest window satisfying an *at-most-K* constraint":** expand unconditionally, shrink only while the constraint is violated, and record the window length **every iteration** (not gated behind a validity check) since the shrink loop already guarantees validity by the time you measure.

---

## One-Line Recap (for quick revision)

> Expand `right` freely, track a running `zeros` count as the only "cost," shrink `left` whenever `zeros > k` to restore the budget, and record window length every iteration — since the shrink loop guarantees the window is always valid by the time you measure it.
