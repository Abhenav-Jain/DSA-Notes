# Insert Interval — LeetCode 57

## 1. Problem Statement

You are given a collection of non-overlapping intervals sorted in ascending order by their start times.

You are also given a new interval `newInterval`.

Insert the new interval into the existing intervals and merge overlapping intervals if necessary.

Return the resulting list of non-overlapping intervals.

**Important constraints:**
- Existing intervals are sorted.
- Existing intervals do not overlap.
- The final result must also be sorted and non-overlapping.
- Modify the result without requiring another sorting operation.

### Example 1

```text
Input:
intervals = [[1,3],[6,9]]
newInterval = [2,5]

Output:
[[1,5],[6,9]]
```

### Example 2

```text
Input:
intervals = [[1,2],[3,5],[6,7],[8,10],[12,16]]
newInterval = [4,8]

Output:
[[1,2],[3,10],[12,16]]
```

---

## 2. Pattern Identification

**Pattern: Greedy + Intervals + Three-Phase Traversal**

The key observation is that the given intervals are already sorted and non-overlapping.

Therefore, we do not need to sort them again.

We can divide the complete problem into three phases:

1. Before — intervals completely before `newInterval`.
2. Overlap — intervals that overlap with `newInterval`.
3. After — intervals completely after the merged interval.

This structure makes the solution possible in a single traversal.

---

## 3. Optimized Approach — Three Phases

### Initialization

```cpp
int n = intervals.size();
vector<vector<int>> result;
int i = 0;
```

We maintain:
- `i`: Current interval index.
- `result`: Final merged interval list.
- `newInterval`: Continuously updated merged interval.

### Phase 1 — Add intervals before newInterval

```cpp
while(i < n && intervals[i][1] < newInterval[0]) {
    result.push_back(intervals[i]);
    i++;
}
```

**Logic:**

If the ending point of the current interval is smaller than the starting point of `newInterval`, the current interval is completely before it.

Therefore, it cannot overlap and can be directly inserted into the result.

Example:

```text
intervals = [[1,2],[6,9]]
newInterval = [3,5]

[1,2] is completely before [3,5].
```

Result:

```text
[[1,2]]
```

### Phase 2 — Merge overlapping intervals

```cpp
while(i < n && newInterval[1] >= intervals[i][0]) {
    newInterval[0] = min(newInterval[0], intervals[i][0]);
    newInterval[1] = max(newInterval[1], intervals[i][1]);
    i++;
}
```

**Logic:**

After Phase 1, we know that:

```text
intervals[i][1] >= newInterval[0]
```

The current interval overlaps with `newInterval` if:

```text
newInterval[1] >= intervals[i][0]
```

Both conditions together establish overlap.

Once an overlap is found:

- Update the starting point using `min()`.
- Update the ending point using `max()`.
- Move to the next interval.

**Why update newInterval repeatedly?**

Because merging can expand its range, which may cause it to overlap with additional intervals.

Example:

```text
newInterval = [4,8]

Existing intervals:
[3,5]
[6,7]
[8,10]
```

Merging:

```text
[4,8] + [3,5]  = [3,8]
[3,8] + [6,7]  = [3,8]
[3,8] + [8,10] = [3,10]
```

Final merged interval:

```text
[3,10]
```

### Phase 3 — Insert merged interval and add remaining intervals

```cpp
result.push_back(newInterval);

while(i < n) {
    result.push_back(intervals[i]);
    i++;
}
```

**Logic:**

After processing all overlapping intervals:

1. Insert the final merged interval.
2. Add all remaining intervals directly.

Why can we add them directly?

Because the original intervals are already sorted and non-overlapping, and all remaining intervals begin after the merged interval ends.

---

## 4. Complete C++ Solution

```cpp
class Solution {
public:
    vector<vector<int>> insert(vector<vector<int>>& intervals, vector<int>& newInterval) {

        int n = intervals.size();
        vector<vector<int>> result;

        // Phase 1: Add intervals before newInterval
        int i = 0;

        while(i < n && intervals[i][1] < newInterval[0]) {
            result.push_back(intervals[i]);
            i++;
        }

        // Phase 2: Merge overlapping intervals
        while(i < n && newInterval[1] >= intervals[i][0]) {
            newInterval[0] = min(newInterval[0], intervals[i][0]);
            newInterval[1] = max(newInterval[1], intervals[i][1]);
            i++;
        }

        // Phase 3: Add the merged interval
        result.push_back(newInterval);

        // Add all remaining intervals
        while(i < n) {
            result.push_back(intervals[i]);
            i++;
        }

        return result;
    }
};
```

---

## 5. Detailed Dry Run

Input:

```text
intervals = [[1,2],[3,5],[6,7],[8,10],[12,16]]
newInterval = [4,8]
```

Initially:

```text
i = 0
result = []
newInterval = [4,8]
```

### Phase 1 — Before

Check `[1,2]`:

```text
2 < 4  → TRUE
```

Push it into result.

```text
result = [[1,2]]
i = 1
```

### Phase 2 — Overlap

Check `[3,5]`:

```text
8 >= 3 → TRUE
```

Merge:

```text
start = min(4,3) = 3
end   = max(8,5) = 8
```

Updated interval:

```text
[3,8]
```

Check `[6,7]`:

```text
8 >= 6 → TRUE
```

Updated interval remains:

```text
[3,8]
```

Check `[8,10]`:

```text
8 >= 8 → TRUE
```

Merge:

