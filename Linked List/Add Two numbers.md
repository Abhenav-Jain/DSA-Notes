# ➕ Add Two Numbers — LeetCode 2

> **Difficulty:** Medium
> **Topics:** Linked List · Math · Simulation · Dummy Node
> **Pattern:** *School wala addition — digit by digit, carry ke saath, dummy + tail se nayi list banao*

---

## 📌 Problem Statement

Do **non-empty** linked lists di hain jo do non-negative numbers represent karti hain. Digits **reverse order** mein stored hain (pehla node = units digit), aur har node mein ek digit hai. Dono numbers ko add karo aur sum ko **usi format** mein linked list ke roop mein return karo.

Numbers mein leading zero nahi hota (sirf `0` khud ho sakta hai).

```
Input:  l1 = 2 → 4 → 3,  l2 = 5 → 6 → 4
Output: 7 → 0 → 8
Kyun:   342 + 465 = 807

Input:  l1 = 0,  l2 = 0
Output: 0

Input:  l1 = 9→9→9→9→9→9→9,  l2 = 9→9→9→9
Output: 8→9→9→9→0→0→0→1
Kyun:   9999999 + 9999 = 10009998
```

**Constraints:**
- Har list mein nodes: `1` to `100`
- `0 <= Node.val <= 9`
- Leading zeros nahi hain

---

## 🧠 Core Intuition

### Reverse order = vardaan hai 🎁

School mein addition **right se left** (units → tens → hundreds) karte hain, carry aage le jaate hue:

```
    3 4 2
  + 4 6 5
  -------
    8 0 7
```

Linked list mein digits **already reverse** stored hain, yaani pehla node hi units digit hai. Toh dono lists ko **left se right** chalana = school wala **right se left** addition! Kuch ulta karne ki zarurat nahi. 🔥

```
l1:  2 → 4 → 3
l2:  5 → 6 → 4
     ↓   ↓   ↓
     7   10  7+1
     7   0   8      (carry 1 aage gaya)
```

### Har step pe kya hota hai

```
sum   = l1 ka digit + l2 ka digit + carry
digit = sum % 10      → nayi list mein ye jaata hai
carry = sum / 10      → agle step mein add hoga (0 ya 1)
```

> 🧠 Do digits (max 9 + 9) + carry (max 1) = **max 19**. Isliye carry hamesha **0 ya 1** hi hota hai.

---

## ❌ Number mein convert karke add kyun nahi?

```cpp
// Galat soch: list → number → add → wapas list
```

List mein **100 digits** tak ho sakte hain. `long long` bhi sirf ~19 digits sambhal sakta hai → **overflow** 💥
Isliye digit-by-digit simulation hi sahi tarika hai.

---

## ⭐ Meri Approach — Step by Step

### Step 1: Dummy + pointer setup

```cpp
ListNode* result = new ListNode(0);    // dummy
ListNode* ptr = result;                // tail — nayi list ka last node
int carry = 0;
```

Yahan **nayi list scratch se** ban rahi hai, isliye dummy ka `next` kisi se jodne ki zarurat nahi. (Yaad hai? *"Nayi list bana rahe ho → `dummy->next = head` mat karo"*)

### Step 2: Loop condition — teen cheezein

```cpp
while(l1 != NULL || l2 != NULL || carry != 0)
```

| Condition | Kab zaroori |
|-----------|-------------|
| `l1 != NULL` | l1 mein digits bache hain |
| `l2 != NULL` | l2 mein digits bache hain (lists ki length alag ho sakti hai) |
| `carry != 0` | 🔥 dono lists khatam, lekin last carry bacha hai (`5 + 5 = 10` → `0 → 1`) |

> `||` (OR) lagao, `&&` (AND) nahi. Jab tak **koi bhi ek** cheez bachi hai, loop chalna chahiye.

### Step 3: Sum nikalo — jo list khatam ho gayi, usko 0 maano

```cpp
int sum = carry;
if(l1 != NULL){
    sum += l1->val;
    l1 = l1->next;
}
if(l2 != NULL){
    sum += l2->val;
    l2 = l2->next;
}
```

`sum = carry` se shuru karna smart hai: carry pehle hi add ho gaya. Aur jo list `NULL` hai, uska kuch add nahi hota, matlab use **0** maan liya. Alag lengths ka case apne aap handle ho gaya. ✅

