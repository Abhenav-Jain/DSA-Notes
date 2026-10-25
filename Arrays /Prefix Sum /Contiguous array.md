# LeetCode 525 — Contiguous Array

> **Difficulty:** Medium  
> **Pattern:** Prefix Sum + HashMap  
> **Core Concepts:** Array Transformation, Prefix Sum, First Occurrence  
> **Time Complexity:** O(n) expected  
> **Space Complexity:** O(n)

---

## 1. Problem Statement

Given a binary array `nums` containing only `0` and `1`, find the maximum length of a contiguous subarray with an equal number of `0`s and `1`s.

### Example 1

**Input:**

```cpp
nums = [0, 1]
```

**Output:**

```cpp
2
```

**Explanation:** The entire array contains one zero and one one.

### Example 2

**Input:**

```cpp
nums = [0, 1, 0]
```

**Output:**

```cpp
2
```

**Explanation:** The subarray `[0, 1]` or `[1, 0]` contains equal numbers of zeros and ones.

### Example 3

**Input:**

```cpp
nums = [0, 1, 0, 1, 1, 0]
```

**Output:**

```cpp
6
```

**Explanation:** The entire array contains three zeros and three ones.

### What are we required to find?

We need to identify:

1. A contiguous subarray.
2. Equal numbers of zeros and ones.
3. The maximum possible length.

The important observation is that we need the **maximum length**, not the number of valid subarrays. This determines what we should store in our HashMap.

---

## 2. Brute Force Approach

The simplest approach is to generate every possible subarray and count its zeros and ones.

### Algorithm

1. Fix a starting index `i`.
2. Initialize `zero = 0` and `one = 0`.
3. Extend the subarray using another loop.
4. Update the zero or one count.
5. Whenever `zero == one`, calculate the subarray length.
6. Update the maximum length.

### C++ Implementation

```cpp
class Solution {
public:
    int findMaxLength(vector<int>& nums) {
        int n = nums.size();
        int max_len = 0;

        for(int i = 0; i < n; i++) {
            int zero = 0;
            int one = 0;

            for(int j = i; j < n; j++) {

                if(nums[j] == 0) {
                    zero++;
                }
                else {
                    one++;
                }

                if(zero == one) {
                    max_len = max(max_len, j - i + 1);
                }
            }
        }

        return max_len;
    }
};
```

### Complexity Analysis

There are O(n²) possible subarrays.

| Complexity | Value |
|---|---|
| Time | O(n²) |
| Auxiliary Space | O(1) |

**Limitation:** We examine every possible subarray. We need a way to reuse information from previously calculated prefixes.

This leads us to Prefix Sum + HashMap.

---

## 3. Core Observation — Transform the Array

This is the most important step in the entire problem.

We are looking for a subarray satisfying:

$$
\text{Number of Zeros} = \text{Number of Ones}
$$

Instead of maintaining two separate counts, we can transform the array.

### Transformation

Replace:

- Every `0` with `-1`.
- Every `1` with `+1`.

For example:

**Original Array:**

```text
[0, 1, 0, 1, 1, 0]
```

**Transformed Array:**

```text
[-1, 1, -1, 1, 1, -1]
```

Now consider a subarray containing two zeros and two ones.

Its transformed sum becomes:

$$
(-1)+1+(-1)+1=0
$$

### Why does this transformation work?

Let:

- $Z$ = Number of zeros.
- $O$ = Number of ones.

After transformation:

$$
\text{Subarray Sum}=O-Z
$$

For a balanced subarray:

$$
O=Z
$$

Therefore:

$$
O-Z=0
$$

Hence, we get the important equivalence:

$$
\boxed{\text{Equal Zeros and Ones} \iff \text{Transformed Subarray Sum}=0}
$$

**Important conclusion:**

Our original problem has now become:

**Find the longest contiguous subarray whose sum is zero.**

This is a classic Prefix Sum problem.

---

## 4. Understanding Prefix Sum

A prefix sum represents the cumulative sum of elements from the beginning of an array up to a particular index.

For the transformed array:

```text
Array: [-1, 1, -1, 1, 1, -1]
```

The prefix sums are:

| Index | 0 | 1 | 2 | 3 | 4 | 5 |
|---|---:|---:|---:|---:|---:|---:|
| Element | -1 | 1 | -1 | 1 | 1 | -1 |
| Prefix Sum | -1 | 0 | -1 | 0 | 1 | 0 |

Mathematically, define the prefix sum as:

