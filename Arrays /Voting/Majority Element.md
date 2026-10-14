# 169. Majority Element

**Difficulty:** Easy  
**Pattern:** Arrays + Moore's Voting Algorithm  
**Core Concept:** Cancel out different elements to identify the majority candidate.

## 1. Problem Statement

Given an integer array `nums` of size `n`, find the element that appears **more than** `n / 2` times.

A majority element must occur strictly more than half the array length.

**Example:**

```text
Input:  nums = [2,2,1,1,1,2,2]
Output: 2
```

The element `2` appears four times out of seven, so it is the majority element.

## 2. 💡 Intuition — Moore's Voting Algorithm

The key idea is to cancel out different elements against each other.

- If the current element matches the candidate, increment `count`.
- If it differs, decrement `count`.
- When `count` becomes zero, choose a new candidate.

Because the majority element appears more than half the time, it cannot be completely cancelled out by all the other elements combined.

**Important:** `count` is a voting balance, not the actual frequency of the candidate in the entire array.

## 3. 💻 C++ Solution — My Approach

```cpp
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        // Moore Voting Algorithm
        int candidate = nums[0];
        int count = 0;

        for (int el : nums) {
            if (candidate == el) {
                count++;
            }
            else {
                count--;

                if (count == 0) {
                    candidate = el;
                    count = 1;
                }
            }
        }

        return candidate;
    }
};
```

This implementation is correct when the problem guarantees that a majority element exists.

## 4. 🔍 Dry Run

**Input:** `nums = [2,2,1,1,1,2,2]`

Initially:

```text
candidate = 2
count = 0
```

| Element | Action | Candidate | Count |
|---:|---|---:|---:|
| 2 | Match | 2 | 1 |
| 2 | Match | 2 | 2 |
| 1 | Different | 2 | 1 |
| 1 | Different | 2 | 0 |
| 1 | Count was zero; reset candidate | 1 | 1 |
| 2 | Different | 1 | 0 |
| 2 | Reset candidate | 2 | 1 |

**Final candidate: `2`**

Notice that when the count reaches zero, the next candidate is initialized with `count = 1` because the current element has already been processed as the first vote for the new candidate.

## 5. 🧠 Why Does It Work?

Let the majority element occur \(m\) times in an array of length \(n\).

By definition:

\[
m > \frac{n}{2}
\]

Therefore:

\[
m > n-m
\]

The majority element appears more often than all non-majority elements combined.

Think of every non-majority element cancelling one occurrence of the majority element. Even if every other element cancels a majority occurrence, some majority occurrences must remain.

The surviving candidate is therefore the majority element.

## 6. ⚠️ Common Mistakes

**Mistake 1: Initializing `count = 1` without adjusting the loop**

If you initialize the candidate using `nums[0]` and set `count = 1`, then also process every element starting from index `0`, the first element gets counted twice.

**Mistake 2: Forgetting to reset the count**

When selecting a new candidate, the count must represent the new candidate's vote. Your implementation correctly sets `count = 1`.

**Mistake 3: Confusing vote balance with frequency**

The voting count does not tell you how many times the candidate appears in the entire array. It only represents the current cancellation balance.

**Mistake 4: Assuming a majority always exists**

Moore's Voting Algorithm finds a candidate. If the problem does not guarantee a majority element, you must verify the candidate by counting its actual occurrences.

## 7. ⏱️ Complexity Analysis

- **Time:** `O(n)` — the array is traversed once.
- **Auxiliary space:** `O(1)` — only a candidate and a count are maintained.

This is optimal in terms of asymptotic time and auxiliary space for the standard problem.

## 8. 🛠️ Variant: When Majority Is Not Guaranteed

If the problem does not guarantee that a majority element exists, verify the candidate after voting:

```cpp
int freq = 0;

for (int el : nums) {
    if (el == candidate) {
        freq++;
    }
}

if (freq > nums.size() / 2) {
    return candidate;
}

// No majority element exists.
```

The complete algorithm remains `O(n)` time and `O(1)` auxiliary space.

## 9. 🎯 Interview Takeaways

1. Majority means **strictly more than** `n / 2`, not equal to `n / 2`.
2. Different elements cancel each other's votes.
3. When the balance reaches zero, select a new candidate.
4. A majority element, if guaranteed, survives the cancellation process.
5. Without a guarantee, verify the final candidate.

**Pattern to remember:** Moore's Voting Algorithm — candidate selection through cancellation.

