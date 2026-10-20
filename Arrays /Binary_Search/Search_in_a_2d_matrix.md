# 🔍 Search a 2D Matrix — LeetCode 74

## 1. Problem Statement

Given an `m × n` matrix with these properties:

1. Each row is sorted in ascending order.
2. The first element of each row is greater than the last element of the previous row.

Return `true` if `target` exists in the matrix; otherwise, return `false`.

### Example

```text
Input:
matrix = [
  [1,  3,  5,  7],
  [10, 11, 16, 20],
  [23, 30, 34, 60]
]
target = 3

Output: true
```

## 2. Core Intuition

The matrix has two important properties:

- Every row is sorted.
- The value ranges of different rows do not overlap.

For example:

```text
Row 0: 1  to  7
Row 1: 10 to 20
Row 2: 23 to 60
```

If `target = 30`, we know it belongs to Row 2's range. There is no need to search the other rows.

**Main idea:** Use binary search to locate the possible row and then binary search within that row.

Your implementation calculates the midpoint row and midpoint column, then decides whether to adjust the row boundaries or the column boundaries.

## 3. Your Accepted C++ Solution

```cpp
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        int m = matrix[0].size(); // columns
        int n = matrix.size();    // rows

        int rl = 0;
        int rh = n - 1;
        int cl = 0;
        int ch = m - 1;

        while (rl <= rh && cl <= ch) {
            int rm = rl + (rh - rl) / 2;
            int cm = cl + (ch - cl) / 2;

            if (matrix[rm][cm] == target) {
                return true;
            }
            else if (target >= matrix[rm][0] &&
                     target <= matrix[rm][m - 1]) {

                // Target belongs to the current row
                if (target > matrix[rm][cm]) {
                    cl = cm + 1;
                }
                else {
                    ch = cm - 1;
                }
            }
            else {
                // Target belongs to another row
                if (target < matrix[rm][0]) {
                    rh = rm - 1;
                }
                else {
                    rl = rm + 1;
                }
            }
        }

        return false;
    }
};
```

**Important:** This implementation assumes a non-empty matrix, as guaranteed by the standard LeetCode 74 constraints.

## 4. Line-by-Line Explanation

### Step 1: Initialize dimensions

```cpp
int m = matrix[0].size();
int n = matrix.size();
```

- `m` = number of columns.
- `n` = number of rows.

### Step 2: Initialize boundaries

```cpp
int rl = 0, rh = n - 1;
int cl = 0, ch = m - 1;
```

- `rl`, `rh` define the current row search range.
- `cl`, `ch` define the current column search range.

Initially, the complete matrix is available for searching.

### Step 3: Calculate the midpoints

```cpp
int rm = rl + (rh - rl) / 2;
int cm = cl + (ch - cl) / 2;
```

`rm` is the middle row, and `cm` is the middle column of the current search range.

### Step 4: Check the middle element

```cpp
if (matrix[rm][cm] == target) {
    return true;
}
```

If the target matches the middle element, the search is complete.

### Step 5: Determine whether the target belongs to this row

```cpp
else if (target >= matrix[rm][0] &&
         target <= matrix[rm][m - 1])
```

The first and last elements define the current row's value range.

If the target falls within that range, search within this row.

```cpp
if (target > matrix[rm][cm]) {
    cl = cm + 1;
}
else {
    ch = cm - 1;
}
```

Because the row is sorted:

- If the target is greater than the middle element, discard the left portion.
- Otherwise, discard the right portion.

The middle element has already been checked, so it does not need to remain in the search range.

### Step 6: Adjust the row boundaries

If the target does not belong to the current row:

```cpp
if (target < matrix[rm][0]) {
    rh = rm - 1;
}
else {
    rl = rm + 1;
}
```

- If the target is smaller than the current row's first element, search earlier rows.
- If the target is greater than the current row's last element, search later rows.

This works because the row ranges are strictly ordered and non-overlapping.

### Step 7: Return false

```cpp
return false;
```

If the search range becomes invalid, the target does not exist in the matrix.

## 5. Dry Run — Target Found

```text
matrix = [
  [1,  3,  5,  7],
  [10, 11, 16, 20],
  [23, 30, 34, 60]
]

target = 30
```

| Iteration | `rm` | `cm` | Middle value | Action                                       |
| --------- | ---: | ---: | -----------: | -------------------------------------------- |
| 1         |    1 |    1 |           11 | Target exceeds row range; move to later rows |
| 2         |    2 |    1 |           30 | Target found                                 |

Output: `true`

## 6. Dry Run — Target Not Found

```text
matrix = [
  [1,  3,  5,  7],
  [10, 11, 16, 20],
  [23, 30, 34, 60]
]

target = 12
```

