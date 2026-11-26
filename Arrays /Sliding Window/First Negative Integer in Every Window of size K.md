# ➖ GFG — First Negative Integer in Every Window of Size K

> **Platform:** GeeksforGeeks  |  **Difficulty:** Medium  |  **Pattern:** Fixed Sliding Window + Queue of Candidate Indices  |  **Time:** O(n)  |  **Space:** O(k)

---

## 🏷️ Tags

`Array` · `Sliding Window` · `Fixed Window` · `Queue` · `Candidate Tracking` · `Monotonic-style Queue`

---

## ⚡ 30-Second Recap

| Cheez | Yaad rakhne wali baat |
|---|---|
| Goal | Har size-`k` window me **pehla negative** number (koi nahi to `0`) |
| Window size | **Fixed** = `k` |
| Data structure | `queue<int>` me **negatives ke INDICES** |
| Window me `arr[j]` aaya | Negative ho to `q.push(j)` |
| Answer | `q.empty()` ? `0` : `arr[q.front()]` |
| Window se `arr[i]` jaa raha hai | Agar `q.front() == i` to `q.pop()` |
| Index kyun | `front == i` se **exact** pata chalta hai ki kaunsa element bahar gaya, aur `front < i` jaisa position-check bhi ban sakta hai |
| Output size | `n - k + 1` |

**One-liner:** *"Queue me negatives ke indices rakho. Front hi window ka pehla negative hai. Window chhodte waqt front ka index `i` ho to pop karo."*

---

## 📝 1. Problem Statement

Integer array `arr` aur ek integer `k` diya hai. **Har size-`k` window** me **pehla negative integer** return karo. Agar window me koi negative nahi hai, to us window ke liye `0` likho.

**Constraints (GFG):** `1 <= arr.size() <= 10⁵`, `1 <= k <= arr.size()`, `-10⁵ <= arr[i] <= 10⁵`

### Examples

| `arr` | `k` | Output |
|---|---|---|
| `[-8, 2, 3, -6, 10]` | 2 | `[-8, 0, -6, -6]` |
| `[12, -1, -7, 8, -15, 30, 16, 28]` | 3 | `[-1, -1, -7, -15, -15, 0]` |

**Example 1 ka breakdown:**

| Window | Pehla negative |
|---|---|
| `[-8, 2]` | `-8` |
| `[2, 3]` | `0` (koi nahi) |
| `[3, -6]` | `-6` |
| `[-6, 10]` | `-6` |

> 📌 `0` negative **nahi** hota. Isliye array ka `0` bhi "negative nahi" gina jaata hai.

---

## 🧠 2. Core Intuition

### Brute force

Har window ke liye `k` elements scan karo aur pehla negative dhundo: **O(n·k)**. Bade `n`, `k` pe slow.

### Observation

Window slide hone par sirf **ek element aata hai, ek jaata hai**. Har baar poori window dobara scan karna bekaar hai.

### Candidate queue

Hume sirf **negative numbers** hi matter karte hain. Unke **indices (positions)** ko **order me** ek queue me rakho:

```
arr = [12, -1, -7, 8, -15, 30, 16, 28]
idx =   0    1   2  3    4   5   6   7

Negatives ke indices (order me): [1, 2, 4]
```

- **Front** = sabse purana (sabse left) negative = **window ka pehla negative** (agar wo window ke andar hai).
- Naya negative aaye to **back** me push (kyunki wo sabse right hai).
- Jab window ka left element `arr[i]` bahar jaaye, aur wo negative tha, to wo **front** hi hoga → pop.

### 🔑 Indices kyun store karte hain?

Maan lo `arr = [-1, 2, -1]`, `k = 2`.

- **Values** store karoge to queue `[-1, -1]` banegi. Yeh bhi **chal sakta hai**: jab `arr[i]` negative hota hai to wo window ka sabse left (sabse purana) negative hota hai, yani queue ka front. To "front ki value `== arr[i]`" se pop sahi ho jaata hai.
- Phir bhi **indices standard aur safer** hain, kyunki:
  1. `q.front() == i` ka matlab bilkul exact hai ki **wahi element** window se nikal raha hai, koi value-comparison ki reasoning nahi chahiye.
  2. Position-based check bhi ban sakta hai (`while (!q.empty() && q.front() < i) q.pop();`), jo zyada robust hai.
  3. Answer ke liye bhi index se `arr[q.front()]` mil jaata hai.

Isse yeh guarantee milti hai ki queue me hamesha **window ke andar ke hi indices** rehte hain.

---

## 💻 3. Solution 1: Queue of Indices (aapka version)

