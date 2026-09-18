# 75. Sort Colors

**Difficulty:** Medium
**Pattern:** Three-Pointer Partitioning (**Dutch National Flag algorithm**)
**Tags:** Array, Two Pointers, Sorting

---

## Problem

Given an array `nums` with only `0`s, `1`s, and `2`s (representing red, white, blue), sort it **in-place** in a single pass, without using a library sort.

### Example
```
nums = [2,0,2,1,1,0]  ->  [0,0,1,1,2,2]
```

---

## ⚠️ Important: this is NOT a sliding-window problem

If you've been doing sliding-window problems back to back (76, 567, 1004...), it's easy to pattern-match "two/three pointers moving through an array" and assume this is the same family. **It isn't.** Sliding window maintains a *contiguous range* `[left, right]` that grows/shrinks to satisfy some condition on its contents. This problem instead uses three pointers to maintain **three partitions of the whole array simultaneously** — there's no "window" being measured, and `mid` sweeps the *entire* array exactly once. The only thing they share is "more than one index variable" — the actual mechanics are unrelated. This algorithm has its own name for a reason: the **Dutch National Flag algorithm**, coined by Edsger Dijkstra (the three stripes of the Dutch flag = three partitions).

---

## Core Idea

Maintain three pointers that divide the array into four conceptual regions at all times:

```
[ 0 ... low-1 ]  [ low ... mid-1 ]  [ mid ... high ]  [ high+1 ... n-1 ]
    all 0s            all 1s          unprocessed          all 2s
```

- `low` — boundary of the "all 0s" region (everything before `low` is a finalized `0`).
- `high` — boundary of the "all 2s" region (everything after `high` is a finalized `2`).
- `mid` — current element being classified; everything between `low` and `mid` is a finalized `1`.

