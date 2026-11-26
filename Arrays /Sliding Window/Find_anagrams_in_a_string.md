# 🔤 LeetCode 438 — Find All Anagrams in a String

> **Difficulty:** Medium  |  **Pattern:** Fixed-Size Sliding Window + Frequency Counter  |  **Time:** O(k) optimal  |  **Space:** O(1) (26 letters)

---

## 🏷️ Tags

`String` · `Hash Table` · `Sliding Window` · `Fixed Window` · `Frequency Count` · `Anagram`

---

## ⚡ 30-Second Recap

| Cheez | Yaad rakhne wali baat |
|---|---|
| Anagram | Same characters, **same frequency**, koi bhi order |
| Window size | **Fixed** = `p.length()` |
| Idea | `s` pe size-`|p|` ki window slide karo, har window ki frequency `p` se match karo |
| Brute force | Har window ke liye naya map/queue copy → **O(k·n)** |
| Optimized | Map me **"abhi kitna aur chahiye"** rakho; add pe `--`, remove pe `++` |
| Match ka test | Saari counts `0` (ya `need == 0` counter se **O(1)**) |
| Output | Un windows ke **starting indices** |

**One-liner:** *"Size-|p| ki window slide karo. Aane wala char count ghataye, jaane wala badhaye. Sab counts zero hon to anagram mila."*

---

## 📝 1. Problem Statement

Do strings `s` aur `p` diye hain. `s` ke un saare **starting indices** ki list return karo jahan se shuru hone wala substring `p` ka **anagram** hai.

**Anagram** = dusre string ke letters ko rearrange karke bana string.

**Constraints:** `1 <= s.length, p.length <= 3 × 10⁴`, dono strings **lowercase English letters** ki hain.

### Examples

| `s` | `p` | Output | Explanation |
|---|---|---|---|
| `"cbaebabacd"` | `"abc"` | `[0, 6]` | `s[0..2] = "cba"`, `s[6..8] = "bac"` |
| `"abab"` | `"ab"` | `[0, 1, 2]` | `"ab"`, `"ba"`, `"ab"` |

---

## 🧠 2. Core Intuition

### Fixed window kyun?

Anagram ki length `p` ke barabar hi hogi. To `s` pe **exactly `|p|` size ki window** slide karni hai (`k` windows ... `|s| - |p| + 1`).

### Har window ko scratch se count karna slow hai

Har window ki frequency nayi nikalo to har baar O(|p|) lagta hai. **Smart tareeka:** window slide hone par sirf **ek char aata hai, ek char jaata hai**, to count ko **incrementally update** karo.

### "Need" map ka idea

`mp[c]` = **p ko c kitni baar aur chahiye** (window ke hisaab se).

| Event | Kya karo |
|---|---|
| Char `c` window me **aaya** (aur `c` `p` me hai) | `mp[c]--` (ek need poori hui) |
| Char `c` window se **gaya** (aur `c` `p` me hai) | `mp[c]++` (need wapas aayi) |
| Window ka size `== |p|` aur **saari counts `0`** | ✅ Anagram mila |

### Saari counts 0 hi kyun kaafi hai?

`p` ki total need = `|p|`. Window ka size bhi `|p|`. Agar saari needs **exactly 0** hain, to window me exactly wohi chars (wahi frequency me) hain jo `p` me hain, koi extra ya foreign char nahi. ✅

(Agar koi char **zyada** aaya to uski count **negative** ho jaati hai, aur koi doosra chhoota hoga, to `0` nahi rahega, check fail.)

---

## 💻 3. Solution 1: Brute Force (aapka pehla version)

