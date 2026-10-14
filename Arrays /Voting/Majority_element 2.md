# 229. Majority Element II

**Difficulty:** Medium  
**Pattern:** Boyer–Moore Voting Algorithm  
**Core Concept:** Two Candidates + Vote Cancellation + Frequency Verification

## 1. Problem Statement

Given an integer array `nums` of size `n`, return all elements that appear **strictly more than `n / 3` times**.

The answer can contain at most **two distinct elements**.

### Example

```text
Input:  nums = [3,2,3]
Output: [3]
```

Element `3` appears twice, and `2` appears once.

Since `3 > 3/3`, element `3` is a majority element under this problem's definition.

## 2. 💡 Core Intuition

In LC 169, we searched for an element appearing more than `n/2` times, so we needed one candidate.

Here, the threshold is `n/3`.

**Why can there be at most two answers?**

If three distinct elements each appeared more than `n/3` times, their combined frequency would exceed `n`, which is impossible.

\[
\boxed{\text{Maximum candidates} = 2}
\]

Therefore, we extend Boyer–Moore Voting Algorithm to maintain two candidates and two vote counts.

### The cancellation idea

When the current element matches a candidate, increase that candidate's count.

When it matches neither candidate and both counts are positive, decrement both counts. This effectively cancels one occurrence of each candidate against the current non-candidate element.

The first pass identifies **potential candidates**. The second pass verifies their actual frequencies.

## 3. 💻 C++ Solution — My Approach

```cpp
class Solution {
public:
    vector<int> majorityElement(vector<int>& nums) {
        int m1 = 0;
        int m2 = 0;
        int cnt1 = 0;
        int cnt2 = 0;
        int n = nums.size();

        // Pass 1: Find potential candidates
        for (int el : nums) {
            if (cnt1 > 0 && m1 == el) {
                cnt1++;
            }
            else if (cnt2 > 0 && m2 == el) {
                cnt2++;
            }
            else if (cnt1 == 0) {
                m1 = el;
                cnt1 = 1;
            }
            else if (cnt2 == 0) {
                m2 = el;
                cnt2 = 1;
            }
            else {
                cnt1--;
                cnt2--;
            }
        }

        // Pass 2: Verify actual frequencies
        cnt1 = 0;
        cnt2 = 0;

        for (int el : nums) {
            if (m1 == el) {
                cnt1++;
            }
            else if (m2 == el) {
                cnt2++;
            }
        }

        vector<int> ans;

        if (cnt1 > n / 3) {
            ans.push_back(m1);
        }

        if (cnt2 > n / 3) {
            ans.push_back(m2);
        }

        return ans;
    }
};
```

**Note:** This implementation correctly handles the `m1 == m2` case in the verification pass because the `if ... else if` counts matching elements only in `cnt1`. Thus, a duplicate candidate will not be added twice.

## 4. 🔍 Dry Run

**Input:** `nums = [3,2,3]`

Initially:

```text
m1 = 0, m2 = 0
cnt1 = 0, cnt2 = 0
```

### Pass 1 — Candidate selection

| Element | Action | `m1, cnt1` | `m2, cnt2` |
|---:|---|---|---|
| 3 | First candidate assigned | `3, 1` | `0, 0` |
| 2 | Second candidate assigned | `3, 1` | `2, 1` |
| 3 | Matches first candidate | `3, 2` | `2, 1` |

Potential candidates: `3` and `2`.

### Pass 2 — Frequency verification

| Candidate | Actual frequency | Threshold | Qualifies? |
|---|---:|---:|---|
| 3 | 2 | `n/3 = 1` | Yes |
| 2 | 1 | `n/3 = 1` | No |

Remember that the condition is **strictly greater than** `n/3`, not greater than or equal to it.

**Output:** `[3]`

## 5. ⚙️ Understanding the First Pass

The order of the conditions is important.

### Case 1: Current element matches candidate 1

```cpp
if (cnt1 > 0 && m1 == el) {
    cnt1++;
}
```

Increase candidate 1's vote count.

### Case 2: Current element matches candidate 2

```cpp
else if (cnt2 > 0 && m2 == el) {
    cnt2++;
}
```

Increase candidate 2's vote count.

### Case 3: Candidate 1 has no active votes

```cpp
else if (cnt1 == 0) {
    m1 = el;
    cnt1 = 1;
}
```

Assign the current element to candidate 1.

### Case 4: Candidate 2 has no active votes

```cpp
else if (cnt2 == 0) {
    m2 = el;
    cnt2 = 1;
}
```

Assign the current element to candidate 2.

### Case 5: Neither candidate matches

```cpp
else {
    cnt1--;
    cnt2--;
}
```

Both candidates lose one vote. The current element effectively cancels one vote from each candidate.

## 6. 🧠 Why Is the Algorithm Correct?

Imagine repeatedly removing groups of three elements with distinct values.

Each such group can contain at most one occurrence of any particular value. Removing these groups cannot eliminate an element that appears more than `n/3` times, because each group removes at most one occurrence of that element.

The cancellation process leaves at most two potential candidates. However, those candidates are not guaranteed to exceed the threshold, so we must count their actual occurrences in the second pass.

This is why the algorithm uses two passes:

1. Find at most two potential candidates.
2. Verify that each candidate appears strictly more than `n/3` times.

## 7. ⚠️ Common Mistakes

- **Assuming the first pass gives the final answer:** It only identifies potential candidates.
- **Skipping frequency verification:** A remaining candidate may not exceed `n/3`.
- **Using `>= n/3`:** The condition must be strictly `> n/3`.
- **Using only one candidate:** There can be two qualifying elements.
- **Ignoring condition order:** Check whether an element matches an existing candidate before assigning a new candidate.
- **Treating candidate values as validity flags:** Values such as `0` are valid array elements. The vote counts determine whether a candidate slot is active.
- **Adding duplicates:** The verification logic should not add the same element twice.

## 8. ⏱️ Complexity Analysis

**Time complexity: `O(n)`**

- First pass: `O(n)` to identify candidates.
- Second pass: `O(n)` to verify frequencies.

Overall, `O(n) + O(n) = O(n)`.

**Auxiliary space: `O(1)`**

Only two candidate variables and two vote counts are maintained, excluding the output vector.

## 9. 🔥 LC 169 vs LC 229

| Feature | LC 169 — Majority Element | LC 229 — Majority Element II |
|---|---|---|
| Threshold | More than `n/2` | More than `n/3` |
| Maximum answers | 1 | 2 |
| Candidates | 1 | 2 |
| Verification | Needed if existence isn't guaranteed | Needed to verify candidates |
| Time | `O(n)` | `O(n)` |
| Auxiliary space | `O(1)` | `O(1)` |

## 10. 🎯 Interview Takeaways

1. For a threshold greater than `n/k`, there can be at most `k - 1` qualifying elements.
2. For `n/3`, maintain two candidates.
3. Match existing candidates before assigning an inactive candidate slot.
4. If neither matches and both counts are positive, decrement both counts.
5. Verify the candidates in a separate pass.
6. Use strict comparison: `frequency > n/3`.

**Pattern to remember:** Generalized Boyer–Moore Voting Algorithm — maintain `k - 1` candidates when searching for elements occurring more than `n/k` times.