Loop while `mid <= high` (once `mid` passes `high`, everything has been classified — there's nothing left unprocessed):

- **`nums[mid] == 0`**: it belongs in the 0-region. Swap it to the `low` boundary, then advance **both** `low` and `mid` (since the element that came from `low`'s position was already known to be a `1` or the region was empty, so it's now safely classified — safe to move `mid` forward too).
- **`nums[mid] == 1`**: it's already in the right place (the 1-region is exactly `[low, mid-1]`, so a `1` at `mid` just extends that region). Only advance `mid`.
- **`nums[mid] == 2`**: it belongs in the 2-region. Swap it to the `high` boundary, then only advance `high` — **not** `mid` — because the element swapped in from `high` hasn't been classified yet and needs to be examined next.

That asymmetry (`0`-case advances both pointers, `2`-case advances only `high`) is the single detail most people get wrong when reconstructing this algorithm from memory — it's worth drilling specifically.

---

## Verified Working Code (C++)

```cpp
class Solution {
public:
    void sortColors(vector<int>& nums) {
        int low = 0;
        int mid = 0;
        int high = nums.size() - 1;

        while (mid <= high) {
            if (nums[mid] == 2) {
                swap(nums[mid], nums[high]);
                high--;                 // do NOT advance mid — swapped-in value is unchecked
            }
            else if (nums[mid] == 1) {
                mid++;                  // already correctly placed
            }
            else {                      // nums[mid] == 0
                swap(nums[mid], nums[low]);
                low++;
                mid++;                  // safe to advance — see "why mid++ is safe" below
            }
        }
    }
};
```

Verified with manual test cases (all-0s, all-1s, all-2s, single element, empty array, already sorted, reverse sorted) **and** a 10,000-iteration randomized stress test comparing against `std::sort` on random arrays of length 0–14 with values in `{0,1,2}` — zero mismatches.

---

## Dry Run

`nums = [2,0,2,1,1,0]` — traced from the actual running code:

| step | low | mid | high | array (before) | nums[mid] | action | array (after) |
|---|---|---|---|---|---|---|---|
| 1 | 0 | 0 | 5 | `[2,0,2,1,1,0]` | 2 | swap(mid,high), high-- | `[0,0,2,1,1,2]` |
| 2 | 0 | 0 | 4 | `[0,0,2,1,1,2]` | 0 | swap(mid,low), low++, mid++ | `[0,0,2,1,1,2]` (no visible change — swapping index 0 with itself) |
| 3 | 1 | 1 | 4 | `[0,0,2,1,1,2]` | 0 | swap(mid,low), low++, mid++ | `[0,0,2,1,1,2]` (again a self-swap) |
| 4 | 2 | 2 | 4 | `[0,0,2,1,1,2]` | 2 | swap(mid,high), high-- | `[0,0,1,1,2,2]` |
| 5 | 2 | 2 | 3 | `[0,0,1,1,2,2]` | 1 | mid++ | `[0,0,1,1,2,2]` |
| 6 | 2 | 3 | 3 | `[0,0,1,1,2,2]` | 1 | mid++ | `[0,0,1,1,2,2]` |
| — | 2 | 4 | 3 | `mid > high` → loop ends | | | **`[0,0,1,1,2,2]`** ✅ |

---

## Why `mid++` is safe in the `0` case but not the `2` case

This is the part worth really internalizing, not just memorizing:

- When `nums[mid] == 0`, you swap it with `nums[low]`. Since `mid` only ever moves forward and `low <= mid` always holds, `nums[low]` before the swap is either the same element (`low == mid`, self-swap, harmless) or an element that `mid` has **already visited and classified** at some earlier step — meaning it's guaranteed to be a `1` (it can't be a `0`, because then `low` would have already advanced past it when it was classified; it can't be a `2`, because `2`s get sent to the `high` end, not left behind at `low`). So swapping it into `mid`'s old position just moves a known `1` there, and `mid` can safely skip past it.
- When `nums[mid] == 2`, you swap it with `nums[high]`. But `nums[high]` (before the swap) is **unprocessed** — `mid` has never seen it, since `mid` sweeps left-to-right and hasn't reached that far yet (or has, but `high` shrinks independently). It could be a `0`, `1`, or `2`. So after the swap, the new `nums[mid]` needs to be examined fresh — advancing `mid` here would skip classifying it, silently corrupting the result.

This asymmetry is exactly why the two branches look almost identical but differ in whether `mid++` happens — and it's the single most common place to introduce a subtle bug when rewriting this from memory.

---

## Complexity

- **Time:** O(n) — single pass; `mid` moves forward monotonically and `high` moves backward monotonically, so the loop runs at most `n` total pointer-advancing steps.
- **Space:** O(1) — in-place, only three integer pointers, no auxiliary array (unlike a counting-sort approach, which would also be O(n) time but uses O(1) *extra* space differently — via a count array of size 3 — and requires two passes: one to count, one to overwrite).

---

## Edge Cases / Gotchas

- **Empty array** — `mid=0, high=-1`, loop condition `mid <= high` is `0 <= -1` → false immediately, loop never runs. Correct, no crash.
- **Single element** — loop runs once, classifies it, terminates correctly regardless of which of 0/1/2 it is.
- **All same value** — e.g., all `2`s: every iteration swaps `nums[high]` with itself and decrements `high`; terminates correctly once `mid > high`, just does some harmless self-swaps.
- **The `low <= mid` invariant** must always hold for the "why `mid++` is safe" argument above to work — it's maintained automatically since both only ever move forward and `low` never advances without `mid` also advancing in the same step.
- **Do not swap-and-advance-mid in the `2` case** — this is the classic bug: copy-pasting the `0`-case's `low++; mid++;` pattern into the `2`-case breaks correctness, because it skips checking the freshly-swapped-in element. Test with an input like `[2,0,2]` to catch this bug immediately if reproducing from memory (a naive wrong version would leave a `0` or `2` misplaced).

---

## Related / Same-Pattern Problems

- **75** (this one) — the canonical Dutch National Flag problem, exactly 3 values.
- **Partition step of Quicksort (Lomuto / Hoare)** — conceptually related two-way partitioning (elements less-than vs greater-than-or-equal to a pivot); Dutch National Flag is the natural extension to **three-way** partitioning, and is in fact used inside optimized quicksort implementations (3-way quicksort) to efficiently handle arrays with many duplicate keys.
- **283. Move Zeroes** — a simpler two-partition version of this same swap-and-advance idea (partition into "non-zero" / "zero" instead of three colors).
- **General template:** when you need to partition an array in-place into a small, fixed number of known categories in one pass, Dutch National Flag's "one pointer per boundary, decide movement based on which category the current element belongs to" is the reusable idea — and the number of pointers scales with the number of categories minus one (2 categories → 1 pointer/pass a la partition step; 3 categories → 2 boundary pointers + 1 scanner, as here).

---

## One-Line Recap (for quick revision)

> Three pointers (`low`, `mid`, `high`) maintain four regions — finalized 0s, finalized 1s, unprocessed, finalized 2s. A `0` swaps to `low` and both pointers advance (swapped-in element is a known `1`); a `1` just advances `mid`; a `2` swaps to `high` and **only** `high` advances, because the swapped-in element is still unclassified.