```cpp
class Solution {
public:
    queue<char> m;
    bool check(queue<char> q, unordered_map<char,int> mp) {   // ⚠️ dono by value (copy!)
        m = q;
        while (!m.empty()) {
            char c = m.front();
            if (mp.find(c) == mp.end() || mp[c] == 0) {
                return false;
            } else {
                mp[c]--;
            }
            m.pop();
        }
        return true;
    }
    vector<int> findAnagrams(string s, string p) {
        int n = p.length();
        int k = s.length();
        vector<int> result;
        queue<char> q;
        unordered_map<char,int> mp;
        for (char c : p) {
            mp[c]++;
        }
        int i = 0, j = 0;
        while (j < k) {
            q.push(s[j]);
            if (j - i + 1 < n) {
                j++;
            } else if (j - i + 1 == n) {
                if (check(q, mp)) {
                    result.push_back(i);
                }
                i++;
                j++;
                q.pop();
            }
        }
        return result;
    }
};
```

### 🔍 Kya ho raha hai

- `q` = current window ke chars. Har window pe `check()` call hota hai.
- `check` window ke chars ko `p` ke map ke against consume karta hai. Koi char na mile ya count 0 ho to `false`.

### ⚠️ Problem: slow kyun?

1. `check(queue<char> q, unordered_map<char,int> mp)` **dono ko by value** leta hai, to **har window pe queue aur map ki poori copy** banti hai: O(n).
2. `check` ke andar poori window (n chars) pe loop: O(n).
3. Kul: `k` windows × O(n) = **O(k · n)**. Worst case `3×10⁴ × 3×10⁴ ≈ 9×10⁸` → **TLE ka risk**.
4. Member `m` bekaar hai (local variable kaafi tha).

---

## 💻 4. Solution 2: Incremental Map (aapka doosra version)

```cpp
class Solution {
public:
    queue<char> m;                                   // (unused, hata sakte ho)
    bool check(unordered_map<char,int>& mp) {        // ✅ reference, copy nahi
        for (auto& x : mp) {
            if (x.second != 0) {
                return false;
            }
        }
        return true;
    }
    vector<int> findAnagrams(string s, string p) {
        int n = p.length();
        int k = s.length();
        vector<int> result;
        queue<char> q;
        unordered_map<char,int> mp;
        for (char c : p) {
            mp[c]++;
        }
        int i = 0, j = 0;
        while (j < k) {
            q.push(s[j]);
            if (mp.find(s[j]) != mp.end()) {
                mp[s[j]]--;                          // char aaya
            }
            if (j - i + 1 < n) {
                j++;
            } else if (j - i + 1 == n) {
                if (check(mp)) {
                    result.push_back(i);
                }
                char left = q.front();
                q.pop();
                if (mp.find(left) != mp.end()) {
                    mp[left]++;                      // char gaya
                }
                i++;
                j++;
            }
        }
        return result;
    }
};
```

### ✅ Kya improve hua

- Map ek hi baar bana, window ke saath **incrementally update** hota hai.
- `check` ab sirf `p` ke **unique chars** (max 26) pe loop karta hai, copy nahi.
- Time: **O(k × u)**, `u` = `p` ke unique chars ≤ 26 → practically **O(k)**.

### ⚠️ Chhoti cheezein

- `queue<char> q` sirf "left char" yaad rakhne ke liye hai, jo `s[i]` se mil jaata hai. Queue **redundant** hai.
- Member `m` unused hai.

---

## 💻 5. Solution 3: Queue Hata ke Clean Version (aapka teesra version) ⭐

```cpp
class Solution {
public:
    bool check(unordered_map<char,int>& mp) {
        for (auto& x : mp) {
            if (x.second != 0) {
                return false;
            }
        }
        return true;
    }
    vector<int> findAnagrams(string s, string p) {
        vector<int> result;
        int n = s.length();
        int k = p.length();
        int i = 0, j = 0;
        unordered_map<char,int> mp;
        for (char c : p) {
            mp[c]++;
        }
        while (j < n) {
            if (mp.find(s[j]) != mp.end()) {
                mp[s[j]]--;                          // s[j] window me aaya
            }
            if (j - i + 1 < k) {
                j++;                                 // window abhi chhoti hai
            } else if (j - i + 1 == k) {
                if (check(mp)) {
                    result.push_back(i);             // anagram mila
                }
                if (mp.find(s[i]) != mp.end()) {
                    mp[s[i]]++;                      // s[i] window se gaya
                }
                i++;
                j++;
            }
        }
        return result;
    }
};
```

