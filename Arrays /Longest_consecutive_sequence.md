# Longest Consecutive Sequence — LeetCode 128

**Difficulty:** Medium
**Patterns:** Sorting · Hashing · Sequence Detection
**Goal:** Find the length of the longest sequence of consecutive integers. The numbers need not be adjacent in the original array.

---

## 1. Problem Understanding

Given an unsorted integer array `nums`, return the length of the longest consecutive sequence.

**Example:**

```text
Input:  nums = [100,4,200,1,3,2]
Output: 4
```

The longest consecutive sequence is:

```text
[1, 2, 3, 4]
```

**Important:** Consecutive numbers do not need to appear next to each other in the original array.

---

## 2. Approach 1 — Sorting-Based Approach

### Intuition

Sort the array so that consecutive values appear next to each other.

Then:

- If the current number equals the next number, skip the duplicate.
- If the next number is exactly one greater, extend the current sequence.
- Otherwise, start a new sequence.

### C++ Solution

```cpp
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        if (nums.empty()) return 0;

        sort(nums.begin(), nums.end());

        int temp = 1;
        int maxi = 1;

        for (int i = 0; i < nums.size() - 1; i++) {
            if (nums[i] == nums[i + 1]) {
                continue;
            }

            if ((long long)nums[i + 1] == (long long)nums[i] + 1) {
                temp++;
                maxi = max(maxi, temp);
            } else {
                temp = 1;
            }
        }

        return maxi;
    }
};
```

**Note:** This implementation modifies the original array by sorting it. The `long long` comparison avoids overflow when checking consecutive values at integer boundaries.

### Dry Run

```text
Input: [100,4,200,1,3,2]

After sorting:
[1,2,3,4,100,200]
```

| Comparison | Action | `temp` | `maxi` |
| ---------- | ------ | -----: | -----: |
| 1 → 2      | Extend |      2 |      2 |
| 2 → 3      | Extend |      3 |      3 |
| 3 → 4      | Extend |      4 |      4 |
| 4 → 100    | Reset  |      1 |      4 |
| 100 → 200  | Reset  |      1 |      4 |

**Output:** `4`

### Complexity

- **Time:** `O(n log n)` due to sorting.
- **Auxiliary Space:** Depends on the sorting implementation; typically `O(log n)` stack space for `std::sort`.
- **Extra sequence storage:** `O(1)` beyond sorting overhead.

### Advantages and Limitations

- Simple to understand and implement.
- Handles duplicates by skipping them.
- Slower than the expected linear-time hashing solution.
- Modifies the input array.

---

## 3. Approach 2 — Optimal Hash Set Approach

### Intuition

Store every number in an `unordered_set` for average `O(1)` membership checks.

Instead of starting a sequence from every number, start only when the current number has **no predecessor** (`num - 1`) in the set.

Why?

If `num - 1` exists, then `num` belongs to a sequence that has already started earlier. Counting from it again would repeat work.

For each starting number, keep checking whether the next number exists and count the sequence length.

### C++ Solution

```cpp
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        if (nums.empty()) return 0;

        unordered_set<int> st(nums.begin(), nums.end());
        int longest = 1;

        for (int num : st) {
            if (num != INT_MIN && st.find(num - 1) == st.end()) {
                int x = num;
                int cnt = 1;

                while (x != INT_MAX && st.find(x + 1) != st.end()) {
                    x++;
                    cnt++;
                }

                longest = max(longest, cnt);
            }
        }

        return longest;
    }
};
```

The `INT_MIN` and `INT_MAX` guards avoid signed integer overflow at the boundaries.

### Dry Run

```text
Input: [100,4,200,1,3,2]

Set:
{100,4,200,1,3,2}
```

| Number | Is predecessor present? | Action              |
| -----: | ----------------------- | ------------------- |
|    100 | No                      | Sequence length = 1 |
|      4 | Yes, 3 exists           | Skip                |
|    200 | No                      | Sequence length = 1 |
|      1 | No, 0 absent            | Count 1 → 2 → 3 → 4 |
|      2 | Yes, 1 exists           | Skip                |
|      3 | Yes, 2 exists           | Skip                |

The longest sequence is `[1,2,3,4]`.

**Output:** `4`

*Note:* `unordered_set` does not guarantee iteration order, so the actual order of these checks may differ. The result remains the same.

### Complexity

- **Time:** `O(n)` average.
- **Space:** `O(n)` for the hash set.

Why is the average time linear? Each number is inserted once, and sequence expansion happens only from sequence starts. Across all sequences, the total number of expansion steps is linear.

`unordered_set` operations are average `O(1)`, but their worst-case complexity can be worse due to hash collisions.

---

## 4. Brute/Sorting vs Optimal

| Criteria           | Sorting                | Hash Set                          |
| ------------------ | ---------------------- | --------------------------------- |
| Time Complexity    | `O(n log n)`           | `O(n)` average                    |
| Extra Space        | Sorting stack overhead | `O(n)`                            |
| Modifies Input     | Yes                    | No                                |
| Handles Duplicates | Yes                    | Yes                               |
| Main Technique     | Sort and scan          | Start only at sequence beginnings |

**Preferred when linear average time is required:** Hash set approach.

---

## 5. Why Not Start Counting from Every Number?

Consider:

```text
nums = [1,2,3,4,5]
```

If every number starts a sequence:

- Starting from `1` checks five numbers.
- Starting from `2` checks four numbers.
- Starting from `3` checks three numbers.
- And so on.

This can take `O(n²)` time.

The predecessor check avoids this repeated traversal by starting only at `1`.

---

## 6. Common Mistakes

1. Forgetting to handle an empty array.
2. Counting duplicate values as extra consecutive elements.
3. Starting a sequence even when its predecessor exists.
4. Assuming consecutive values must be adjacent in the original array.
5. Claiming `O(n)` worst-case time for `unordered_set`; the expected bound is average `O(n)`.
6. Using `nums[i] + 1` or `x + 1` without considering integer overflow at extreme boundaries.

---

## 7. Interview Explanation

"I first considered sorting the array, which makes consecutive numbers adjacent and allows a linear scan after sorting. That solution takes `O(n log n)` time.

To optimize it, I store all numbers in an unordered set. For each number, I check whether its predecessor exists. If it does, I skip it because the sequence starts earlier. Otherwise, I count consecutive successors until the sequence ends. This achieves average `O(n)` time using `O(n)` extra space."

---

## 8. Final Revision Checklist

- [ ] Understand why sorting simplifies sequence detection.
- [ ] Remember to skip duplicates in the sorting approach.
- [ ] Know why a sequence starts when `num - 1` is absent.
- [ ] Explain why the set approach is average `O(n)`.
- [ ] Know the difference between average and worst-case hash-set performance.
- [ ] State the time and space complexities correctly.

**One-line memory trick:** Sorting = arrange and scan. Hash set = start only where the predecessor is missing.
