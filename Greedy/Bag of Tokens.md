# Bag of Tokens (LeetCode 948) — Notes

## 1. Problem (1 line mein)

Tokens ka array aur starting `power` diya hai. Har token ko **ek baar** hi use kar sakte ho, do tareeke se:

| Move | Condition | Effect |
|---|---|---|
| **Face-up** | `power >= token` | `power -= token`, `score += 1` |
| **Face-down** | `score >= 1` | `power += token`, `score -= 1` |

Goal: **maximum score** jo kabhi bhi achieve ho sake.

---

## 2. Core Idea: Greedy + Two Pointers

Do sawal, do jawab:

| Sawal | Jawab | Kyun |
|---|---|---|
| Score kamane ke liye kaunsa token kharidun? | **Sabse chhota** | Kam power kharch, zyada tokens kharid sakte hain |
| Score bechke power lena ho toh kaunsa token bechun? | **Sabse bada** | Ek score ke badle maximum power wapas milta hai |

Isliye:
1. Array **sort** karo
2. `left` = sabse chhota token, `right` = sabse bada token
3. Power hai toh `left` wala **face-up** karo (score +1)
4. Power nahi hai aur score hai toh `right` wala **face-down** karo (power wapas lo)
5. Power bhi nahi, score bhi nahi → **break**

---

## 3. Code

```cpp
class Solution {
public:
    int bagOfTokensScore(vector<int>& tokens, int power) {
        int n = tokens.size();
        if (n == 0) return 0;

        sort(tokens.begin(), tokens.end());

        int left = 0, right = n - 1;
        int score = 0, max_score = 0;

        while (left <= right) {
            if (power >= tokens[left]) {          // buy cheapest
                power -= tokens[left];
                left++;
                score++;
                max_score = max(max_score, score);
            }
            else if (score >= 1) {                // sell most expensive
                power += tokens[right];
                right--;
                score--;
            }
            else break;                           // stuck
        }
        return max_score;
    }
};
```

> Tumhare code mein `if(power < tokens[0]) return 0;` wali line **redundant** hai: agar power sabse chhote token se bhi kam hai aur score 0 hai, toh loop pehli hi iteration mein `break` kar dega. Rakhna ho toh chalega, hataoge toh bhi answer same.

---

## 4. Line-by-line samajh

### `max_score` alag kyun?
Face-down karne se `score` **ghatta** hai. Hum sirf **peak** score maangte hain, isliye `max_score` sirf face-up ke baad update hota hai.

### `max_score` face-down ke baad update kyun nahi?
Face-down se score kam hi hota hai, toh naya peak ban hi nahi sakta.

### `left <= right` kyun (`<` nahi)?
Jab `left == right`, ek hi token bacha hai. Use face-up kar sakte ho (agar power hai). `<` likhoge toh yeh aakhri token miss ho jayega.

### Face-down kab "waste" jaisa lagta hai?
Agar `left == right` par face-down karo toh `right--` se loop khatam ho jata hai. Score ghata, par `max_score` pehle se safe hai, toh answer pe asar nahi.

---

## 5. Dry Run

`tokens = [100, 200, 300, 400]`, `power = 200`

| Step | left | right | power | score | Action |
|---|---|---|---|---|---|
| 1 | 0 | 3 | 200 → 100 | 0 → 1 | Face-up `100` |
| 2 | 1 | 3 | 100 → 500 | 1 → 0 | `100 < 200`, face-down `400` |
| 3 | 1 | 2 | 500 → 300 | 0 → 1 | Face-up `200` |
| 4 | 2 | 2 | 300 → 0 | 1 → 2 | Face-up `300` |
| 5 | 3 | 2 | — | — | `left > right`, stop |

**Answer = 2** ✅

### Chhota example: `tokens = [200, 100]`, `power = 150`

Sorted: `[100, 200]`
- Face-up `100` → power 50, score 1, `max_score = 1`
- `50 < 200`, face-down `200` → power 250, score 0, loop khatam

**Answer = 1** ✅

---

## 6. Greedy kyun sahi hai? (Intuition proof)

- **Face-up ke liye smallest:** Fixed power se zyada se zyada tokens kharidne hain, toh sasta wala pehle lena hi optimal hai (exchange argument: bade ko pehle lene se hamesha kam ya barabar tokens milte).
- **Face-down ke liye largest:** Har face-down ki **cost 1 score** hi hai, chahe token kitna bhi bada ho. Toh wo token bechna jo **maximum power** de.
- **Bade tokens kharidne ki zarurat nahi:** Agar bada token kharidne layak power ho, toh pehle sasta wala le lo, aur bada wala aakhir mein face-down ke liye bachao.
- **Kyun `left` aur `right` ek doosre ko cross nahi karte:** Jo token face-up ho gaya wo wapas bech nahi sakte, aur jo face-down ho gaya wo kharid nahi sakte. Dono pointers ke beech wale tokens hi "available" hain.

---

## 7. Common Galtiyan ⚠️

| Galti | Kya hoga | Fix |
|---|---|---|
| Sort karna bhool jana | Greedy toot jayega, wrong answer | Pehle `sort` |
| `score` ko hi return karna | Face-down ke baad score ghata hota hai | `max_score` track karo |
| `while (left < right)` | Aakhri single token miss | `left <= right` |
| Face-down mein `left` wala token bechna | Kam power milegi | Hamesha `right` (sabse bada) |
| `score >= 1` check na karna | Score negative ho jayega | Face-down se pehle check |
| Face-up ke baad hi `max_score` update na karna | Peak miss ho sakta hai | Har face-up ke baad `max` lo |

---

## 8. Edge Cases

| Case | Output |
|---|---|
| `tokens = []` | `0` |
| `power` < sabse chhota token | `0` |
| Ek token, `power >= token` | `1` |
| Ek token, `power < token` | `0` |
| Saare tokens affordable | `n` |
| Power 0 aur token 0 bhi ho | Face-up free, score badhta hai |

---

## 9. Complexity

- **Time:** `O(n log n)` (sort), phir two-pointer loop `O(n)`
- **Space:** `O(1)` extra (sort in-place; stack `O(log n)`)

---

## 10. Pattern Yaad Rakho 🧠

> **"Resource do cheezon mein convert hoti hai, aur ek direction mein sasta, doosri direction mein mehenga"**
> → **Sort + two pointers + greedy**

Isi family ke problems:
- Boats to Save People (LC 881): sort + two pointers
- Assign Cookies (LC 455): sort + greedy
- Two City Scheduling (LC 1029): sort + greedy
- Minimum Number of Refueling Stops (LC 871): greedy + heap

---

## 11. Interview mein kaise bolna hai

1. "Har token ek baar use hota hai, aur score kamane ke liye power kharch hoti hai, power lene ke liye score."
2. "Score badhane ke liye sabse sasta token lunga, taaki zyada tokens afford ho."
3. "Score gawake power lene ke liye sabse mehenga token bechunga, kyunki cost 1 score hi hai."
4. "Isliye sort karke two pointers: left se kharido, right se becho."
5. "Peak score track karta hun kyunki face-down se score ghat sakta hai. Time `O(n log n)`, space `O(1)`."

---

## 12. Quick Cheat Sheet

```
sort(tokens)
left = smallest, right = largest

while left <= right:
    power >= tokens[left]  →  buy  (score++, update max)
    else score >= 1        →  sell tokens[right]  (score--)
    else                   →  break

return max_score
```