### Step 4: Carry aur digit alag karo

```cpp
carry = sum / 10;     // 17 / 10 = 1
sum = sum % 10;       // 17 % 10 = 7
```

> ⚠️ **Order matter karta hai!** Pehle `carry` nikalo, phir `sum` ko overwrite karo. Ulta kiya toh `sum` already `7` ban chuka hoga aur `carry = 7/10 = 0` aayega ❌

### Step 5: Nayi node jodo aur tail aage badhao

```cpp
ptr->next = new ListNode(sum);
ptr = ptr->next;
```

### Step 6: Dummy ke baad se return

```cpp
return result->next;
```

---

## ✅ Full Code (Meri Approach)

```cpp
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        ListNode* result = new ListNode(0);    // dummy
        ListNode* ptr = result;                // tail
        int carry = 0;

        while(l1 != NULL || l2 != NULL || carry != 0){
            int sum = carry;
            if(l1 != NULL){
                sum += l1->val;
                l1 = l1->next;
            }
            if(l2 != NULL){
                sum += l2->val;
                l2 = l2->next;
            }
            carry = sum / 10;      // pehle carry
            sum = sum % 10;        // phir digit

            ptr->next = new ListNode(sum);
            ptr = ptr->next;
        }
        return result->next;
    }
};
```

---

## 🔍 Dry Run

### Case A: `2→4→3` + `5→6→4` (342 + 465)

| Step | l1 | l2 | carry (in) | sum | carry (out) | digit | Result list |
|------|----|----|-----------|-----|-------------|-------|-------------|
| 1 | 2 | 5 | 0 | 7 | 0 | 7 | `7` |
| 2 | 4 | 6 | 0 | 10 | 1 | 0 | `7 → 0` |
| 3 | 3 | 4 | 1 | 8 | 0 | 8 | `7 → 0 → 8` |
| — | NULL | NULL | 0 | | | | loop khatam |

**Output: `7 → 0 → 8`** (807) ✅

### Case B: Alag length — `9→9→9→9→9→9→9` + `9→9→9→9`

| Step | l1 | l2 | carry in | sum | carry out | digit |
|------|----|----|----------|-----|-----------|-------|
| 1 | 9 | 9 | 0 | 18 | 1 | 8 |
| 2 | 9 | 9 | 1 | 19 | 1 | 9 |
| 3 | 9 | 9 | 1 | 19 | 1 | 9 |
| 4 | 9 | 9 | 1 | 19 | 1 | 9 |
| 5 | 9 | NULL | 1 | 10 | 1 | 0 |
| 6 | 9 | NULL | 1 | 10 | 1 | 0 |
| 7 | 9 | NULL | 1 | 10 | 1 | 0 |
| 8 | NULL | NULL | 1 | 1 | 0 | 1 👈 sirf carry ki wajah se |

**Output: `8→9→9→9→0→0→0→1`** ✅
Step 5 se l2 khatam → 0 maana gaya. Step 8 mein dono khatam, sirf `carry != 0` ne loop chalaya.

### Case C: Last carry — `5` + `5`

| Step | l1 | l2 | carry in | sum | carry out | digit |
|------|----|----|----------|-----|-----------|-------|
| 1 | 5 | 5 | 0 | 10 | 1 | 0 |
| 2 | NULL | NULL | 1 | 1 | 0 | 1 |

**Output: `0 → 1`** (10) ✅
> Agar loop condition mein `carry != 0` nahi hota → output sirf `0` aata ❌

---

## 🧪 Edge Cases

| l1 | l2 | Output | Kaise handle hua |
|----|----|--------|------------------|
| `[0]` | `[0]` | `[0]` | ek step, sum 0 |
| `[5]` | `[5]` | `[0,1]` | `carry != 0` condition |
| `[1,8]` | `[0]` | `[1,8]` | l2 jaldi khatam → 0 maana |
| `[9,9]` | `[1]` | `[0,0,1]` | carry chain, end mein extra node |
| 100 digits | 100 digits | 100 ya 101 digits | koi overflow nahi, digit-by-digit |

---

## ⏱ Complexity

| | Value | Kyun |
|---|---|---|
| **Time** | `O(max(m, n))` | Lambi list jitne steps (+1 last carry ke liye) |
| **Space** | `O(max(m, n))` | Output list ke nodes — ye **answer** hai, isliye zaroori hai |
| **Extra Space** | `O(1)` | Output ke alawa sirf `carry`, `sum`, `ptr` |