```cpp
class Solution {
  public:
    vector<int> firstNegInt(vector<int>& arr, int k) {
        vector<int> result;
        queue<int> q;
        int i = 0;
        int j = 0;
        int n = arr.size();
        while (j < n) {
            if (arr[j] < 0) {
                q.push(j);                              // negative ka index store
            }
            if (j - i + 1 < k) {
                j++;                                    // window abhi chhoti hai
            } else if (j - i + 1 == k) {
                if (q.empty()) {
                    result.push_back(0);                // koi negative nahi
                } else if (!q.empty()) {
                    result.push_back(arr[q.front()]);   // pehla negative
                }
                if (!q.empty() && q.front() == i) {
                    q.pop();                            // arr[i] window se jaa raha hai
                }
                i++;
                j++;
            }
        }
        return result;
    }
};
```

### 📝 Chhoti cleanup suggestions

- `else if (!q.empty())` ko seedha `else` likh sakte ho (dusra condition tab already true hai).
- `else if (j - i + 1 == k)` ko bhi `else` likh sakte ho, kyunki `j - i + 1` kabhi `k` se bada nahi hota.

```cpp
if (q.empty()) result.push_back(0);
else           result.push_back(arr[q.front()]);
```

### ✅ Kya achha hai

- **Answer pehle compute, pop baad me.** Yeh order sahi hai: pehle current window ka answer lo, phir left element ko hatao agle window ke liye.
- `q.front() == i` check sahi hai, kyunki queue me indices **increasing order** me hain, to sabse chhota index (front) hi pehle bahar jaayega.

---

## 🔍 4. Dry Run: `arr = [12, -1, -7, 8, -15, 30, 16, 28]`, `k = 3`

| j | `arr[j]` | Queue (push ke baad) | Window `[i..j]` | Answer | Pop? (`front == i`) | Queue (pop ke baad) | i |
|---|---|---|---|---|---|---|---|
| 0 | 12 | `[]` | (size 1) | | | `[]` | 0 |
| 1 | -1 | `[1]` | (size 2) | | | `[1]` | 0 |
| 2 | -7 | `[1,2]` | `[0..2]` | `arr[1]` = **-1** | front=1 ≠ 0, nahi | `[1,2]` | 1 |
| 3 | 8 | `[1,2]` | `[1..3]` | `arr[1]` = **-1** | front=1 == 1, **pop** | `[2]` | 2 |
| 4 | -15 | `[2,4]` | `[2..4]` | `arr[2]` = **-7** | front=2 == 2, **pop** | `[4]` | 3 |
| 5 | 30 | `[4]` | `[3..5]` | `arr[4]` = **-15** | front=4 ≠ 3, nahi | `[4]` | 4 |
| 6 | 16 | `[4]` | `[4..6]` | `arr[4]` = **-15** | front=4 == 4, **pop** | `[]` | 5 |
| 7 | 28 | `[]` | `[5..7]` | queue khaali → **0** | | `[]` | 6 |

Answer = **`[-1, -1, -7, -15, -15, 0]`** ✅

---

## 💻 5. Solution 2: Next-Negative Array (Queue ke bina)

Idea: Pehle har index `i` ke liye **"mujhse aage (ya mujhpe) pehla negative kahan hai"** precompute karo (right se left scan). Phir har window ke liye O(1) answer.

```cpp
vector<int> firstNegInt(vector<int>& arr, int k) {
    int n = arr.size();
    vector<int> nxt(n + 1, n);                  // nxt[i] = pehla negative ka index >= i, warna n
    for (int i = n - 1; i >= 0; i--) {
        nxt[i] = (arr[i] < 0) ? i : nxt[i + 1];
    }
    vector<int> res;
    for (int i = 0; i + k <= n; i++) {          // window [i .. i+k-1]
        res.push_back(nxt[i] < i + k ? arr[nxt[i]] : 0);
    }
    return res;
}
```

| | Queue (Sol 1) | Next array (Sol 2) |
|---|---|---|
| Time | O(n) | O(n) |
| Space | O(k) | O(n) |
| Idea | Sliding window | Precomputation |
| Simplicity | Thoda tricky | Bahut simple |

---

## 🚀 6. Solution 3: Single Pointer, O(1) Extra Space

Ek pointer `p` rakho jo "pehla negative candidate" dhundta hai. Wo **sirf aage badhta hai**, to amortized O(n).

```cpp
vector<int> firstNegInt(vector<int>& arr, int k) {
    int n = arr.size();
    vector<int> res;
    int p = 0;                                      // pehle negative ka candidate index
    for (int i = 0; i + k <= n; i++) {              // window [i .. i+k-1]
        if (p < i) p = i;                           // purana negative window se bahar
        while (p < i + k && arr[p] >= 0) p++;       // agla negative dhundo
        res.push_back(p < i + k ? arr[p] : 0);
    }
    return res;
}
```

**Kyun sahi?** Jab `p` ko window `[i, i+k-1]` me koi negative nahi mila to wo `i+k` pe pahunch jaata hai. Agli window me `[i+1 .. i+k-1]` pehle hi non-negative dekhe ja chuke hain, to scan naye element `i+k` se aage badhta hai. Koi element do baar scan nahi hota.

---

## ⏱️ 7. Complexity Comparison

