# 🔗 21. Merge Two Sorted Lists

![Difficulty](https://img.shields.io/badge/Difficulty-Easy-brightgreen)
![Topic](https://img.shields.io/badge/Topic-Linked%20List-blue)
![Topic](https://img.shields.io/badge/Topic-Two%20Pointers-orange)
![Topic](https://img.shields.io/badge/Topic-Recursion-purple)
![Language](https://img.shields.io/badge/Language-C%2B%2B-00599C)

> **LeetCode:** [21. Merge Two Sorted Lists](https://leetcode.com/problems/merge-two-sorted-lists/)

---

## 📌 Problem Statement

Do **sorted** linked lists `list1` aur `list2` di hain. Dono ko merge karke **ek sorted list** banani hai — **existing nodes ko hi jodkar** (naye nodes nahi banane). Merged list ka head return karo.

| `list1` | `list2` | Output |
|---|---|---|
| `1 → 2 → 4` | `1 → 3 → 4` | `1 → 1 → 2 → 3 → 4 → 4` |
| `[]` | `[]` | `[]` |
| `[]` | `0` | `0` |

**Constraints:** dono lists me nodes `0` se `50` tak, `-100 <= Node.val <= 100`, dono **non-decreasing** order me sorted.

---

## 🧠 Core Intuition (Yaad rakhne wali line)

> **"Do sorted lines ke aage khade logon me se jo chhota hai, usko nayi line me bhejo. Ek line khatam → doosri ka bacha hua poora part seedha jod do."**

- Dono lists ke **front** pe ek-ek pointer (`curr1`, `curr2`).
- Har step pe **chhota node** utha ke result ke **tail** pe jodo, aur usi list ka pointer aage badhao.
- Ek list khatam hote hi → doosri list ka bacha hua part **already sorted** hai → bas ek link se jod do (loop ki zarurat nahi).

🔑 **Key trick — Dummy Node:** Result ka head pehle se pata nahi hota (list1 se aayega ya list2 se?). Ek **fake starting node** (`dummy`) bana lo, uske aage list banao, aur end me `dummy.next` return karo. Isse **"head empty hai kya?"** wala special case khatam ho jaata hai.

---

## 🪜 Approach (Step by Step)

1. `ListNode dummy(-1);` aur `tail = &dummy` *(tail = result ka last node)*
2. Jab tak **dono** lists me nodes hain:
   - `curr1->val <= curr2->val` → `tail->next = curr1`, `curr1` aage
   - warna → `tail->next = curr2`, `curr2` aage
   - `tail = tail->next` *(tail ko naye last node pe le jao)*
3. Jo list bachi hai usko jod do: `tail->next = curr1 ? curr1 : curr2`
4. Return `dummy.next` *(dummy khud answer ka hissa nahi hai)*

---

## 🔍 Dry Run — `list1 = 1 → 2 → 4`, `list2 = 1 → 3 → 4`

*(Subscripts sirf samajhne ke liye: `1ᵃ` list1 ka, `1ᵇ` list2 ka)*

| Step | `curr1` | `curr2` | Compare | Kisko joda | Result (dummy ke baad) |
|---|---|---|---|---|---|
| 1 | 1ᵃ | 1ᵇ | `1 <= 1` ✅ | 1ᵃ | `1ᵃ` |
| 2 | 2 | 1ᵇ | `2 <= 1` ❌ | 1ᵇ | `1ᵃ → 1ᵇ` |
| 3 | 2 | 3 | `2 <= 3` ✅ | 2 | `1 → 1 → 2` |
| 4 | 4ᵃ | 3 | `4 <= 3` ❌ | 3 | `1 → 1 → 2 → 3` |
| 5 | 4ᵃ | 4ᵇ | `4 <= 4` ✅ | 4ᵃ | `1 → 1 → 2 → 3 → 4ᵃ` |
| — | NULL | 4ᵇ | loop khatam | bacha hua `curr2` joda | `1 → 1 → 2 → 3 → 4 → 4` ✅ |

```
dummy → 1ᵃ → 1ᵇ → 2 → 3 → 4ᵃ → 4ᵇ → NULL
  ↑
return dummy.next (yani 1ᵃ)
```

---

## 💻 Code 1 (C++) — Iterative + Dummy Node ✅ (Best)

```cpp
/**
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* list1, ListNode* list2) {
        ListNode dummy(-1);          // fake head (stack pe — delete ki tension nahi)
        ListNode* tail = &dummy;     // result ka last node

        while (list1 != nullptr && list2 != nullptr) {
            if (list1->val <= list2->val) {   // <= → stable (equal pe list1 pehle)
                tail->next = list1;
                list1 = list1->next;
            } else {
                tail->next = list2;
                list2 = list2->next;
            }
            tail = tail->next;
        }

        tail->next = (list1 != nullptr) ? list1 : list2;  // bacha hua part jod do
        return dummy.next;
    }
};
```

> 💡 Tumhare original code me `curr1`/`curr2` alag variables the — wo bhi bilkul sahi hai. Yahan `list1`/`list2` ko hi seedha aage badha diya, kyunki original heads ki baad me zarurat nahi.
> Aur last ke do `if` ki jagah ek line ka **ternary** kaafi hai — dono me se max ek hi non-NULL hoga.

| | Value | Reason |
|---|---|---|
| **Time** | `O(n + m)` | Har node ek baar visit |
| **Space** | `O(1)` | Naye nodes nahi bane, sirf pointers re-link kiye |

---

## 💻 Code 2 (C++) — Recursive (Follow-up)

**Idea:** "Dono heads me jo chhota hai wahi answer ka head hai. Uske `next` me baaki dono lists ka merge recursion se laa do."

```cpp
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* list1, ListNode* list2) {
        if (list1 == nullptr) return list2;   // base case
        if (list2 == nullptr) return list1;   // base case

        if (list1->val <= list2->val) {
            list1->next = mergeTwoLists(list1->next, list2);
            return list1;
        } else {
            list2->next = mergeTwoLists(list1, list2->next);
            return list2;
        }
    }
};
```

### 🔍 Recursive dry run — `1 → 3` & `2`

```
merge(1→3, 2)    : 1 <= 2 → 1->next = merge(3, 2)
  merge(3, 2)    : 3 >  2 → 2->next = merge(3, NULL)
    merge(3, NULL): base case → return 3
  return 2   (2 → 3)
return 1     (1 → 2 → 3) ✅
```

| | Value | Reason |
|---|---|---|
| **Time** | `O(n + m)` | Har call ek node fix karta hai |
| **Space** | `O(n + m)` | Recursion stack — har node ke liye ek call |

---

## ⚖️ Approach Comparison

| Approach | Time | Space | Note |
|---|---|---|---|
| Saari values vector me daalo → sort → nayi list banao | `O((n+m) log(n+m))` | `O(n+m)` | ❌ Sorted hone ka fayda hi nahi uthaya |
| **Iterative + Dummy** | `O(n + m)` | `O(1)` | ⭐ Interview answer |
| **Recursive** | `O(n + m)` | `O(n + m)` | Short & elegant, par lambi lists pe stack overflow risk |

---

## 🎭 Dummy Node: Kyun use karein?

| Dummy ke bina 😩 | Dummy ke saath 😎 |
|---|---|
| Pehle alag se decide karo head kaunsa hai | Seedha `dummy` ke aage jodna shuru |
| `if (head == NULL) head = node; else tail->next = node;` har baar | Sirf `tail->next = node;` |
| Empty list ke edge cases alag handle | Apne aap handle — `dummy.next` NULL hi rahega |

> 🧠 **Rule of thumb:** Jab bhi **nayi linked list build** karni ho ya **head change** ho sakta ho → **dummy node** lagao.

**Stack vs Heap dummy:**
- `ListNode dummy(-1);` → stack pe, function khatam hote hi apne aap free ✅ (tumne yahi kiya — best)
- `ListNode* dummy = new ListNode(-1);` → heap pe, `delete` nahi kiya toh memory leak (LeetCode pe chalta hai, par clean code me dhyan rakho)

---

## ⚠️ Common Mistakes / Gotchas

- ❌ **`tail = tail->next` bhool jaana** → tail aage nahi badhega, har baar same node ka `next` overwrite hoga.
- ❌ **`return dummy.next` ki jagah `dummy` / `&dummy` return karna** → answer me fake `-1` node aa jaayega.
- ❌ Loop ke baad bacha hua part **loop chala ke** ek-ek node jodna → kaam karta hai par bekaar; ek link kaafi hai.
- ❌ Loop condition `||` likh dena (`list1 || list2`) → andar NULL ka `val` access → **crash** 💥
- ❌ Naye nodes banana (`new ListNode(val)`) → problem existing nodes **splice** karne ko bolti hai, aur extra space bhi lagta hai.
- 💡 `<=` use karne se merge **stable** rehta hai (equal values me list1 wala pehle) — merge sort me ye property kaam aati hai.
- 💡 Ek ya dono lists empty → code apne aap handle kar leta hai.

---

## 🧩 Pattern Recognition

> **"Do sorted cheezein ek sorted cheez me jodni hain"** → **Two-pointer merge** (Merge Sort ka merge step).
> **"Nayi list build karni hai"** → **Dummy node**.

Isi pattern pe based problems:

- [88. Merge Sorted Array](https://leetcode.com/problems/merge-sorted-array/) — same merge, par array me (peeche se bharo)
- [23. Merge k Sorted Lists](https://leetcode.com/problems/merge-k-sorted-lists/) — min-heap ya divide & conquer + yahi function
- [148. Sort List](https://leetcode.com/problems/sort-list/) — merge sort = middle (876) + merge (21)
- [2. Add Two Numbers](https://leetcode.com/problems/add-two-numbers/) — dono lists saath traverse + dummy
- [86. Partition List](https://leetcode.com/problems/partition-list/) — do dummy lists banao, phir jodo
- [1669. Merge In Between Linked Lists](https://leetcode.com/problems/merge-in-between-linked-lists/)
- [977. Squares of a Sorted Array](https://leetcode.com/problems/squares-of-a-sorted-array/) — two-pointer merge idea

---

## 📝 30-Second Revision Card

```
ListNode dummy(-1); tail = &dummy
while (l1 && l2):
    if (l1->val <= l2->val): tail->next = l1; l1 = l1->next
    else:                    tail->next = l2; l2 = l2->next
    tail = tail->next                 // bhoolna mat!
tail->next = l1 ? l1 : l2             // bacha hua part
return dummy.next                     // dummy nahi!

Iterative: O(n+m) time | O(1) space   ⭐
Recursive: chhota->next = merge(chhota->next, doosra); return chhota
           O(n+m) time | O(n+m) space
```

---

<p align="center"><i>⭐ Revise → Dry run khud karo → Code bina dekhe likho ⭐</i></p>
