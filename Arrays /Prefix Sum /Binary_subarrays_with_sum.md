# LeetCode 930 — Binary Subarrays With Sum

> **Difficulty:** Medium  
> **Pattern:** Prefix Sum + HashMap  
> **Core Concepts:** Prefix Sum Frequency, Subarray Sum, Hashing  
> **Time Complexity:** O(n) expected  
> **Space Complexity:** O(n)

---

## 1. Problem Statement

Given a binary array `nums` and an integer `goal`, return the number of non-empty subarrays whose sum is equal to `goal`.

A binary array contains only `0` and `1`.

### Example 1

**Input:**

```cpp
nums = {1, 0, 1, 0, 1};
goal = 2;
```

**Output:**

```cpp
4
```

**Explanation:**

There are four subarrays whose sum equals `2`:

- `[1, 0, 1]` — indices 0 to 2
- `[1, 0, 1]` — indices 2 to 4
- `[1, 0, 1, 0]` — indices 0 to 3
- `[0, 1, 0, 1]` — indices 1 to 4

### Example 2

**Input:**

```cpp
nums = {0, 0, 0, 0, 0};
goal = 0;
```

**Output:**

```cpp
15
```

**Explanation:**

Every non-empty subarray has sum zero.

The total number of subarrays is:

$$
\frac{n(n+1)}{2}
$$

For $n=5$:

$$
\frac{5\times 6}{2}=15
$$

### What are we required to find?

We need to count all non-empty contiguous subarrays satisfying:

$$
\boxed{\text{Subarray Sum}=goal}
$$

The word **count** is important. It determines what information we need to maintain in our HashMap.

---

## 2. Brute Force Approach

The simplest approach is to generate every possible subarray and calculate its sum.

### Algorithm

1. Fix a starting index `i`.
2. Initialize the current subarray sum to zero.
3. Extend the subarray using another loop.
4. Add each new element to the sum.
5. Whenever `sum == goal`, increment the answer.

### C++ Implementation

```cpp
class Solution {
public:
    int numSubarraysWithSum(vector<int>& nums, int goal) {

        int n = nums.size();
        int count = 0;

        for(int i = 0; i < n; i++) {

            int sum = 0;

            for(int j = i; j < n; j++) {

                sum += nums[j];

                if(sum == goal) {
                    count++;
                }
            }
        }

        return count;
    }
};
```

### Complexity Analysis

There are:

$$
\frac{n(n+1)}{2}
$$

possible non-empty subarrays.

| Complexity | Value |
|---|---|
| Time | O(n²) |
| Auxiliary Space | O(1) |

**Limitation:** We repeatedly calculate subarray sums. We need to reuse information from previously calculated prefix sums.

This leads us to Prefix Sum + HashMap.

---

## 3. Core Concept — Prefix Sum

A prefix sum represents the cumulative sum of array elements from the beginning up to a particular index.

Consider:

```text
nums = [1, 0, 1, 0, 1]
```

The prefix sums are:

| Index | 0 | 1 | 2 | 3 | 4 |
|---|---:|---:|---:|---:|---:|
| Element | 1 | 0 | 1 | 0 | 1 |
| Prefix Sum | 1 | 1 | 2 | 2 | 3 |

Mathematically:

$$
P[i]=\sum_{x=0}^{i} nums[x]
$$

Now suppose we want the sum of a subarray from index $i$ to index $j$.

The subarray sum can be calculated using:

$$
\boxed{SubarraySum(i,j)=P[j]-P[i-1]}
$$

For a subarray starting at index zero:

$$
SubarraySum(0,j)=P[j]
$$

### Important Observation

We want:

$$
P[j]-P[i-1]=goal
$$

Rearranging:

$$
\boxed{P[i-1]=P[j]-goal}
$$

This is the mathematical foundation of our solution.

At every index, we need to find how many previous prefix sums are equal to:

$$
sum-goal
$$

---

## 4. Why Do We Need a HashMap?

