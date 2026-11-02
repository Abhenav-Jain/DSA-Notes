# Merge Intervals — LeetCode 56

**Pattern:** Greedy + Sorting + Intervals  
**Difficulty:** Medium  
**Core Concept:** Sort intervals by starting point and merge overlapping intervals into one continuous interval.

## 1. Problem Statement

You are given an array of intervals where:

`intervals[i] = [startᵢ, endᵢ]`

Merge all overlapping intervals and return an array of non-overlapping intervals that cover all the intervals in the input.

### Example 1

```cpp
Input:  [[1,3],[2,6],[8,10],[15,18]]
Output: [[1,6],[8,10],[15,18]]
```

Explanation:

- `[1,3]` and `[2,6]` overlap.
- They merge into `[1,6]`.
- `[8,10]` and `[15,18]` do not overlap with the merged interval.

### Example 2

```cpp
Input:  [[1,4],[4,5]]
Output: [[1,5]]
```

Explanation:

Both intervals share the endpoint `4`, so they are merged.

---

## 2. Greedy Intuition

The main challenge is to identify which intervals overlap and combine them correctly.

Imagine these intervals:

```cpp
[[1,3],[2,6],[8,10],[15,18]]
```

Without sorting, intervals may appear in any order, making overlap detection difficult.

Therefore, first sort intervals by their starting point.

After sorting, we process intervals from left to right.

Maintain two variables:

- `start`: Starting point of the current merged interval.
- `end`: Ending point of the current merged interval.

For every new interval, there are two possibilities:

**Case 1: Overlapping intervals**

If:

`end >= intervals[i][0]`

The current interval overlaps with the previous merged interval.

Update:

`end = max(end, intervals[i][1])`

**Case 2: Non-overlapping intervals**

If:

`end < intervals[i][0]`

The current interval does not overlap with the previous merged interval.

Therefore:

1. Push the previous merged interval into the result.
2. Start a new merged interval using the current interval.

After completing the traversal, push the last pending interval into the result.

---

## 3. Why Is Sorting Necessary?

Consider:

```cpp
[[8,10],[1,3],[2,6],[15,18]]
```

The intervals are not arranged according to their starting points.

After sorting:

```cpp
[[1,3],[2,6],[8,10],[15,18]]
```

Now we can process intervals in a predictable order.

**Important observation:**

After sorting by starting point, the starting points are non-decreasing.

Therefore, if the current interval starts after the previous merged interval ends, no future interval can overlap with that previous merged interval.

This makes a single left-to-right traversal sufficient.

---

## 4. Optimized Greedy Approach

### C++ Implementation

```cpp
class Solution {
public:
    vector<vector<int>> merge(vector<vector<int>>& intervals) {

        int n = intervals.size();

        sort(intervals.begin(), intervals.end());

        int start = intervals[0][0];
        int end = intervals[0][1];

        vector<vector<int>> result;

        for(int i = 1; i < n; i++) {

            if(end >= intervals[i][0]) {

                end = max(end, intervals[i][1]);

            }
            else {

                result.push_back({start, end});

                start = intervals[i][0];
                end = intervals[i][1];

            }
        }

        result.push_back({start, end});

        return result;
    }
};
```

---

## 5. Step-by-Step Algorithm

1. Calculate the number of intervals.
2. Sort intervals in ascending order.
3. Initialize `start` and `end` using the first interval.
4. Create an empty result vector.
5. Traverse the remaining intervals.
6. Check whether the current interval overlaps with the current merged interval.

   **If overlapping:**

   - Update `end` using `max(end, intervals[i][1])`.

   **If non-overlapping:**

   - Push `{start, end}` into the result.
   - Update `start` and `end` using the current interval.

7. After the loop, push the final merged interval.
8. Return the result.

---

## 6. Dry Run — Standard Example

Input:

```cpp
intervals = [[1,3],[2,6],[8,10],[15,18]]
```

After sorting:

```cpp
[[1,3],[2,6],[8,10],[15,18]]
```

Initially:

```cpp
start = 1;
end = 3;
result = {};
```

| i | Current interval | Overlap? | start | end | Result |
|---:|---|---|---:|---:|---|
| 1 | `[2,6]` | Yes | 1 | 6 | `{}` |
| 2 | `[8,10]` | No | 8 | 10 | `[[1,6]]` |
| 3 | `[15,18]` | No | 15 | 18 | `[[1,6],[8,10]]` |

After the loop:

```cpp
result.push_back({15,18});
```

Final output:

```cpp
[[1,6],[8,10],[15,18]]
```

---

## 7. Important Case — Nested Intervals

This is one of the most important test cases for understanding the problem.

Input:

```cpp
[[1,10],[2,3],[4,5],[12,15]]
```

Initially:

```cpp
start = 1;
end = 10;
```

Now process the intervals.

**Interval `[2,3]`:**

```cpp
end >= 2
10 >= 2  // true
```

Update:

```cpp
end = max(10,3);
end = 10;
```

**Interval `[4,5]`:**

```cpp
end >= 4
10 >= 4  // true
```

Update:

```cpp
end = max(10,5);
end = 10;
```

**Interval `[12,15]`:**

```cpp
10 >= 12  // false
```

Push `[1,10]` into the result and start a new interval `[12,15]`.

Final output:

```cpp
[[1,10],[12,15]]
```

### Why Is `max()` So Important?

Never write:

```cpp
end = intervals[i][1];
```

Because a nested interval can have a smaller ending point than the current merged interval.

Correct:

```cpp
end = max(end, intervals[i][1]);
```

**Rule:** The ending point of a merged interval must always represent the farthest right endpoint encountered so far.

---

## 8. Overlap Detection — Mathematical Understanding

