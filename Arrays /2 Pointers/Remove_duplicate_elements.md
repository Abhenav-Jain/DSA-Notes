# 🧹 Remove Duplicates from Sorted Array — LeetCode 26

> **Difficulty:** Easy
> **Topics:** Array · Two Pointers
> **Pattern:** *Slow (write) pointer + Fast (read) pointer — "Filter Template" array pe*

---

## 📌 Problem Statement

Ek **sorted (non-decreasing)** integer array `nums` diya hai. Duplicates ko **in-place** hatao taaki har unique element **sirf ek baar** aaye. Elements ka relative order same rehna chahiye.

Return karo `k` = unique elements ki count.

- Pehle `k` positions (`nums[0..k-1]`) mein unique elements hone chahiye.
- `k` ke baad kya hai, **koi farak nahi padta**.
- **Extra array nahi** lena — `O(1)` extra memory.

```
Input:  nums = [1, 1, 2]
Output: 2,  nums = [1, 2, _]

Input:  nums = [0, 0, 1, 1, 1, 2, 2, 3, 3, 4]
Output: 5,  nums = [0, 1, 2, 3, 4, _, _, _, _, _]
```

**Constraints:**
- `1 <= nums.length <= 3 * 10^4`
- `-100 <= nums[i] <= 100`
- `nums` **sorted** hai ← sabse bada hint

---

## 🧠 Core Intuition

### Sorted hai → duplicates saath-saath hain

```
[0, 0, 1, 1, 1, 2, 2, 3, 3, 4]
 └─┘  └──┴──┘  └─┘  └─┘  └
  0     1       2    3   4
```

Same values hamesha **ek group** mein adjacent aati hain. Isliye kisi element ko "naya" kehne ke liye sirf **pichhle unique element** se compare karna kaafi hai, poore array se nahi.

### In-place = array ko hi answer ki jagah use karo

Array ke shuru ke hisse ko **answer area** maan lo. Jaise-jaise naya unique element milta hai, use answer area ke end mein likhte jao.

```
answer area           baaki array (padhna baaki)
[ 0  1  2 | ...........................]
        ↑   ↑
   last unique   abhi padh rahe hain
```

> 🧠 Ye wahi **"Filter Template"** hai jo linked list mein dekha tha (LC 82/83): ek pointer **padhta** hai, doosra **likhta** hai. Linked list mein `tail` tha, yahan `i` hai. 🔗

---

## 🐢 Approach 1: Brute Force — Extra Vector (Meri)

### Idea
Har duplicate group ko skip karo aur group ka **ek element** naye vector `ans` mein daal do. End mein `nums = ans`.

### Code

```cpp
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        vector<int> ans;
        for(int i = 0 ; i < nums.size() ;){
            // group ke last element tak jao
            while(i+1 < nums.size() && nums[i] == nums[i+1]){
                i++;
            }
            ans.push_back(nums[i]);   // group ka ek element
            i++;                      // agle group pe
        }
        nums = ans;
        return ans.size();
    }
};
```

### Step by Step

1. `i` kisi group ke **pehle** element pe hai.
2. Inner `while`: jab tak agla element same hai, `i` aage badhao → `i` group ke **last** element pe ruk jaata hai.
3. `nums[i]` ko `ans` mein daalo (group ka representative).
4. `i++` → agle group ka pehla element.
5. End mein `nums = ans` aur `ans.size()` return.

> ⚠️ `i+1 < nums.size()` check **pehle** likho, phir `nums[i+1]`. Order ulta kiya toh array ke bahar access → crash / undefined behaviour.

> Note: `for` loop mein `i++` nahi likha kyunki `i` andar hi badh raha hai. Ye intentional hai.

### Dry Run: `[0, 0, 1, 1, 1, 2]`

| i (start) | Inner while | i (group end) | push | ans |
|-----------|-------------|---------------|------|-----|
| 0 | 0==0 → i=1 | 1 | 0 | `[0]` |
| 2 | 1==1 → i=3, 1==1 → i=4 | 4 | 1 | `[0,1]` |
| 5 | `i+1 = 6` bahar | 5 | 2 | `[0,1,2]` |
| 6 | loop khatam | | | |

`nums = [0,1,2]`, **return 3** ✅

### Complexity

| | Value |
|---|---|
| Time | `O(n)` — har element ek baar (nested while dekh ke O(n²) mat sochna, `i` sirf aage badhta hai) |
| Space | **`O(n)`** ❌ — `ans` vector |