We need to efficiently track how many times each prefix sum has appeared.

Therefore, we use:

```cpp
unordered_map<int, int> mp;
```

Our HashMap stores:

| Key | Value |
|---|---|
| Prefix Sum | Frequency of occurrence |

For example:

```text
mp = {
    0 : 1,
    1 : 2,
    2 : 2
}
```

This means:

- Prefix sum `0` has occurred once.
- Prefix sum `1` has occurred twice.
- Prefix sum `2` has occurred twice.

### Why Store Frequency Instead of First Occurrence?

Because the problem asks us to **count all valid subarrays**, not find the maximum length.

Suppose the current prefix sum is `5` and:

$$
goal=2
$$

The required previous prefix sum is:

$$
5-2=3
$$

Suppose prefix sum `3` has appeared three times previously.

Each occurrence represents a different valid starting boundary.

Therefore, three valid subarrays end at the current index.

We must add the frequency:

```cpp
count += mp[sum - goal];
```

### General Rule

- For counting subarrays, store prefix sum frequencies.
- For maximum subarray length, store the first occurrence of each prefix sum.

---

## 5. Why Do We Initialize `mp[0] = 1`?

This is one of the most important implementation details.

Consider:

```cpp
nums = {1, 0, 1};
goal = 2;
```

At index `2`, the current prefix sum becomes:

$$
sum=1+0+1=2
$$

The required previous prefix sum is:

$$
sum-goal=2-2=0
$$

But before processing the array, no actual prefix sum has been inserted into the HashMap.

How do we count the subarray starting at index zero?

We introduce an empty prefix:

- Its sum is `0`.
- It occurs once before the first element.

Therefore:

```cpp
mp[0] = 1;
```

Now the current prefix sum can find the empty prefix and correctly count the entire subarray.

### Mathematical Interpretation

For a subarray beginning at index zero:

$$
P[j]=goal
$$

This is equivalent to:

$$
P[j]-0=goal
$$

The initial frequency of zero ensures this case is counted automatically.

**Remember:** `mp[0] = 1` represents one empty prefix, not an actual zero-valued element in the array.

---

## 6. Optimal Approach — Prefix Sum + HashMap

Now we have all the necessary observations.

### Algorithm

1. Initialize an unordered map.
2. Insert `mp[0] = 1`.
3. Maintain a running prefix sum.
4. Traverse the array.
5. Add the current element to the prefix sum.
6. Calculate the required previous prefix sum: `sum - goal`.
7. If it exists, add its frequency to the answer.
8. Insert or increment the frequency of the current prefix sum.
9. Return the total count.

---

## 7. Optimized C++ Implementation

```cpp
class Solution {
public:
    int numSubarraysWithSum(vector<int>& nums, int goal) {

        int n = nums.size();

        unordered_map<int, int> mp;

        mp[0] = 1;

        int sum = 0;
        int count = 0;

        for(int i = 0; i < n; i++) {

            sum += nums[i];

            if(mp.find(sum - goal) != mp.end()) {
                count += mp[sum - goal];
            }

            mp[sum] += 1;
        }

        return count;
    }
};
```

**This is the optimal expected O(n) Prefix Sum + HashMap solution.**

---

## 8. Detailed Line-by-Line Explanation

### A. Initialize the HashMap

```cpp
unordered_map<int, int> mp;
mp[0] = 1;
```

The HashMap stores:

```text
Key   = Previous Prefix Sum
Value = Frequency
```

The initial entry handles subarrays starting at index zero.

### B. Calculate the Current Prefix Sum

```cpp
sum += nums[i];
```

We maintain:

$$
sum=P[i]
$$

It represents the sum of all elements from index zero to the current index.

### C. Find the Required Previous Prefix Sum

```cpp
if(mp.find(sum - goal) != mp.end()) {
    count += mp[sum - goal];
}
```

From the prefix sum equation:

$$
P[j]-P[i-1]=goal
$$