| Iteration | `rm` | `cm` | Middle value | Action                                   |
| --------- | ---: | ---: | -----------: | ---------------------------------------- |
| 1         |    1 |    1 |           11 | Target belongs to this row; set `cl = 2` |
| 2         |    1 |    2 |           16 | Target is smaller; set `ch = 1`          |

Now `cl > ch`, so the loop terminates.

Output: `false`

## 7. Why Column Boundaries Do Not Need Resetting

This is the most important subtlety in your implementation.

Suppose the target does not belong to the current row. Your code updates only `rl` or `rh`; it does not update `cl` or `ch` in that branch.

Therefore, the column boundaries are still at their initial values when the search moves to another row.

Once the target belongs to the current row, the algorithm performs ordinary binary search on that row. If the target is absent, the column range becomes invalid and the algorithm returns `false`.

This is safe because the problem guarantees that the rows' value ranges do not overlap.

**Your accepted implementation is logically correct under the standard LeetCode 74 constraints.**

## 8. Complexity Analysis

Let:

- `n` = number of rows
- `m` = number of columns

The row search takes `O(log n)` iterations, and the column search takes `O(log m)` iterations.

- **Time Complexity:** `O(log n + log m)`, equivalent to `O(log(n × m))`.
- **Auxiliary Space:** `O(1)`.

## 9. Alternative Approach — Virtual 1D Binary Search

The matrix can also be treated as one globally sorted array without physically flattening it.

```cpp
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        int n = matrix.size();
        int m = matrix[0].size();

        int low = 0;
        int high = n * m - 1;

        while (low <= high) {
            int mid = low + (high - low) / 2;

            int row = mid / m;
            int col = mid % m;

            if (matrix[row][col] == target) {
                return true;
            }
            else if (matrix[row][col] < target) {
                low = mid + 1;
            }
            else {
                high = mid - 1;
            }
        }

        return false;
    }
};
```

### How does index conversion work?

For a matrix with four columns:

```text
mid = 6
row = 6 / 4 = 1
col = 6 % 4 = 2
```

So the virtual index `6` corresponds to `matrix[1][2]`.

This approach has the same asymptotic time and auxiliary space complexity as your solution.

## 10. Comparison of Both Approaches

| Feature          | Your approach                    | Virtual 1D approach                 |
| ---------------- | -------------------------------- | ----------------------------------- |
| Strategy         | Search row range, then columns   | Treat matrix as one sorted sequence |
| Time             | `O(log n + log m)`               | `O(log(n × m))`                     |
| Auxiliary space  | `O(1)`                           | `O(1)`                              |
| Index conversion | Not needed                       | Uses division and modulo            |
| Main learning    | Two-dimensional boundary control | Binary search on a virtual sequence |

Both approaches are optimal. Your solution is a valid implementation and does not need to be replaced merely because another implementation is more common.

## 11. Common Mistakes

1. Confusing the number of rows with the number of columns.
2. Updating row boundaries when the target belongs to the current row.
3. Updating column boundaries when the target belongs to a different row.
4. Forgetting that the target may not exist.
5. Using this strategy on a matrix whose rows are sorted but whose row ranges overlap.
6. Forgetting to handle an empty matrix when adapting the code beyond the standard problem constraints.

## 12. Interview Follow-up Questions

**Q1. Why can you identify a possible row using its first and last elements?**

Because the problem guarantees that each row's range is strictly greater than the previous row's range.

**Q2. Why can you binary-search inside the selected row?**

Every row is independently sorted in ascending order.

**Q3. Is your solution optimal?**

Yes. It uses `O(log n + log m)` time and `O(1)` auxiliary space.

**Q4. When would this algorithm not work?**

If the first element of a row is not guaranteed to be greater than the previous row's last element, the row ranges may overlap. In that case, this row-selection logic is not generally valid.

**Q5. Why does virtual 1D indexing work?**

Because the matrix's ordering guarantees that reading each row from left to right, top to bottom produces one globally sorted sequence.

## 13. Pattern to Remember

**For your approach:**

Check the middle element → determine whether the target belongs to the middle row → binary-search the row or adjust the row boundaries.

**For the virtual 1D approach:**

Convert a virtual index into `(row, column)` → compare the element → eliminate half the sequence.

### Final Revision Checklist

- [ ] Know the two sorted-matrix properties.
- [ ] Understand `rl`, `rh`, `cl`, and `ch`.
- [ ] Explain why row boundaries and column boundaries are updated in different branches.
- [ ] Dry-run both a successful and unsuccessful search.
- [ ] Know the virtual 1D alternative.
- [ ] State `O(log n + log m)` time and `O(1)` auxiliary space.