### ❌ Problem kya hai?
- Problem **in-place** maangti hai. `ans` extra array hai, aur `nums = ans` ek **copy** hai, in-place nahi.
- LeetCode accept kar leta hai kyunki sirf output check karta hai, lekin **interviewer reject karega**.

> Interview mein bolo: *"Brute force mein extra vector le sakte hain, lekin problem in-place maangti hai, toh main two pointers se O(1) space mein karunga."*

---

## 🌉 Bridge: Same Logic, In-place

Brute force ka hi logic, bas `ans.push_back` ki jagah `nums` mein hi aage likho:

```cpp
int removeDuplicates(vector<int>& nums) {
    int k = 0;                                   // agla likhne ka index
    for (int i = 0; i < nums.size(); ) {
        while (i + 1 < nums.size() && nums[i] == nums[i + 1]) {
            i++;
        }
        nums[k] = nums[i];                       // ans.push_back ki jagah
        k++;
        i++;
    }
    return k;
}
```

**Safe kyun hai?** `k` hamesha `i` se peeche ya barabar rehta hai. Toh jo element abhi padhna hai, wo kabhi overwrite nahi hota.

Ab space `O(1)` ho gaya. Isi soch ko thoda simple karo → optimal approach.

---

## ⚡ Approach 2: Optimal — Two Pointers (Meri)

### Idea

| Pointer | Kaam | Naam |
|---------|------|------|
| `i` | Answer area ka **last unique element** | slow / write pointer |
| `j` | Array ko **scan** karta hai | fast / read pointer |

Har `j` pe ek hi sawaal: **"Kya `nums[j]` naya hai?"** (yaani last unique `nums[i]` se alag?)
- **Haan** → `i` aage badhao aur wahan `nums[j]` likh do.
- **Nahi** → duplicate hai, skip (`j` aage).

### Code

```cpp
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        int i = 0;          // last unique element ka index
        int j = 1;          // scanner
        while(j < nums.size()){
            if(nums[i] != nums[j]){
                i++;                // answer area mein jagah banao
                nums[i] = nums[j];  // naya unique element likho
            }
            j++;
        }
        return i+1;         // index 0..i → i+1 elements
    }
};
```

### Step by Step

1. `i = 0`: pehla element hamesha unique hai, wo already sahi jagah pe hai.
2. `j = 1` se scan shuru.
3. `nums[i] != nums[j]` → naya value mila → `i++`, `nums[i] = nums[j]`.
4. Barabar hai → duplicate → sirf `j++`.
5. End mein `i` last unique ka index hai, toh count = **`i + 1`**.

> ⚠️ **`i++` pehle, phir likho.** `nums[i]` pe already ek unique value hai. Pehle likha toh wo overwrite ho jaayegi.

### Dry Run: `[0, 0, 1, 1, 1, 2, 2, 3, 3, 4]`

| j | nums[j] | nums[i] | Naya? | Action | i | Answer area `nums[0..i]` |
|---|---------|---------|-------|--------|---|--------------------------|
| start | | | | | 0 | `[0]` |
| 1 | 0 | 0 | ❌ | skip | 0 | `[0]` |
| 2 | 1 | 0 | ✅ | i=1, nums[1]=1 | 1 | `[0,1]` |
| 3 | 1 | 1 | ❌ | skip | 1 | `[0,1]` |
| 4 | 1 | 1 | ❌ | skip | 1 | `[0,1]` |
| 5 | 2 | 1 | ✅ | i=2, nums[2]=2 | 2 | `[0,1,2]` |
| 6 | 2 | 2 | ❌ | skip | 2 | `[0,1,2]` |
| 7 | 3 | 2 | ✅ | i=3, nums[3]=3 | 3 | `[0,1,2,3]` |
| 8 | 3 | 3 | ❌ | skip | 3 | `[0,1,2,3]` |
| 9 | 4 | 3 | ✅ | i=4, nums[4]=4 | 4 | `[0,1,2,3,4]` |

**Return `i + 1 = 5`** ✅

### Complexity

| | Value |
|---|---|
| Time | `O(n)` — `j` ek baar poora array scan karta hai |
| Space | **`O(1)`** ✅ — sirf do integers |

---

## ⚖️ Brute vs Optimal