We rearrange:

$$
P[i-1]=P[j]-goal
$$

Since `sum` represents the current prefix sum, the required previous prefix sum is:

```cpp
sum - goal
```

If it exists, its frequency tells us how many valid subarrays end at the current index.

### D. Update the Frequency

```cpp
mp[sum] += 1;
```

After counting valid subarrays, insert the current prefix sum into the HashMap.

This makes it available as a previous prefix sum for future iterations.

### Why Count First and Update Later?

We must count only subarrays formed using previous prefix positions.

If we update the frequency first, then when `goal == 0`, the current prefix sum could match itself and incorrectly count an empty subarray.

Therefore, the correct order is:

```text
1. Calculate current prefix sum.
2. Search for sum - goal.
3. Add its frequency to count.
4. Update frequency of current sum.
```

---

## 9. Complete Dry Run

Consider:

```cpp
nums = {1, 0, 1, 0, 1};
goal = 2;
```

Initial state:

```text
mp = {0 : 1}

sum = 0
count = 0
```

### Iteration 1 — Index 0

Current element:

```text
nums[0] = 1
```

Update prefix sum:

$$
sum=0+1=1
$$

Required previous prefix sum:

$$
sum-goal=1-2=-1
$$

`-1` does not exist.

Update frequency:

```text
mp = {
    0 : 1,
    1 : 1
}
```

Count remains `0`.

### Iteration 2 — Index 1

Current element:

```text
nums[1] = 0
```

Update:

$$
sum=1+0=1
$$

Required previous prefix sum:

$$
1-2=-1
$$

Not found.

Update frequency:

```text
mp = {
    0 : 1,
    1 : 2
}
```

Count remains `0`.

### Iteration 3 — Index 2

Current element:

```text
nums[2] = 1
```

Update:

$$
sum=1+1=2
$$

Required previous prefix sum:

$$
2-2=0
$$

Frequency of prefix sum `0` is `1`.

Therefore:

$$
count=0+1=1
$$

Update frequency of `2`.

### Iteration 4 — Index 3

Current element:

```text
nums[3] = 0
```

Update:

$$
sum=2+0=2
$$

Required previous prefix sum:

$$
2-2=0
$$

Frequency of prefix sum `0` is `1`.

Therefore:

$$
count=1+1=2
$$

Update frequency of `2` from `1` to `2`.

### Iteration 5 — Index 4

Current element:

```text
nums[4] = 1
```

Update:

$$
sum=2+1=3
$$

Required previous prefix sum:

$$
3-2=1
$$

Frequency of prefix sum `1` is `2`.

Therefore:

$$
count=2+2=4
$$

Update frequency of `3`.

### Final Dry Run Table

| Index | Element | Prefix Sum | Required Sum | Previous Frequency | Count |
|---:|---:|---:|---:|---:|---:|
| 0 | 1 | 1 | -1 | 0 | 0 |
| 1 | 0 | 1 | -1 | 0 | 0 |
| 2 | 1 | 2 | 0 | 1 | 1 |
| 3 | 0 | 2 | 0 | 1 | 2 |
| 4 | 1 | 3 | 1 | 2 | 4 |

**Final Answer:**

$$
\boxed{count=4}
$$

---

## 10. Proof of Correctness

We can prove that the algorithm counts every valid non-empty subarray exactly once.

### Lemma 1: Prefix Sum Difference Gives Subarray Sum

For any subarray from index $i$ to index $j$:

$$
SubarraySum(i,j)=P[j]-P[i-1]
$$

For $i=0$, the empty prefix has sum zero.

### Lemma 2: Every Valid Subarray Is Counted

A subarray is valid when:

$$
P[j]-P[i-1]=goal
$$

Rearranging:

$$
P[i-1]=P[j]-goal
$$

When the algorithm reaches index $j$, it searches for the frequency of `sum - goal`.

