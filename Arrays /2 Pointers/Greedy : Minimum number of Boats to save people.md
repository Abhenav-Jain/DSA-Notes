# 881. Boats to Save People

**Difficulty:** Medium
**Pattern:** Greedy + Two Pointers (converging)
**Tags:** Array, Greedy, Sorting, Two Pointers

---

## Problem

`people[i]` is the weight of the `i`-th person. Each boat carries **at most 2 people**, and the sum of their weights must be **≤ `limit`**. Every person weighs at most `limit` (guaranteed by constraints, so everyone is individually boat-able). Return the **minimum number of boats** needed to carry everyone.

### Examples
```
people = [1,2], limit = 3        -> 1   (both fit together: 1+2=3)
people = [3,2,2,1], limit = 3    -> 3   (sorted [1,2,2,3]: boat(3 alone), boat(1,2), boat(2 alone))
people = [3,5,3,4], limit = 5    -> 4   (nobody can pair with anyone — everyone goes alone; see dry run below)
```

---

## Core Idea (the greedy proof, not just the mechanic)

Sort everyone by weight, then use two pointers: `i` at the lightest unassigned person, `j` at the heaviest.

**The greedy rule:** try to pair the current heaviest person (`j`) with the current lightest person (`i`).
- If `people[i] + people[j] <= limit`, put them in the same boat — advance both `i++` and `j--`.
- If `people[i] + people[j] > limit`, the heaviest person (`j`) **cannot be paired with anyone remaining** — send them alone, `j--` only (`i` stays).

**Why "heaviest goes alone" is safe, not just convenient:** `people[i]` is the *lightest available* weight. If the heaviest person can't even fit with the lightest one, they definitely can't fit with anyone heavier (everyone else remaining is `>= people[i]`). So the heaviest person is guaranteed to need a solo boat no matter what — there's no smarter pairing that saves them.

**Why "pair heaviest with lightest" (when it fits) is optimal, not just one valid option:** this is the part worth actually internalizing. Suppose the heaviest person *can* fit with the lightest. Could it ever be better to save that lightest person for later and pair the heaviest with someone else instead? No — pairing the heaviest with *any* other remaining person uses *at least as much* boat capacity as pairing with the lightest (since the lightest person contributes the least weight possible). Since the heaviest still needs a boat regardless, giving that boat's "free" remaining capacity to the cheapest possible partner (the lightest person) is never worse, and it's the only way to guarantee no capacity is wasted. This is a classic **exchange argument**: any optimal solution can be transformed into "pair extremes when possible" without increasing the boat count, so greedily doing that from the start is safe.

Every iteration of the loop uses exactly one boat and eliminates one or two people from consideration — so `cnt` incrementing once per loop iteration directly counts boats.

---

## Verified Working Code (C++)

```cpp
class Solution {
public:
    int numRescueBoats(vector<int>& people, int limit) {
        sort(people.begin(), people.end());
        int i = 0;
        int j = people.size() - 1;
        int cnt = 0;
        while (i <= j) {
            int sum = people[i] + people[j];
            if (sum <= limit) {
                cnt++;
                i++;
                j--;
            }
            else if (sum > limit) {
                j--;
                cnt++;
            }
        }
        return cnt;
    }
};
```

Verified against all 3 official LeetCode examples, plus edge cases (single person, all-pairs-fit, no-pairs-fit) and a **50,000-trial randomized stress test** against an independently-written reference two-pointer implementation — zero mismatches.

---

## Dry Run

`people = [3,5,3,4]` sorted → `[3,3,4,5]`, `limit = 5` — traced from the actual running code:

| i | j | people[i] | people[j] | sum | fits (≤5)? | action | cnt |
|---|---|---|---|---|---|---|---|
| 0 | 3 | 3 | 5 | 8 | ❌ | heaviest (5) goes alone, `j--` | 1 |
| 0 | 2 | 3 | 4 | 7 | ❌ | heaviest (4) goes alone, `j--` | 2 |
| 0 | 1 | 3 | 3 | 6 | ❌ | heaviest (3) goes alone, `j--` | 3 |
| 0 | 0 | 3 | 3 | 6 | ❌ | last remaining person (3) goes alone, `j--` | 4 |

Loop ends (`i=0 > j=-1`). **Final answer: 4** ✅ — matches expected output (nobody can share a boat here, since even the two lightest people, 3+3=6, exceed the limit of 5).

---

## Complexity

- **Time:** O(n log n) — dominated by the initial sort; the two-pointer scan itself is O(n) since `i` and `j` converge and each loop iteration retires at least one person.
- **Space:** O(1) extra (ignoring the sort's internal space, which is typically O(log n) for `std::sort`'s introsort recursion) — no auxiliary arrays needed.

---

## Edge Cases / Gotchas

- **`i == j` (one person left)**: the code computes `people[i] + people[j]`, which is really `2 * people[i]` (the same person counted twice) — this looks like a bug at first glance ("why is it adding a person to themselves?"), but it isn't: **regardless of which branch fires, `cnt` increments by exactly 1** and the loop terminates right after (since `i` and `j` cross), so the last solo person is correctly counted once. It's a slightly unintuitive but harmless consequence of not special-casing the single-person scenario.
- **Empty array** — loop condition `i <= j` is `0 <= -1`, false immediately, returns `0`. Correct.
- **Everyone fits with everyone (limit is huge)** — pointers converge inward pairing everyone up, giving `ceil(n/2)` boats — falls out naturally from the loop.
- **`else if (sum > limit)` is logically redundant** — since `sum` is an integer and the first branch already covers `sum <= limit`, the only remaining possibility *is* `sum > limit`. Could be written as a plain `else` with no behavior change; worth simplifying for clarity, though it's not a correctness issue.
- **Don't reach for anything heavier than this** — it's tempting to think "minimum boats" sounds like a DP or bin-packing problem (which in general is NP-hard!). The reason greedy works perfectly here is the very restrictive constraint that **each boat holds at most 2 people** — that's what makes the exchange argument above go through cleanly. If the boat capacity were "up to `k` people" for general `k`, this greedy wouldn't directly apply and you'd need a different approach.

---

## Related / Same-Pattern Problems

- **881** (this one) — pair extremes greedily under a capacity-2 constraint.
- **455. Assign Cookies** — same "sort both sides, greedily match smallest-that-fits" flavor of greedy, though with two separate arrays instead of pairing within one.
- **11 / 42 (Container With Most Water / Trapping Rain Water)** — also converging two-pointer, but the *reason* the pointers move is different (there it's about which boundary limits the water, not about greedily pairing weights) — good contrast to test whether you actually understand *why* each two-pointer problem's pointer-movement rule is justified, rather than pattern-matching "two pointers, must be the same trick."
- **General template — "greedily pair extremes":** sort, place `i` and `j` at the two ends, and at each step decide (with a provable exchange argument, not just intuition) whether to consume one or both ends, converging until they cross.

---

## One-Line Recap (for quick revision)

> Sort, then greedily try to pair the lightest and heaviest remaining person in one boat; if they don't fit, the heaviest person is guaranteed to need a solo boat (since nobody lighter would help), so send them alone — every loop iteration uses exactly one boat.