$$
P[i]=\sum_{x=0}^{i} A[x]
$$

Here, $A$ represents the transformed array.

Notice that some prefix sums repeat:

- Prefix sum `-1` occurs at indices `0` and `2`.
- Prefix sum `0` occurs at indices `1`, `3`, and `5`.

This repetition is the key to solving the problem.

---

## 5. Mathematical Derivation — Why Repeated Prefix Sums Work

Suppose the prefix sum at two indices $i$ and $j$ is equal, where:

$$
i<j
$$

Then:

$$
P[i]=P[j]
$$

We know that the sum of the subarray from index $i+1$ to index $j$ is:

$$
\text{Subarray Sum}=P[j]-P[i]
$$

Since both prefix sums are equal:

$$
P[j]-P[i]=0
$$

Therefore:

$$
\boxed{\text{Subarray Sum}=0}
$$

Remember our transformation:

$$
0\rightarrow -1
$$

$$
1\rightarrow +1
$$

A zero-sum subarray in the transformed array means:

$$
\text{Number of Zeros}=\text{Number of Ones}
$$

Thus:

$$
\boxed{P[i]=P[j]\implies\text{Balanced Subarray}}
$$

The length of this balanced subarray is:

$$
\boxed{\text{Length}=j-i}
$$

### The Most Important Insight

If the same prefix sum occurs at two different indices, the subarray between those indices has an equal number of zeros and ones.

---

## 6. Why Do We Need a HashMap?

We need to identify whether a prefix sum has appeared previously.

We can use:

```cpp
unordered_map<int, int> mp;
```

Our HashMap stores:

| Key | Value |
|---|---|
| Prefix Sum | First Occurrence Index |

For example:

```text
mp = {
     0 : -1,
    -1 :  0,
     1 :  4
}
```

This means:

- Prefix sum `0` first appeared at index `-1`.
- Prefix sum `-1` first appeared at index `0`.
- Prefix sum `1` first appeared at index `4`.

### Why store the first occurrence instead of frequency?

Because the question asks for maximum length.

Suppose the same prefix sum occurs at these indices:

```text
Prefix Sum:  2, 2, 2
Index:       1, 4, 7
```

At index `7`, possible subarray lengths are:

Using index `1`:

$$
7-1=6
$$

Using index `4`:

$$
7-4=3
$$

The earliest occurrence gives the longest subarray.

Therefore:

$$
\boxed{\text{Store the First Occurrence of Every Prefix Sum}}
$$

**Never overwrite the first occurrence.**

---

## 7. Why Do We Initialize `mp[0] = -1`?

This is one of the most important implementation details.

Consider:

```cpp
nums = [0, 1]
```

After transformation:

```text
[-1, 1]
```

Prefix sums:

| Index | 0 | 1 |
|---|---:|---:|
| Element | -1 | 1 |
| Prefix Sum | -1 | 0 |

At index `1`, the prefix sum becomes zero.

The entire array is balanced, so the answer should be `2`.

But how do we calculate this using the same prefix-sum logic?

We treat the empty prefix as having:

- Prefix sum = `0`.
- Index = `-1`.

Therefore:

```cpp
mp[0] = -1;
```

Now:

$$
\text{Length}=1-(-1)=2
$$

This correctly counts subarrays that begin at index zero.

### General Rule

Initializing the empty prefix before index zero helps us handle valid subarrays that start at the beginning of the array.

---

## 8. Optimal Algorithm

Now we have all the required observations.

### Algorithm

1. Initialize `sum = 0`.
2. Create a HashMap to store the first occurrence of every prefix sum.
3. Initialize `mp[0] = -1`.
4. Traverse the array.
5. If the current element is `0`, decrement the sum.
6. If the current element is `1`, increment the sum.
7. Check whether the current prefix sum already exists.
8. If it exists, calculate the distance from its first occurrence.
9. Update the maximum length.
10. Otherwise, store the current index as its first occurrence.

---

## 9. Optimized C++ Implementation

```cpp
class Solution {
public:
    int findMaxLength(vector<int>& nums) {

        int n = nums.size();

        int sum = 0;
        int max_len = 0;

        unordered_map<int, int> mp;

        // Empty prefix before index 0
        mp[0] = -1;

        for(int i = 0; i < n; i++) {

            // Transform 0 -> -1 and 1 -> +1
            if(nums[i] == 0) {
                sum -= 1;
            }
            else if(nums[i] == 1) {
                sum += 1;
            }

            // Check whether the prefix sum appeared earlier
            if(mp.find(sum) != mp.end()) {

                max_len = max(
                    max_len,
                    i - mp[sum]
                );
            }

            // Store only the first occurrence
            else {
                mp[sum] = i;
            }
        }

        return max_len;
    }
};
```