Every occurrence of that prefix sum represents a valid starting boundary. Therefore, all valid subarrays ending at index $j$ are counted.

### Lemma 3: No Invalid Subarray Is Counted

The algorithm adds to the answer only when a previous prefix sum satisfies:

$$
P[i-1]=P[j]-goal
$$

Consequently:

$$
P[j]-P[i-1]=goal
$$

Thus, every counted subarray has exactly the required sum.

### Theorem

Every valid subarray has a unique ending index and is counted when the algorithm processes that ending index.

By Lemmas 2 and 3, all and only valid subarrays are counted.

Therefore, the algorithm returns the correct total number of subarrays.

---

## 11. Complexity Analysis

Let $n$ be the number of elements in the array.

### Time Complexity

We traverse the array exactly once.

Each iteration performs:

- One prefix sum update: O(1).
- One HashMap lookup: O(1) expected.
- One HashMap frequency update: O(1) expected.

Therefore:

$$
\boxed{\text{Expected Time Complexity}=O(n)}
$$

### Space Complexity

In the worst case, all prefix sums can be different.

The HashMap may store up to $n+1$ distinct prefix sums.

Therefore:

$$
\boxed{\text{Auxiliary Space Complexity}=O(n)}
$$

The input array is not modified.

---

## 12. Important Edge Cases

| Input | Goal | Output | Explanation |
|---|---:|---:|---|
| `[1,0,1,0,1]` | 2 | 4 | Standard example |
| `[0,0,0,0,0]` | 0 | 15 | Every non-empty subarray is valid |
| `[1,1,1]` | 0 | 0 | No zero-sum subarray |
| `[1,1,1]` | 2 | 2 | Two subarrays of length 2 |
| `[0,1,0]` | 0 | 2 | Two individual zero elements |

### Special Case: `goal = 0`

Consider:

```cpp
nums = {0, 0, 0};
goal = 0;
```

Every non-empty subarray has sum zero.

The number of valid subarrays is:

$$
\frac{n(n+1)}{2}
$$

For $n=3$:

$$
\frac{3\times4}{2}=6
$$

Our HashMap algorithm handles this automatically.

For `goal = 0`, the lookup becomes:

```cpp
mp.find(sum)
```

Every previous occurrence of the same prefix sum represents a zero-sum subarray.

**Important:** The count must happen before updating the current prefix sum frequency.

---

## 13. Common Mistakes and Interview Traps

### Mistake 1: Not Initializing `mp[0] = 1`

Without this initialization, subarrays beginning at index zero will not be counted correctly.

Correct:

```cpp
mp[0] = 1;
```

### Mistake 2: Searching for `goal - sum`

Incorrect:

```cpp
mp.find(goal - sum);
```

Correct:

```cpp
mp.find(sum - goal);
```

Because:

$$
P[j]-P[i-1]=goal
$$

Therefore:

$$
P[i-1]=P[j]-goal
$$

### Mistake 3: Incrementing the Frequency Before Counting

Incorrect order:

```cpp
mp[sum]++;

if(mp.find(sum - goal) != mp.end()) {
    count += mp[sum - goal];
}
```

This can count an empty subarray when `goal == 0`.

Correct order:

```cpp
if(mp.find(sum - goal) != mp.end()) {
    count += mp[sum - goal];
}

mp[sum]++;
```

### Mistake 4: Storing Only the First Occurrence

Incorrect strategy for this problem:

```cpp
mp[sum] = i;
```

We need the frequency of each prefix sum because the problem asks us to count all valid subarrays.

Correct:

```cpp
mp[sum]++;
```

### Mistake 5: Confusing Subarray Count With Maximum Length

These are different objectives.

- Counting valid subarrays requires frequency.
- Finding the longest valid subarray requires first occurrence.

---

## 14. Prefix Sum + HashMap — Comparison With Previous Problems

This is the most important section for pattern recognition.

You have now solved four closely related problems.

