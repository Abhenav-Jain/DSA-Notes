# LeetCode 453 – Minimum Moves to Equal Array Elements

**Difficulty:** Easy | **Topics:** Array, Math

---

## 1. Problem Statement

Ek integer array `nums` of size `n` diya hai. Ek **move** mein tum **n − 1 elements ko 1 se increment** kar sakte ho. Sabhi elements ko equal karne ke liye **minimum moves** return karo.

### Examples

| Input | Output | Explanation |
|---|---|---|
| `[1,2,3]` | `3` | `[1,2,3] → [2,3,3] → [3,4,3] → [4,4,4]` |
| `[1,1]` | `0` | Pehle se equal |

### Constraints
- `n == nums.length`
- `1 <= n <= 10^5`
- `-10^9 <= nums[i] <= 10^9`
- Answer 32-bit integer mein fit hota hai.

---

## 2. Brute Force Approach

### Intuition
Jaisa problem bolti hai waisa hi simulate karo: har move mein **sabse bada element chhod ke baaki sab +1** karo. Max ko chhodna isliye sensible hai ki hume max tak baaki sabko pahunchana hai, max ko aur bada karne ka koi fayda nahi.

### Algorithm
1. Jab tak array ke sab elements equal nahi hote:
   1. Max element ka index `idx` dhundo.
   2. `idx` ko chhod ke baaki sab `+1` karo.
   3. `cnt++`.
2. `cnt` return karo.

### Code (C++ – Brute Force)

```cpp
class Solution {
public:
    bool check(vector<int>& nums){
        for(int i = 1 ; i < nums.size() ; i++){
            if(nums[i] != nums[i-1]){
                return false;
            }
        }
        return true;
    }
    int max_index(vector<int>& nums){
        int n = nums.size();
        int ans = 0;
        int max_num = nums[0];
        for(int i = 1 ; i < n ; i++){
            if(nums[i] > max_num){
                max_num = nums[i];
                ans = i;
            }
        }
        return ans;
    }
    int minMoves(vector<int>& nums) {
        int n = nums.size();
        int idx = 0;
        int cnt = 0;
        while(!check(nums)){
            // Step 1 :: Find the max index
            idx = max_index(nums);

            // Step 2 :: Increment n-1 elements except the max_index
            for(int i = 0 ; i < n ; i++){
                if(i == idx){
                    continue;
                }
                else{
                    nums[i] += 1;
                }
            }
            cnt += 1;
        }
        return cnt;
    }
};
```

### Complexity
- **Time:** `O(moves × n)`, jahan `moves = sum(nums) − n × min(nums)`. Values 1e9 tak ja sakti hain, to moves astronomically bade ho sakte hain → **TLE**.
- **Space:** `O(1)`.

### Note
Agar max do baar aaye (tie), to sirf pehla max skip hota hai. Phir bhi answer sahi aata hai kyunki relative differences wahi rehte hain.

---

## 3. Key Insight (Brute Force → Optimal)

> **n − 1 elements ko +1 karna ≡ ek element ko −1 karna (relative difference ke hisaab se).**

Kyunki sirf **differences** matter karte hain, na ki absolute values. Agar sab elements ko +1 kar do, to differences same rehte hain. To:

```
(n-1 elements ko +1)  =  (sab ko +1)  +  (ek element ko -1)
                            ^ differences same        ^ asli effect
```

Matlab har move ka asli effect hai: **ek element ko 1 se ghataya gaya**.

Ab problem ban gayi: *"Sabhi elements ko minimum tak laane mein kitne decrements lagenge?"*

- Minimum element ko kabhi ghatane ki zaroorat nahi (wo already sabse chhota hai).
- Har element `x` ko `min` tak laane ke liye `x − min` decrements chahiye.

```
moves = Σ (nums[i] − min) = sum(nums) − n × min(nums)
```

### Mathematical Proof (alternate)
Maan lo `m` moves lage, original sum `S`, original min `mn`, final equal value `x`.

- Har move mein sum `n − 1` se badhta hai → final sum = `S + m(n−1)`.
- Final sab elements `x` hain → final sum = `n·x`.
- Min element har move mein increment hota rehta hai (kyunki kabhi max nahi banta jab tak sab equal na ho), to `x = mn + m`.

```
n(mn + m) = S + m(n − 1)
n·mn + n·m = S + n·m − m
m = S − n·mn
```

Same formula mil gaya.

---

## 4. Understanding the Process (Dry Run)

