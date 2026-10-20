
# LC 1011 — Capacity To Ship Packages Within D Days

**Pattern:** Binary Search on Answer + Greedy  
**Difficulty:** Medium  
**Status:** Accepted — 90/90 test cases passed

## 1. Problem Summary

Given:
- `weights[i]`: weight of the i-th package.
- `days`: maximum number of days available.

Packages must be shipped in the given order. We cannot reorder packages, and each day's total load must not exceed the ship's capacity.

Find the **minimum ship capacity** required to ship all packages within `days`.

## 2. Core Intuition

Suppose the ship's capacity is `k`.

We can greedily load as many consecutive packages as possible on each day without exceeding `k`.

Now observe:

- If capacity `k` is sufficient, any larger capacity will also be sufficient.
- If capacity `k` is insufficient, any smaller capacity will also be insufficient.

This monotonic property allows us to apply **Binary Search on Answer**.

## 3. Search Space

### Minimum possible capacity

```cpp
low = *max_element(weights.begin(), weights.end());
```

The ship must carry the heaviest package, so capacity cannot be smaller than the maximum individual weight.

### Maximum possible capacity

```cpp
high = accumulate(weights.begin(), weights.end(), 0);
```

If the capacity equals the sum of all weights, every package can be shipped in one day.

Therefore:

`low = maximum individual weight`

`high = sum of all weights`

## 4. Greedy Check Function

For a candidate capacity `k`:

1. Start with an empty day.
2. Add the next package if `sum + weights[i] <= k`.
3. Otherwise, start a new day and process that package there.
4. Count the total days required.
5. If required days `<= days`, capacity is feasible.

### My Accepted Check Function

```cpp
bool check(vector<int>& weights, int days, int k) {
    int count = 0;
    int n = weights.size();
    int sum = 0;
    int i = 0;

    while (i < n) {
        while (i < n && sum + weights[i] <= k) {
            sum += weights[i];
            i++;
        }

        sum = 0;
        count += 1;
    }

    if (count <= days) {
        return true;
    }

    return false;
}
```

**Important:** The inner loop stops before exceeding capacity. The outer loop then counts one day and resets the load. Each package is processed exactly once.

## 5. Binary Search

```cpp
int shipWithinDays(vector<int>& weights, int days) {
    int low = *max_element(weights.begin(), weights.end());
    int high = accumulate(weights.begin(), weights.end(), 0);
    int ans = high;

    while (low <= high) {
        int mid = low + (high - low) / 2;

        if (check(weights, days, mid)) {
            ans = min(ans, mid);
            high = mid - 1;
        } else {
            low = mid + 1;
        }
    }

    return ans;
}
```

### Why update the boundaries this way?

**When `check(mid)` is true:**
- Capacity `mid` is sufficient.
- We want the minimum sufficient capacity.
- Store `mid` and search for a smaller answer: `high = mid - 1`.

**When `check(mid)` is false:**
- Capacity `mid` is insufficient.
- We need a larger capacity: `low = mid + 1`.

## 6. Dry Run

```text
weights = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
days = 5

low = 10
high = 55
```

Try capacity `k = 15`:

| Day | Packages | Total weight |
|---|---|---:|
| 1 | 1, 2, 3, 4, 5 | 15 |
| 2 | 6, 7 | 13 |
| 3 | 8 | 8 |
| 4 | 9 | 9 |
| 5 | 10 | 10 |

Required days = 5.

Since `5 <= days`, capacity `15` is feasible. Binary search continues on smaller capacities to find the minimum.

**Answer = 15**

## 7. Complexity Analysis

Let:
- `n` = number of packages.
- `S` = sum of all package weights.
- `W` = maximum package weight.

Each `check()` takes `O(n)` time.

Binary search examines a range of size approximately `S - W + 1`.

- **Time Complexity:** `O(n log(S - W + 1))`, commonly written as `O(n log S)`.
- **Auxiliary Space:** `O(1)`.

## 8. Common Mistakes

1. **Wrong lower bound:** Using `1` instead of the maximum package weight.
2. **Wrong upper bound:** Using the maximum package weight instead of the sum.
3. **Incorrect capacity condition:** Checking `sum <= k` before adding a package instead of checking `sum + weights[i] <= k`.
4. **Changing package order:** Packages must be shipped in their original order.
5. **Wrong binary-search direction:** A feasible capacity means search left; an infeasible capacity means search right.
6. **Counting days incorrectly:** Every new batch of packages must count as one day.
7. **Forgetting the minimum requirement:** We need the smallest feasible capacity, not just any feasible capacity.

## 9. Interview Follow-Up Questions

**Q1. Why is Binary Search applicable?**

Because feasibility is monotonic with capacity. Once a capacity works, every larger capacity also works.

**Q2. Why is the lower bound the maximum package weight?**

A package heavier than the ship's capacity cannot be shipped, regardless of the number of days.

**Q3. Why is the upper bound the sum of all weights?**

With this capacity, all packages can be shipped in a single day.

**Q4. Why is greedy valid inside `check()`?**

For a fixed capacity, loading as many consecutive packages as possible on each day minimizes the number of days needed.

**Q5. What does `check()` return?**

Whether the candidate capacity can ship all packages within the allowed number of days.

**Q6. What is the difference between Binary Search on a sorted array and Binary Search on Answer?**

- Sorted-array search finds a target in ordered data.
- Binary Search on Answer searches a monotonic range of candidate answers using a feasibility check.

## 10. Reusable Template

```cpp
int low = minimumPossibleAnswer;
int high = maximumPossibleAnswer;
int ans = high;

while (low <= high) {
    int mid = low + (high - low) / 2;

    if (check(mid)) {
        ans = mid;
        high = mid - 1;
    } else {
        low = mid + 1;
    }
}

return ans;
```

Use this template when the problem asks for the **minimum feasible answer** and feasibility is monotonic.

## 11. Related Problems

1. LC 875 — Koko Eating Bananas
2. LC 1482 — Minimum Number of Days to Make m Bouquets
3. Allocate Books
4. Aggressive Cows
5. Split Array Largest Sum — LC 410

## Final Revision

**Search space:** `[max(weights), sum(weights)]`

**Feasibility:** Can we ship within `days` using capacity `mid`?

**Feasible:** Save answer, search left.

**Not feasible:** Search right.

**Pattern:** Binary Search on Answer + Greedy.
