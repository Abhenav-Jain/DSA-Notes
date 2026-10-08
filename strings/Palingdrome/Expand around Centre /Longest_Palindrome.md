# 🔤 Longest Palindrome — LeetCode 409

> **Difficulty:** Easy
> **Topics:** Hash Table · Counting · Greedy · String
> **Pattern:** *Frequency count → jodiyan gino → center ke liye ek extra*

---

## 📌 Problem Statement

Ek string `s` di hai jisme **lowercase aur uppercase English letters** hain. Inn letters se banne wale **sabse lambe palindrome ki length** return karo.

- Letters ko **kisi bhi order mein rearrange** kar sakte ho.
- **Case-sensitive** hai: `'A'` aur `'a'` alag letters hain. (`"Aa"` palindrome nahi hai)
- Saare letters use karna zaroori nahi.

```
Input:  "abccccdd"     Output: 7      (ek possible: "dccaccd")
Input:  "a"            Output: 1
Input:  "Aa"           Output: 1
```

**Constraints:**
- `1 <= s.length <= 2000`
- `s` mein sirf lowercase aur/ya uppercase English letters hain

---

## ⚔️ LC 409 vs LC 5 — Confusion mat karna!

| | **LC 409 Longest Palindrome** | LC 5 Longest Palindromic Substring |
|---|---|---|
| Kya dhoondhna hai | Letters se **bana sakne wala** palindrome | String ke **andar already maujood** palindrome |
| Rearrange allowed? | ✅ Haan | ❌ Nahi (substring = lagatar characters) |
| Return | Sirf **length** (`int`) | **Substring** khud (`string`) |
| Approach | Counting | Brute force / Expand around center / DP |
| Time | **O(n)** | O(n²) ya O(n³) |

> 🧠 **Yaad rakho:** Jab "rearrange" allowed ho, toh order matter nahi karta, sirf **kitni baar** aaya (frequency) matter karta hai.

---

## 🧠 Core Intuition

Palindrome ka structure dekho:

```
  d c c a c c d
  ↑ ↑ ↑ ↑ ↑ ↑ ↑
  └─┼─┼─┼─┼─┼─┘   d - d   (jodi)
    └─┼─┼─┼─┘     c - c   (jodi)
      └─┼─┘       c - c   (jodi)
        ↑
      center: a  (akela)
```

1. **Har character jodi (pair) mein aata hai**: ek left side, ek mirror pe right side.
2. **Sirf center pe ek akela character** aa sakta hai (wo bhi optional, sirf odd-length palindrome mein).

Toh:
- Kisi character ki count **even** hai → saare use ho jaayenge (sab jodiyan ban gayi).
- Count **odd** hai → `count - 1` use honge (jodiyan), aur **ek bach jaayega**.
- Agar kahin bhi koi character bacha → usme se **ek** ko center mein daal do → `+1`.

> ⚠️ Kitne bhi characters odd hon, center mein sirf **ek** hi jaa sakta hai. Isliye `+1` sirf ek baar.

---

## ⭐ Meri Approach — Step by Step

### Step 1: Frequency count (lower aur upper alag)

```cpp
vector<int> lower(26,0);
vector<int> upper(26,0);
for(char c : s){
    if(c >= 'a'){
        lower[c-'a']++;     // 'a'→0, 'b'→1, ... 'z'→25
    }
    else{
        upper[c-'A']++;     // 'A'→0, 'B'→1, ... 'Z'→25
    }
}
```

**`c >= 'a'` check kyun kaam karta hai?** ASCII values:

| Characters | ASCII range |
|-----------|-------------|
| `'A'` – `'Z'` | 65 – 90 |
| `'a'` – `'z'` | 97 – 122 |

Saare uppercase letters lowercase se **pehle** aate hain. Input mein sirf letters hain, toh `c >= 'a'` → lowercase, warna uppercase. ✅

> `c - 'a'` trick: character ko `0-25` ke index mein badal deta hai. `'c' - 'a' = 99 - 97 = 2`.

### Step 2: Lowercase ki jodiyan gino

```cpp
int count = 0;
bool odd = 0;
for(int i = 0 ; i < 26 ; i++){
    if(lower[i] % 2 == 0){
        count += lower[i];          // even → saare use
    }
    else{
        count += (lower[i]-1);      // odd → ek chhod ke baaki use
        odd = 1;                    // ek bacha hai, center ke liye note karo
    }
}
```

### Step 3: Uppercase ki jodiyan gino (same logic)

```cpp
for(int i = 0 ; i < 26 ; i++){
    if(upper[i] % 2 == 0){
        count += upper[i];
    }
    else{
        count += (upper[i]-1);
        odd = 1;
    }
}
```

### Step 4: Center add karo

```cpp
return count + odd;     // odd true → +1, false → +0
```

`bool` ko `int` mein add karne pe `true = 1`, `false = 0` ban jaata hai. ✅

---

## ✅ Full Code (Meri Approach)

