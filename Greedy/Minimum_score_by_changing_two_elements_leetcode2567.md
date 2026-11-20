# 🎯 LeetCode 2567 — Minimum Score by Changing Two Elements

> **Difficulty:** Medium  |  **Pattern:** Sort + Greedy (Remove from Ends)  |  **Time:** O(n log n)  |  **Space:** O(1)

---

## 🏷️ Tags

`Array` · `Greedy` · `Sorting` · `Case Analysis` · `Min-Max Range`

---

## ⚡ 30-Second Recap

| Cheez | Yaad rakhne wali baat |
|---|---|
| Score | `low + high` |
| `low` | Min abs difference of any pair. **Hamesha 0 kar sakte hain** (do elements equal bana do) |
| `high` | Max abs difference = `max - min`. **Yehi minimize karna hai** |
| Insight | 2 elements change karna ≈ 2 elements **delete** karna (ends se) |
| Sirf 3 cases | 2 chhote hatao / 1 chhota + 1 bada hatao / 2 bade hatao |
| Answer | `min(nums[n-2]-nums[1], nums[n-1]-nums[2], nums[n-3]-nums[0])` (sorted array pe) |

**One-liner:** *"Sort karo, `low = 0` pakka hai, ab bas do extreme elements ko 'hata' ke range sabse chhoti karo: 3 hi tareeke hain."*

---

## 📝 1. Problem Statement

Integer array `nums` di hai. Definitions:

- **Low score** = kisi bhi do alag indices `i ≠ j` ke liye `|nums[i] - nums[j]|` ka **minimum**.
- **High score** = kisi bhi do indices ke liye `|nums[i] - nums[j]|` ka **maximum**.
- **Score** = `low + high`.

Tum **at most 2 elements** ki value kisi bhi number me change kar sakte ho. Return karo **minimum possible score**.

**Constraints:** `3 <= n <= 10⁵`, `1 <= nums[i] <= 10⁹`

### Examples

| nums | Answer | Explanation |
|---|---|---|
| `[1,4,7,8,5]` | 3 | Index 0 → 5, index 1 → 5: `[5,5,7,8,5]`, low = 0, high = 8-5 = 3 |
| `[1,4,3]` | 0 | Index 1 → 1, index 2 → 1: `[1,1,1]`, low = 0, high = 0 |

---

## 🧠 2. Core Intuition (Step by Step)

### Step 1: `low` ko ignore kar do (hamesha 0)

Kam se kam 2 elements change kar sakte ho, aur `n >= 3`. To kisi bhi element ko kisi dusre ke barabar bana ke `low = 0` mil jaata hai. Isliye problem asal me bas **`high` minimize karna** hai.

### Step 2: `high = max - min`

Poore array ka sabse bada aur sabse chhota element hi high decide karte hain.

### Step 3: Change karna = Delete karna (range ke nazariye se)

Ek element ko kisi **bachche hue elements ke range ke andar** wali value bana do (jaise kisi neighbor ke equal), to woh range ko badhata nahi. Yani practically woh element "gayab" ho gaya. Isliye:

> **2 changes = 2 elements ko hata ke bachi hui array ki `max - min` nikalo.**

### Step 4: Kaunse 2 elements hatayein?

Range sirf **ends** se decide hoti hai. Beech ka element hatane se range kabhi kam nahi hoti. To sirf sorted array ke ends se hatao.

---

## 🧩 3. The 3 Cases (Sorted array: `a[0] ≤ a[1] ≤ ... ≤ a[n-1]`)

| Case | Kya hataya | Bachi range | Formula |
|---|---|---|---|
| **A** | Dono sabse chhote (`a[0], a[1]`) | `a[2] ... a[n-1]` | `a[n-1] - a[2]` |
| **B** | Ek chhota + ek bada (`a[0], a[n-1]`) | `a[1] ... a[n-2]` | `a[n-2] - a[1]` |
| **C** | Dono sabse bade (`a[n-2], a[n-1]`) | `a[0] ... a[n-3]` | `a[n-3] - a[0]` |

```
Index:   0    1    2   ...  n-3  n-2  n-1
         ■    ■    ·   ...   ·    ■    ■
Case A:  ✂    ✂    keep ........... keep
Case B:  ✂    keep ........... keep   ✂
Case C:  keep ........... keep   ✂    ✂
```

**Answer = min(A, B, C)**

---

## 🔬 4. Proof Sketch (Sirf ends kyun?)

Sorted array me kisi bhi subset ki range = `(largest kept) - (smallest kept)`.

- Agar tum beech ka element hatate ho, to kept set ka min aur max wahi rehta hai jo pehle tha (jab tak woh element end pe na ho). **Range same, koi fayda nahi.**
- Range tabhi ghat'ti hai jab **smallest** ya **largest** element hate.
- 2 hatane ka matlab: `(0 chhote + 2 bade)`, `(1 chhota + 1 bada)`, `(2 chhote + 0 bade)`. Bas yehi 3 combinations.

Isliye 3 cases exhaustive hain. ✅

---

