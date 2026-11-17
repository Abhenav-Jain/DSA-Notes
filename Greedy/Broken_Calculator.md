# 🧮 LeetCode 991 — Broken Calculator

> **Difficulty:** Medium  |  **Pattern:** Greedy + Reverse Thinking  |  **Time:** O(log target)  |  **Space:** O(1)

---

## 🏷️ Tags

`Greedy` · `Math` · `Working Backwards` · `Bit-like Halving` · `Shortest Operations`

---

## ⚡ 30-Second Recap (Revision se pehle sirf yeh padho)

| Cheez | Yaad rakhne wali baat |
|---|---|
| Forward ops | `×2` aur `−1` |
| Trick | **Reverse me socho**: `target` se `startValue` tak jao |
| Reverse ops | `÷2` (sirf even pe) aur `+1` |
| Even target | Seedha `÷2` |
| Odd target | `+1` karke even banao |
| Loop ruk jaye jab | `target <= startValue` |
| Final answer | `cnt + (startValue - target)` |

**One-liner:** *"Target ko bada se chhota banao: even to half, odd to +1. Jab chhota ho jaye, gap +1 se bhar do."*

---

## 📝 1. Problem Statement

Ek broken calculator hai jisme display pe `startValue` dikhta hai. Ek move me tum:

- display ke number ko **double** kar sakte ho, ya
- display ke number me se **1 subtract** kar sakte ho.

Return karo **minimum moves** `startValue` ko `target` banane ke liye.

**Constraints:** `1 <= startValue, target <= 10⁹`

### Examples

| startValue | target | Answer | Explanation |
|---|---|---|---|
| 2 | 3 | 2 | 2 → 4 → 3 |
| 5 | 8 | 2 | 5 → 4 → 8 |
| 3 | 10 | 3 | 3 → 6 → 5 → 10 |

---

## 🧠 2. Core Intuition

### ❌ Forward soch kyun fail hoti hai?

`startValue` se shuru karoge to har step pe 2 choices hain (`×2` ya `−1`). Kab double karna hai, kab subtract, yeh decide karna mushkil hai. Branching badi hai (BFS karoge to exponential state space).

### ✅ Reverse soch kyun kaam karti hai?

Forward ops ko ulta karo:

| Forward | Reverse |
|---|---|
| `x → 2x` | `x → x / 2` (**sirf jab x even ho**) |
| `x → x − 1` | `x → x + 1` |

Reverse me har state pe **choice kam ho jaati hai**:

- **Odd** number pe `÷2` allowed hi nahi, to `+1` ke alawa koi option nahi.
- **Even** number pe `÷2` lena best hai (proof neeche).

Yeh **deterministic greedy** ban jaata hai.

> 💡 **Pattern yaad rakho:** Jab forward me choices zyada hon aur reverse me kam, to **reverse engineering** try karo.

---

## 🔬 3. Greedy Kyun Correct Hai? (Proof Sketch)

### Claim: Target even ho to hamesha `÷2` lo.

Maan lo `target = t` even hai. Do options compare karo:

**Option A: Pehle `÷2`, phir `+1`**
```
t → t/2 → t/2 + 1        (2 moves)
```

**Option B: Pehle `+1` do baar, phir `÷2`**
```
t → t+1 → t+2 → (t+2)/2 = t/2 + 1        (3 moves)
```

Dono ka result same (`t/2 + 1`) hai, lekin **A me 2 moves, B me 3 moves**. Matlab `+1` pehle karna kabhi faydemand nahi. Isliye even pe `÷2` best hai.

### Claim: Target odd ho to `+1` forced hai.

Odd pe `÷2` possible nahi, to `+1` ke alawa koi legal reverse move nahi. Uske baad number even ho jaata hai aur halving ho sakti hai.

### Claim: `target <= startValue` hone pe sirf `+1` bachta hai.

Ab `÷2` karoge to `target` aur chhota hoga, `startValue` se aur door. Isliye bacha hua kaam sirf gap bharna hai: `startValue - target` baar `+1`.

---

## 🧩 4. Algorithm

```
cnt = 0
while target > startValue:
    if target is even:  target = target / 2
    else:               target = target + 1
    cnt++
return cnt + (startValue - target)
```

---

## 💻 5. Solution (C++)

