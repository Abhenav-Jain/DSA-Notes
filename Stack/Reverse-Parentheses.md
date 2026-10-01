# 🔁 1190. Reverse Substrings Between Each Pair of Parentheses

![Difficulty](https://img.shields.io/badge/Difficulty-Medium-orange)
![Topic](https://img.shields.io/badge/Topic-Stack-blue)
![Topic](https://img.shields.io/badge/Topic-String-green)
![Language](https://img.shields.io/badge/Language-C%2B%2B-00599C)

> **LeetCode:** [1190. Reverse Substrings Between Each Pair of Parentheses](https://leetcode.com/problems/reverse-substrings-between-each-pair-of-parentheses/)

---

## 📌 Problem Statement

Ek string `s` di hai jisme **lowercase letters** aur **brackets `(` `)`** hain.
Har matching bracket pair ke andar ki string ko **reverse** karna hai — **innermost pair se shuru karke** bahar ki taraf.
Final answer me **koi bracket nahi** hona chahiye.

| Input | Output |
|---|---|
| `"(abcd)"` | `"dcba"` |
| `"(u(love)i)"` | `"iloveu"` |
| `"(ed(et(oc))el)"` | `"leetcode"` |
| `"a(bcdefghijkl(mno)p)q"` | `"apmnolkjihgfedcbq"` |

**Constraints:** `1 <= s.length <= 2000`, brackets hamesha balanced hote hain.

---

## 🧠 Core Intuition (Yaad rakhne wali line)

> **"Har `(` pe yaad rakho ki answer kitna lamba tha. Har `)` pe wahi index se end tak reverse kar do."**

- Stack me **characters nahi**, balki **`ans` ki length (index)** store karte hain.
- Wo index batata hai: *"is bracket ka content `ans` me kahan se shuru hua tha"*.
- `)` milte hi → `ans[start ... end]` reverse. Ye automatically **inner-to-outer** order follow karta hai, kyunki inner `)` pehle aata hai.

🔑 **Key insight:** Andar wala part 2 baar reverse hota hai (ek baar khud ke `)` pe, ek baar bahar wale `)` pe) → isliye nested reversals apne aap sahi ho jaate hain.

---

## 🪜 Approach (Step by Step)

1. Ek `stack<int> st` aur ek empty `string ans` lo.
2. String ko left → right traverse karo:
   - **`(`** → `st.push(ans.length())` *(start index save)*
   - **`)`** → `start = st.top(); st.pop();` → `reverse(ans.begin() + start, ans.end())`
   - **letter** → `ans.push_back(ch)`
3. Return `ans`.

> Brackets kabhi `ans` me add hi nahi hote → alag se remove karne ki zarurat nahi. ✅

---

## 🔍 Dry Run — `s = "(u(love)i)"`

| i | `s[i]` | Action | Stack | `ans` |
|---|---|---|---|---|
| 0 | `(` | push `ans.length()` = 0 | `[0]` | `""` |
| 1 | `u` | append | `[0]` | `"u"` |
| 2 | `(` | push `ans.length()` = 1 | `[0, 1]` | `"u"` |
| 3–6 | `l o v e` | append | `[0, 1]` | `"ulove"` |
| 7 | `)` | pop 1 → reverse `ans[1..]` | `[0]` | `"u` **`evol`** `"` → `"uevol"` |
| 8 | `i` | append | `[0]` | `"uevoli"` |
| 9 | `)` | pop 0 → reverse `ans[0..]` | `[]` | **`"iloveu"`** ✅ |

```
"ulove"   --reverse(1..end)-->  "uevol"
"uevoli"  --reverse(0..end)-->  "iloveu"
```

---

## 💻 Code (C++) — Stack + In-place Reverse

```cpp
class Solution {
public:
    string reverseParentheses(string s) {
        stack<int> st;      // har '(' ke time ans ki length (start index)
        string ans = "";

        for (char ch : s) {
            if (ch == '(') {
                st.push(ans.length());              // yahan se content shuru hoga
            }
            else if (ch == ')') {
                int start = st.top(); st.pop();
                reverse(ans.begin() + start, ans.end());  // start → end reverse
            }
            else {
                ans.push_back(ch);                  // sirf letters add karo
            }
        }
        return ans;
    }
};
```

---

## ⏱️ Complexity

| | Value | Reason |
|---|---|---|
| **Time** | `O(n²)` worst case | Har `)` pe reverse `O(n)` tak ho sakta hai, aur `n/2` brackets ho sakte hain (e.g. `"((((abc))))"`) |
| **Space** | `O(n)` | `ans` string + stack |

> `n <= 2000` hai, toh `O(n²)` ≈ 4 × 10⁶ ops → easily accepted ✅

---

## 🚀 Follow-up: Optimal `O(n)` — Wormhole / Teleportation Trick

**Idea:** Reverse karne ki jagah, **har bracket ko uske pair se link** kar do. Traverse karte waqt jab bracket mile → **pair pe jump karo aur direction flip karo**.

1. **Pass 1:** Stack se `pair[i] = j` aur `pair[j] = i` banao (matching brackets).
2. **Pass 2:** `i = 0`, `dir = +1`
   - Bracket mila → `i = pair[i]`, `dir = -dir`
   - Letter mila → `ans += s[i]`
   - `i += dir`

```cpp
class Solution {
public:
    string reverseParentheses(string s) {
        int n = s.length();
        vector<int> pair(n);
        stack<int> st;

        // Pass 1: matching brackets ko link karo
        for (int i = 0; i < n; i++) {
            if (s[i] == '(') st.push(i);
            else if (s[i] == ')') {
                int j = st.top(); st.pop();
                pair[i] = j;
                pair[j] = i;
            }
        }

        // Pass 2: wormhole traversal
        string ans = "";
        for (int i = 0, dir = 1; i < n; i += dir) {
            if (s[i] == '(' || s[i] == ')') {
                i = pair[i];   // teleport to partner bracket
                dir = -dir;    // direction ulta
            } else {
                ans += s[i];
            }
        }
        return ans;
    }
};
```

| | Value |
|---|---|
| **Time** | `O(n)` — har character max 2 baar visit |
| **Space** | `O(n)` |

---

## ⚖️ Approach Comparison

| Approach | Time | Space | Kab use kare |
|---|---|---|---|
| Brute (recursion / substring rebuild) | `O(n²)` | `O(n)` | Sirf samajhne ke liye |
| **Stack + reverse (mera code)** | `O(n²)` | `O(n)` | Interview me pehla answer — simple & clean |
| **Wormhole / pairing** | `O(n)` | `O(n)` | Interviewer optimize bole tab |

---

## ⚠️ Common Mistakes / Gotchas

- ❌ Stack me **characters** push karna → phir pop karke reverse karna messy aur slow ho jaata hai. ✅ **Index** push karo.
- ❌ `s` pe reverse lagana (`reverse(s.begin()+...)`) — galat! Reverse **`ans`** pe hota hai, kyunki `ans` me brackets nahi hain aur indices usi ke hisaab se save kiye hain.
- ❌ Brackets ko `ans` me add kar dena → baad me remove karna padega.
- ❌ Wormhole approach me jump ke baad `i += dir` karna bhool jaana → infinite loop / bracket pe atak jaana. (Loop ka `i += dir` isko handle karta hai.)
- 🧹 Unused variables (jaise `mark`) hata do — clean code interview me plus point hai.

---

## 🧩 Pattern Recognition

> **"Nested brackets + inner-to-outer processing"** → **Stack** socho.

Similar problems:

- [20. Valid Parentheses](https://leetcode.com/problems/valid-parentheses/)
- [394. Decode String](https://leetcode.com/problems/decode-string/)
- [1021. Remove Outermost Parentheses](https://leetcode.com/problems/remove-outermost-parentheses/)
- [856. Score of Parentheses](https://leetcode.com/problems/score-of-parentheses/)
- [224. Basic Calculator](https://leetcode.com/problems/basic-calculator/)

---

## 📝 30-Second Revision Card

```
(  → st.push(ans.size())
)  → start = pop;  reverse(ans.begin()+start, ans.end())
a-z→ ans += ch
Time O(n²) | Space O(n)
Optimal: pair brackets + teleport & flip direction → O(n)
```

---

<p align="center"><i>⭐ Revise → Dry run khud karo → Code bina dekhe likho ⭐</i></p>