```text
start = min(3,8) = 3
end   = max(8,10) = 10
```

Updated interval:

```text
[3,10]
```

Now:

```text
i = 4
```

Check `[12,16]`:

```text
10 >= 12 → FALSE
```

Overlap phase ends.

Push merged interval:

```text
result = [[1,2],[3,10]]
```

### Phase 3 — After

Remaining interval:

```text
[12,16]
```

Push it directly.

Final output:

```text
[[1,2],[3,10],[12,16]]
```

---

## 6. Correctness Explanation

The algorithm is correct because the sorted, non-overlapping input allows us to partition intervals into three groups.

**Phase 1:** Every interval ending before `newInterval` begins is guaranteed not to overlap. These intervals can be safely added.

**Phase 2:** Every interval whose start is at or before the current merged interval's end overlaps with it. Repeatedly taking the minimum start and maximum end produces the complete merged interval.

**Phase 3:** Every remaining interval begins after the merged interval ends. Since the original intervals are sorted and non-overlapping, these intervals can be appended without further merging.

Thus, the resulting list is sorted and contains no overlapping intervals.

---

## 7. Complexity Analysis

Let N be the number of existing intervals.

| Complexity | Value |
|---|---|
| Time Complexity | O(N) |
| Auxiliary Space | O(1), excluding output |
| Output Space | O(N) |

**Time Complexity: O(N)**

All three phases use a single index `i`, which only moves forward.

Every existing interval is processed at most once.

No sorting is required.

**Space Complexity: O(1) auxiliary space**

Apart from the output vector, only a constant number of variables are used.

The result vector requires O(N) output space.

---

## 8. Important Edge Cases

| Input | New Interval | Output |
|---|---|---|
| `[]` | `[2,5]` | `[[2,5]]` |
| `[[1,3]]` | `[4,6]` | `[[1,3],[4,6]]` |
| `[[4,6]]` | `[1,2]` | `[[1,2],[4,6]]` |
| `[[1,5]]` | `[2,3]` | `[[1,5]]` |
| `[[1,2],[5,7]]` | `[3,4]` | `[[1,2],[3,4],[5,7]]` |
| `[[1,2],[6,9]]` | `[2,6]` | `[[1,9]]` |

**Important:** LeetCode 57 treats intervals sharing an endpoint as overlapping. That's why we use strict `<` in Phase 1 and `>=` in Phase 2.

---

## 9. Common Mistakes

### Mistake 1 — Sorting unnecessarily

The input is already sorted. Sorting again would increase the time complexity to O(N log N).

### Mistake 2 — Incorrect before condition

Incorrect:

```cpp
intervals[i][1] <= newInterval[0]
```

This would classify intervals sharing an endpoint as separate, whereas this problem treats shared endpoints as overlapping.

Correct:

```cpp
intervals[i][1] < newInterval[0]
```

### Mistake 3 — Checking only one overlap condition

Overlap requires both:

```text
intervals[i][0] <= newInterval[1]
intervals[i][1] >= newInterval[0]
```

The three-phase algorithm handles the second condition implicitly because Phase 1 has already removed intervals ending before `newInterval` starts.

### Mistake 4 — Not updating the merged interval

After merging one interval, the merged range may expand. Always update both boundaries using `min()` and `max()`.

### Mistake 5 — Forgetting empty input

When `intervals` is empty, Phase 1 and Phase 2 are skipped. The algorithm must still push `newInterval` into the result.

---

## 10. Interview Follow-up Questions

**Q1. Why don't we sort the intervals again?**

Because the input is already sorted and non-overlapping.

**Q2. Why are three separate loops useful?**

They clearly separate intervals before, overlapping with, and after the new interval.

**Q3. Why do we update newInterval instead of creating another merged interval?**

The new interval itself represents the continuously expanding merged range.

**Q4. Can this be solved in one loop?**

Yes. The same three phases can be implemented using conditional logic inside one traversal, but three loops make the logic easier to understand.

**Q5. What is the time complexity?**

O(N), because every existing interval is processed at most once.

---

## 11. Pattern Recognition

This problem teaches an important interval-processing technique.

When the input is already sorted and non-overlapping, look for opportunities to process intervals in a single pass.

Related problems:

| Problem | LeetCode |
|---|---:|
| Merge Intervals | 56 |
| Insert Interval | 57 |
| Non-overlapping Intervals | 435 |
| Minimum Number of Arrows to Burst Balloons | 452 |

Remember the difference:

- **Merge Intervals:** Sort first, then merge.
- **Insert Interval:** Already sorted, so use three-phase traversal.
- **Non-overlapping Intervals:** Choose intervals strategically to minimize removals.

---

## 12. Final Revision Cheat Sheet

```text
PROBLEM: Insert Interval (LC 57)

PATTERN:
Greedy + Intervals + Three-Phase Traversal

INPUT:
Sorted, non-overlapping intervals.

PHASE 1 — BEFORE:
intervals[i][1] < newInterval[0]
Push directly.

PHASE 2 — OVERLAP:
newInterval[1] >= intervals[i][0]

Merge:
start = min(start1, start2)
end   = max(end1, end2)

PHASE 3 — AFTER:
Push merged interval.
Push all remaining intervals.

TIME: O(N)
AUXILIARY SPACE: O(1), excluding output.

KEY INSIGHT:
Exploit the already sorted input.
No additional sorting is required.
```

**Core takeaway:** Insert Interval is a classic example of how sorted input can eliminate unnecessary sorting and allow a clean O(N) greedy traversal.