---

## 10. Detailed Line-by-Line Explanation

### A. Initialization

```cpp
int sum = 0;
int max_len = 0;

unordered_map<int, int> mp;

mp[0] = -1;
```

- `sum` maintains the transformed prefix sum.
- `max_len` stores the maximum balanced subarray length.
- `mp` stores the first index of every prefix sum.
- `mp[0] = -1` handles subarrays starting at index zero.

### B. Transform the Current Element

```cpp
if(nums[i] == 0) {
    sum -= 1;
}
else if(nums[i] == 1) {
    sum += 1;
}
```

We maintain:

$$
sum=\text{Number of Ones}-\text{Number of Zeros}
$$

If the balance becomes equal at two different prefix positions, the elements between them form a balanced subarray.

### C. Check Whether the Prefix Sum Exists

```cpp
if(mp.find(sum) != mp.end()) {
    max_len = max(max_len, i - mp[sum]);
}
```

Suppose the same prefix sum occurred earlier at index `j`.

Then:

$$
P[i]=P[j]
$$

Therefore:

$$
P[i]-P[j]=0
$$

The current subarray has equal zeros and ones.

Its length is:

$$
i-j
$$

We update `max_len` using this length.

### D. Store the First Occurrence

```cpp
else {
    mp[sum] = i;
}
```

If the prefix sum has never appeared before, store the current index.

If it already exists, leave the original index unchanged.

This is essential for maximizing the length.

---

## 11. Complete Dry Run

Consider:

```cpp
nums = [0, 1, 0, 1, 1, 0]
```

After transformation:

```text
[-1, 1, -1, 1, 1, -1]
```

Initial state:

```text
sum = 0
max_len = 0

mp = {
    0 : -1
}
```

### Iteration 1 — Index 0

Current element:

```text
nums[0] = 0
```

Transform:

$$
sum=0-1=-1
$$

Prefix sum `-1` does not exist.

Store:

```cpp
mp[-1] = 0;
```

Maximum length remains `0`.

### Iteration 2 — Index 1

Current element:

```text
nums[1] = 1
```

Update:

$$
sum=-1+1=0
$$

Prefix sum `0` already exists at index `-1`.

Calculate:

$$
Length=1-(-1)=2
$$

Update:

```cpp
max_len = 2;
```

### Iteration 3 — Index 2

Current element:

```text
nums[2] = 0
```

Update:

$$
sum=0-1=-1
$$

Prefix sum `-1` already exists at index `0`.

Calculate:

$$
Length=2-0=2
$$

Maximum remains `2`.

Do not overwrite the first occurrence of `-1`.

### Iteration 4 — Index 3

Current element:

```text
nums[3] = 1
```

Update:

$$
sum=-1+1=0
$$

Prefix sum `0` already exists at index `-1`.

Calculate:

$$
Length=3-(-1)=4
$$

Update:

```cpp
max_len = 4;
```

### Iteration 5 — Index 4

Current element:

```text
nums[4] = 1
```

Update:

$$
sum=0+1=1
$$

Prefix sum `1` does not exist.

Store:

```cpp
mp[1] = 4;
```

Maximum remains `4`.

### Iteration 6 — Index 5

Current element:

```text
nums[5] = 0
```

Update:

$$
sum=1-1=0
$$

Prefix sum `0` already exists at index `-1`.

Calculate:

$$
Length=5-(-1)=6
$$

Update:

```cpp
max_len = 6;
```

### Final Dry Run Table

| Index | Element | Prefix Sum | First Occurrence | Current Length | max_len |
|---:|---:|---:|---:|---:|---:|
| 0 | 0 | -1 | 0 (new) | — | 0 |
| 1 | 1 | 0 | -1 | 2 | 2 |
| 2 | 0 | -1 | 0 | 2 | 2 |
| 3 | 1 | 0 | -1 | 4 | 4 |
| 4 | 1 | 1 | 4 (new) | — | 4 |
| 5 | 0 | 0 | -1 | 6 | 6 |

**Final Output:**

$$
\boxed{6}
$$

The entire array contains three zeros and three ones.

---

## 12. Proof of Correctness

