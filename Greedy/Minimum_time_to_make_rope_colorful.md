# 🎈 LeetCode 1578 — Minimum Time to Make Rope Colorful

> **Difficulty:** Medium  |  **Pattern:** Greedy on Consecutive Groups  |  **Time:** O(n)  |  **Space:** O(1)

---

## 🏷️ Tags

`String` · `Greedy` · `Array` · `Dynamic Programming (optional)` · `Consecutive Groups` · `Running Max`

---

## ⚡ 30-Second Recap

| Cheez | Yaad rakhne wali baat |
|---|---|
| Goal | Koi bhi **2 adjacent balloons same color ke na rahein** |
| Rule | Har consecutive same-color group me **sirf 1 balloon rakho** |
| Greedy | Jo balloon rakhna hai woh **sabse costly** ho, baaki sab hata do |
| Group ka cost | `sum(group) - max(group)` |
| Code trick | `prev` = group ka running max; `time += min(prev, cur)`; `prev = max(prev, cur)` |
| Reset | Color change hote hi `prev = 0` |

**One-liner:** *"Har same-color group me sabse mehenga balloon bacha lo, baaki sab ka time jod do."*

---

## 📝 1. Problem Statement

Alice ke paas `n` balloons ki rope hai. `colors[i]` i-th balloon ka color hai. Rope **colorful** tab hai jab koi bhi **do consecutive balloons same color** ke na ho.

Bob balloons hata sakta hai. `neededTime[i]` = i-th balloon hatane me lagne wala time (seconds). Return karo **minimum total time** rope ko colorful banane ke liye.

**Constraints:** `n == colors.length == neededTime.length`, `1 <= n <= 10⁵`, `1 <= neededTime[i] <= 10⁴`

### Examples

| colors | neededTime | Answer | Explanation |
|---|---|---|---|
| `"abaac"` | `[1,2,3,4,5]` | 3 | Index 2 ka `a` (time 3) hatao → `"abac"` |
| `"abc"` | `[1,2,3]` | 0 | Pehle se colorful |
| `"aabaa"` | `[1,2,3,4,1]` | 2 | Index 0 (1s) aur index 4 (1s) hatao |

---

## 🧠 2. Core Intuition

### Step 1: Sirf same-color consecutive groups matter karte hain

`"abaac"` ko groups me todo: `a | b | aa | c`. Sirf `aa` problem hai. Alag-alag groups ek dusre ko affect nahi karte.

### Step 2: Har group me exactly 1 balloon bachana hai

Group me `k` balloons hain to `k - 1` hatane padenge (kam se kam). Ek se zyada hatana faltu hai, kyunki time sirf badhega.

### Step 3: Kaunsa balloon bachayein?

Hatane ka cost = group ka total - jo bachaya uska time. Cost minimum karne ke liye **jo bachaya uska time maximum** hona chahiye.

> **Group cost = `sum(group) - max(group)`**

### Step 4: Ek pass me kaise nikalein?

Pehle sum aur max alag nikalne ki zarurat nahi. **Running max** rakho (`prev`):

- Naya balloon `cur` aaya, group me pehle se running max `prev` hai.
- In dono me se **chhota** wala hatana padega: `time += min(prev, cur)`.
- Jo bada hai woh "bacha hua" candidate ban jaata hai: `prev = max(prev, cur)`.

---

## 🔬 3. Proof: Running Min Trick = `sum - max`

Maan lo ek group ke elements `x₁, x₂, ..., xₖ` hain, aur `mᵢ` = pehle `i` elements ka max.

Har step pe: `cost += min(mᵢ₋₁, xᵢ)`, `mᵢ = max(mᵢ₋₁, xᵢ)`

Kyunki `min(a, b) + max(a, b) = a + b`:

```
min(mᵢ₋₁, xᵢ) = (mᵢ₋₁ + xᵢ) - mᵢ
```

Isse total cost telescope hota hai:

```
Total = (x₁ + x₂ + ... + xₖ) - mₖ  =  sum - max   ✅
```

(Pehle element pe `prev = 0` hai, to `min(0, x₁) = 0` aur `m₁ = x₁`, formula consistent rehta hai.)

---

## 🧩 4. Algorithm

```
time = 0, prev = 0
for i in 0..n-1:
    if i > 0 and colors[i] != colors[i-1]:
        prev = 0                          # naya group, purana max bhool jao
    time += min(prev, neededTime[i])      # chhota wala hatao
    prev  = max(prev, neededTime[i])      # bada wala bachao
return time
```

---

## 💻 5. Solution (C++)

```cpp
class Solution {
public:
    int minCost(string colors, vector<int>& neededTime) {
        int time = 0;
        int prev = 0;
        int n = colors.size();
        for (int i = 0; i < n; i++) {
            if (i - 1 >= 0 && colors[i] != colors[i - 1]) {
                prev = 0;                              // new group
            }
            time += min(prev, neededTime[i]);          // remove the cheaper one
            prev = max(prev, neededTime[i]);           // keep the costlier one
        }
        return time;
    }
};
```

### Alternative: Group sum - max (same answer)