| Problem | LC | HashMap Stores | Lookup |
|---|---:|---|---|
| Subarray Sum Equals K | 560 | Prefix Sum Frequency | `sum - k` |
| Subarray Sums Divisible by K | 974 | Remainder Frequency | Same remainder |
| Contiguous Array | 525 | First Prefix Sum Index | Same prefix sum |
| Binary Subarrays With Sum | 930 | Prefix Sum Frequency | `sum - goal` |

### LC 560 vs LC 930

Both use exactly the same mathematical condition:

$$
P[j]-P[i-1]=target
$$

Rearranging:

$$
P[i-1]=P[j]-target
$$

The only difference is the input constraint:

- LC 560 accepts general integers.
- LC 930 accepts only binary values.

The Prefix Sum + HashMap algorithm is the same.

### LC 974 vs LC 930

LC 974 asks for subarrays whose sum is divisible by $k$.

Its mathematical condition is:

$$
P[j]-P[i-1]\equiv0\pmod{k}
$$

Therefore:

$$
P[j]\bmod k=P[i-1]\bmod k
$$

It uses the frequency of the same remainder.

LC 930 instead searches for a specific previous prefix sum:

$$
P[i-1]=P[j]-goal
$$

### LC 525 vs LC 930

LC 525 asks for the maximum length of a balanced binary subarray.

We transform:

$$
0\rightarrow-1,\qquad 1\rightarrow+1
$$

Then store the first occurrence of each prefix sum.

LC 930 does not require this transformation because the actual sum must equal `goal`.

---

## 15. Alternative Approach — Sliding Window

Because LC 930 contains only non-negative binary elements, it also has a sliding window-based solution.

The important observation is:

For a binary array, the number of subarrays with sum exactly `goal` can be calculated as:

$$
\boxed{Exactly(goal)=AtMost(goal)-AtMost(goal-1)}
$$

### Why Does This Work?

Every subarray with sum at most `goal` belongs to the group counted by `AtMost(goal)`.

But this group also contains subarrays with sums less than `goal`.

Subtracting all subarrays with sum at most `goal-1` leaves only subarrays whose sum is exactly `goal`.

Therefore:

$$
\boxed{Exactly(goal)=AtMost(goal)-AtMost(goal-1)}
$$

### At Most K Function

```cpp
int atMost(vector<int>& nums, int k) {

    if(k < 0) return 0;

    int left = 0;
    int sum = 0;
    int count = 0;

    for(int right = 0; right < nums.size(); right++) {

        sum += nums[right];

        while(sum > k) {
            sum -= nums[left];
            left++;
        }

        count += right - left + 1;
    }

    return count;
}
```

### Complete Sliding Window Implementation

```cpp
class Solution {
public:

    int atMost(vector<int>& nums, int k) {

        if(k < 0) return 0;

        int left = 0;
        int sum = 0;
        int count = 0;

        for(int right = 0; right < nums.size(); right++) {

            sum += nums[right];

            while(sum > k) {
                sum -= nums[left];
                left++;
            }

            count += right - left + 1;
        }

        return count;
    }

    int numSubarraysWithSum(vector<int>& nums, int goal) {

        return atMost(nums, goal) -
               atMost(nums, goal - 1);
    }
};
```

### Why Add `right - left + 1`?

After shrinking the window, the current window `[left, right]` has sum at most `k`.

Since all elements are non-negative, every suffix ending at `right` and starting between `left` and `right` also has sum at most `k`.

The number of such subarrays is:

$$
\boxed{right-left+1}
$$

### Complexity

| Complexity | Value |
|---|---|
| Time | O(n) |
| Auxiliary Space | O(1) |

**Important:** This sliding window technique relies on non-negative array elements. It is not directly applicable to arbitrary arrays containing negative numbers.

---

## 16. Prefix Sum vs Sliding Window

Both approaches solve LC 930 in linear time.