```cpp
class Solution {
public:
    int longestPalindrome(string s) {
        vector<int> lower(26,0);
        vector<int> upper(26,0);

        // Step 1: frequency count
        for(char c : s){
            if(c >= 'a'){
                lower[c-'a']++;
            }
            else{
                upper[c-'A']++;
            }
        }

        int count = 0;
        bool odd = 0;

        // Step 2: lowercase ki jodiyan
        for(int i = 0 ; i < 26 ; i++){
            if(lower[i] % 2 == 0){
                count += lower[i];
            }
            else{
                count += (lower[i]-1);
                odd = 1;
            }
        }

        // Step 3: uppercase ki jodiyan
        for(int i = 0 ; i < 26 ; i++){
            if(upper[i] % 2 == 0){
                count += upper[i];
            }
            else{
                count += (upper[i]-1);
                odd = 1;
            }
        }

        // Step 4: center ke liye +1 (agar koi odd mila)
        return count + odd;
    }
};
```

---

## 🔍 Dry Run

### Case A: `"abccccdd"`

**Frequency:**

| Char | Count | Even/Odd | `count` mein add | `odd` |
|------|-------|----------|------------------|-------|
| a | 1 | odd | `1 - 1 = 0` | 1 |
| b | 1 | odd | `1 - 1 = 0` | 1 |
| c | 4 | even | 4 | 1 |
| d | 2 | even | 2 | 1 |

`count = 6`, `odd = 1` → **return 7** ✅
Ek possible palindrome: `d c c a c c d` (center pe `a`, `b` use nahi hua)

### Case B: `"Aa"`

| Char | Array | Count | add | odd |
|------|-------|-------|-----|-----|
| A | upper | 1 | 0 | 1 |
| a | lower | 1 | 0 | 1 |

`count = 0`, `odd = 1` → **return 1** ✅ (`"A"` ya `"a"` — case-sensitive hai, isliye `"Aa"` palindrome nahi)

### Case C: `"aaabbbcc"`

| Char | Count | add | odd |
|------|-------|-----|-----|
| a | 3 | 2 | 1 |
| b | 3 | 2 | 1 |
| c | 2 | 2 | 1 |

`count = 6`, `odd = 1` → **return 7** ✅ (jaise `abcacba` — ek `a` center mein, ek `b` chhoota)

---

## 🧪 Edge Cases

| Input | Output | Kyun |
|-------|--------|------|
| `"a"` | 1 | ek akela char center mein |
| `"aa"` | 2 | ek jodi, koi odd nahi → `+0` |
| `"ab"` | 1 | dono odd, center mein sirf ek |
| `"Aa"` | 1 | case-sensitive → alag letters |
| `"aaaa"` | 4 | sab even, `odd = 0` |
| `"abcde"` | 1 | sab odd, sirf ek center |
| `"ccc"` | 3 | `2` jodi + `1` center |

---

## ⏱ Complexity

| | Value | Kyun |
|---|---|---|
| **Time** | `O(n)` | String ek baar traverse + 2 × 26 ka fixed loop |
| **Space** | `O(1)` | Do fixed size (26) arrays — input pe depend nahi karta |

> 26 fixed hai, isliye arrays ko O(1) space hi maana jaata hai.

---

## ⚠️ Common Mistakes / Traps

1. **Har odd character ke liye `+1` kar dena** ❌
   Center mein sirf **ek** character aa sakta hai. `"abc"` ka answer 1 hai, 3 nahi.
2. **Odd character ko poora chhod dena** ❌
   `"aaa"` mein `a` odd hai, lekin 2 `a` jodi ban sakte hain. Answer 3 hai, 1 nahi. Isliye `count - 1` add karo, `0` nahi.
3. **Case-insensitive treat karna** ❌
   `'A'` aur `'a'` ko same maan liya toh `"Aa"` ka answer 2 aayega, jo galat hai. Alag arrays (ya alag index) chahiye.
4. **`count + 1` hamesha return karna** ❌
   Agar saare counts even hain (`"aabb"`), toh center khaali rehta hai. Answer 4 hai, 5 nahi. Isliye `odd` flag zaroori hai.
5. **LC 5 wali approach lagana** ❌
   Substrings check karna yahan galat hai, kyunki rearrange allowed hai.

---

## 💡 Pro Tips (Tagda Level 🔥)

1. **`bool odd = 0` → `false` likho.** Same kaam karta hai, lekin padhne mein intent saaf dikhta hai.
2. **Odd flag ki jagah ek aur tarika:** agar final `count < s.size()`, matlab koi character bacha hai → `+1`.
   ```cpp
   return count < s.size() ? count + 1 : count;
   ```
3. **Interview line:** *"Palindrome mein har character mirror pair mein aata hai, sirf center akela ho sakta hai. Isliye har frequency ki even part lo, aur agar koi odd mila toh ek center ke liye add karo."*

---

## 🧩 Pattern Recognition — "Frequency se Palindrome"

| Problem | Connection |
|---------|-----------|
| **LC 409 Longest Palindrome** | Jodiyan gino + ek center |
| LC 266 Palindrome Permutation | Kya rearrange karke palindrome ban sakta hai? → **odd count wale characters ≤ 1** |
| LC 2131 Longest Palindrome by Concatenating Two Letter Words | Same idea, bas character ki jagah 2-letter words ki jodiyan |
| LC 1400 Construct K Palindrome Strings | Odd count wale characters ≤ k hone chahiye |

> 🎯 **Golden rule:** Rearrange allowed palindrome → sirf **odd frequency wale characters ki ginti** matter karti hai.

---

## 📝 One-Line Revision

> **"Har letter ki frequency gino (lower/upper alag). Even → poora add, odd → `count - 1` add karo aur `odd = 1` mark karo. End mein `count + odd` return karo, kyunki center mein sirf ek akela character aa sakta hai. O(n) time, O(1) space."**