```cpp
int minCost(string colors, vector<int>& neededTime) {
    int ans = 0, n = colors.size(), i = 0;
    while (i < n) {
        int j = i, sum = 0, mx = 0;
        while (j < n && colors[j] == colors[i]) {
            sum += neededTime[j];
            mx = max(mx, neededTime[j]);
            j++;
        }
        ans += sum - mx;
        i = j;
    }
    return ans;
}
```

---

## 🔍 6. Dry Runs

### Example A: `colors = "abaac"`, `neededTime = [1,2,3,4,5]`

| i | char | Color change? | prev (before) | `time += min(prev, cur)` | prev (after) | time |
|---|---|---|---|---|---|---|
| 0 | a | n/a | 0 | +min(0,1) = 0 | 1 | 0 |
| 1 | b | haan → reset | 0 | +min(0,2) = 0 | 2 | 0 |
| 2 | a | haan → reset | 0 | +min(0,3) = 0 | 3 | 0 |
| 3 | a | nahi | 3 | +min(3,4) = **3** | 4 | 3 |
| 4 | c | haan → reset | 0 | +min(0,5) = 0 | 5 | 3 |

Answer = **3** ✅

> 📌 **Clarification:** Jab `b → a` pe aate ho, `prev` **reset** hota hai (`0`), aur phir `a` ke liye `prev = max(0, 3) = 3` set hota hai. Matlab `prev` hamesha **sirf current group ka max** rakhta hai, purane group ka nahi.

### Example B: `colors = "aabaa"`, `neededTime = [1,2,3,4,1]`

| i | char | prev (before) | added | prev (after) | time |
|---|---|---|---|---|---|
| 0 | a | 0 | 0 | 1 | 0 |
| 1 | a | 1 | min(1,2) = 1 | 2 | 1 |
| 2 | b | 0 (reset) | 0 | 3 | 1 |
| 3 | a | 0 (reset) | 0 | 4 | 1 |
| 4 | a | 4 | min(4,1) = 1 | 4 | 2 |

Answer = **2** ✅

### Example C: Ek lamba group `"aaa"`, `[3,1,2]`

Group sum = 6, max = 3, cost = **3**.
Code: `0 → min(3,1) = 1 → prev=3 → min(3,2) = 2` → total = **3** ✅

---

## ⏱️ 7. Complexity

| | Value | Reason |
|---|---|---|
| **Time** | O(n) | Single pass |
| **Space** | O(1) | Sirf `time` aur `prev` |

---

## ⚠️ 8. Common Mistakes

1. **Color change pe `prev = 0` karna bhoolna.** Purane group ka max naye group me chala jaata hai, answer galat.
2. **Group ke andar pehla element hata dena.** Hamesha *chhota* hatao, pehla/last nahi.
3. **Sirf adjacent pair ko compare karna** (`min(neededTime[i], neededTime[i-1])`). 3+ length groups me galat hai. Running max chahiye.
4. **Greedy "pehle wala hata do" try karna.** Cost-optimal nahi.
5. **Variable name `time`.** `<ctime>` ke `time()` ko shadow karta hai. LeetCode pe chal jaata hai, par `ans` naam safer hai.

---

## 🧪 9. Edge Cases Checklist

- [ ] `n = 1` → **0**
- [ ] Sab alag colors (`"abcd"`) → **0**
- [ ] Sab same color (`"aaaa"`) → `sum - max`
- [ ] Do groups side by side (`"aabb"`)
- [ ] Equal times in a group (`[2,2,2]`)
- [ ] Group start/end pe ho (`"aab"`, `"baa"`)

---

## 🔗 10. Related Problems (Same Pattern)

| Problem | Link in concept |
|---|---|
| **1047. Remove All Adjacent Duplicates in String** | Adjacent duplicates handle karna |
| **1209. Remove All Adjacent Duplicates in String II** | Consecutive groups ke saath kaam |
| **1957. Delete Characters to Make Fancy String** | Consecutive equal characters ki limit |

> 💡 **Pattern:** "Consecutive equal elements ke group me se ek rakho, cost minimize karo" dikhe to **`sum - max` per group** socho.

---

## 🎯 11. Interview Talking Points

1. *"Problem ko consecutive same-color groups me todta hu."*
2. *"Har group me ek hi balloon rakhna hai, aur jo rakhunga woh sabse costly hoga."*
3. *"Isliye group cost = `sum - max`, jo running max se ek pass me nikal jaata hai."*
4. *"`min(a,b) + max(a,b) = a + b` se proof telescope hota hai."*
5. *"O(n) time, O(1) space."*

---

## ✅ 12. Final Revision Checklist

- [ ] Group cost = `sum - max` samajh aaya
- [ ] `prev` ka matlab (current group ka running max) clear hai
- [ ] Color change pe `prev = 0` yaad hai
- [ ] `min(prev, cur)` hatao, `max(prev, cur)` bachao
- [ ] Dry run `"abaac"` bina dekhe kar sakta hu

> **Memory hook:** 🎈 *"Same color ka group = ek bachao (sabse mehenga), baaki sab hatao. Naya color aaya to prev zero."*
