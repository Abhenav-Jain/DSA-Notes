# 1477. Find Two Non-overlapping Sub-arrays Each With Target Sum

**Difficulty:** Medium
**Pattern:** Sliding Window + DP (prefix of best-so-far)
**Tags:** Array, Sliding Window, Dynamic Programming

---

## Problem

Given an array of **positive integers** `arr` and an integer `target`, find **two non-overlapping sub-arrays**, each with sum exactly `target`, such that the **sum of their lengths is minimized**.

Return that minimum sum of lengths, or `-1` if no such pair exists.

> All elements are positive (`1 <= arr[i] <= 1000`). This constraint is what makes the sliding window valid — see below.

### Examples
```
arr = [3,2,2,4,3], target = 3   -> 2   ([3] and [3])
arr = [7,3,4,7],   target = 7   -> 2   ([7] and [7])
arr = [4,3,2,6,2,3,4], target = 6 -> -1
arr = [5,5,4,4,5], target = 3   -> -1  (no subarray sums to 3 at all)
arr = [3,1,1,1,5,1,2,1], target = 3 -> 3  ([3] and [1,2] or [1,1,1])
```

---

## Core Idea

Two things bolted together:

1. **Sliding window** to find, as we scan left→right, every index `right` where a valid window `[left, right]` sums to `target`. Works in O(n) *only because all values are positive* — `curr_sum` is monotonic, so shrinking from the left when `curr_sum > target` never needs to reconsider a previously-passed `left`.

2. **"Best window ending by index i" DP array** (`best_min_len_till_idx`) — a running minimum of the shortest valid window found so far, indexed by right-endpoint. This lets you answer, in O(1), "what's the shortest valid subarray fully contained in `arr[0..k]`?" for any `k`.

Combine them: whenever the sliding window finds a valid window `[left, right]`, the *best possible partner* for it is the shortest valid window that ends **before `left`** (so it can't overlap). That's exactly `best_min_len_till_idx[left - 1]`. Add current window's length to it, take the running minimum over all `right` — that's the answer.

This is the standard trick for "find two/three non-overlapping subarrays satisfying X, minimize combined length/sum" — split the array at the current window's left boundary, and ask a **prefix** array for the best answer to the left of that split.

---

## Why sliding window (not prefix sum + hashmap) is enough here

Because `arr[i] > 0` always:
- `curr_sum` strictly increases as `right` moves forward.
- So once `curr_sum > target`, shrinking from the left is safe and monotonic — `left` only ever moves forward, giving amortized O(n) total pointer movement.

If negative numbers were allowed, you'd need prefix sums + hashmap instead (sliding window breaks because sum isn't monotonic).

---

## Verified Working Code (C++)

```cpp
class Solution {
public:
    int minSumOfLengths(vector<int>& arr, int target) {
        int n = arr.size();
        int best_min = INT_MAX;                              // shortest valid window seen so far
        vector<int> best_min_len_till_idx(n, INT_MAX);        // best_min, snapshotted per index
        int result = INT_MAX;
        int curr_sum = 0;
        int left = 0, right = 0, len = 0;

        while (right < n) {
            curr_sum += arr[right];

            // shrink while sum overshoots target
            while (curr_sum > target) {
                curr_sum -= arr[left];
                left++;
            }

            if (curr_sum == target) {
                len = right - left + 1;

                // try pairing current window with best window ending strictly before `left`
                if (left > 0 && best_min_len_till_idx[left - 1] != INT_MAX) {
                    result = min(result, len + best_min_len_till_idx[left - 1]);
                }

                best_min = min(best_min, len);
            }

            best_min_len_till_idx[right] = best_min;   // snapshot running best up to `right`
            right++;
        }

        return (result == INT_MAX) ? -1 : result;
    }
};
```

Verified against all 5 official LeetCode examples + edge cases (`[1,1,1,1,1]` target 1 → 2, single-element array → -1, array that sums to target only as one whole window → -1) — all pass.

---

## Dry Run

`arr = [3,1,1,1,5,1,2,1]` (indices 0-7), `target = 3` — traced by instrumenting the actual code, not by hand:

| right | arr[right] | curr_sum after shrink | left | valid? | len | pair contribution | best_min | result |
|---|---|---|---|---|---|---|---|---|
| 0 | 3 | 3 | 0 | ✅ | 1 | left=0 → no pairing possible | 1 | – |
| 1 | 1 | 1 (shrank: 3+1=4 → drop arr[0]=3) | 1 | ❌ | – | – | 1 | – |
| 2 | 1 | 2 | 1 | ❌ | – | – | 1 | – |
| 3 | 1 | 3 | 1 | ✅ | 3 `[1..3]` | `best_min_len_till_idx[0]=1` → 3+1=**4** | 1 | 4 |
| 4 | 5 | 0 (shrank hard: 3+5=8 → drop arr[1..4]=1,1,1,5 until left=5) | 5 | ❌ | – | – | 1 | 4 |
| 5 | 1 | 1 | 5 | ❌ | – | – | 1 | 4 |
| 6 | 2 | 3 | 5 | ✅ | 2 `[5..6]` | `best_min_len_till_idx[4]=1` → 2+1=**3** | 1 | 3 |
| 7 | 1 | 3 (shrank: 3+1=4 → drop arr[5]=1) | 6 | ✅ | 2 `[6..7]` | `best_min_len_till_idx[5]=1` → 2+1=3 | 1 | 3 |

`best_min` never drops below `1` because the very first window `[0,0]` (just the single `3`) is already length 1 — nothing beats that, so every later window ends up pairing against that same length-1 window at index 0.

Final `result = 3` ✅ (matches LeetCode's expected output). Confirmed by running the actual compiled code, not eyeballed — when in doubt, instrument and run the trace rather than tracing by hand, since it's easy to mis-track `left` during the shrink loop.

---

## Complexity

- **Time:** O(n) — `right` and `left` each move forward at most `n` times total (classic two-pointer amortized analysis).
- **Space:** O(n) — for `best_min_len_till_idx`.

---

## Edge Cases / Gotchas

- **No valid window at all** → `best_min` never updates → `result` stays `INT_MAX` → return `-1`.
- **Only one valid window exists, nothing to pair it with** → `left == 0` (can't pair) or `best_min_len_till_idx[left-1] == INT_MAX` (nothing valid before it) → that window alone never contributes to `result` → correctly excluded, must return `-1` if it's the only one.
- **Two adjacent windows** (no gap) — e.g. `[1,1,1,1,1]`, target `1`: windows `[0,0]` and `[1,1]` are adjacent, not overlapping — still valid, since pairing rule is "ends strictly before `left`", i.e. `best_min_len_till_idx[left-1]`, and `left-1 = 0` is the previous window's own end index. Correctly handled.
- **Off-by-one on `left > 0` guard** — needed because `best_min_len_till_idx[left-1]` with `left == 0` would be `best_min_len_till_idx[-1]`, out of bounds. Easy bug to introduce if you forget this check.
- **Constraint reminder:** this whole approach (sliding window instead of prefix-sum/hashmap) relies on **all positive integers**. If a variant of this problem allows 0s or negatives, the shrink-window logic breaks and needs prefix sums + hashmap instead.

---

## Related / Same-Pattern Problems

- **1477** (this one) — two non-overlapping subarrays, minimize sum of lengths.
- **1546. Maximum Number of Non-Overlapping Subarrays With Sum Equals Target** — same window-finding mechanic, but greedy (maximize *count*, not minimize length) — greedily take a window the moment you find one and reset, no DP array needed.
- Any "**split array into k non-overlapping parts, each satisfying X, optimize Y**" problem — the general template is: slide/scan to detect valid segments, maintain a **prefix-best** array (best answer using only `arr[0..i]`), and whenever a new valid segment closes at position `i`, combine it with `prefix_best[start_of_segment - 1]`.

---

## One-Line Recap (for quick revision)

> Sliding window (works because all positive) finds every valid `[left, right]` window; a prefix array `best_min_len_till_idx[i]` tracks the shortest valid window ending at-or-before `i`; every time a window closes, pair it with `best_min_len_till_idx[left-1]` to get the best non-overlapping combo ending here, and take the running min.
