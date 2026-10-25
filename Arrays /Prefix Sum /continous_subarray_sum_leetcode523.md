# LeetCode 523 — Continuous Subarray Sum

**Pattern:** Prefix Sum + HashMap + Modulo  
**Difficulty:** Medium  
**Time Complexity:** O(n) average  
**Space Complexity:** O(n)

---

## 1. Problem Understanding

Given an integer array `nums` and an integer `k`, determine whether there exists a contiguous subarray:

1. Having a length of at least 2.
2. Whose sum is a multiple of `k`.

Return `true` if such a subarray exists; otherwise return `false`.

### Example 1

```cpp
nums = {23, 2, 4, 6, 7}
k = 6
```

Output:

```cpp
true
```

Explanation:

The subarray `[2, 4]` has:

- Sum = 2 + 4 = 6
- Length = 2
- 6 is divisible by 6.

Therefore, answer is `true`.

### Example 2

```cpp
nums = {23, 2, 6, 4, 7}
k = 13
```

Output:

```cpp
false
```

No contiguous subarray of length at least 2 has a sum divisible by 13.

---

## 2. Brute Force Approach

The simplest approach is to generate every possible subarray and calculate its sum.

```cpp
class Solution {
public:
    bool checkSubarraySum(vector<int>& nums, int k) {
        int n = nums.size();

        for(int i = 0; i < n; i++) {
            int sum = 0;

            for(int j = i; j < n; j++) {
                sum += nums[j];

                if(j - i + 1 >= 2 && sum % k == 0) {
                    return true;
                }
            }
        }

        return false;
    }
};
```

### Complexity

- Time: O(n²)
- Auxiliary Space: O(1)

We can improve this using Prefix Sum + HashMap.

---

## 3. Core Concept — Prefix Sum

Prefix Sum represents the cumulative sum of elements from index `0` to the current index.

For an array:

```cpp
nums = {23, 2, 4, 6, 7}
```

Prefix sums:

| Index | Element | Prefix Sum |
|---:|---:|---:|
| 0 | 23 | 23 |
| 1 | 2 | 25 |
| 2 | 4 | 29 |
| 3 | 6 | 35 |
| 4 | 7 | 42 |

Let:

- `P[i]` = Prefix sum ending at index `i`.
- `P[j]` = Prefix sum ending at index `j`.

The sum of subarray `(i+1 ... j)` is:

$$
SubarraySum = P[j] - P[i]
$$

We need this sum to be divisible by `k`.

Therefore:

$$
(P[j]-P[i]) \bmod k = 0
$$

Rearranging:

$$
P[j] \bmod k = P[i] \bmod k
$$

**Important conclusion:**

If two prefix sums have the same remainder when divided by `k`, the sum of elements between those two prefix positions is divisible by `k`.

This is the mathematical foundation of LC 523.

---

## 4. Why Does Same Remainder Work?

Consider:

```cpp
k = 6

Prefix Sum 1 = 23
Prefix Sum 2 = 29
```

Their remainders:

$$
23 \bmod 6 = 5
$$

$$
29 \bmod 6 = 5
$$

Both prefix sums have the same remainder.

Their difference:

$$
29 - 23 = 6
$$

And:

$$
6 \bmod 6 = 0
$$

Therefore, the subarray between these prefix positions has a sum divisible by 6.

This gives us an efficient way to identify valid subarrays without explicitly calculating every subarray sum.

---

## 5. Why HashMap?

We need to remember previously encountered remainders.

We use:

```cpp
unordered_map<int, int> mp;
```

Here:

- Key = Remainder.
- Value = First index where that remainder appeared.

For example:

```cpp
mp[5] = 0;
```

Means remainder `5` was first encountered at index `0`.

When the same remainder appears again, we calculate the distance between the current index and its first occurrence.

If the distance is at least 2, we have found a valid subarray.

---

## 6. Why Store the First Occurrence Instead of Frequency?

This is one of the most important concepts in this problem.

Compare:

**LC 560 — Subarray Sum Equals K**

We store frequency because we need to count the number of valid subarrays.

**LC 974 — Subarray Sums Divisible by K**

We store frequency because we need to count how many prefix sums have the required remainder.

**LC 523 — Continuous Subarray Sum**

We only need to determine whether at least one valid subarray exists.

Therefore, we store the earliest index of each remainder.

Why the earliest index?

Suppose remainder `5` occurs at:

```cpp
Index = 0
Index = 3
Index = 6
```

If the current index is `7`:

- Distance from index 0 = 7
- Distance from index 3 = 4
- Distance from index 6 = 1

The earliest index gives us the longest possible subarray.

It also gives the best chance of satisfying the minimum length requirement.

**Rule: Never overwrite the first occurrence of a remainder.**

---

