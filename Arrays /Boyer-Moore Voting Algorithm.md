# LeetCode 169 — Majority Element

**Pattern:** Array + Boyer-Moore Voting Algorithm  
**Difficulty:** Easy  
**Importance:** ⭐⭐⭐⭐⭐

---

## 1. Problem Statement

Given an integer array `nums` of size `n`, return the majority element.

The majority element is the element that appears **more than n/2 times**.

It is guaranteed that a majority element always exists.

### Example

Input:
`nums = [2,2,1,1,1,2,2]`

Output:
`2`

Because `2` appears 4 times and `4 > 7/2`.

---

## 2. Main Idea

Use the **Boyer-Moore Voting Algorithm**.

Maintain two variables:

- `candidate` → current possible majority element
- `count` → current voting advantage of the candidate

For every element `el`:

1. If `count == 0`, make `el` the new candidate.
2. If `el == candidate`, increase `count`.
3. Otherwise, decrease `count`.

Core logic:

    if(count == 0){
        candidate = el;
    }

    if(el == candidate){
        count++;
    }
    else{
        count--;
    }

---

## 3. Why Does It Work?

The majority element occurs **more than n/2 times**.

Therefore, its frequency is greater than the combined frequency of all other elements.

We can think of the algorithm as cancelling pairs:

    Majority + Different Element
              ↓
           CANCEL

Every time a different element is encountered, one vote of the candidate is cancelled.

Since the majority element has more occurrences than all other elements combined, it cannot be completely cancelled.

Therefore, the final candidate is the majority element.

---

## 4. Core Pattern

    count == 0
         ↓
    New Candidate

    Same as Candidate
         ↓
       count++

    Different Element
         ↓
       count--

At the end:

    return candidate;

---

## 5. Dry Run

Input:

    nums = [2,2,1,1,1,2,2]

Initial:

    candidate = 2
    count = 0

| Element | Candidate | Count | Action |
|---------|-----------|-------|--------|
| 2 | 2 | 1 | count = 0 → new candidate, then +1 |
| 2 | 2 | 2 | Same → count++ |
| 1 | 2 | 1 | Different → count-- |
| 1 | 2 | 0 | Different → count-- |
| 1 | 1 | 1 | count = 0 → new candidate |
| 2 | 1 | 0 | Different → count-- |
| 2 | 2 | 1 | count = 0 → new candidate |

Final:

    candidate = 2

Answer:

    2

---

## 6. Code

    class Solution {
    public:
        int majorityElement(vector<int>& nums) {

            int candidate = nums[0];
            int count = 0;

            for(int el : nums){

                if(count == 0){
                    candidate = el;
                }

                if(el == candidate){
                    count++;
                }
                else{
                    count--;
                }
            }

            return candidate;
        }
    };

---

## 7. Important Understanding of `count`

`count` does NOT represent the actual frequency of the candidate.

It represents the **net voting advantage** after cancellation.

For example:

    candidate = 2
    count = 3

does not necessarily mean `2` has appeared exactly 3 times.

It means that after cancelling different elements, candidate `2` currently has a voting advantage of 3.

---

## 8. Why `count == 0`?

When:

    count == 0

the current candidate has been completely cancelled by different elements.

Therefore, the current element can become a new candidate:

    candidate = el;

This allows the algorithm to continue searching for the majority element.

---

## 9. Why Not Use HashMap?

Using a frequency map:

    unordered_map<int,int>

would give:

    Time  → O(n)
    Space → O(n)

Boyer-Moore gives:

    Time  → O(n)
    Space → O(1)

Therefore, Boyer-Moore is the optimal approach when a majority element is guaranteed to exist.

---

## 10. Complexity

### Time Complexity

    O(n)

The array is traversed only once.

### Space Complexity

    O(1)

Only `candidate` and `count` are maintained.

---

## 11. Important Edge Cases

### Single Element

    nums = [5]

Output:

    5

### All Elements Same

    nums = [7,7,7,7]

Output:

    7

### Majority at Beginning

    nums = [3,3,3,2,2]

Output:

    3

### Majority at End

    nums = [2,2,3,3,3]

Output:

    3

---

## 12. Important Condition

The simple Boyer-Moore solution assumes that a majority element **is guaranteed to exist**.

LeetCode 169 provides this guarantee.

If a majority element is NOT guaranteed to exist, then after finding the candidate, we must count its actual occurrences again and verify:

    frequency > n/2

---

## 13. Common Mistake

Do not think of `count` as the actual frequency.

Wrong:

    count = frequency of candidate

Correct:

    count = voting advantage after cancellations

---

## 14. Interview Pattern

Whenever the question says:

"Find the element occurring more than n/2 times."

Immediately think:

    Majority Element
          ↓
    Boyer-Moore Voting
          ↓
    candidate + count
          ↓
    Same → count++
    Different → count--
    count == 0 → new candidate
          ↓
    return candidate

---

## 15. Related Problem

### LeetCode 229 — Majority Element II

This is an important follow-up.

Instead of finding elements occurring more than:

    n/2

we find elements occurring more than:

    n/3

It extends the same **voting/cancellation concept**.

---

## 16. One-Line Revision

> Maintain a candidate and its voting advantage; same elements increase the count, different elements cancel a vote, and because the majority element appears more than n/2 times, it survives all cancellations.

---

**LeetCode:** 169 — Majority Element  
**Pattern:** Array → Boyer-Moore Voting  
**Core Concept:** Candidate + Count + Cancellation  
**Time:** O(n)  
**Space:** O(1)  
**Key Condition:** Majority element is guaranteed to exist