`nums = [1, 2, 3]`, `n = 3`, `min = 1`, `sum = 6`

**Simulation (brute force):**

| Move | Array before | Max skip | Array after |
|---|---|---|---|
| 1 | `[1,2,3]` | `3` | `[2,3,3]` |
| 2 | `[2,3,3]` | `3` (first) | `[3,4,3]` |
| 3 | `[3,4,3]` | `4` | `[4,4,4]` |

Total = **3 moves**.

**Formula se:** `6 − 3×1 = 3` ✔

**Dikhte hue decrements:** `[1,2,3]` ko min `1` tak laao → `2` ko 1 step, `3` ko 2 steps = `0 + 1 + 2 = 3` ✔

Edge cases:
- `[1]` → `0`
- `[1,1,1]` → `0`
- `[-1, 0, 1]` → `(0) + (1) + (2) = 3`

---

## 5. Optimal Approach

### Algorithm
1. Array ka minimum `mn` nikalo.
2. Har element ke liye `nums[i] − mn` ko add karo.
3. Total return karo.

(Ya seedha `sum(nums) − n × min(nums)`.)

### Code 1 (C++ – Optimal, aapka solution)

```cpp
class Solution {
public:
    int min_element_idx(vector<int>& nums){
        int n = nums.size();
        int ans = 0;
        int min_num = nums[0];
        for(int i = 1 ; i < n ; i++){
            if(nums[i] < min_num){
                min_num = nums[i];
                ans = i;
            }
        }
        return ans;
    }
    int minMoves(vector<int>& nums) {
        int n = nums.size();
        int idx = min_element_idx(nums);
        int cnt = 0;
        for(int i = 0 ; i < n ; i++){
            cnt += (nums[i] - nums[idx]);
        }
        return cnt;
    }
};
```

### Code 2 (Python3 – Optimal, aapka solution)

```python3
class Solution:
    def min_el_idx(self, nums: list[int]) -> int:
        min_el = nums[0]
        ans = 0
        for i in range(1,len(nums)):
            if nums[i] < min_el:
                min_el = nums[i]
                ans = i

        return ans


    def minMoves(self, nums: list[int]) -> int:
        n = len(nums)
        idx = self.min_el_idx(nums)
        cnt = 0
        for i in range(0,n):
            cnt += nums[i]-nums[idx]

        return cnt
```

### Complexity (dono optimal codes ke liye)
- **Time:** `O(n)`: ek pass min dhundne ko, ek pass sum ke liye.
- **Space:** `O(1)`.

---

## 6. Cleaner / Shorter Versions (Reference)

**C++ (single pass, overflow-safe):**
```cpp
class Solution {
public:
    int minMoves(vector<int>& nums) {
        long long sum = 0;
        int mn = INT_MAX;
        for (int x : nums) {
            sum += x;
            mn = min(mn, x);
        }
        return sum - (long long)nums.size() * mn;
    }
};
```

**Python (one-liner):**
```python
class Solution:
    def minMoves(self, nums: list[int]) -> int:
        return sum(nums) - len(nums) * min(nums)
```

---

## 7. Comparison

| Approach | Time | Space | Result |
|---|---|---|---|
| Brute force (simulation) | `O(moves × n)` | `O(1)` | TLE |
| Optimal (sum − n·min) | `O(n)` | `O(1)` | Accepted |

---

## 8. Important Points / Gotchas

- **Overflow (C++):** Intermediate sum `long long` mein rakhna safe habit hai (`n` up to 1e5, values up to 1e9 → sum 1e14 tak ja sakta hai). Aapke solution mein `cnt` sirf differences add karta hai aur final answer int mein fit hone ki guarantee hai, isliye accept ho jata hai, par `long long cnt` better practice hai.
- **Python:** Integers arbitrary precision, overflow ka tension nahi.
- Helper mein min ka **index** nikalne ki zaroorat nahi, min **value** hi kaafi hai (`*min_element(...)` / `min(nums)`).
- Brute force ko `const vector<int>&` ke saath likhna better hai jahan modify nahi karte.

---

## 9. Takeaway (Pattern)

> Jab operation "kuch elements ko sath mein badhao" jaisa ho, to **relative difference** ka perspective lo. "n−1 ko +1" ko **"1 ko −1"** mein badal do, aur problem ek simple sum − n·min formula ban jaati hai.

Similar pattern wale problems: **LeetCode 462** (Minimum Moves to Equal Array Elements II, jahan ±1 allowed hai, to median target hota hai).