## 7. The Most Important Initialization

```cpp
mp[0] = -1;
```

Why?

Consider:

```cpp
nums = {6, 2}
k = 6
```

At index `0`:

```cpp
sum = 6
remainder = 0
```

The remainder `0` has already been initialized at index `-1`.

Therefore:

```cpp
i - mp[0] = 0 - (-1) = 1
```

This represents the subarray `[6]`, whose length is only 1.

We cannot return true yet.

At index `1`:

```cpp
sum = 8
remainder = 2
```

No valid repeated remainder exists, so the answer is false.

Now consider:

```cpp
nums = {6, 0}
k = 6
```

At index `1`, the prefix sum remains `6`, so remainder is still `0`.

Distance:

```cpp
1 - (-1) = 2
```

The subarray `[6, 0]` has length 2 and sum 6.

Answer = `true`.

**Interpretation of `mp[0] = -1`:**

It represents an imaginary prefix sum of zero before the array begins.

This allows us to correctly handle subarrays starting at index 0.

---

## 8. Minimum Subarray Length Condition

The problem requires:

$$
Length \geq 2
$$

If a remainder repeats at indices `j` and `i`, where `j > i`, then:

$$
Length = j-i
$$

Therefore:

```cpp
if(i - mp[rem] >= 2)
```

This condition is essential.

Without it, a single element divisible by `k` could incorrectly produce `true`.

---

## 9. Complete Optimized C++ Solution

```cpp
class Solution {
public:
    bool checkSubarraySum(vector<int>& nums, int k) {

        int n = nums.size();

        unordered_map<int,int> mp;

        // Imaginary prefix sum before index 0
        mp[0] = -1;

        int sum = 0;

        if(n < 2) {
            return false;
        }

        for(int i = 0; i < n; i++) {

            // Calculate prefix sum
            sum += nums[i];

            // Calculate remainder
            int rem = sum % k;

            // Check whether remainder appeared before
            if(mp.find(rem) != mp.end()) {

                // Verify minimum subarray length
                if(i - mp[rem] >= 2) {
                    return true;
                }
            }

            else {
                // Store only the first occurrence
                mp[rem] = i;
            }
        }

        return false;
    }
};
```

This implementation follows the problem's constraints, where `k >= 1`.

---

## 10. Complete Line-by-Line Dry Run

Input:

```cpp
nums = {23, 2, 4, 6, 7}
k = 6
```

Initial state:

```cpp
mp[0] = -1;
sum = 0;
```

### Iteration 1 — i = 0

```cpp
sum = 0 + 23 = 23
rem = 23 % 6 = 5
```

Remainder `5` is not present.

Store:

```cpp
mp[5] = 0;
```

### Iteration 2 — i = 1

```cpp
sum = 23 + 2 = 25
rem = 25 % 6 = 1
```

Remainder `1` is not present.

Store:

```cpp
mp[1] = 1;
```

### Iteration 3 — i = 2

```cpp
sum = 25 + 4 = 29
rem = 29 % 6 = 5
```

Remainder `5` already exists.

Previously stored index:

```cpp
mp[5] = 0;
```

Calculate length:

```cpp
2 - 0 = 2
```

Since:

```cpp
2 >= 2
```

Return:

```cpp
true;
```

We found the subarray:

```cpp
{2, 4}
```

Its sum is 6, which is divisible by 6.

The algorithm terminates immediately.

### HashMap state

| Remainder | First occurrence index |
|---:|---:|
| 0 | -1 |
| 5 | 0 |
| 1 | 1 |

---

## 11. Complexity Analysis

Let `n` be the size of the array.

### Time Complexity

We traverse the array exactly once.

Each iteration performs:

- Prefix sum calculation: O(1)
- Modulo operation: O(1)
- HashMap lookup: O(1) average
- HashMap insertion: O(1) average

Therefore:

$$
Time = O(n) \text{ average}
$$

HashMap operations have O(n) worst-case behavior in pathological collision scenarios, so the strict worst-case time can be higher.

### Space Complexity

In the worst case, every prefix sum can produce a different remainder.

The HashMap can store up to `n` distinct remainders.

$$
Space = O(n)
$$

---

## 12. Important Edge Cases

| Input | k | Expected Output | Reason |
|---|---:|---|---|
| `{0}` | 1 | false | Length less than 2 |
| `{0,0}` | 1 | true | Sum = 0 |
| `{6,2}` | 6 | false | Only valid multiple candidate has length 1 |
| `{6,0}` | 6 | true | Sum = 6, length = 2 |
| `{23,2,4,6,7}` | 6 | true | Subarray `[2,4]` |
| `{23,2,6,4,7}` | 13 | false | No valid subarray |
| `{1,2,3}` | 5 | true | Subarray `[2,3]` has sum 5 |

