# Insert Interval — LeetCode 57

**Difficulty:** Medium  
**Pattern:** Intervals + Greedy + Two Pointers  
**Time Complexity:** `O(n)`  
**Auxiliary Space:** `O(1)` excluding output storage

## 1. Problem Statement

You are given a sorted list of non-overlapping intervals `intervals`, where `intervals[i] = [start, end]`, and a new interval `newInterval = [start, end]`.

Insert the new interval into the list and merge any overlapping intervals. Return the resulting list of non-overlapping intervals.

**Important:** The input intervals are already sorted by start time and do not overlap each other.

### Examples

```text
Input:
intervals = [[1,3],[6,9]]
newInterval = [2,5]

Output:
[[1,5],[6,9]]
```

```text
Input:
intervals = [[1,2],[3,5],[6,7],[8,10],[12,16]]
newInterval = [4,8]

Output:
[[1,2],[3,10],[12,16]]
```

## 2. Core Intuition

Unlike Merge Intervals (LC 56), we do **not need to sort the intervals** because the input is already sorted and non-overlapping.

We divide the input into three portions:

1. **Left portion:** Intervals that end strictly before the new interval starts.
2. **Overlapping portion:** Intervals that overlap the new interval; expand its boundaries to merge them.
3. **Right portion:** Remaining intervals that start after the merged interval ends.

### Key overlap conditions

Two intervals `[a,b]` and `[c,d]` overlap or touch when:

```cpp
b >= c && a <= d
```

For this problem, because existing intervals are sorted and non-overlapping, we can process the three portions without checking both conditions explicitly.

## 3. Algorithm — Three Phases

### Phase 1: Add intervals before the new interval

```cpp
while (i < n && intervals[i][1] < start) {
    result.push_back(intervals[i]);
    i++;
}
```

If an interval ends before `start`, it cannot overlap the new interval. Add it directly to the result.

Notice the strict comparison `<`. An interval ending exactly at `start` is treated as overlapping.

### Phase 2: Merge all overlapping intervals

```cpp
while (i < n && intervals[i][0] <= end) {
    start = min(start, intervals[i][0]);
    end = max(end, intervals[i][1]);
    i++;
}
result.push_back({start, end});
```

As long as the next interval begins at or before the current `end`, merge it into the new interval.

- `min()` updates the left boundary.
- `max()` updates the right boundary.
- `i++` moves to the next interval.

**Important:** The `end` boundary can grow after every merge, so the loop must check against the updated `end`.

After the loop, push the merged interval exactly once.

### Phase 3: Add all remaining intervals

```cpp
while (i < n) {
    result.push_back(intervals[i]);
    i++;
}
```

The remaining intervals occur after the merged interval and can be copied directly.

## 4. Complete C++ Solution

```cpp
class Solution {
public:
    vector<vector<int>> insert(
        vector<vector<int>>& intervals,
        vector<int>& newInterval
    ) {
        int n = intervals.size();
        int start = newInterval[0];
        int end = newInterval[1];

        vector<vector<int>> result;
        int i = 0;

        // Phase 1: Add intervals before the new interval
        while (i < n && intervals[i][1] < start) {
            result.push_back(intervals[i]);
            i++;
        }

        // Phase 2: Merge overlapping intervals
        while (i < n && intervals[i][0] <= end) {
            start = min(start, intervals[i][0]);
            end = max(end, intervals[i][1]);
            i++;
        }

        result.push_back({start, end});

        // Phase 3: Add the remaining intervals
        while (i < n) {
            result.push_back(intervals[i]);
            i++;
        }

        return result;
    }
};
```

## 5. Detailed Dry Run

Input:

```text
intervals = [[1,2],[3,5],[6,7],[8,10],[12,16]]
newInterval = [4,8]
```

Initial state:

```text
start = 4
end   = 8
i     = 0
result = []
```

### Phase 1 — Left portion

Check `[1,2]`:

`2 < 4` → true.

Add `[1,2]` to `result` and move `i` to `1`.

Check `[3,5]`:

`5 < 4` → false.

Stop Phase 1.

```text
result = [[1,2]]
```

### Phase 2 — Overlapping portion

Check `[3,5]`:

`3 <= 8` → true.

```text
start = min(4,3) = 3
end   = max(8,5) = 8
```

Check `[6,7]`:

`6 <= 8` → true. The merged interval remains `[3,8]`.

Check `[8,10]`:

`8 <= 8` → true.

```text
start = min(3,8) = 3
end   = max(8,10) = 10
```

Now check `[12,16]`:

`12 <= 10` → false.

Stop Phase 2 and append `[3,10]`.

```text
result = [[1,2],[3,10]]
```

### Phase 3 — Remaining intervals

Append `[12,16]`.

Final output:

```text
[[1,2],[3,10],[12,16]]
```

## 6. Edge Cases

| Input intervals | New interval | Output |
|---|---|---|
| `[]` | `[2,5]` | `[[2,5]]` |
| `[[1,3]]` | `[4,6]` | `[[1,3],[4,6]]` |
| `[[4,7]]` | `[1,2]` | `[[1,2],[4,7]]` |
| `[[1,5]]` | `[2,3]` | `[[1,5]]` |
| `[[1,2],[6,9]]` | `[2,6]` | `[[1,9]]` |
| `[[1,5],[8,10]]` | `[3,7]` | `[[1,7],[8,10]]` |

The final row is especially important: merging `[1,5]` expands `end` to `7`, allowing the algorithm to detect and merge any subsequent interval that now overlaps.

## 7. Complexity Analysis

**Time Complexity: `O(n)`**

Every existing interval is visited at most once across the three loops. No sorting is needed.

**Auxiliary Space Complexity: `O(1)`** for the algorithm's working variables, excluding the output.

**Output Space: `O(n + 1)`** in the worst case.

## 8. Common Mistakes

1. **Sorting the input unnecessarily:** The input is already sorted and non-overlapping.
2. **Using `<= start` in Phase 1:** This can incorrectly exclude an interval whose endpoint equals `start` under the problem's overlap convention.
3. **Not updating `end`:** A merge may extend the interval enough to overlap additional intervals.
4. **Pushing the merged interval repeatedly:** Append it once, after Phase 2.
5. **Forgetting the remaining intervals:** Phase 3 preserves all intervals after the merged range.
6. **Confusing LC 56 and LC 57:** LC 56 merges arbitrary intervals after sorting; LC 57 inserts one interval into an already sorted, non-overlapping list.

## 9. Interview Follow-up Questions

**Q1. Why is the time complexity `O(n)` instead of `O(n log n)`?**

Because the input is already sorted, so no sorting operation is required. Each interval is processed at most once.

**Q2. Why does Phase 2 use `intervals[i][0] <= end`?**

Because any interval starting at or before the current merged endpoint overlaps or touches the merged interval. The endpoint may increase after each merge.

**Q3. Can the output be built in-place?**

Potentially, yes, but modifying the input requires careful index management. Building a separate result vector keeps the implementation straightforward.

**Q4. What is the difference between this and Merge Intervals?**

- LC 56: Sort all intervals, then merge overlaps.
- LC 57: Use the given sorted order and insert one new interval in linear time.

## 10. Pattern to Remember

**Left → Merge → Right**

- Add everything ending before the new interval starts.
- Merge everything starting before or at the current end.
- Add the merged interval once.
- Copy the remaining intervals.

This is a classic interval-insertion pattern that combines sorted input, boundary comparisons, and a single linear scan.