We can prove that the algorithm returns the maximum length of a balanced subarray.

### Lemma 1: A balanced subarray has transformed sum zero.

Every zero contributes `-1`, and every one contributes `+1`.

For a subarray containing $Z$ zeros and $O$ ones:

$$
\text{Subarray Sum}=O-Z
$$

The subarray is balanced exactly when:

$$
O=Z
$$

Therefore:

$$
\text{Subarray Sum}=0
$$

### Lemma 2: Equal prefix sums identify a balanced subarray.

For indices $i<j$:

$$
\text{Subarray Sum}(i+1,j)=P[j]-P[i]
$$

If:

$$
P[j]=P[i]
$$

Then:

$$
\text{Subarray Sum}(i+1,j)=0
$$

By Lemma 1, this subarray is balanced.

### Lemma 3: The First Occurrence Produces the Longest Subarray for a Given Ending Index.

For a fixed current index $j$, every earlier occurrence $i$ of the same prefix sum produces a valid balanced subarray.

Its length is:

$$
j-i
$$

This length is maximized by choosing the smallest possible $i$, which is the first occurrence.

### Theorem

The algorithm examines every index and checks the earliest occurrence of its current prefix sum.

By Lemmas 1 and 2, every candidate it considers is balanced.

By Lemma 3, it considers the longest balanced subarray ending at each index.

Since it takes the maximum over all indices, it returns the globally maximum balanced subarray length.

Therefore, the algorithm is correct.

---

## 13. Complexity Analysis

Let $n$ be the number of elements in the array.

### Time Complexity

We traverse the array exactly once.

Each iteration performs:

- One transformation: O(1).
- One HashMap lookup: O(1) expected.
- At most one HashMap insertion: O(1) expected.

Therefore:

$$
\boxed{\text{Expected Time Complexity}=O(n)}
$$

HashMap operations can theoretically degrade under severe collisions, but expected constant-time operations give expected linear overall complexity.

### Space Complexity

In the worst case, all prefix sums can be different.

The HashMap can contain up to $n+1$ entries.

Therefore:

$$
\boxed{\text{Auxiliary Space Complexity}=O(n)}
$$

The algorithm does not modify the input array.

---

## 14. Edge Cases

| Input | Output | Explanation |
|---|---:|---|
| `[0,1]` | 2 | Entire array is balanced |
| `[0,1,0]` | 2 | Maximum balanced subarray has length 2 |
| `[0,0,0,1,1]` | 4 | `[0,0,1,1]` is balanced |
| `[1,1,1,1]` | 0 | No balanced subarray exists |
| `[0,0,0,0]` | 0 | No balanced subarray exists |
| `[0,1,0,1]` | 4 | Entire array is balanced |

### Important Edge Cases

**Case 1: Balanced subarray starts at index zero**

Handled by:

```cpp
mp[0] = -1;
```

**Case 2: No balanced subarray exists**

`max_len` remains zero.

**Case 3: Prefix sum repeats multiple times**

Always use the first occurrence. Never overwrite it.

---

## 15. Common Mistakes and Interview Traps

### Mistake 1: Not Transforming Zero Into Negative One

Incorrect:

```cpp
if(nums[i] == 0) {
    sum += 0;
}
else {
    sum += 1;
}
```

This does not represent the difference between zeros and ones.

Correct:

```cpp
if(nums[i] == 0) {
    sum--;
}
else {
    sum++;
}
```

### Mistake 2: Incorrect Initial Index

Incorrect:

```cpp
mp[0] = 0;
```

Correct:

```cpp
mp[0] = -1;
```

The empty prefix is located before index zero.

### Mistake 3: Updating the HashMap Unconditionally

Incorrect:

```cpp
mp[sum] = i;
```

This overwrites the earliest occurrence and can reduce the maximum length.

Correct:

```cpp
if(mp.find(sum) == mp.end()) {
    mp[sum] = i;
}
```

### Mistake 4: Incorrect Length Calculation

Incorrect:

```cpp
i - mp[sum] + 1
```

Correct:

```cpp
i - mp[sum]
```

The stored index represents the prefix immediately before the candidate subarray.

### Mistake 5: Storing Frequency Instead of First Index

In LC 525, we need maximum length, not the number of valid subarrays.

Therefore, storing frequency is not the right information for this particular solution.

---

## 16. Comparison With Other Prefix Sum Problems

This is one of the most important interview concepts.