**Important constraint check:** In this LeetCode problem, `1 <= k <= 2^31 - 1`. Therefore, modulo by zero is not a valid input scenario. The implementation does not need a `k == 0` branch.

Also, the problem guarantees that the total sum does not exceed `2^31 - 1`, so `int` is sufficient for the prefix sum under the stated constraints.

---

## 13. Common Mistakes

### Mistake 1 — Forgetting the initial remainder

Incorrect:

```cpp
unordered_map<int,int> mp;
```

Correct:

```cpp
mp[0] = -1;
```

Without initialization, subarrays starting from index 0 may not be detected.

### Mistake 2 — Checking only whether remainder repeats

Incorrect:

```cpp
if(mp.find(rem) != mp.end()) {
    return true;
}
```

Why incorrect?

The repeated remainder might occur at a distance of only 1.

Correct:

```cpp
if(mp.find(rem) != mp.end()) {
    if(i - mp[rem] >= 2) {
        return true;
    }
}
```

### Mistake 3 — Overwriting the first occurrence

Incorrect:

```cpp
mp[rem] = i;
```

on every iteration.

Correct:

```cpp
if(mp.find(rem) == mp.end()) {
    mp[rem] = i;
}
```

We must preserve the earliest index.

### Mistake 4 — Storing frequency instead of index

A frequency map does not directly provide the index needed to validate the subarray length.

Store the first index instead.

---

## 14. Interview-Level Mathematical Proof

Suppose two prefix sums have equal remainders:

$$
P[j] \bmod k = P[i] \bmod k
$$

Then:

$$
(P[j]-P[i]) \bmod k = 0
$$

Since:

$$
SubarraySum(i+1,j)=P[j]-P[i]
$$

The subarray sum is divisible by `k`.

The algorithm additionally checks:

$$
j-i \geq 2
$$

Therefore, every subarray accepted by the algorithm:

1. Has a sum divisible by `k`.
2. Has length at least 2.

Conversely, any valid subarray creates two prefix sums with equal remainders and a distance of at least 2. Since the algorithm preserves the earliest index for every remainder, it will detect that valid subarray when the later prefix is encountered.

Hence, the algorithm is correct.

---

## 15. Connection With Other Prefix Sum Problems

| Problem | What HashMap Stores | Objective |
|---|---|---|
| LC 560 — Subarray Sum Equals K | Prefix Sum → Frequency | Count subarrays |
| LC 974 — Subarray Sums Divisible by K | Remainder → Frequency | Count subarrays |
| LC 525 — Contiguous Array | Balance → First Index | Maximum length |
| LC 930 — Binary Subarrays With Sum | Prefix Sum → Frequency | Count subarrays |
| LC 523 — Continuous Subarray Sum | Remainder → First Index | Check existence |

### Pattern Recognition

Ask yourself:

- Need number of subarrays? → Store frequency.
- Need longest subarray? → Store first occurrence.
- Need to check existence with a minimum length? → Store first occurrence and validate distance.
- Need divisibility by `k`? → Think about prefix sum modulo `k`.

---

## 16. Interview Follow-Up Questions

**Q1. Why do equal prefix remainders imply divisibility?**

Because the difference between two prefix sums is the subarray sum. Equal remainders mean their difference is divisible by `k`.

**Q2. Why is `mp[0] = -1` necessary?**

It represents the prefix sum before the array starts, allowing subarrays beginning at index 0 to be considered.

**Q3. Why do we store the first index?**

The earliest index maximizes the length of a subarray ending at the current index.

**Q4. Why is the minimum length condition important?**

Because a single element divisible by `k` is not sufficient. The problem requires at least two elements.

**Q5. Can we solve this with a frequency map?**

A frequency map alone does not preserve the index required for checking the minimum length. An index-based map is the natural approach.

---

## 17. Final Revision Cheat Sheet

```cpp
unordered_map<int,int> mp;
mp[0] = -1;

int sum = 0;

for(int i = 0; i < n; i++) {

    sum += nums[i];

    int rem = sum % k;

    if(mp.find(rem) != mp.end()) {

        if(i - mp[rem] >= 2) {
            return true;
        }
    }
    else {
        mp[rem] = i;
    }
}

return false;
```

**Remember these 5 things:**

1. Prefix Sum helps calculate subarray sums through differences.
2. Equal remainders imply the difference is divisible by `k`.
3. Store remainder → first index.
4. Initialize `mp[0] = -1`.
5. Validate `i - mp[rem] >= 2`.

**Core Pattern:**

`Prefix Sum + Modulo + HashMap (First Occurrence)`

**One-line intuition:** If two prefix sums have the same remainder when divided by `k`, their difference is divisible by `k`; check whether the distance between their indices is at least 2.