| Approach | Time | Space |
|---|---|---|
| Brute force (har window scan) | O(n · k) | O(1) |
| **Queue of indices (aapka)** | **O(n)** | O(k) |
| Next-negative array | O(n) | O(n) |
| Single pointer | O(n) | **O(1)** (output ke alawa) |

- Queue me max `k` indices (window me zyada se zyada `k` negatives).
- Output vector ka size `n - k + 1` alag se.

---

## 🧰 8. Pattern: "Candidates ki Queue" ka Bada Roop (LC 239)

Yeh problem **Sliding Window Maximum (LC 239)** ka chhota bhai hai. Dono me:

- Window slide hoti hai.
- Queue me **candidates ke indices** rehte hain.
- Window se bahar gaye indices front se hatate hain (`front == i` ya `front <= j-k`).

| | Yeh problem | LC 239 (Sliding Window Maximum) |
|---|---|---|
| Candidates | Saare negatives | Sirf wo jo **maximum ban sakte hain** |
| Queue ke back me push se pehle | Kuch nahi hatate | **Chhote** elements back se pop karte hain (monotonic deque) |
| Answer | `arr[q.front()]` | `arr[dq.front()]` |

> 💡 Isi se monotonic deque ka concept aata hai. Is problem me **kuch hatane ki zarurat nahi**, kyunki sabhi negatives equally valid candidate hain (sabse left wala jeetega).

---

## ⚠️ 9. Common Mistakes

1. **Values store karna, indices nahi.** Chal sakta hai, par position-based pop check nahi ban paata aur reasoning zyada delicate ho jaati hai. Indices standard aur safe hain.
2. **Left element hatana bhool jaana** (`q.front() == i` wala pop).
3. **Pop pehle, answer baad me karna.** Tab current window ka answer galat nikal sakta hai (jo element bahar jaa raha hai wo abhi bhi is window ka hissa hai).
4. **`q.front()` ko bina `empty()` check ke access karna.** Crash.
5. **`0` ko negative maan lena.** `arr[j] < 0` hi sahi hai, `<= 0` nahi.
6. **Window size ka off-by-one** (`j - i + 1`).
7. **`k > n` ka case.** Yahan GFG constraints me `k <= n` hai, par general code me empty result hona chahiye.

---

## 🧪 10. Edge Cases Checklist

- [ ] `k = 1` → har element: negative ho to khud, warna `0`
- [ ] `k = n` → sirf ek answer
- [ ] Saare elements non-negative → saare `0`
- [ ] Saare elements negative → har window ka pehla element
- [ ] Negative window ke **left boundary** pe (abhi bahar jaane wala)
- [ ] Negative window ke **right boundary** pe (abhi aaya)
- [ ] Duplicate negatives (`[-1, 2, -1]`)
- [ ] `arr` me `0` hai (negative nahi maana jaayega)
- [ ] Ek hi negative jo kai windows me aata hai

---

## 🔗 11. Related Problems

| Problem | Link in concept |
|---|---|
| **239. Sliding Window Maximum** | Same idea, **monotonic deque** ke saath |
| **438. Find All Anagrams in a String** | Fixed window + frequency |
| **643. Maximum Average Subarray I** | Fixed window, sum |
| **1343. Number of Sub-arrays of Size K and Average >= Threshold** | Fixed window, sum |
| **567. Permutation in String** | Fixed window, frequency |
| **209. Minimum Size Subarray Sum** | **Variable** window |
| **1438. Longest Subarray with Abs Diff <= Limit** | Do monotonic deques |
| **480. Sliding Window Median** | Fixed window, advanced |

---

## 🎯 12. Interview Talking Points

1. *"Fixed size `k` ki window hai, to sliding window use karta hu."*
2. *"Har window me pehla negative chahiye, to negatives ke **indices** order me queue me rakhta hu. Front hi window ka pehla negative hoga."*
3. *"Indices isliye, taaki jab left element window se jaaye to `front == i` se exact pata chale ki wahi element nikla."*
4. *"Har element ek baar push aur zyada se zyada ek baar pop hota hai, to O(n) time. Queue me max `k` indices, to O(k) space."*
5. *"Aur chhoti space ke liye single pointer wala approach bhi hai. Aur yahi idea LC 239 me monotonic deque ban jaata hai."*

---

## ✅ 13. Final Revision Checklist

- [ ] Queue me **indices** (values nahi)
- [ ] Answer: `q.empty() ? 0 : arr[q.front()]`
- [ ] Window chhodte waqt `if (q.front() == i) pop`
- [ ] Order: **answer pehle, pop baad me**
- [ ] `0` negative nahi hota
- [ ] LC 239 se connection samajh aaya
- [ ] Dry run `[12,-1,-7,8,-15,30,16,28]`, `k=3` → `[-1,-1,-7,-15,-15,0]` bina dekhe kar sakta hu

> **Memory hook:** ➖ *"Negatives ke index queue me. Front = pehla negative. Left nikle to pop. Queue khaali to 0."*
