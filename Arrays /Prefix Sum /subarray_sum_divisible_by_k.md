# LeetCode 974 — Subarray Sums Divisible by K

**Difficulty:** Medium  
**Topics:** Prefix Sum, HashMap, Mathematics  
**Main Pattern:** Prefix Sum + Remainder Frequency

**Approaches:**
1. Prefix Sum + Nested Loops — O(n²)
2. Prefix Sum + HashMap — O(n) Expected

---

# 1. Problem Understanding

Given an integer array `nums` and an integer `k`, return the total number of non-empty subarrays whose sum is divisible by `k`.

A subarray is a contiguous and non-empty part of an array.

### Example

```cpp
nums = {4, 5, 0, -2, -3, 1};
k = 5;
```

Output:

```cpp
7
```

We need to count all subarrays satisfying:

$$
\text{subarraySum} \bmod k = 0
$$

---

# 2. Approach 1 — Prefix Sum + Nested Loops

**Time Complexity:** O(n²)  
**Auxiliary Space:** O(1)

## Intuition

The brute-force approach calculates the sum of every possible subarray by traversing its elements.

We can optimize this using Prefix Sum.

Instead of calculating every subarray sum from scratch, we first construct a prefix sum array.

## Step 1: Construct Prefix Sum

The prefix sum at index `i` is:

$$
P[i] = \sum_{t=0}^{i} nums[t]
$$

In simple words:

`P[i]` stores the sum of all elements from index `0` to index `i`.

### Code

```cpp
for(int i = 1; i < n; i++){
    nums[i] += nums[i-1];
}
```

### Example

Original array:

```text
[4, 5, 0, -2, -3, 1]
```

Prefix sum array:

```text
[4, 9, 9, 7, 4, 5]
```

## Step 2: Calculate Subarray Sum

Suppose we want the sum of a subarray from index `i` to index `j`.

There are two cases.

**Case 1: When i = 0**

The subarray starts from the beginning, so:

$$
S(i,j) = P[j]
$$

**Case 2: When i > 0**

Subtract the prefix sum before index `i`:

$$
\boxed{S(i,j) = P[j] - P[i-1]}
$$

This allows us to calculate any subarray sum in O(1) time.

## Step 3: Check Divisibility

```cpp
if(sum % k == 0){
    result++;
}
```

If the subarray sum is divisible by `k`, increment the answer.

## My Implementation

```cpp
class Solution {
public:
    int subarraysDivByK(vector<int>& nums, int k) {
        int result = 0;
        int n = nums.size();

        // Step 1: Construct prefix sum in-place
        for(int i = 1; i < n; i++){
            nums[i] += nums[i-1];
        }

        // Step 2: Generate every possible subarray
        for(int i = 0; i < n; i++){
            for(int j = i; j < n; j++){

                int sum = (i == 0)
                          ? nums[j]
                          : nums[j] - nums[i-1];

                // Step 3: Check divisibility
                if(sum % k == 0){
                    result++;
                }
            }
        }

        return result;
    }
};
```

## Dry Run

Input:

```cpp
nums = {4, 5, 0, -2, -3, 1};
k = 5;
```

After constructing prefix sum:

```text
[4, 9, 9, 7, 4, 5]
```

Some valid subarrays:

| Subarray | Sum | Divisible by 5? |
|---|---:|---|
| [4, 5, 0, -2, -3, 1] | 5 | Yes |
| [5] | 5 | Yes |
| [5, 0] | 5 | Yes |
| [5, 0, -2, -3] | 0 | Yes |
| [0] | 0 | Yes |
| [0, -2, -3] | -5 | Yes |
| [-2, -3] | -5 | Yes |

Total valid subarrays = 7.

## Complexity Analysis

There are:

$$
\frac{n(n+1)}{2}
$$

possible non-empty subarrays.

- Prefix Sum Construction: O(n)
- Nested Loops: O(n²)
- Subarray Sum Calculation: O(1) per subarray

Therefore:

$$
\boxed{\text{Time Complexity} = O(n^2)}
$$

Since the original array is modified in-place:

$$
\boxed{\text{Auxiliary Space} = O(1)}
$$

### Limitation

Prefix Sum reduces the cost of calculating an individual subarray sum, but we still visit every possible subarray.

Can we avoid generating all subarrays?

Yes. This leads to the optimal approach.

---

# 3. Approach 2 — Prefix Sum + HashMap (Optimal)

**Time Complexity:** O(n) Expected  
**Auxiliary Space:** O(min(n, k))

## Core Intuition

We can avoid checking every possible subarray.