## 💻 5. Solution (C++)

```cpp
class Solution {
public:
    int minimizeSum(vector<int>& nums) {
        int n = nums.size();
        sort(nums.begin(), nums.end());
        return min({
            nums[n-2] - nums[1],   // Case B: remove smallest + largest
            nums[n-1] - nums[2],   // Case A: remove 2 smallest
            nums[n-3] - nums[0]    // Case C: remove 2 largest
        });
    }
};
```

> ⚠️ Is problem ka asli function name `minimizeSum` hai. Function name LeetCode ke template se match karna.

---

## 🔍 6. Dry Runs

### Example A: `nums = [1,4,7,8,5]`

Sorted: `[1,4,5,7,8]`, `n = 5`

| Case | Kya hataya | Bachi hui array | Range |
|---|---|---|---|
| A | 1, 4 | `[5,7,8]` | `8 - 5 = 3` |
| B | 1, 8 | `[4,5,7]` | `7 - 4 = 3` |
| C | 7, 8 | `[1,4,5]` | `5 - 1 = 4` |

`min(3, 3, 4)` = **3** ✅

Notes me jo array transformations likhe the unka mapping:

- `[4,4,5,7,7]` → B (ends ko neighbors ke equal), high = 3
- `[1,4,5,5,5]` → C (2 bade ko `5` bana diya), high = 4
- `[5,5,5,7,8]` → A (2 chhote ko `5` bana diya), high = 3

### Example B: `nums = [1,4,3]`

Sorted: `[1,3,4]`, `n = 3`

| Case | Formula | Value |
|---|---|---|
| A | `a[2] - a[2]` | 0 |
| B | `a[1] - a[1]` | 0 |
| C | `a[0] - a[0]` | 0 |

Answer = **0** ✅

---

## ⏱️ 7. Complexity

| | Value | Reason |
|---|---|---|
| **Time** | O(n log n) | Sorting dominate karti hai |
| **Space** | O(1) | In-place sort (auxiliary ignore) |

### 🚀 Optimization: O(n) solution

Sorting ki zarurat nahi. Sirf **3 smallest** aur **3 largest** elements chahiye: `s0 ≤ s1 ≤ s2` aur `l2 ≤ l1 ≤ l0` (`l0` = max). Phir:

```
min( l0 - s2,  l1 - s1,  l2 - s0 )
```

Ya `nth_element`/`partial_sort` use karo. Interview me bonus point milta hai.

---

## ⚠️ 8. Common Mistakes

1. **`low` ko calculate karne baith jana.** Woh hamesha 0 hai, time waste.
2. **Index galat likhna.** Formula ke indices mix ho jaate hain. Rule: *"hataye hue elements ke baad ka pehla (smallest) aur aakhri (largest) bacha element"*.
3. **Sort karna bhool jaana.** Bina sort ke indices ka koi matlab nahi.
4. **Beech ke elements change karna.** Kabhi faydemand nahi (proof upar).
5. **`n = 3` edge case ignore karna.** Formula tab bhi sahi chalta hai (answer 0), par dhyan rakho `n-3 = 0` valid hai.

---

## 🧪 9. Edge Cases Checklist

- [ ] `n = 3` → answer hamesha **0**
- [ ] Sab elements equal → **0**
- [ ] Bahut duplicates (e.g. `[5,5,5,5,9]`)
- [ ] Bade values (`10⁹`), difference `int` me fit hota hai
- [ ] Already sorted / reverse sorted input

---

## 🔗 10. Related Problems (Same Pattern)

| Problem | Link in concept |
|---|---|
| **2567 (this one)** | Change 2 elements, minimize low + high |
| **1509. Minimum Difference Between Largest and Smallest Value in Three Moves** | Exactly same idea, 3 moves ke saath **4 cases** |
| **910. Smallest Range II** | Range minimize karna, ends pe focus |
| **561. Array Partition** | Sort + greedy pairing |

> 💡 **Generalization (1509 se):** Agar `k` elements change kar sakte ho, to `k+1` cases hote hain: `i` chhote aur `k-i` bade hatao, `i = 0..k`.

---

## 🎯 11. Interview Talking Points

1. *"Do elements equal bana ke low score 0 kar sakte hain, to sirf high minimize karna hai."*
2. *"Changing 2 elements = 2 elements ko range se hata dena."*
3. *"Beech ka element hatane se range nahi ghat'ti, isliye sirf ends se 3 combinations."*
4. *"Sort karke O(n log n), ya 3 smallest/3 largest se O(n)."*

---

## ✅ 12. Final Revision Checklist

- [ ] `low = 0` kyun hota hai, explain kar sakta hu
- [ ] "Change = Delete from ends" ka concept samajh aaya
- [ ] 3 cases aur unke formulas yaad hain
- [ ] O(n) optimization ka idea pata hai
- [ ] Dry run `[1,4,5,7,8]` bina dekhe kar sakta hu

> **Memory hook:** ✂️ *"Low to zero, High ko kaato: dono chhote, ek-ek, ya dono bade. Bas teen raaste."*