| | Brute (extra vector) | Bridge (in-place group skip) | **Optimal (two pointers)** |
|---|---|---|---|
| Time | O(n) | O(n) | O(n) |
| Space | O(n) ❌ | O(1) ✅ | **O(1)** ✅ |
| In-place? | ❌ | ✅ | ✅ |
| Group ka kaunsa element likhta hai | last | last | **first** |
| Loops | nested | nested | **single** |
| Code | 10 lines | 10 lines | 🔥 8 lines, sabse simple |
| Interview | Brute force bata ke | — | **Final answer** |

---

## 🧪 Edge Cases

| Input | Output | Kyun |
|-------|--------|------|
| `[1]` | `1` | loop chalta hi nahi, `i + 1 = 1` |
| `[1, 1, 1, 1]` | `1` | har `j` duplicate, `i = 0` hi rehta hai |
| `[1, 2, 3]` | `3` | koi duplicate nahi, har element khud pe hi likha jaata hai |
| `[-1, -1, 0, 0]` | `2` | negative values bhi same tarah |
| `[]` | — | constraint ki wajah se nahi aayega. Aaye toh optimal `1` return karega ❌. Safety: `if (nums.empty()) return 0;` |

---

## ⚠️ Common Mistakes / Traps

1. **Extra array lena** → in-place requirement fail (interview mein).
2. **Likhne se pehle `i++` bhoolna** → pichhla unique element overwrite ho jaata hai.
3. **`i` return karna `i + 1` ki jagah** → count ek kam aayega.
4. **`nums[j] != nums[j-1]` vs `nums[j] != nums[i]` confusion** → dono yahan kaam karte hain (sorted array), lekin `nums[i]` se compare karna zyada general hai aur LC 80 jaise variants mein wahi chalta hai.
5. **Brute mein bounds check baad mein** (`nums[i] == nums[i+1] && i+1 < n`) → out of bounds.
6. **`erase()` use karna** (`nums.erase(...)`) → har erase O(n), total **O(n²)**. ❌
7. **Sorted wala hint ignore karke set/map lagana** → O(n) space, aur zarurat hi nahi.

---

## 💡 Pro Tips (Tagda Level 🔥)

### Tip 1: General "at most k copies" template
Write pointer `k` ko pichhle written element se compare karo:

```cpp
int removeDuplicates(vector<int>& nums) {
    int k = 0;
    for (int x : nums) {
        if (k < 1 || x != nums[k - 1]) {
            nums[k++] = x;
        }
    }
    return k;
}
```

Isme `1` ko `2` kar do → **LC 80** (har element max 2 baar) solve! 🔥

```cpp
if (k < 2 || x != nums[k - 2]) nums[k++] = x;
```

### Tip 2: STL one-liner (sirf jaankari ke liye)
```cpp
return unique(nums.begin(), nums.end()) - nums.begin();
```
`std::unique` andar se exactly yahi two-pointer karta hai. Interview mein khud likho, lekin bata sakte ho ki STL mein ye hai.

### Tip 3: Interview line
*"Array sorted hai toh duplicates adjacent hain. Main ek slow pointer `i` rakhunga jo last unique element pe hai, aur fast pointer `j` scan karega. Jab `nums[j]` naya ho, `i` badha ke wahan likh dunga. Answer `i + 1`. O(n) time, O(1) space."*

---

## 🧩 Pattern Recognition — Filter Template Family

| Problem | Write karne ki condition |
|---------|--------------------------|
| **LC 26 Remove Duplicates** | `nums[j] != nums[i]` |
| LC 80 Remove Duplicates II | `x != nums[k - 2]` (max 2 copies) |
| LC 27 Remove Element | `nums[j] != val` |
| LC 283 Move Zeroes | `nums[j] != 0` (phir baaki zero bhar do) |
| LC 83 Remove Duplicates from Sorted **List** 🔗 | Same, linked list pe |
| LC 82 Remove Duplicates II (**List**) 🔗 | Sirf group size 1 wale — `tail` pointer |

> 🎯 **Golden template:**
> ```
> write = 0
> for read in array:
>     if (keep(read)): array[write++] = read
> return write
> ```
> Linked list mein `write` = `tail`, array mein `write` = index. Soch ek hi hai.

---

## 📝 One-Line Revision

> **"Sorted → duplicates adjacent. Brute: groups skip karke extra vector (O(n) space, in-place fail). Optimal: `i = 0` (last unique), `j` se scan; `nums[j] != nums[i]` → `i++`, `nums[i] = nums[j]`. Return `i + 1`. O(n) time, O(1) space."**