Instead, we use an important mathematical observation:

**If two prefix sums have the same remainder when divided by k, the subarray between them is divisible by k.**

This observation is the foundation of the optimal solution.

## Step 1: Understand the Mathematical Derivation

Consider an array:

```text
[a, b, c, d, e, f]
```

Suppose we calculate two prefix sums:

$$
P_1 = a+b+c
$$

$$
P_2 = a+b+c+d+e
$$

The subarray between these two prefix sums is:

$$
S = P_2-P_1
$$

Therefore:

$$
S = d+e
$$

Now suppose both prefix sums have the same remainder when divided by `k`.

Let their common remainder be `r`.

$$
P_1 \bmod k = r
$$

$$
P_2 \bmod k = r
$$

According to the division algorithm, we can write:

$$
P_1 = q_1k+r
$$

$$
P_2 = q_2k+r
$$

Subtract the two equations:

$$
P_2-P_1 = (q_2k+r)-(q_1k+r)
$$

Simplifying:

$$
P_2-P_1 = q_2k-q_1k
$$

$$
P_2-P_1 = (q_2-q_1)k
$$

Since the difference is a multiple of `k`:

$$
\boxed{(P_2-P_1)\bmod k=0}
$$

But `P₂ - P₁` is the sum of the subarray between those prefix sums.

Therefore:

$$
\boxed{
P_1\bmod k=P_2\bmod k
\implies
\text{subarraySum}\bmod k=0
}
$$

**Final conclusion:** If two prefix sums have the same remainder, the subarray between them is divisible by `k`.

This is the most important mathematical concept in this problem.

---

## Step 2: Why Do We Need a HashMap?

We need to remember how many times each remainder has appeared previously.

For this, we use:

```cpp
unordered_map<int, int> mp;
```

The hashmap stores:

| Key | Value |
|---|---|
| Prefix Sum Remainder | Frequency |

For example:

```cpp
mp[2] = 3;
```

This means remainder `2` has appeared three times previously.

Now suppose the current prefix sum also has remainder `2`.

Each of those three previous prefix sums forms a valid subarray ending at the current index.

Therefore:

```cpp
result += mp[rem];
```

We add the entire frequency, not just one.

### Mathematical Representation

Let:

$$
freq[r] = \text{Number of previous prefix sums with remainder }r
$$

When the current remainder is `r`:

$$
\boxed{result = result + freq[r]}
$$

After counting, update its frequency:

$$
\boxed{freq[r] = freq[r]+1}
$$

---

## Step 3: Why Do We Initialize mp[0] = 1?

This is an extremely important interview concept.

Consider:

```cpp
nums = {5, 0, 0};
k = 5;
```

The first prefix sum is:

$$
P[0]=5
$$

Its remainder is:

$$
5\bmod 5=0
$$

The subarray `[5]` itself is divisible by 5.

But how will we count it?

We need to consider an empty prefix sum before the array starts.

Therefore, initialize:

```cpp
mp[0] = 1;
```

Mathematically, this represents:

$$
P[-1]=0
$$

Now the first prefix sum with remainder zero can match this initial empty prefix.

**Remember:** `mp[0] = 1` handles all valid subarrays that begin at index zero.

---

# 4. My Optimal Implementation

```cpp
class Solution {
public:
    int subarraysDivByK(vector<int>& nums, int k) {

        int n = nums.size();

        unordered_map<int, int> mp;

        // Empty prefix sum
        mp[0] = 1;

        int result = 0;
        int sum = 0;

        for(int i = 0; i < n; i++){

            // Calculate running prefix sum
            sum += nums[i];

            // Normalize negative remainder
            int rem = ((sum % k) + k) % k;

            // Count previous prefix sums with same remainder
            if(mp.find(rem) != mp.end()){
                result += mp[rem];
            }

            // Store current remainder frequency
            mp[rem] += 1;
        }

        return result;
    }
};
```

## Line-by-Line Explanation

### 1. Initialize the HashMap

```cpp
unordered_map<int, int> mp;
mp[0] = 1;
```

Stores frequencies of previously encountered remainders.

The initial zero represents the empty prefix sum.

### 2. Maintain Running Prefix Sum

```cpp
sum += nums[i];
```

At every index, calculate the prefix sum up to that position.

Mathematically:

$$
P[i]=P[i-1]+nums[i]
$$

### 3. Calculate the Remainder

```cpp
int rem = ((sum % k) + k) % k;
```

Calculates a normalized remainder between `0` and `k-1`.

### 4. Check Previous Occurrences

```cpp
if(mp.find(rem) != mp.end()){
    result += mp[rem];
}
```