Suppose we have two intervals:

`A = [a,b]`

`B = [c,d]`

If the intervals are sorted by starting point, then `a <= c`.

They overlap or touch when:

`b >= c`

They do not overlap when:

`b < c`

Therefore, our condition becomes:

```cpp
if(end >= intervals[i][0])
```

### Important: Why `>=` Instead of `>`?

Consider:

```cpp
[[1,4],[4,5]]
```

The intervals share endpoint `4`.

LeetCode considers them overlapping, so they should merge.

Therefore, use:

```cpp
end >= intervals[i][0]
```

If the problem instead required intervals to overlap by a positive length, the boundary condition could be different. Always check the problem's definition.

---

## 9. Why Does This Greedy Work?

After sorting, we process intervals in increasing order of their starting points.

At any moment, we maintain the merged interval covering all overlapping intervals processed so far.

For the next interval:

- If its start is less than or equal to the current merged end, it belongs to the same merged group.
- Otherwise, there is a genuine gap between the current merged group and the next interval.

Because all future intervals have starting points at least as large as the current interval's start, none of them can reconnect to the previous group once such a gap exists.

Therefore, we can safely finalize the previous merged interval and begin a new group.

This is why the greedy algorithm produces the correct non-overlapping intervals.

---

## 10. Complexity Analysis

Let `N` be the number of intervals.

| Operation | Complexity |
|---|---|
| Sorting | O(N log N) |
| Traversal | O(N) |
| Total Time | O(N log N) |
| Auxiliary Space | O(1) excluding sorting stack and output |
| Output Space | O(N) in the worst case |

The sorting operation dominates the overall time complexity.

**Note:** Sorting is performed in-place on the input vector, so the original input order is modified.

---

## 11. Edge Cases

| Input | Output | Reason |
|---|---|---|
| `[[1,4],[2,3]]` | `[[1,4]]` | Nested interval |
| `[[1,4],[4,5]]` | `[[1,5]]` | Shared endpoint |
| `[[1,2],[3,4]]` | `[[1,2],[3,4]]` | No overlap |
| `[[1,10],[2,3],[4,5]]` | `[[1,10]]` | Multiple nested intervals |
| `[[1,5],[2,6],[4,8]]` | `[[1,8]]` | Chain of overlapping intervals |
| `[[1,10]]` | `[[1,10]]` | Single interval |

**Empty input consideration:** LeetCode 56 specifies at least one interval. If implementing this outside LeetCode, add an empty-input check before accessing `intervals[0]`.

---

## 12. Common Mistakes

**1. Forgetting to sort**

Without sorting, the simple overlap condition is not sufficient.

**2. Incorrectly updating the ending point**

Wrong:

```cpp
end = intervals[i][1];
```

Correct:

```cpp
end = max(end, intervals[i][1]);
```

**3. Using the wrong overlap condition**

For this problem, use:

```cpp
end >= intervals[i][0]
```

**4. Forgetting the final push**

The last merged interval remains pending after the loop. Always push it.

**5. Confusing overlapping with adjacent intervals**

`[1,4]` and `[5,8]` are not overlapping under the problem's definition.

But `[1,4]` and `[4,8]` do overlap because they share an endpoint.

---

## 13. Interview Follow-up Questions

**Q1. Why do we sort by starting point?**

To process intervals in increasing order and make overlap detection possible with a single traversal.

**Q2. Why do we use `max()` while merging?**

To preserve the farthest ending point when intervals are nested or overlap partially.

**Q3. Can we solve this without sorting?**

A straightforward one-pass solution using only the current merged interval relies on sorted input. Without sorting, we would need a more complicated approach.

**Q4. Does sorting modify the original input?**

Yes. `sort(intervals.begin(), intervals.end())` modifies the input vector.

**Q5. Can this algorithm merge three or more overlapping intervals?**

Yes. The `end` variable keeps expanding as long as subsequent intervals overlap.

---

## 14. Pattern Recognition — When to Think of Merge Intervals

Look for these clues:

- Input contains ranges represented as `[start, end]`.
- You need to combine overlapping ranges.
- You need to eliminate duplicate or redundant coverage.
- You need to produce non-overlapping intervals.

Think:

**Sort → Maintain Current Interval → Check Overlap → Expand End → Finalize When Gap Appears.**

---

## 15. Connection to Upcoming Interval Problems

Merge Intervals is the foundation for several important problems.

| Problem | LeetCode | Main Concept |
|---|---:|---|
| Merge Intervals | 56 | Merge overlapping ranges |
| Insert Interval | 57 | Insert and merge a new interval |
| Non-overlapping Intervals | 435 | Remove minimum intervals |
| Minimum Number of Arrows to Burst Balloons | 452 | Greedy interval intersection |

These problems share the sorting and interval reasoning foundation, but their greedy decisions are different.

---

## 16. Quick Revision Cheat Sheet

```cpp
sort(intervals.begin(), intervals.end());

int start = intervals[0][0];
int end = intervals[0][1];

vector<vector<int>> result;

for(int i = 1; i < intervals.size(); i++) {

    if(end >= intervals[i][0]) {

        end = max(end, intervals[i][1]);

    }
    else {

        result.push_back({start, end});

        start = intervals[i][0];
        end = intervals[i][1];

    }
}

result.push_back({start, end});

return result;
```

**One-line intuition:**

Sort all intervals, keep expanding the current merged interval while overlap exists, and finalize it whenever a gap appears.

**Most important formula:**

`overlap condition: current_end >= next_start`

`merged end: max(current_end, next_end)`

**Final takeaway:** Merge Intervals teaches the fundamental technique of sorting ranges and greedily combining overlapping segments. Master this before moving to Insert Interval and Non-overlapping Intervals.