```cpp
class Solution {
public:
    int brokenCalc(int startValue, int target) {
        int cnt = 0;
        while (target > startValue) {
            if (target % 2 == 0) {
                target /= 2;
            } else {
                target += 1;
            }
            cnt++;
        }
        return cnt + (startValue - target);
    }
};
```

### Compact version

```cpp
while (target > startValue) {
    target = (target % 2 == 0) ? target / 2 : target + 1;
    cnt++;
}
return cnt + (startValue - target);
```

---

## 🔍 6. Dry Runs

### Example A: `startValue = 3, target = 10`

| Step | target | Action | cnt |
|---|---|---|---|
| 1 | 10 | even → `÷2` → 5 | 1 |
| 2 | 5 | odd → `+1` → 6 | 2 |
| 3 | 6 | even → `÷2` → 3 | 3 |
| stop | 3 | `target == startValue` | 3 |

Answer = `3 + (3 − 3)` = **3** ✅

### Example B: `startValue = 5, target = 8`

| Step | target | Action | cnt |
|---|---|---|---|
| 1 | 8 | even → `÷2` → 4 | 1 |
| stop | 4 | `4 < 5` | 1 |

Answer = `1 + (5 − 4)` = **2** ✅ (forward: 5 → 4 → 8)

### Example C: `startValue = 2, target = 3`

| Step | target | Action | cnt |
|---|---|---|---|
| 1 | 3 | odd → `+1` → 4 | 1 |
| 2 | 4 | even → `÷2` → 2 | 2 |
| stop | 2 | `target == startValue` | 2 |

Answer = **2** ✅

### Example D (edge): `startValue = 10, target = 1`

Loop chalta hi nahi (`target < startValue`). Answer = `0 + (10 − 1)` = **9** ✅

---

## ⏱️ 7. Complexity

| | Value | Reason |
|---|---|---|
| **Time** | O(log target) | Har 2 iterations me target kam se kam aadha ho jaata hai (odd → `+1` → even → `÷2`) |
| **Space** | O(1) | Sirf kuch variables |

**Overflow:** `target <= 10⁹`, to `target += 1` int me safe hai.

---

## ⚠️ 8. Common Mistakes

1. **Forward BFS/DP try karna.** Time limit exceed ho jaata hai.
2. **Odd pe `÷2` karne ki koshish.** Illegal hai, `+1` hi karna hai.
3. **Loop ke baad gap bhoolna.** `+ (startValue - target)` likhna mat bhoolo.
4. **Loop condition `>=` likhna.** `target > startValue` hi sahi hai, warna extra iteration lag sakti hai.
5. **Even pe bhi `+1` karna.** Proof ke hisaab se halving hamesha behtar hai.

---

## 🧪 9. Edge Cases Checklist

- [ ] `startValue == target` → **0**
- [ ] `startValue > target` → **startValue − target**
- [ ] `target` ek bada odd number (e.g. `10⁹ − 1`)
- [ ] `startValue = 1`, `target` bada
- [ ] Target power of 2 (sirf halving hogi)

---

## 🔗 10. Related Problems (Same Pattern)

| Problem | Link in concept |
|---|---|
| **397. Integer Replacement** | Even → `÷2`, odd → `±1` choose karna; greedy/bit logic |
| **2139. Minimum Moves to Reach Target Score** | Almost same reverse-halving idea |
| **650. 2 Keys Keyboard** | Reverse/divisor thinking se minimum steps |

---

## 🎯 11. Interview Talking Points

1. *"Forward me branching zyada hai, isliye main target se reverse jaunga."*
2. *"Reverse me odd pe sirf `+1` possible hai, even pe `÷2` greedy best hai."*
3. *"Proof: `+1, +1, ÷2` (3 moves) vs `÷2, +1` (2 moves), same result."*
4. *"Time O(log target), space O(1)."*

---

## ✅ 12. Final Revision Checklist

- [ ] Reverse ops yaad hain (`÷2` on even, `+1`)
- [ ] Greedy proof (3 moves vs 2 moves) explain kar sakta hu
- [ ] Loop condition `target > startValue`
- [ ] Final answer me `startValue - target` add karna yaad hai
- [ ] Dry run `3 → 10` bina dekhe kar sakta hu

> **Memory hook:** 🔁 *"Ulta chalo: even to aadha, odd to +1, aur end me gap bhar do."*
