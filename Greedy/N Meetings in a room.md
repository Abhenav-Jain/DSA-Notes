# N Meetings in One Room — Greedy

**Problem:** Given the start and finish times of `N` meetings and only one room, select the maximum number of non-overlapping meetings.

The returned meeting indices must be in increasing order. If meetings have the same finish time, prefer the smaller original index.

## 1. Greedy intuition

The key observation is:

**The meeting that finishes earliest leaves the maximum remaining time for future meetings.**

Therefore:
1. Sort meetings by increasing finish time.
2. Select the first meeting.
3. Traverse the remaining meetings.
4. Select a meeting if its start time is strictly greater than the finish time of the last selected meeting.
5. Update the last selected finish time.
6. Sort the selected original indices before returning.

This is the **Earliest Finish Time First** greedy strategy.

## 2. C++ Solution

```cpp
class Solution {
public:
    vector<int> maxMeetings(vector<int>& s, vector<int>& f) {
        int n = s.size();

        vector<tuple<int, int, int>> meeting;

        // {finish time, original index, start time}
        for(int i = 0; i < n; i++) {
            meeting.push_back({f[i], i + 1, s[i]});
        }

        sort(meeting.begin(), meeting.end());

        vector<int> ans;

        if(n == 0) return ans;

        // Select the first meeting
        ans.push_back(get<1>(meeting[0]));

        int last_time = get<0>(meeting[0]);

        // Select compatible meetings
        for(int i = 1; i < n; i++) {
            int startTime = get<2>(meeting[i]);
            int finishTime = get<0>(meeting[i]);
            int index = get<1>(meeting[i]);

            if(startTime > last_time) {
                ans.push_back(index);
                last_time = finishTime;
            }
        }

        // Return original indices in increasing order
        sort(ans.begin(), ans.end());

        return ans;
    }
};
```

## 3. Why use a tuple?

We store:

`{finish_time, original_index, start_time}`

C++ sorts tuples lexicographically:

1. First compare finish times.
2. If equal, compare original indices.
3. If both are equal, compare start times.

This automatically handles the required tie-breaking.

## 4. Dry Run

Input:

```cpp
s = {1, 3, 0, 5, 8, 5};
f = {2, 4, 6, 7, 9, 9};
```

Sorted meetings:

| Index | Start | Finish |
|---:|---:|---:|
| 1 | 1 | 2 |
| 2 | 3 | 4 |
| 3 | 0 | 6 |
| 4 | 5 | 7 |
| 5 | 8 | 9 |
| 6 | 5 | 9 |

Selection:
- Meeting 1: Select. `last_time = 2`.
- Meeting 2: `3 > 2`, select. `last_time = 4`.
- Meeting 3: `0 <= 4`, reject.
- Meeting 4: `5 > 4`, select. `last_time = 7`.
- Meeting 5: `8 > 7`, select. `last_time = 9`.
- Meeting 6: `5 <= 9`, reject.

Final answer: `{1, 2, 4, 5}`.

## 5. Complexity Analysis

Let `N` be the number of meetings.

| Operation | Complexity |
|---|---|
| Creating meeting tuples | `O(N)` |
| Sorting meetings | `O(N log N)` |
| Selecting meetings | `O(N)` |
| Sorting selected indices | `O(N log N)` |

**Time Complexity:** `O(N log N)`

**Auxiliary Space:** `O(N)` for storing meeting tuples and the answer.

## 6. Correctness Intuition

Choosing the earliest-finishing available meeting is always a safe greedy choice.

Suppose an optimal solution starts with a meeting that finishes later than the earliest-finishing meeting. Replacing that first meeting with the earliest-finishing one cannot reduce the time available for subsequent meetings.

Thus, there exists an optimal solution that begins with the earliest-finishing meeting. Repeating this reasoning for the remaining compatible meetings establishes the greedy strategy.

## 7. Important Edge Cases

- Only one meeting.
- All meetings overlap.
- Every meeting is compatible with the previous one.
- Multiple meetings have the same finish time.
- Meetings with equal finish and start boundaries.
- Empty input, if permitted by the constraints.

**Important:** This problem uses strict compatibility: `startTime > last_time`, as specified in the problem statement.

## 8. Common Mistakes

1. Sorting by start time instead of finish time.
2. Comparing the next start time with the previous meeting's index.
3. Updating `last_time` with the meeting index instead of its finish time.
4. Forgetting to preserve original 1-based indices.
5. Forgetting to sort the selected indices before returning.
6. Using `>=` when the problem requires strict `>`.

## 9. Pattern Recognition

Whenever a problem asks:

- Maximum number of non-overlapping activities.
- Maximum meetings in one room.
- Maximum compatible intervals.

Think:

**Greedy → Sort by finish time → Select earliest finishing compatible activity.**

## 10. Interview Follow-ups

- Why sort by finish time rather than start time?
- Why is choosing the earliest-finishing meeting optimal?
- What changes if we need to minimize the number of rooms?
- What changes if we need to remove the minimum number of overlapping intervals?

