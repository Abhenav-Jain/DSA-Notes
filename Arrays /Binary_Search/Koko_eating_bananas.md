
# Koko Eating Bananas — LeetCode 875

## 1. Problem Statement

Given an integer array `piles`, where `piles[i]` represents the bananas in the i-th pile, and an integer `h` representing the available hours, find the **minimum integer eating speed `k`** such that Koko can finish all bananas within `h` hours.

Rules:
- Koko chooses one pile per hour.
- She eats at most `k` bananas from that pile during that hour.
- If the pile has fewer than `k` bananas, she finishes it and cannot eat another pile during that hour.

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

We binary-search the possible eating speeds rather than searching for a value in a sorted array.

- Minimum speed: `1`
- Maximum speed: `max(piles)`

At speed `max(piles)`, Koko can finish each pile in one hour.

### Monotonic Property

If speed `k` is sufficient, every greater speed is also sufficient.

If speed `k` is insufficient, every smaller speed is also insufficient.

Therefore, we can binary-search for the **minimum feasible speed**.

## 4. Feasibility Function — `check()`

The function determines whether speed `k` can finish all piles within `h` hours.

For a pile containing `el` bananas:

- If `el <= k`, it takes one hour.
- Otherwise, it takes `ceil(el / k)` hours.

### Mathematical Formula

The number of hours required for one pile is:

$$
\text{Hours}=\left\lceil\frac{el}{k}\right\rceil
$$

Using integer arithmetic in C++:

$$
\left\lceil\frac{el}{k}\right\rceil
=\frac{el+k-1}{k}
$$

C++ integer division truncates the fractional part, so the following expression calculates ceiling division for positive integers:

```cpp
(el + k - 1) / k
```

**Example:** If `el = 10` and `k = 3`:

$$
\left\lceil\frac{10}{3}\right\rceil=4
$$

Koko eats 3, 3, 3, and then 1 banana, requiring four hours.

The total required hours are compared with `h`:

- `count <= h` → feasible, return `true`.
- `count > h` → infeasible, return `false`.

### Why `long long count`?

The total hours are accumulated across all piles. Using `long long` is a safe choice for the sum.

## 5. Dry Run

Input:

```cpp
piles = {3, 6, 7, 11};
h = 8;
```

Initial search range:

```text
low = 1
high = 11
```

| low | high | mid | Required Hours | Decision |
|---:|---:|---:|---|---|
| 1 | 11 | 6 | 1 + 1 + 2 + 2 = 6 | Feasible; search smaller |
| 1 | 5 | 3 | 1 + 2 + 3 + 4 = 10 | Infeasible; search larger |
| 4 | 5 | 4 | 1 + 2 + 2 + 3 = 8 | Feasible; search smaller |

After the last check, `high = 3` and `low = 4`. The loop ends because `low > high`.

**Answer: `4`**

At speed 4, Koko requires exactly 8 hours. Speed 3 requires 10 hours, so it is too slow.

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

The current speed might be the minimum, so save it and search smaller speeds.

```cpp
ans = min(ans, mid);
high = mid - 1;
```

### If the speed is infeasible

The speed is too low, so search larger speeds.

```cpp
low = mid + 1;
```

This implementation uses `while (low <= high)` and stores the best feasible answer in `ans`.

**Alternative:** With `while (low < high)`, use `high = mid` when feasible and `low = mid + 1` otherwise. When both boundaries converge, `low` is the answer.

## 7. Complexity Analysis

Let:
- `n` = number of piles
- `M` = maximum pile size

Each feasibility check examines all piles in `O(n)` time. Binary search performs `O(log M)` checks.

- **Time Complexity:** `O(n log M)`
- **Auxiliary Space:** `O(1)`

## 8. Common Mistakes

1. **Searching from `0`:** Eating speed must be positive, so use `low = 1`.
2. **Using `high = h`:** The upper bound should be `max(piles)`, not the available hours.
3. **Finding any feasible speed:** Continue searching smaller speeds after a feasible result.
4. **Using floor division:** A partially unfinished pile still requires a full hour.
5. **Simulating each banana individually:** Calculate required hours mathematically.
6. **Mixing binary-search templates:** The `while (low <= high)` and `while (low < high)` versions require consistent boundary updates.
7. **Ignoring arithmetic overflow:** `long long count` protects the accumulated sum, but `(el + k - 1)` is evaluated before being added to `count`. For potentially larger bounds, promote the arithmetic or use `el / k + (el % k != 0)`.

## 9. Interview Follow-up Questions

**Q1. Why use Binary Search on Answer?**

The answer lies in a bounded integer range, and feasibility is monotonic with respect to eating speed.

**Q2. Why does a larger speed never require more hours?**

Increasing the speed cannot increase the number of hours needed for any pile.

**Q3. Why use ceiling division?**

A partially consumed final batch still takes one full hour.

**Q4. Why is `max(piles)` a valid upper bound?**

At that speed, each pile can be completed in one hour.

**Q5. What is the role of `check()`?**

It separates the feasibility test from the binary-search logic, making the solution easier to understand and reuse.

**Q6. Why search left after a feasible result?**

A smaller speed might also work. We search left to find the minimum feasible speed.

## 10. Pattern Recognition

Use **Binary Search on Answer** when:

- The answer is an integer in a known range.
- You can write a function to check whether a candidate answer is feasible.
- Feasibility changes monotonically with the candidate.
- You need the minimum feasible or maximum feasible value.

**Pattern:** Define the search range → build the feasibility function → use monotonicity → binary-search the minimum feasible answer.

## 11. Final Revision Summary

- **Problem:** Minimum eating speed.
- **Pattern:** Binary Search on Answer.
- **Search Range:** `[1, max(piles)]`.
- **Feasibility:** Sum the ceiling-divided hours for all piles.
- **If feasible:** Save the answer and search left.
- **If infeasible:** Search right.
- **Time:** `O(n log M)`.
- **Auxiliary Space:** `O(1)`.
