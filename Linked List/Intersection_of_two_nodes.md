# ✂️ Intersection of Two Linked Lists — LeetCode 160

> **Difficulty:** Easy (lekin trick Medium-level ki hai)
> **Topics:** Linked List · Two Pointers · Hash Table
> **Pattern:** *Do pointers, end pe pahunch ke doosri list pe switch → lengths ka farak apne aap barabar*

---

## 📌 Problem Statement

Do singly linked lists ke heads `headA` aur `headB` diye hain. Woh **node** return karo jahan dono lists **milti (intersect)** hain. Agar intersection nahi hai toh `NULL` return karo.

- Intersection ka matlab: **same node (same address)**, sirf same value nahi.
- Intersection ke baad dono lists ka **poora aage ka hissa common** hota hai (Y shape).
- Lists ko **modify nahi** karna, aur list mein cycle nahi hai.

```
A:       4 → 1 ─┐
                ├→ 8 → 4 → 5 → NULL
B:  5 → 6 → 1 ──┘

Output: node with value 8
```

**Constraints:**
- `1 <= m, n <= 3 * 10^4`
- `1 <= Node.val <= 10^5`

**Follow-up:** `O(m + n)` time aur `O(1)` space mein kar sakte ho? ✅ (Meri approach yahi hai)

---

## ⚠️ Value vs Address — Sabse important baat

```
A:  4 → [1] ─┐
              ├→ 8 → 4 → 5
B:  5 → 6 → [1] ┘
```

Dono lists mein `1` hai, lekin woh **alag nodes** hain (alag address). Intersection `8` pe hai, jahan se **same node** shuru hota hai.

> 🧠 Isliye comparison hamesha `curr1 == curr2` (pointers) se karo, **`curr1->val == curr2->val` se nahi**. ❌

---

## 🧠 Core Intuition

### Problem kya hai?

Agar dono lists ki length **same** hoti, toh dono pointers ek saath chalate aur jahan `curr1 == curr2` hota, wahi intersection hota.

Lekin lengths **alag** hain. Chhoti list wala pointer intersection pe pehle pahunch jaata hai, lamba wala baad mein. Dono kabhi ek saath nahi milte. 😵

### Trick: dono pointers ko barabar distance chalwao 🔥

List ko teen hisson mein dekho:

```
A:  [  a  ] ─┐
             ├→ [  c  ] → NULL
B:  [   b   ] ┘

a = sirf A ka hissa,  b = sirf B ka hissa,  c = common hissa
```

| Pointer | Pehle chalta hai | Switch karke chalta hai | Intersection tak total |
|---------|------------------|-------------------------|------------------------|
| `curr1` | A poori: `a + c` | B ka apna hissa: `b` | **`a + c + b`** |
| `curr2` | B poori: `b + c` | A ka apna hissa: `a` | **`b + c + a`** |

Dono ka distance **same** (`a + b + c`) hai! Isliye dono **ek hi step pe** intersection node pe pahunchte hain. 🎯

> 🧠 **Mantra:** *"Tum meri raah chalo, main tumhari — beech mein mil jaayenge."*
> Har pointer apni list + doosre ki list chalta hai, toh dono ka total safar barabar ho jaata hai.

---

## ⭐ Meri Approach — Step by Step

### Step 1: Do pointers dono heads pe

```cpp
ListNode* curr1 = headA;
ListNode* curr2 = headB;
```

### Step 2: Jab tak dono same node pe nahi, chalte raho

```cpp
while(curr1 != curr2){
```

Loop do situations mein rukta hai:
- Dono **intersection node** pe mil gaye → wahi answer
- Dono ek saath **`NULL`** pe pahunch gaye → intersection nahi hai, answer `NULL`

### Step 3: End pe pahunche toh doosri list pe switch, warna aage badho

```cpp
    if(curr1 == NULL){
        curr1 = headB;          // A khatam → B pe switch
    }
    else{
        curr1 = curr1->next;
    }
    if(curr2 == NULL){
        curr2 = headA;          // B khatam → A pe switch
    }
    else{
        curr2 = curr2->next;
    }
}
```

### Step 4: Return

```cpp
return curr1;     // intersection node ya NULL
```

---

## ✅ Full Code (Meri Approach)

```cpp
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        ListNode* curr1 = headA;
        ListNode* curr2 = headB;

        while(curr1 != curr2){
            // curr1: A khatam hui toh B pe switch
            if(curr1 == NULL){
                curr1 = headB;
            }
            else{
                curr1 = curr1->next;
            }
            // curr2: B khatam hui toh A pe switch
            if(curr2 == NULL){
                curr2 = headA;
            }
            else{
                curr2 = curr2->next;
            }
        }
        return curr1;    // intersection ya NULL
    }
};
```