> Interview mein bol do: *"Output list ko chhod ke extra space O(1) hai."*

---

## ⚠️ Common Mistakes / Traps

1. **Loop condition mein `carry` bhoolna** → `5 + 5` ka answer `0` aayega, `10` nahi. Sabse common bug! 🔥
2. **`&&` lagana `||` ki jagah** → chhoti list khatam hote hi loop ruk jaayega, lambi list ke baaki digits gayab.
3. **`sum % 10` pehle, `sum / 10` baad mein** → carry hamesha 0 aayega.
4. **`l1->val` bina NULL check** → alag length pe crash.
5. **Number mein convert karna** → 100 digits pe overflow.
6. **`result` return karna `result->next` ki jagah** → output ke aage ek faltu `0` aa jaayega (dummy).
7. **Har list ke liye alag loop likhna** (pehle dono saath, phir bachi hui l1, phir bachi hui l2) → chalega, lekin 3 loops = 3 jagah bug ka chance. Mera single loop + NULL check zyada clean hai. ✅

---

## 💡 Pro Tips (Tagda Level 🔥)

### Tip 1: Stack pe dummy (memory leak se bachao)
`new ListNode(0)` heap pe banta hai aur kabhi `delete` nahi hota. LeetCode pe chalta hai, lekin clean code ke liye:
```cpp
ListNode dummy(0);
ListNode* ptr = &dummy;
// ... same loop ...
return dummy.next;
```

### Tip 2: Ternary se compact sum
```cpp
int sum = carry + (l1 ? l1->val : 0) + (l2 ? l2->val : 0);
if (l1) l1 = l1->next;
if (l2) l2 = l2->next;
```
Same kaam, kam lines. Lekin meri if-wali version padhne mein zyada clear hai. 👍

### Tip 3: Interview line
*"Digits reverse order mein hain, toh list ka head units place hai. Main school wala addition simulate karunga: har step pe dono digits + carry, `% 10` nayi node mein, `/ 10` agle carry mein. Loop tab tak jab tak koi list ya carry bacha hai."*

---

## 🔁 Alternative: Recursive (sirf samajhne ke liye)

```cpp
ListNode* add(ListNode* l1, ListNode* l2, int carry) {
    if (!l1 && !l2 && carry == 0) return NULL;      // base case

    int sum = carry;
    if (l1) { sum += l1->val; l1 = l1->next; }
    if (l2) { sum += l2->val; l2 = l2->next; }

    ListNode* node = new ListNode(sum % 10);
    node->next = add(l1, l2, sum / 10);              // baaki recursion pe
    return node;
}

ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
    return add(l1, l2, 0);
}
```

Base case wahi loop condition ka ulta hai. Space `O(max(m, n))` recursion stack, isliye iterative better hai.

---

## 🧩 Pattern Recognition

| Problem | Connection |
|---------|-----------|
| **LC 2 Add Two Numbers** | Reverse order → seedha simulation |
| **LC 445 Add Two Numbers II** 🔥 | Digits **seedhe order** mein hain! Do tarike: (1) dono lists reverse karo (LC 206) → LC 2 lagao → result reverse karo, ya (2) dono ko **stack** mein daalo aur pop karte hue add karo |
| LC 67 Add Binary | Same carry logic, bas base 2 (`% 2`, `/ 2`) |
| LC 415 Add Strings | Same, strings pe — peeche se index chalao |
| LC 66 Plus One | Carry chain (`999 + 1`) |
| LC 21 Merge Two Sorted Lists | Same "dummy + tail + do lists saath chalao" template |

> 🎯 **Golden template (carry addition):**
> ```
> while (a bacha || b bacha || carry) {
>     sum = carry + (a ka digit ya 0) + (b ka digit ya 0)
>     digit = sum % base,  carry = sum / base
> }
> ```
> Base 10 → LC 2, 415, 445. Base 2 → LC 67.

---

## 📝 One-Line Revision

> **"Digits reverse mein hain toh head = units place. Dummy + tail lo. Jab tak `l1 || l2 || carry` bacha hai: `sum = carry + dono digits (NULL = 0)`, pehle `carry = sum / 10`, phir `digit = sum % 10` ki nayi node jodo. Return `dummy->next`. O(max(m,n)) time."**