Different Prefix Sum problems use the same mathematical foundation but store different information in the HashMap.

| Problem | HashMap Value | Lookup Condition | Objective |
|---|---|---|---|
| LC 560 — Subarray Sum Equals K | Frequency | `sum - k` | Count subarrays |
| LC 974 — Subarray Sums Divisible by K | Frequency | Same remainder | Count subarrays |
| LC 525 — Contiguous Array | First index | Same prefix sum | Maximum length |
| LC 930 — Binary Subarrays With Sum | Frequency | `sum - goal` | Count subarrays |

### Frequency-Based Approach

When the question asks us to count the number of valid subarrays:

```cpp
count += mp[target];
mp[sum]++;
```

### First-Occurrence-Based Approach

When the question asks us to maximize the length:

```cpp
if(mp.find(sum) != mp.end()) {
    max_len = max(max_len, i - mp[sum]);
}
else {
    mp[sum] = i;
}
```

**Interview takeaway:** The HashMap is not tied to one fixed strategy. Its stored value depends on what the problem asks us to calculate.

---

## 17. Important Interview Follow-up Questions

### Q1. Why do we convert 0 into -1?

To convert the equal-frequency condition into a zero-sum condition.

### Q2. Why does the same prefix sum indicate a balanced subarray?

Because the difference between two equal prefix sums is zero. After transformation, zero sum means equal zeros and ones.

### Q3. Why do we store the first occurrence?

For a fixed ending index, the earliest starting boundary gives the maximum subarray length.

### Q4. Why do we initialize `mp[0] = -1`?

To correctly calculate the length of a balanced subarray beginning at index zero.

### Q5. Can we use a sliding window?

A standard sliding window is not directly suitable because the balance condition is not monotonic.

Extending or shrinking a window does not guarantee that we move consistently toward the desired balance.

Prefix Sum + HashMap is the appropriate technique.

### Q6. What if the array contains values other than 0 and 1?

The specific transformation `0 → -1` and `1 → +1` is designed for this binary problem. For other values, we must derive a transformation matching the required subarray condition.

### Q7. What is the difference between LC 525 and LC 560?

LC 560 asks for the number of subarrays whose sum equals a target $k$. We store prefix sum frequencies.

LC 525 asks for the maximum length of a zero-sum transformed subarray. We store the first index of each prefix sum.

---

## 18. Pattern Recognition — When Should You Use This Approach?

Look for these signals in a problem:

- Find the longest contiguous subarray.
- Equal number of two types of elements.
- Difference between two frequencies should be zero.
- A transformed subarray must have sum zero.
- Repeated prefix sums can identify valid subarrays.

### General Mathematical Pattern

If:

$$
P[j]=P[i]
$$

Then:

$$
P[j]-P[i]=0
$$

Therefore:

$$
\text{Subarray Sum}(i+1,j)=0
$$

The same-prefix-sum technique can be applied to many other problems involving zero-sum subarrays.

---

## 19. Final Revision Cheat Sheet

### Core Transformation

$$
0\rightarrow -1
$$

$$
1\rightarrow +1
$$

### Core Observation

$$
\boxed{\text{Equal Zeros and Ones}\iff\text{Subarray Sum}=0}
$$

### Prefix Sum Property

$$
P[j]=P[i]\implies P[j]-P[i]=0
$$

### Subarray Length

$$
\boxed{\text{Length}=j-i}
$$

### HashMap Structure

```cpp
unordered_map<int, int> mp;
```

```text
Key   = Prefix Sum
Value = First Occurrence Index
```

### Initialization

```cpp
mp[0] = -1;
```

### Update Logic

```cpp
if(mp.find(sum) != mp.end()) {
    max_len = max(max_len, i - mp[sum]);
}
else {
    mp[sum] = i;
}
```

### Complexity

$$
\boxed{\text{Expected Time}=O(n)}
$$

$$
\boxed{\text{Space}=O(n)}
$$

---

## 20. One-Line Intuition

**Convert every `0` into `-1` and every `1` into `+1`, then find the maximum distance between two occurrences of the same prefix sum using a HashMap.**

### The Most Important Lesson From LC 525

Prefix Sum + HashMap can solve different kinds of problems depending on what we store:

- **Frequency:** To count valid subarrays.
- **First occurrence:** To maximize subarray length.
- **Remainder frequency:** To count subarrays divisible by a number.

**Never choose the HashMap structure blindly. First understand what the problem asks you to calculate, then decide what information must be stored.**
