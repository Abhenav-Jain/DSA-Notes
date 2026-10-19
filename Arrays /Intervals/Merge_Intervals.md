# Merge Intervals — LeetCode 56

**Difficulty:** Medium  
**Pattern:** Sorting + Greedy  
**Time Complexity:** `O(n log n)`  
**Auxiliary Space:** `O(1)` excluding sorting implementation details and output storage

## 1. Problem Statement

Given an array of intervals where `intervals[i] = [start, end]`, merge all overlapping intervals and return an array of non-overlapping intervals that cover all the original intervals.

### Example

```text
Input:  [[1,3], [2,6], [8,10], [15,18]]
Output: [[1,6], [8,10], [15,18]]
```

Explanation:
- `[1,3]` and `[2,6]` overlap, so they merge into `[1,6]`.
- `[8,10]` and `[15,18]` do not overlap with the previous interval, so they remain separate.

## 2. Core Intuition

The main challenge is identifying which intervals overlap.

**Key observation:** If intervals are sorted by their starting points, we only need to compare the current interval with the last interval being merged.

For two intervals:

`[start, end]` and `[currentStart, currentEnd]`

They overlap when:

```cpp
currentStart <= end
```

If they overlap, update:

```cpp
end = max(end, currentEnd);
```

Otherwise, save the current merged interval and start processing a new one.

**Why sorting matters:** Without sorting, an interval that should merge with an earlier interval could appear much later, making the process harder to manage.

## 3. Algorithm — Step by Step

### Step 1: Sort the intervals

```cpp
sort(intervals.begin(), intervals.end());
```

For vectors of pairs, lexicographical sorting primarily sorts by start, and then by end when starts are equal.

### Step 2: Initialize the current interval

```cpp
int start = intervals[0][0];
int end = intervals[0][1];
```

Treat the first interval as the initial interval to merge.

### Step 3: Process remaining intervals

```cpp
for (int i = 1; i < n; i++) {
    if (intervals[i][0] <= end) {
        end = max(end, intervals[i][1]);
    } else {
        ans.push_back({start, end});
        start = intervals[i][0];
        end = intervals[i][1];
    }
}
```

**Case A — Overlap**

If the next interval starts before or exactly when the current interval ends, merge them by extending `end` when necessary.

**Case B — No overlap**

Save the completed merged interval in `ans`, then initialize `start` and `end` using the new interval.

### Step 4: Add the final interval

```cpp
ans.push_back({start, end});
```

The final merged interval is still stored in `start` and `end` after the loop. This line ensures it is included in the answer.

## 4. Complete C++ Solution

```cpp
class Solution {
public:
    vector<vector<int>> merge(vector<vector<int>>& intervals) {
        int n = intervals.size();

        if (intervals.empty()) return {};

        sort(intervals.begin(), intervals.end());

        vector<vector<int>> ans;

        int start = intervals[0][0];
        int end = intervals[0][1];

        for (int i = 1; i < n; i++) {
            if (intervals[i][0] <= end) {
                end = max(end, intervals[i][1]);
            } else {
                ans.push_back({start, end});

                start = intervals[i][0];
                end = intervals[i][1];
            }
        }

        ans.push_back({start, end});

        return ans;
    }
};
```

*Note:* The empty-input check is a defensive addition. The original algorithm is correct under the usual LeetCode 56 constraints.

## 5. Detailed Dry Run

Input:

```text
intervals = [[1,3], [2,6], [8,10], [15,18]]
```

After sorting:

```text
[[1,3], [2,6], [8,10], [15,18]]
```

Initial state:

```text
start = 1
end   = 3
ans   = []
```

| Current interval | Condition | Action | `ans` |
|---|---|---|---|
| `[2,6]` | `2 <= 3` | Merge; `end = max(3,6) = 6` | `[]` |
| `[8,10]` | `8 > 6` | Save `[1,6]`; start new interval | `[[1,6]]` |
| `[15,18]` | `15 > 10` | Save `[8,10]`; start new interval | `[[1,6],[8,10]]` |

After the loop, append `[15,18]`.

Final output:

```text
[[1,6], [8,10], [15,18]]
```

## 6. Important Edge Cases

### Case 1: All intervals overlap

```text
Input:  [[1,4], [2,5], [3,6]]
Output: [[1,6]]
```

Every interval merges into one.

### Case 2: No intervals overlap

```text
Input:  [[1,2], [4,5], [7,8]]
Output: [[1,2], [4,5], [7,8]]
```

Every interval gets saved separately.

### Case 3: One interval is contained inside another

```text
Input:  [[1,10], [2,3], [4,5]]
Output: [[1,10]]
```

The `max()` operation prevents the ending boundary from shrinking.

### Case 4: Touching boundaries

```text
Input:  [[1,4], [4,7]]
Output: [[1,7]]
```

Because the condition uses `<=`, intervals sharing an endpoint are merged, as expected in LeetCode 56.

### Case 5: Empty input

```text
Input:  []
Output: []
```

The defensive empty-input check avoids accessing `intervals[0]`.

## 7. Complexity Analysis

**Time Complexity: `O(n log n)`**

- Sorting: `O(n log n)`
- Single traversal: `O(n)`
- Total: `O(n log n)`

**Auxiliary Space Complexity:** The merging logic itself uses `O(1)` working variables, excluding the output. Sorting may use implementation-dependent auxiliary space.

**Output Space:** `O(n)` in the worst case when no intervals overlap.

## 8. Common Mistakes

1. **Forgetting to sort:** The merge logic relies on intervals being ordered by start.
2. **Using `end = intervals[i][1]`:** This can shrink the ending boundary when an interval is contained inside another. Use `max()`.
3. **Using `<` instead of `<=`:** This changes how touching endpoints are treated.
4. **Forgetting the final push:** The last merged interval would be missing from the result.
5. **Updating `start` and `end` in the overlap case:** This can lose the accumulated merged interval.
6. **Ignoring empty input:** `intervals[0]` is invalid when the vector is empty.

## 9. Interview Follow-up Questions

**Q1. Why do we sort by start time?**

So every subsequent interval starts at or after the previous interval's start, allowing us to process merges in one forward traversal.

**Q2. Why use `max(end, intervals[i][1])`?**

Because a new interval may be fully contained inside the current merged interval. The merged ending boundary must never decrease.

**Q3. Can this be solved in `O(n)` time?**

For arbitrary unsorted intervals, comparison-based sorting generally requires `O(n log n)` time. Once the intervals are sorted, merging itself is linear.

**Q4. Is the input modified?**

Yes. Sorting changes the order of the input intervals. The problem allows this.

## 10. Pattern to Remember

**Sort → Initialize → Merge overlaps → Save completed intervals → Save the final interval**

The essential condition is:

```cpp
if (currentStart <= end)
    end = max(end, currentEnd);
else
    saveCurrentIntervalAndStartNewOne();
```

**When to recognize this pattern:** Whenever a problem asks to merge overlapping ranges, combine time periods, or produce non-overlapping intervals from a collection of ranges.
