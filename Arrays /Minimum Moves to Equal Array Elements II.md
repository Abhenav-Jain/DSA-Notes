# 🚀 LeetCode 462: Minimum Moves to Equal Array Elements II

> **Difficulty:** Medium | **Topics:** Array, Math, Sorting, Quickselect | **Pattern:** *Median minimizes sum of absolute deviations*

---

## 📑 Table of Contents
1. [Problem Statement](#1-problem-statement)
2. [One-Line Intuition](#2-one-line-intuition)
3. [Why Median? (Proofs)](#3-why-median-proofs)
4. [Pairing Insight](#4-pairing-insight-median-ki-value-bhi-nahi-chahiye)
5. [Approaches and Codes](#5-approaches-and-codes)
6. [Dry Runs](#6-dry-runs)
7. [Complexity Comparison](#7-complexity-comparison)
8. [453 vs 462: Pattern Notes](#8-453-vs-462-pattern-notes)
9. [Gotchas and Edge Cases](#9-gotchas-and-edge-cases)
10. [Interview Q&A](#10-interview-qa)
11. [Similar Problems](#11-similar-problems)
12. [Cheat Sheet](#12-cheat-sheet)

---

## 1. Problem Statement

Integer array `nums` of size `n` diya hai. Ek **move** mein tum **kisi ek element ko 1 se increment ya decrement** kar sakte ho. Sabhi elements ko equal karne ke liye **minimum moves** return karo.

### Examples

| Input | Output | Explanation |
|---|---|---|
| `[1,2,3]` | `2` | `[1,2,3] → [2,2,3] → [2,2,2]` |
| `[1,10,2,9]` | `16` | Target 2 ya 9 (dono middle elements) |

### Constraints
- `1 <= n <= 10^5`
- `-10^9 <= nums[i] <= 10^9`
- Answer 32-bit integer mein fit hota hai.

---

## 2. One-Line Intuition

> Hume aisa target `t` chahiye jo `Σ abs(nums[i] - t)` ko minimize kare. **Wo target median hai.**

Har element ko `t` tak laane ka cost `abs(nums[i] - t)` hai (ek move = ek step). Elements independent hain, to total cost = sab distances ka sum.

---

## 3. Why Median? (Proofs)

### Proof A: Slope / Shifting argument (best for interviews)

Define `f(t) = Σ abs(nums[i] - t)`.

Agar `t` ko 1 step right le jao:
- Jo elements `t` ke **left (ya barabar)** hain, unki distance **+1** badhti hai.
- Jo elements `t` ke **right** hain, unki distance **-1** ghatti hai.

Net change = `(#left-or-equal) − (#right)`.

- Jab tak `#left-or-equal < #right`, right jaane se cost **ghatti** hai.
- Jab `#left-or-equal ≥ #right`, right jaane se cost **kam nahi hoti**.

Ye balance point wahi hai jahan dono sides mein (lagbhag) barabar elements hon, yaani **median**. `f(t)` convex hai, to ye local minimum hi global minimum hai.

### Proof B: Pairing argument (sabse clean)

Array sorted maan lo. Smallest `a = nums[0]` aur largest `b = nums[n-1]` ko pair karo.

Kisi bhi target `t` ke liye:
```
abs(a - t) + abs(b - t) >= b - a        (triangle inequality)
```
Barabari tab hoti hai jab `a ≤ t ≤ b`.

Ab agla pair `(nums[1], nums[n-2])`, phir `(nums[2], nums[n-3])`, ... Sab pairs ka lower bound `Σ (nums[n-1-i] − nums[i])` hai. Ye bounds tabhi achieve hote hain jab `t` **har pair ke andar** ho. Sabse andar wale pair ke andar `t` ho to baaki sab pairs ke andar bhi hoga (nested intervals). Median exactly sabse andar wale pair mein aata hai. ✔

### Even n mein?
Dono middle elements (`nums[n/2 - 1]` aur `nums[n/2]`) aur unke beech ki **har value** same minimum cost deti hai. Isliye `nums[n/2]` lo ya `nums[(n-1)/2]`, dono sahi.

---

## 4. Pairing Insight (Median ki value bhi nahi chahiye)

Sorted array mein har pair `(nums[i], nums[n-1-i])` ke liye, `nums[i] ≤ mid ≤ nums[n-1-i]` hota hai, to:

```
abs(mid - nums[i]) + abs(mid - nums[n-1-i])
= (mid - nums[i]) + (nums[n-1-i] - mid)
= nums[n-1-i] - nums[i]          ← mid cancel ho gaya
```

**Final formula:**
```
answer = Σ (nums[n-1-i] − nums[i])   for i = 0 .. n/2 − 1
```

> 💡 Ye formula tab bhi valid hai jab array **sirf median pe partition** ho (jaise `nth_element` ke baad), kyunki `i < n/2` ke liye `nums[i] ≤ mid ≤ nums[n-1-i]` phir bhi guaranteed hai. Poora sorted hona zaroori nahi, **median pe partition hona zaroori hai**.

---

## 5. Approaches and Codes

### Approach 1: Sort + Median + abs loop

**Algorithm**
1. Array sort karo.
2. `mid = nums[n/2]`.
3. Har element ke liye `abs(mid - nums[i])` add karo.

```cpp
class Solution {
public:
    int minMoves2(vector<int>& nums) {
        int n = nums.size();
        sort(nums.begin(),nums.end());
        int mid = nums[n/2];
        int cnt = 0;
        for(int i = 0 ; i < n ; i++){
            cnt += abs(mid - nums[i]);
        }
        return cnt;
    }
};
```
**Time:** `O(n log n)` | **Space:** `O(1)`

---

### Approach 2: Sort + Pairing abs (smallest aur largest ek saath)

**Algorithm**
1. Sort karo, `mid = nums[n/2]`.
2. `i = 0 .. n/2 - 1` ke liye index `i` aur `n-1-i` dono ka distance add karo.
3. Odd `n` mein beech ka element median hai, uska cost `0`, skip theek hai.

```cpp
class Solution {
public:
    int minMoves2(vector<int>& nums) {
        int n = nums.size();
        sort(nums.begin(),nums.end());
        int mid = nums[n/2];
        int cnt = 0;
        for(int i = 0 ; i < n/2 ; i++){
            cnt += abs(mid - nums[i]);
            cnt += abs(mid - nums[n-i-1]);
        }
        return cnt;
    }
};
```
**Time:** `O(n log n)` | **Space:** `O(1)`

---

### Approach 3: Sort + Two Pointer (no median needed)

Section 4 ke formula ka direct use.

```cpp
class Solution {
public:
    int minMoves2(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        int l = 0, r = nums.size() - 1;
        long long cnt = 0;
        while (l < r) {
            cnt += nums[r--] - nums[l++];
        }
        return cnt;
    }
};
```
**Time:** `O(n log n)` | **Space:** `O(1)`

---

### Approach 4: Quickselect (`nth_element`) + Pairing abs ⚡ (Optimal)

**Idea:** Median ke liye poora sort kyun? Sirf `n/2`-th element sahi jagah chahiye. `nth_element` ye **O(n) average** mein kar deta hai.

`nth_element(begin, begin + n/2, end)` guarantee:
- `nums[n/2]` = sorted array wali median value
- Index `< n/2`: sab `≤ mid`
- Index `> n/2`: sab `≥ mid`
- Dono sides ke andar order random

Aapka loop har element ka distance alag se nikalta hai, to order se farq nahi padta.

```cpp
class Solution {
public:
    int minMoves2(vector<int>& nums) {
        int n = nums.size();
        nth_element(nums.begin(),nums.begin()+n/2,nums.end());
        int mid = nums[n/2];
        int cnt = 0;
        for(int i = 0 ; i < n/2 ; i++){
            cnt += abs(mid - nums[i]);
            cnt += abs(mid - nums[n-i-1]);
        }
        return cnt;
    }
};
```
**Time:** `O(n)` average | **Space:** `O(1)` extra (input reorder hota hai)

---

### Approach 5: Python (Sort + Pairing abs)

```python3
class Solution:
    def minMoves2(self, nums: list[int]) -> int:
        n = len(nums)
        nums.sort()
        mid = nums[n//2]
        cnt = 0
        for i in range(0,n//2):
            cnt += abs(mid-nums[i])
            cnt += abs(mid-nums[n-i-1])

        return cnt
```
**Time:** `O(n log n)` | **Space:** `O(1)` extra (Timsort ka temp worst case `O(n)`)

**Pythonic one-liners:**
```python
# Pair difference
class Solution:
    def minMoves2(self, nums: list[int]) -> int:
        nums.sort()
        return sum(nums[~i] - nums[i] for i in range(len(nums) // 2))

# Median + abs
class Solution:
    def minMoves2(self, nums: list[int]) -> int:
        nums.sort()
        mid = nums[len(nums) // 2]
        return sum(abs(x - mid) for x in nums)
```
(`nums[~i]` ka matlab `nums[n-1-i]`.)

---

## 6. Dry Runs

### Dry run 1: `nums = [1, 10, 2, 9]` (even n)

Sorted: `[1, 2, 9, 10]`, `n = 4`, `mid = nums[2] = 9`

**Approach 2 (pairing abs):**

| i | nums[i] | nums[n-1-i] | cost |
|---|---|---|---|
| 0 | 1 | 10 | 8 + 1 = 9 |
| 1 | 2 | 9 | 7 + 0 = 7 |

Total = **16** ✔

**Approach 3 (difference):** `(10 − 1) + (9 − 2) = 9 + 7 = 16` ✔

**Check: dusra middle (target = 2):** `1 + 0 + 7 + 8 = 16` ✔ (even n mein dono middle sahi)

### Dry run 2: `nums = [1, 2, 3]` (odd n)

Sorted: `[1, 2, 3]`, `mid = nums[1] = 2`

| i | nums[i] | nums[n-1-i] | cost |
|---|---|---|---|
| 0 | 1 | 3 | 1 + 1 = 2 |

Beech wala `2` skip (cost 0). Total = **2** ✔

### Dry run 3: Approach 4 (`nth_element`) on `[1, 10, 2, 9]`

`nth_element` ke baad kuch aisa ho sakta hai: `[2, 1, 9, 10]`, `mid = nums[2] = 9`

| i | nums[i] | nums[n-1-i] | cost |
|---|---|---|---|
| 0 | 2 | 10 | 7 + 1 = 8 |
| 1 | 1 | 9 | 8 + 0 = 8 |

Total = **16** ✔ (array sorted nahi tha, phir bhi sahi)

---

## 7. Complexity Comparison

| # | Approach | Time | Space | Note |
|---|---|---|---|---|
| 1 | Sort + abs loop | `O(n log n)` | `O(1)` | Simple, standard |
| 2 | Sort + pairing abs | `O(n log n)` | `O(1)` | Half iterations |
| 3 | Sort + two-pointer diff | `O(n log n)` | `O(1)` | Median ki zaroorat nahi |
| 4 | `nth_element` + pairing | `O(n)` avg | `O(1)` | **Best** |
| 5 | Python sort + pairing | `O(n log n)` | `O(1)` extra | Python version |

> `nth_element` (introselect) ka average `O(n)` hai, worst case guarantee implementation pe depend karti hai.

---

## 8. 453 vs 462: Pattern Notes

| | **453** | **462** |
|---|---|---|
| Operation | `n-1` elements ko `+1` | **1** element ko `±1` |
| Key trick | `n-1` ko `+1` ≡ `1` ko `-1` | Distance minimize karo |
| Target | **Minimum** element | **Median** element |
| Formula | `sum(nums) - n * min(nums)` | `Σ abs(nums[i] - median)` |
| Time | `O(n)` | `O(n log n)` ya `O(n)` avg |
| Sorting chahiye? | Nahi | Haan (ya quickselect) |
| Moves ka type | Sirf decrement-equivalent | Increment + decrement dono |

### Kyun target alag?
- **453:** Sirf "ghatana" (relative) allowed hai, to koi element apni value se **neeche** nahi jaa sakta... sab ko min tak laana padta hai. Target = min.
- **462:** Dono directions free hain, to sabko **beech ki value** pe milana sabse sasta hai. Target = median.

### Common Mental Model
> **"Operation ko reframe karo, phir target decide karo."**
> - Sirf relative difference matter karta hai → operation ko simplify karo (453).
> - Sum of absolute distances minimize karna hai → median (462).

---

## 9. Gotchas and Edge Cases

- **`n = 1`** → answer `0` (loop `n/2 = 0` baar chalta hai ✔).
- **Sab elements equal** → `0`.
- **Negative numbers** → `abs()` handle kar leta hai; median ka concept same.
- **Overflow (C++):** Values `±1e9`, `n` up to `1e5` → answer 32-bit mein fit hone ki guarantee hai, par intermediate sums ke liye **`long long cnt`** safer habit hai.
- **Mean (average) mat lo!** Mean `Σ(x - t)²` minimize karta hai, `Σ abs(x - t)` nahi. Outliers ke saath mean galat deta hai.
  - Example: `[1, 1, 1, 100]` → mean ≈ 25.75 galat, median `1` sahi (cost 99).
- **`nth_element` input ko reorder karta hai**, original order chahiye to copy banao.
- **Unsorted array pe pairing + abs** tabhi sahi hai jab array median pe partition ho (`nth_element` ke baad ✔), random unsorted pe nahi.
- **Even `n` mein `nums[n/2]` ya `nums[(n-1)/2]`** dono valid.

---

## 10. Interview Q&A

**Q1. Median hi kyun? Mean kyun nahi?**
Kyunki cost function `Σ abs(x - t)` hai (L1 norm), jiska minimizer median hai. Mean L2 (squared) ka minimizer hai.

**Q2. Even `n` mein median kaun sa lein?**
Dono middle elements, aur unke beech ki har value, same minimum cost deti hai. `nums[n/2]` safe hai.

**Q3. Sorting avoid kar sakte ho?**
Haan, quickselect (`nth_element`) se median `O(n)` average mein milti hai.

**Q4. Median ki value bhi na nikalni ho to?**
Sorted array pe `Σ (nums[n-1-i] - nums[i])` for `i < n/2`.

**Q5. 453 aur 462 mein core difference?**
453 mein operation `n-1` elements pe hai, jo relative terms mein "ek ko ghatao" ban jata hai, target = min. 462 mein ek element pe `±1` hai, target = median.

**Q6. Time complexity improve ho sakti hai `O(n)` worst case tak?**
Median of Medians algorithm se deterministic `O(n)`, par practically `nth_element` kaafi hai.

---

## 11. Similar Problems

| Problem | Concept |
|---|---|
| **453** Minimum Moves to Equal Array Elements | Reframe operation, target = min |
| **296** Best Meeting Point | Median in 1D, independently for rows and cols |
| **2448** Minimum Cost to Make Array Equal | Weighted median |
| **1131 / 1703 type** | Median + sliding window |
| **Allocate Mailboxes (1478)** | Median minimizes distance within a group |

---

## 12. Cheat Sheet

```
453:  ans = sum(nums) - n * min(nums)
462:  sort(nums);  ans = Σ abs(nums[i] - nums[n/2])
462 (no median):  ans = Σ (nums[n-1-i] - nums[i])  for i in [0, n/2)
462 (fast):  nth_element(nums, n/2);  then same loop   → O(n) avg

Rule of thumb:
  minimize Σ abs(x - t)   →  t = MEDIAN
  minimize Σ (x - t)^2    →  t = MEAN
  "n-1 ko +1" type op     →  reframe to "1 ko -1", target = MIN
```

---

⭐ **Takeaway:** *"Cost function pehchano, to target khud mil jata hai."* L1 distance → median. Aur agar operation ajeeb lage, to pehle usko simple form mein reframe karo.