### 🎯 Yeh "Fixed Window Template" hai

```
while (j < n):
    1. s[j] ko window me add karo (calculation)
    2. if window size < k:   j++
       else if size == k:
           3. answer nikalo
           4. s[i] ko window se hatao (undo calculation)
           5. i++, j++
```

### 🚨 Ek important trap jo aapne sahi handle kiya

`unordered_map` me `mp[x]` likhne se **agar `x` nahi hai to naya key insert ho jaata hai** (value 0). Aapne `mp.find(...) != mp.end()` ka guard lagaya hai, isliye **foreign chars ke naye keys nahi bante**.

Agar guard na hota aur foreign char pe `mp[s[i]]++` chalta, to us key ki value `1` ho jaati (ya `-1`, jaise add pe `--`), aur `check()` hamesha `false` dega. Yeh bahut common bug hai.

---

## 🚀 6. Solution 4: Optimal O(k), Array + `need` Counter

Do improvements:
1. `unordered_map` ki jagah **`vector<int>(26)`**: fast, koi hashing nahi.
2. `check()` ka loop hata ke **`need` counter** rakho: **O(1) match test**.

```cpp
class Solution {
public:
    vector<int> findAnagrams(string s, string p) {
        vector<int> result;
        int n = s.size(), k = p.size();
        if (k > n) return result;                    // p lamba hai, koi window nahi

        vector<int> cnt(26, 0);
        for (char c : p) cnt[c - 'a']++;             // cnt[c] = c ki need

        int need = k;                                // abhi kitne chars (p ke) aur chahiye
        int i = 0;
        for (int j = 0; j < n; j++) {
            if (cnt[s[j] - 'a']-- > 0) need--;       // useful char aaya
            if (j - i + 1 == k) {
                if (need == 0) result.push_back(i);  // sab need poori
                if (++cnt[s[i] - 'a'] > 0) need++;   // useful char gaya
                i++;
            }
        }
        return result;
    }
};
```

### 🧠 Counter logic samjho

| Line | Kya matlab |
|---|---|
| `cnt[c]-- > 0` | Pehle `cnt[c] > 0` tha (c ki need baaki thi) → ek useful char mila → `need--`. Foreign/extra char ho to `cnt[c] <= 0` tha, `need` nahi badlega |
| `++cnt[c] > 0` | Increment ke baad `> 0` matlab pehle `>= 0` tha, yani yeh char ek **useful** copy tha jo ab gaya → `need++`. Extra copy jaaye to `cnt` abhi bhi `<= 0`, `need` nahi badlega |

> **Invariant:** `need` = `p` ke wo chars jo window me **abhi tak match nahi hue**. `need == 0` ⟺ anagram.

---

## 🔍 7. Dry Run (Solution 4): `s = "cbaebabacd"`, `p = "abc"`

Shuruat: `cnt: a=1, b=1, c=1`, `need = 3`, `k = 3`

| j | Char add | Window | `need` (add ke baad) | Match? | Char remove | `need` (remove ke baad) |
|---|---|---|---|---|---|---|
| 0 | c | `c` | 2 | (size < 3) | | 2 |
| 1 | b | `cb` | 1 | (size < 3) | | 1 |
| 2 | a | `cba` | **0** | ✅ **index 0** | c | 1 |
| 3 | e | `bae` | 1 | ❌ | b | 2 |
| 4 | b | `aeb` | 1 | ❌ | a | 2 |
| 5 | a | `eba` | 1 | ❌ | e | 1 |
| 6 | b | `bab` | 1 | ❌ | b | 1 |
| 7 | a | `aba` | 1 | ❌ | a | 1 |
| 8 | c | `bac` | **0** | ✅ **index 6** | b | 1 |
| 9 | d | `acd` | 1 | ❌ | a | 2 |

Answer = **`[0, 6]`** ✅

> 📌 `j = 3` ('e') par `e` foreign hai, `need` nahi badla (`1` hi raha), par `e` window me hai to count `1` hi mil nahi sakti. Match nahi hua. ✅