If the same remainder has appeared previously, every previous occurrence forms a valid subarray ending at the current index.

### 5. Update Frequency

```cpp
mp[rem] += 1;
```

Record the current prefix sum remainder for future iterations.

**Important:** Always count previous occurrences before inserting the current occurrence. Otherwise, an empty subarray could be counted.

---

# 5. Negative Remainder Handling

One of the most important details in this implementation is:

```cpp
int rem = ((sum % k) + k) % k;
```

Why do we need this?

In C++, the modulo operator can return a negative remainder.

For example:

```cpp
-2 % 5 = -2
```

But mathematically, we want normalized remainders in the range:

$$
0\le rem<k
$$

Therefore, we use:

$$
\boxed{rem=((sum\%k)+k)\%k}
$$

### Example

Suppose:

$$
sum=-2,\quad k=5
$$

Then:

$$
rem=((-2\%5)+5)\%5
$$

$$
rem=(-2+5)\%5
$$

$$
\boxed{rem=3}
$$

This ensures that mathematically equivalent remainders are represented by the same hashmap key.

**Important:** This expression normalizes negative remainders; it does not sort them.

---

# 6. Complete Dry Run — Optimal Approach

Input:

```cpp
nums = {4, 5, 0, -2, -3, 1};
k = 5;
```

Initial state:

```cpp
mp = {{0, 1}};
sum = 0;
result = 0;
```

| i | nums[i] | Prefix Sum | Remainder | Previous Frequency | Result |
|---:|---:|---:|---:|---:|---:|
| 0 | 4 | 4 | 4 | 0 | 0 |
| 1 | 5 | 9 | 4 | 1 | 1 |
| 2 | 0 | 9 | 4 | 2 | 3 |
| 3 | -2 | 7 | 2 | 0 | 3 |
| 4 | -3 | 4 | 4 | 3 | 6 |
| 5 | 1 | 5 | 0 | 1 | 7 |

Let's understand the important iterations.

### Iteration 1 — Index 1

Current prefix sum:

$$
sum=4+5=9
$$

Current remainder:

$$
rem=9\bmod 5=4
$$

Previously, remainder `4` appeared once.

Therefore:

```cpp
result += mp[4];
```

We add one valid subarray:

```text
[5]
```

### Iteration 2 — Index 2

Current prefix sum:

$$
sum=9+0=9
$$

Current remainder:

$$
rem=9\bmod 5=4
$$

Previously, remainder `4` appeared twice.

Therefore:

```cpp
result += mp[4];
```

We add two valid subarrays:

```text
[5, 0]
[0]
```

### Iteration 4 — Index 4

Current prefix sum:

$$
sum=4
$$

Current remainder:

$$
rem=4\bmod 5=4
$$

Previously, remainder `4` appeared three times.

Therefore, we add three new valid subarrays.

### Final Answer

$$
\boxed{result=7}
$$

---

# 7. Why Must We Update the Map After Counting?

Correct order:

```cpp
if(mp.find(rem) != mp.end()){
    result += mp[rem];
}

mp[rem]++;
```

First, count previous occurrences.

Then insert the current occurrence.

If we insert first, the current prefix sum can match itself.

That would represent an empty subarray, which is not allowed.

**Rule:** Only previous prefix sums should be considered.

---

# 8. Comparison of Both Approaches

| Feature | Approach 1 | Approach 2 |
|---|---|---|
| Technique | Prefix Sum + Nested Loops | Prefix Sum + HashMap |
| Subarray generation | Every possible subarray | No explicit generation |
| Main idea | Calculate each subarray sum | Match equal remainders |
| Time | O(n²) | O(n) expected |
| Auxiliary Space | O(1) | O(min(n, k)) |
| Negative numbers | Supported | Supported with normalization |
| Implementation | Simple | More optimized |

### Why Is the Second Approach Better?

In Approach 1, we calculate the sum of every possible subarray.

In Approach 2, we avoid generating subarrays completely.

Instead, we remember previous prefix sum remainders and directly count how many valid subarrays end at the current index.

This reduces expected time complexity from:

$$
O(n^2)\longrightarrow O(n)
$$

---

# 9. Complexity Analysis

Let:

- `n` = number of elements.
- `k` = divisor.
- `d` = number of distinct remainders stored.

Since valid normalized remainders lie between `0` and `k-1`:

$$
d\leq \min(n+1,k)
$$

### Approach 1

| Operation | Complexity |
|---|---|
| Prefix Sum | O(n) |
| Nested Loops | O(n²) |
| Subarray Sum | O(1) |

Total:

$$
\boxed{\text{Time}=O(n^2)}
$$

$$
\boxed{\text{Auxiliary Space}=O(1)}
$$

### Approach 2

| Operation | Complexity |
|---|---|
| Running Prefix Sum | O(n) |
| Remainder Calculation | O(1) |
| HashMap Lookup | O(1) expected |
| HashMap Update | O(1) expected |

Total expected time:

$$
\boxed{\text{Time}=O(n)}
$$

Auxiliary space:

$$
\boxed{\text{Space}=O(\min(n,k))}
$$

Hashmap operations have O(n) worst-case total time complexity under pathological collision behavior, so O(n) is the expected complexity.

---

# 10. Common Interview Traps

### Trap 1: Using result++ Instead of result += mp[rem]

Incorrect:

```cpp
result++;
```

Correct:

```cpp
result += mp[rem];
```

Reason: Multiple previous prefix sums can have the same remainder.

### Trap 2: Forgetting mp[0] = 1

This causes subarrays starting at index zero to be missed.

### Trap 3: Ignoring Negative Remainders

Use:

```cpp
int rem = ((sum % k) + k) % k;
```

### Trap 4: Updating the Map Before Counting

This can count an empty subarray.

### Trap 5: Confusing Prefix Sum with Subarray Sum

Remember:

$$
\text{Subarray Sum}=P[j]-P[i-1]
$$

The hashmap stores prefix sum remainders, not individual subarray sums.

---

# 11. Connection to Other Important DSA Problems

This problem belongs to a broader family of Prefix Sum + HashMap problems.

| Problem | LeetCode | Main Pattern |
|---|---:|---|
| Subarray Sum Equals K | 560 | Prefix Sum + Frequency |
| Subarray Sums Divisible by K | 974 | Prefix Sum + Remainder |
| Contiguous Array | 525 | Prefix Sum + Transformed Values |
| Continuous Subarray Sum | 523 | Prefix Sum + Remainder + Length |
| Binary Subarrays With Sum | 930 | Prefix Sum + Frequency |

### Important Difference: LC 560 vs LC 974

**LC 560 — Subarray Sum Equals K**

We need:

$$
P[j]-P[i-1]=K
$$

Rearranging:

$$
P[i-1]=P[j]-K
$$

So we look for:

```cpp
mp[prefixSum - k]
```

**LC 974 — Subarray Sums Divisible by K**

We need:

$$
(P[j]-P[i-1])\bmod k=0
$$

Rearranging:

$$
P[j]\bmod k=P[i-1]\bmod k
$$

So we look for:

```cpp
mp[remainder]
```

**This is the key difference between the two problems.**

---

# 12. Pattern Recognition — When to Use This Approach?

Whenever you see:

- Count subarrays.
- Subarray sum divisible by `k`.
- Subarray sum has a specific remainder.
- Negative numbers are present.
- Need to optimize O(n²) subarray enumeration.

Think about:

$$
\boxed{\text{Prefix Sum + HashMap}}
$$

The general principle is:

**Use information from previous prefix sums to count valid subarrays ending at the current index.**

---

# 13. Final Revision Cheat Sheet

| Concept | Remember |
|---|---|
| Problem | LC 974 |
| Pattern | Prefix Sum + HashMap |
| Approach 1 | Prefix Sum + Nested Loops |
| Approach 1 Time | O(n²) |
| Approach 1 Space | O(1) |
| Approach 2 | Prefix Sum + Remainder Frequency |
| Approach 2 Time | O(n) expected |
| Approach 2 Space | O(min(n, k)) |
| Initial Condition | `mp[0] = 1` |
| Negative Remainder | `((sum % k) + k) % k` |
| Count | `result += mp[rem]` |
| Update | `mp[rem]++` |

### Three Formulas You Must Remember

**1. Subarray Sum:**

$$
\boxed{S(i,j)=P[j]-P[i-1]}
$$

**2. Equal Remainder Property:**

$$
\boxed{
P[a]\bmod k=P[b]\bmod k
\implies
(P[b]-P[a])\bmod k=0
}
$$

**3. Negative-Safe Remainder:**

$$
\boxed{rem=((sum\%k)+k)\%k}
$$

---

## Final Learning

Your first approach taught you how Prefix Sum reduces repeated subarray sum calculations.

Your second approach introduced a much more powerful idea: instead of checking every subarray, use a hashmap to remember previous prefix sum remainders.

The biggest takeaway:

**Don't generate all subarrays when their mathematical properties allow you to count them directly.**

This is the fundamental optimization behind Prefix Sum + HashMap problems.