---

## 🔥 `NULL` pe switch kyun, last node pe kyun nahi?

Mera code `curr1 == NULL` pe switch karta hai. Yaani pointer pehle **`NULL` pe ek step rukta** hai, phir agle step mein doosri list pe jaata hai.

Isse har pointer ka path ban jaata hai:

```
curr1:  A ke saare nodes → NULL → B ke saare nodes → NULL
curr2:  B ke saare nodes → NULL → A ke saare nodes → NULL
```

Dono paths ki length `m + n + 2` hai, **barabar**. Isliye:
- Intersection hai → beech mein mil jaayenge
- Intersection nahi hai → **dono ek saath doosre `NULL` pe** pahunchenge → `NULL == NULL` → loop khatam ✅

### ❌ Agar `curr1->next == NULL` pe switch kiya toh?

```cpp
if (curr1->next == NULL) curr1 = headB;   // galat version
```

Ab pointer kabhi `NULL` pe aata hi nahi. Intersection na ho toh `curr1` aur `curr2` kabhi barabar nahi honge → **infinite loop** 💥

> 🧠 `NULL` ko ek "extra node" maan lo jo dono lists ke end pe hai. Dono pointers usse guzarte hain, isliye no-intersection case mein wahi meeting point ban jaata hai.

---

## 🔍 Dry Run

### Case A: Intersection hai

```
A:       4 → 1 ─┐                     a = 2
                ├→ 8 → 4 → 5          c = 3
B:  5 → 6 → 1 ──┘                     b = 3
```

| Step | curr1 | curr2 | Equal? |
|------|-------|-------|--------|
| 0 | 4 (A) | 5 (B) | ❌ |
| 1 | 1 (A) | 6 (B) | ❌ |
| 2 | **8** | 1 (B) | ❌ |
| 3 | 4 | **8** | ❌ |
| 4 | 5 | 4 | ❌ |
| 5 | NULL | 5 | ❌ |
| 6 | 5 (B) ← switch | NULL | ❌ |
| 7 | 6 (B) | 4 (A) ← switch | ❌ |
| 8 | 1 (B) | 1 (A) | ❌ (value same, **node alag**) |
| 9 | **8** | **8** | ✅ **Mil gaye!** |

**Output: node 8** ✅
Dono ne `a + c + b = 2 + 3 + 3 = 8` nodes chale, phir 9th step pe intersection. (Step 8 dekho: values same hain lekin nodes alag, isliye address compare zaroori hai.)

### Case B: Intersection nahi hai

```
A:  2 → 6 → 4 → NULL      (m = 3)
B:  1 → 5 → NULL          (n = 2)
```

| Step | curr1 | curr2 | Equal? |
|------|-------|-------|--------|
| 0 | 2 | 1 | ❌ |
| 1 | 6 | 5 | ❌ |
| 2 | 4 | NULL | ❌ |
| 3 | NULL | 2 ← switch | ❌ |
| 4 | 1 ← switch | 6 | ❌ |
| 5 | 5 | 4 | ❌ |
| 6 | **NULL** | **NULL** | ✅ dono NULL |

**Output: `NULL`** ✅

### Case C: Same length — pehli hi pass mein mil jaate hain

```
A:  1 → 2 ─┐
           ├→ 9 → NULL
B:  3 → 4 ─┘
```
Step 2 pe dono `9` pe → switch ki zarurat hi nahi padi ✅

---

## 🧪 Edge Cases

| Situation | Output | Kaise handle hua |
|-----------|--------|------------------|
| Intersection nahi | `NULL` | dono ek saath NULL pe pahunchte hain |
| Same length, intersect | intersection node | pehli pass mein hi mil gaye |
| `headA == headB` (poori list common) | `headA` | loop chalega hi nahi, step 0 pe hi equal |
| Intersection last node pe | last node | `a + 1 + b` steps |
| Ek list single node, no intersect | `NULL` | `m + n + 2` steps baad dono NULL |
| Dono lists ka value same, nodes alag | `NULL` | address compare hota hai, value nahi |

---

## ⏱ Complexity

| | Value | Kyun |
|---|---|---|
| **Time** | `O(m + n)` | Har pointer max ek baar apni list + ek baar doosri list chalta hai |
| **Space** | `O(1)` | Sirf do pointers ✅ (follow-up satisfied) |

---

## ⚠️ Common Mistakes / Traps