---

## ⏱️ 8. Complexity Comparison

| Approach | Time | Space | Note |
|---|---|---|---|
| Sol 1 (brute, copy per window) | O(k · n) | O(n) | Copy + loop har window pe, TLE risk |
| Sol 2 (map + queue) | O(k · u), `u ≤ 26` | O(n) | Queue redundant |
| Sol 3 (map, no queue) | O(k · u), `u ≤ 26` | O(u) | Clean |
| **Sol 4 (array + `need`)** | **O(k)** | **O(1)** (26) | Optimal |

`k = |s|`, `n = |p|`, `u` = `p` ke unique chars.

---

## ⚠️ 9. Common Mistakes

1. **Check ke baad `s[i]` ko window se hatana bhool jaana.** Window ka count galat ho jaata hai.
2. **`mp[x]` seedha use karna foreign char par.** Naya key ban jaata hai, `check()` hamesha fail (guard lagao).
3. **Window size check galat** (`<` vs `==` ya off-by-one).
4. **`p` ka size `s` se bada** hone par bina check kiye aage badhna. (Solution 1–3 me loop apne aap kuch return nahi karta, par Solution 4 me explicit guard rakho.)
5. **Index ke bajaye substring ya window return karna.** Output **starting index** hai.
6. **Har window pe map ki poori copy** (Solution 1 wali galti).
7. **Sorting se compare karna** har window ko (`O(n log n)` per window), bahut slow.

---

## 🧪 10. Edge Cases Checklist

- [ ] `|p| > |s|` → `[]`
- [ ] `|p| == |s|`, anagram hai → `[0]`
- [ ] `p` ke saare chars same (`"aaa"`), `s = "aaaaa"` → `[0,1,2]`
- [ ] Overlapping anagrams (`s = "abab"`, `p = "ab"` → `[0,1,2]`)
- [ ] `s` me `p` ke chars hain par extra chars bhi (`"abcc"` vs `p = "abc"` ke window me)
- [ ] Koi anagram nahi → `[]`
- [ ] Single character `p` aur `s`

---

## 🔗 11. Related Problems

| Problem | Link in concept |
|---|---|
| **567. Permutation in String** | **Exactly yehi**, bas boolean return (koi bhi window match hua?) |
| **242. Valid Anagram** | Do strings ki frequency compare |
| **49. Group Anagrams** | Frequency/sorted key se group |
| **76. Minimum Window Substring** | **Variable** window + `need/have` counter (isi idea ka bada bhai) |
| **30. Substring with Concatenation of All Words** | Words ke level pe window |
| **3. Longest Substring Without Repeating Characters** | Variable window + map |
| **643. Maximum Average Subarray I** | Fixed window ka numeric version |

---

## 🎯 12. Interview Talking Points

1. *"Anagram ki length `p` ke barabar hogi, to fixed-size window use karta hu."*
2. *"Brute force: har window ki frequency nikalo, O(k·n)."*
3. *"Optimize: map me 'need' rakhta hu. Char aaye to ghatao, jaaye to badhao. Saari counts 0 hon to match."*
4. *"Aur optimize: array of 26 aur ek `need` counter se match test O(1), total O(k)."*
5. *"Space O(1) kyunki sirf lowercase letters (26)."*
6. *"Isi template se Permutation in String (567) aur Minimum Window Substring (76) bhi hote hain."*

---

## ✅ 13. Final Revision Checklist

- [ ] Window size fixed = `|p|`
- [ ] `mp[c]` = need; add pe `--`, remove pe `++`
- [ ] Saari counts zero ⟺ anagram (kyun, samajh aaya)
- [ ] Map me foreign key insert hone ka trap yaad hai
- [ ] Fixed Window Template (add → check → remove) yaad hai
- [ ] `need` counter wala O(1) check samajh aaya
- [ ] Dry run `"cbaebabacd"`, `"abc"` → `[0,6]` bina dekhe kar sakta hu

> **Memory hook:** 🔤 *"Aane wala ghataye, jaane wala badhaye. Sab zero to anagram. Index i push karo."*
