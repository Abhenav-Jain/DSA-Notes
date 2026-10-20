# Koko Eating Bananas — LeetCode 875

## 1. Problem Statement

Given an array `piles`, where `piles[i]` represents the bananas in the \(i\)-th pile, and an integer `h` representing the available hours, find the **minimum integer eating speed `k`** such that Koko can finish all bananas within `h` hours.

Rules:
- Koko chooses one pile per hour.
- She eats at most `k` bananas from that pile in that hour.
- If the pile has fewer than `k` bananas, she finishes it and does not eat another pile during that hour.

## 2. My Accepted C++ Solution

```cpp
class Solution {
public:
    bool check(vector<int>& piles, int h, int k) {
        long long count = 0;

        for (int el : piles) {
            if (el <= k) {
                count += 1;
            }
            else {
                count += (el + k - 1) / k;
            }
        }

        if (count > h) {
            return false;
        }

        return true;
    }

    int minEatingSpeed(vector<int>& piles, int h) {
        int low = 1;
        int high = *max_element(piles.begin(), piles.end());
        int ans = high;

        while (low <= high) {
            int mid = low + (high - low) / 2;

            if (check(piles, h, mid)) {
                ans = min(ans, mid);
                high = mid - 1;
            }
            else {
                low = mid + 1;
            }
        }

        return ans;
    }
};
```

## 3. Pattern: Binary Search on Answer

This is not ordinary binary search on a sorted array. We binary-search the possible eating speeds.

- Minimum speed: `1`
- Maximum speed: `max(piles)`

Why is the maximum sufficient? At that speed, Koko can finish every pile in one hour. Under the problem's constraints, a valid answer exists.

### Monotonic property

If speed `k` is sufficient, every speed greater than `k` is also sufficient.

If speed `k` is insufficient, every smaller speed is also insufficient.

Therefore, we can binary-search for the **first feasible speed**.

## 4. Feasibility Function — `check()`

The function determines whether speed `k` can finish all piles within `h` hours.

For each pile containing `el` bananas:

- If `el <= k`, it takes one hour.
- Otherwise, it takes \(\lceil el/k\rceil\) hours.

Mathematically:

\[
\text{hours for a pile}=\left\lceil\frac{el}{k}\right\rceil
\]

Using integer arithmetic:

\[
\left\lceil\frac{el}{k}\right\rceil
=\frac{el+k-1}{k}
\]

In C++, because integer division truncates the fractional part:

```cpp
(el + k - 1) / k
```

For example, if `el = 10` and `k = 3`:

\[
\left\lceil\frac{10}{3}\right\rceil=4
\]

Koko eats 3, 3, 3, and then 1 banana: four hours.

The total required hours are compared with `h`.

- `count <= h` → feasible, return `true`.
- `count > h` → infeasible, return `false`.

## 5. Dry Run

Input:

```cpp
piles = {3, 6, 7, 11};
h = 8;
```

Search range:

```text
low = 1
high = 11
```

| low | high | mid | Required hours | Decision |
|---:|---:|---:|---:|---|
| 1 | 11 | 6 | 1 + 1 + 2 + 2 = 6 | Feasible; search smaller |
| 1 | 5 | 3 | 1 + 2 + 3 + 4 = 10 | Infeasible; search larger |
| 4 | 5 | 4 | 1 + 2 + 2 + 3 = 8 | Feasible; search smaller |
| 4 | 3 | — | — | Stop |

Answer: `4`.

At speed 4, the total required time is exactly 8 hours. Speed 3 needs 10 hours, so it cannot work.

## 6. Binary Search Logic

```cpp
if (check(piles, h, mid)) {
    ans = min(ans, mid);
    high = mid - 1;
}
else {
    low = mid + 1;
}
```

### If the speed is feasible

The current speed might be the minimum, so save it in `ans` and search the smaller speeds.

```cpp
ans = min(ans, mid);
high = mid - 1;
```

### If the speed is infeasible

The speed is too low, so search the larger speeds.

```cpp
low = mid + 1;
```

This finds the first feasible speed.

**Alternative:** A `while (low < high)` implementation can use `high = mid` when feasible and `low = mid + 1` otherwise. In that version, the converged `low` is the answer, so a separate `ans` variable is unnecessary.

## 7. Complexity Analysis

Let:
- \(n\) = number of piles
- \(M\) = maximum pile size

Each feasibility check examines every pile, taking \(O(n)\) time.

Binary search makes \(O(\log M)\) checks.

- **Time complexity:** \(O(n\log M)\)
- **Auxiliary space:** \(O(1)\)

The `count` variable is `long long` to safely accumulate the required hours.

## 8. Common Mistakes

1. **Searching from `0`.** Eating speed must be positive, so `low = 1`.
2. **Using `high = h`.** The upper bound should be `max(piles)`, not the available hours.
3. **Finding any feasible speed instead of the minimum.** On a feasible result, continue searching smaller speeds.
4. **Using floor division instead of ceiling division.** A partially unfinished pile still requires a full hour.
5. **Simulating each banana individually.** Calculate hours mathematically for efficiency.
6. **Moving `high` to `mid - 1` in a `while (low < high)` version without adjusting the rest of the logic.** Keep the binary-search invariant consistent with the loop condition.

## 9. Interview Follow-up Questions

**Q1. Why binary search on the answer?**

Because the answer lies in a bounded integer range, and feasibility is monotonic with respect to speed.

**Q2. Why does a larger speed never require more hours?**

Increasing the eating speed cannot increase the number of hours needed for any pile.

**Q3. Why use ceiling division?**

A partially consumed final batch still takes one full hour.

**Q4. Why is the maximum pile size a valid upper bound?**

At that speed, each pile can be completed in one hour, which is sufficient under the problem's constraints.

**Q5. What is the role of `check()`?**

It separates the feasibility test from the binary-search logic, making the solution easier to understand and reuse.

## 10. Pattern Recognition

Use Binary Search on Answer when:

- The answer is an integer in a known range.
- You can write a function that checks whether a candidate answer is feasible.
- Feasibility changes monotonically as the candidate increases or decreases.
- You need the minimum feasible or maximum feasible value.

**Revision mantra:** Define the search range → build the feasibility function → use monotonicity → binary-search the minimum feasible answer.

## 11. Final Revision Summary

- **Problem:** Minimum eating speed.
- **Pattern:** Binary Search on Answer.
- **Search range:** `[1, max(piles)]`.
- **Feasibility:** Sum the ceiling-divided hours for all piles.
- **If feasible:** Search left.
- **If infeasible:** Search right.
- **Time:** \(O(n\log M)\).
- **Space:** \(O(1)\).