| Feature | Prefix Sum + HashMap | Sliding Window |
|---|---|---|
| Time | O(n) expected | O(n) |
| Auxiliary Space | O(n) | O(1) |
| Works with negative numbers? | Yes, for this sum-count formulation | Not generally |
| Main concept | Previous prefix sum frequency | At most K counting |
| Reusable for LC 560? | Yes | Not generally |
| Reusable for LC 974? | Yes, with remainder logic | Not generally |

### Which One Should You Prefer?

For your current Prefix Sum pattern practice, the HashMap solution is particularly important because it connects directly with LC 560.

The sliding window approach is an additional technique worth knowing because it demonstrates how binary/non-negative constraints can unlock another solution.

---

## 17. Important Interview Follow-up Questions

### Q1. Why do we use `sum - goal`?

Because the difference between the current prefix sum and a previous prefix sum must equal `goal`.

$$
P[j]-P[i-1]=goal
$$

Therefore:

$$
P[i-1]=P[j]-goal
$$

### Q2. Why do we store frequencies?

Because multiple previous prefix positions can produce different valid subarrays ending at the current index.

### Q3. Why initialize `mp[0] = 1`?

To count valid subarrays beginning at index zero.

### Q4. Why count before updating the frequency?

To avoid counting the current prefix position as a previous position, especially when `goal == 0`.

### Q5. Can this approach handle negative numbers?

Yes. Prefix Sum + HashMap can handle negative numbers for the exact-sum counting problem, provided the prefix sum type is sufficient for the constraints.

### Q6. Can we solve it using Sliding Window?

Yes, because the array contains only non-negative values.

Use:

$$
Exactly(goal)=AtMost(goal)-AtMost(goal-1)
$$

### Q7. What is the difference between LC 525 and LC 930?

LC 525 asks for maximum length and stores the first occurrence of a prefix sum.

LC 930 asks for the count and stores the frequency of each prefix sum.

---

## 18. Pattern Recognition — When Should You Use This Approach?

Look for these signals:

- Find the number of contiguous subarrays.
- Subarray sum must equal a target.
- Repeated prefix sums can help identify valid subarrays.
- Multiple starting boundaries can produce the same current subarray sum.

### General Formula

For current prefix sum $P[j]$ and target $K$:

$$
P[j]-P[i-1]=K
$$

Rearrange:

$$
\boxed{P[i-1]=P[j]-K}
$$

Therefore, the general counting pattern is:

```cpp
count += mp[sum - k];
mp[sum]++;
```

Remember to initialize:

```cpp
mp[0] = 1;
```

And check that the required key exists before adding its frequency.

---

## 19. Final Revision Cheat Sheet

### Problem

Count non-empty subarrays whose sum equals `goal`.

### Core Formula

$$
P[j]-P[i-1]=goal
$$

### Rearranged Formula

$$
\boxed{P[i-1]=P[j]-goal}
$$

### HashMap Structure

```cpp
unordered_map<int, int> mp;
```

```text
Key   = Prefix Sum
Value = Frequency
```

### Initialization

```cpp
mp[0] = 1;
```

### Core Logic

```cpp
sum += nums[i];

if(mp.find(sum - goal) != mp.end()) {
    count += mp[sum - goal];
}

mp[sum]++;
```

### Complexity

$$
\boxed{\text{Expected Time}=O(n)}
$$

$$
\boxed{\text{Auxiliary Space}=O(n)}
$$

### Alternative Sliding Window Formula

$$
\boxed{Exactly(goal)=AtMost(goal)-AtMost(goal-1)}
$$

---

## 20. One-Line Intuition

**Maintain a running prefix sum, search for the frequency of `currentSum - goal`, add that frequency to the answer, and then update the current prefix sum's frequency.**

### The Most Important Lesson From LC 930

The HashMap's stored value depends on the problem's objective:

- Count subarrays → Store frequency.
- Find maximum length → Store first occurrence.
- Count divisible subarrays → Store remainder frequency.

**Understand what information the problem needs before deciding what your HashMap should store.**