1. **Values compare karna** (`curr1->val == curr2->val`) ❌ → same value wale alag nodes pe galat answer.
2. **`curr->next == NULL` pe switch karna** ❌ → no-intersection case mein infinite loop.
3. **Switch ke time `headA` aur `headB` mix kar dena** ❌ → `curr1` ko **`headB`** pe jaana hai (doosri list), apni hi list pe wapas nahi.
4. **Dono pointers ke liye ek hi if-else** ❌ → dono ko independently check karo, kyunki dono alag time pe NULL hote hain.
5. **Lists modify karna** (jaise nodes mark karna ya link todna) ❌ → problem mana karti hai.

---

## 🔁 Alternative 1: Length Difference (Two Pass)

Lengths gino, lambi list ke pointer ko `|m - n|` steps aage badha do, phir dono saath chalao.

```cpp
ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
    int m = 0, n = 0;
    ListNode* p = headA;
    while (p != NULL) { m++; p = p->next; }
    p = headB;
    while (p != NULL) { n++; p = p->next; }

    ListNode* curr1 = headA;
    ListNode* curr2 = headB;
    while (m > n) { curr1 = curr1->next; m--; }   // A lambi → A aage
    while (n > m) { curr2 = curr2->next; n--; }   // B lambi → B aage

    while (curr1 != curr2) {
        curr1 = curr1->next;
        curr2 = curr2->next;
    }
    return curr1;
}
```

Same `O(m + n)` time, `O(1)` space. Logic seedha hai, lekin code lamba. **Meri approach exactly yahi kaam karti hai**, bas lengths gine bina — switch karne se lambi list ka extra hissa apne aap cancel ho jaata hai. 🔥

## 🔁 Alternative 2: Hash Set

A ke saare node addresses set mein daalo, phir B chalao — jo pehla node set mein mile, wahi intersection.

```cpp
ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
    unordered_set<ListNode*> seen;
    while (headA) { seen.insert(headA); headA = headA->next; }
    while (headB) {
        if (seen.count(headB)) return headB;
        headB = headB->next;
    }
    return NULL;
}
```

Time `O(m + n)`, lekin space **`O(m)`** ❌ (follow-up fail). Set mein **pointer** store karo, value nahi.

### Teeno compare

| | Hash Set | Length Difference | **Two Pointer Switch (meri)** |
|---|---|---|---|
| Time | O(m + n) | O(m + n) | O(m + n) |
| Space | O(m) ❌ | O(1) ✅ | O(1) ✅ |
| Code | Easy | Medium | 🔥 Sabse chhota |
| Interview | Brute force bata ke | Explain karne mein easy | Final answer |

---

## 💡 Pro Tips (Tagda Level 🔥)

1. **Compact version (ternary):**
   ```cpp
   while (curr1 != curr2) {
       curr1 = (curr1 == NULL) ? headB : curr1->next;
       curr2 = (curr2 == NULL) ? headA : curr2->next;
   }
   ```
   Same logic, 2 lines. Interview mein if-else wala likho agar explain karna ho.

2. **Infinite loop ka darr?** Har pointer max `m + n + 2` steps chalta hai. Uske baad dono ya toh mil chuke honge ya dono NULL pe honge. Loop hamesha khatam hota hai. ✅

3. **Interview line:** *"Dono pointers apni list khatam karke doosri list pe switch karte hain. Isse dono `a + b + c` distance chalte hain aur intersection pe ek saath pahunchte hain. Intersection na ho toh dono ek saath NULL pe pahunchte hain."*

---

## 🧩 Pattern Recognition

| Problem | Connection |
|---------|-----------|
| **LC 160 Intersection of Two Lists** | Path switch karke distance barabar karo |
| LC 141 Linked List Cycle | Do pointers, alag speed → mil gaye toh cycle |
| LC 142 Linked List Cycle II | Meeting point ke baad ek pointer head pe → dono same speed → cycle start pe milte hain (same "distance barabar" soch) |
| LC 1650 Lowest Common Ancestor of a Binary Tree III | 🔥 Exactly yahi problem! Har node se parent pointer pe upar chalo = do linked lists, intersection = LCA |
| LC 19 Remove Nth Node From End | Ek pointer ko pehle aage badhao, phir dono saath — length difference wali soch |

> 🎯 **Golden idea:** Jab do pointers ko **ek jagah ek saath pahunchana** ho aur distance alag ho, toh ya toh **gap pehle cover karo** (length difference), ya **paths swap karo** (meri approach).

---

## 📝 One-Line Revision

> **"`curr1 = headA`, `curr2 = headB`. Jab tak `curr1 != curr2`: NULL pe pahuncho toh doosri list ke head pe switch, warna aage badho. Dono `a + b + c` chalte hain toh intersection pe milte hain, warna dono ek saath NULL pe. Address compare karo, value nahi. O(m+n) time, O(1) space."**
