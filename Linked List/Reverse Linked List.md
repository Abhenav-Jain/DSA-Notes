# 🔄 206. Reverse Linked List

![Difficulty](https://img.shields.io/badge/Difficulty-Easy-brightgreen)
![Topic](https://img.shields.io/badge/Topic-Linked%20List-blue)
![Topic](https://img.shields.io/badge/Topic-Recursion-purple)
![Language](https://img.shields.io/badge/Language-C%2B%2B-00599C)

> **LeetCode:** [206. Reverse Linked List](https://leetcode.com/problems/reverse-linked-list/)

---

## 📌 Problem Statement

Ek singly linked list ka `head` diya hai. List ko **reverse** karke naya `head` return karna hai.

| Input | Output |
|---|---|
| `1 → 2 → 3 → 4 → 5` | `5 → 4 → 3 → 2 → 1` |
| `1 → 2` | `2 → 1` |
| `[]` (empty) | `[]` |

**Constraints:** nodes `0` se `5000` tak, `-5000 <= Node.val <= 5000`

**Follow-up:** Iterative aur recursive dono tarike se karo.

---

## 🧠 Core Intuition (Yaad rakhne wali line)

> **"Har node ka arrow ulta kar do — par ulta karne se pehle aage wala node save kar lo, warna list kho jaayegi."**

- Naye nodes nahi banane, sirf **`next` pointers ki direction ulti** karni hai.
- 3 pointers chahiye:
  - **`prev`** → piche wala (jisko ab arrow point karega)
  - **`curr`** → abhi jis node pe kaam ho raha hai
  - **`next`** → aage wala node (backup, taaki link toote toh bhi aage jaa sakein)

🔑 **Key insight:** Jab `curr` NULL ho jaata hai, tab `prev` last node pe hota hai → wahi **naya head** hai.

---

## 🪜 Approach (4 Steps — Ratt lo 🔁)

Har iteration me, **isi order me**:

```
1. next = curr->next     // 💾 aage wala save karo
2. curr->next = prev     // 🔄 arrow ulta karo
3. prev = curr           // ⏩ prev aage badhao
4. curr = next           // ⏩ curr aage badhao
```

> 🧠 **Trick to remember:** Har line ka **right side**, agli line ka **left side** ban jaata hai —
> `curr->next` → `curr->next` → `curr` → `curr`... ek chain jaisa pattern.
> `next = curr->next` ➜ `curr->next = prev` ➜ `prev = curr` ➜ `curr = next`

---

## 🔍 Dry Run — `1 → 2 → 3 → NULL`

**Initial:** `prev = NULL`, `curr = 1`

| Iteration | `next` | Arrow ulta | `prev` | `curr` | List ki state |
|---|---|---|---|---|---|
| 1 | 2 | `1 → NULL` | 1 | 2 | `NULL ← 1`  &nbsp; `2 → 3 → NULL` |
| 2 | 3 | `2 → 1` | 2 | 3 | `NULL ← 1 ← 2`  &nbsp; `3 → NULL` |
| 3 | NULL | `3 → 2` | 3 | NULL | `NULL ← 1 ← 2 ← 3` |
| — | — | `curr == NULL` → loop khatam | **3** | NULL | return `prev` ✅ |

```
Before:   1 → 2 → 3 → NULL
After:    NULL ← 1 ← 2 ← 3
                          ↑
                       new head (prev)
```

---

## 💻 Code 1 (C++) — Iterative ✅ (Best)

```cpp
/**
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(nullptr) {}
 * };
 */
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        ListNode* prev = nullptr;
        ListNode* curr = head;

        while (curr != nullptr) {
            ListNode* next = curr->next; // 1. aage wala save
            curr->next = prev;           // 2. arrow ulta
            prev = curr;                 // 3. prev aage
            curr = next;                 // 4. curr aage
        }
        return prev; // prev hi naya head hai
    }
};
```

| | Value | Reason |
|---|---|---|
| **Time** | `O(n)` | Har node ek baar visit |
| **Space** | `O(1)` | Sirf 3 pointers |

---

## 💻 Code 2 (C++) — Recursive (Follow-up)

**Idea:** "Baaki list (`head->next` se aage) recursion reverse kar dega. Mujhe sirf apne aap ko uske end me jodna hai."

```cpp
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        // Base case: empty list ya single node
        if (head == nullptr || head->next == nullptr) return head;

        ListNode* newHead = reverseList(head->next); // baaki list reverse

        head->next->next = head;   // aage wala node ab mujhe point kare
        head->next = nullptr;      // mera purana link todo (cycle avoid)

        return newHead;            // naya head har level pe same rehta hai
    }
};
```

### 🔍 Recursive dry run — `1 → 2 → 3`

```
reverseList(1)
 └─ reverseList(2)
     └─ reverseList(3) → base case, return 3   (newHead = 3)
     2->next->next = 2   → 3 → 2
     2->next = NULL      → 3 → 2 → NULL
     return 3
 1->next->next = 1       → 2 → 1
 1->next = NULL          → 3 → 2 → 1 → NULL
 return 3 ✅
```

| | Value | Reason |
|---|---|---|
| **Time** | `O(n)` | Har node pe ek call |
| **Space** | `O(n)` | Recursion stack (n calls deep) |

---

## ⚖️ Approach Comparison

| Approach | Time | Space | Note |
|---|---|---|---|
| Stack / vector me values daal ke rebuild | `O(n)` | `O(n)` | Kaam karta hai par interview me weak |
| **Iterative (3 pointers)** | `O(n)` | `O(1)` | ⭐ Default answer |
| **Recursive** | `O(n)` | `O(n)` | Follow-up ke liye; bahut lambi list pe stack overflow ka risk |

---

## ⚠️ Common Mistakes / Gotchas

- ❌ **`next` save kiye bina `curr->next = prev` kar dena** → baaki list ka link kho jaata hai. Step 1 hamesha pehle!
- ❌ **`head` return kar dena** → `head` ab last node hai. Return **`prev`** karo.
- ❌ Loop condition `curr->next != NULL` rakhna → last node reverse hi nahi hoga.
- ❌ Recursion me `head->next = nullptr` bhool jaana → **cycle** ban jaati hai (`1 ⇄ 2`).
- ❌ Empty list (`head == NULL`) handle na karna → iterative code me ye apne aap handle hota hai (loop chalega hi nahi, `prev = NULL` return).
- 💡 C++ me `NULL` ki jagah **`nullptr`** use karo — modern & type-safe.
- 💡 `next` ko loop ke andar declare karna cleaner hai (scope chhota rehta hai).

---

## 🧩 Pattern Recognition

> **"Linked list me direction / order badalna"** → **prev–curr–next** 3-pointer technique.

Ye problem bahut saare problems ka **building block** hai:

- [92. Reverse Linked List II](https://leetcode.com/problems/reverse-linked-list-ii/) — sirf `left` se `right` tak reverse
- [25. Reverse Nodes in k-Group](https://leetcode.com/problems/reverse-nodes-in-k-group/) — har k nodes ka group reverse
- [234. Palindrome Linked List](https://leetcode.com/problems/palindrome-linked-list/) — second half reverse karke compare
- [143. Reorder List](https://leetcode.com/problems/reorder-list/) — middle find + reverse + merge
- [24. Swap Nodes in Pairs](https://leetcode.com/problems/swap-nodes-in-pairs/)
- [2130. Maximum Twin Sum of a Linked List](https://leetcode.com/problems/maximum-twin-sum-of-a-linked-list/)

---

## 📝 30-Second Revision Card

```
prev = NULL, curr = head
while (curr):
    next = curr->next     // save
    curr->next = prev     // reverse
    prev = curr           // move
    curr = next           // move
return prev

Iterative: O(n) time | O(1) space   ⭐
Recursive: newHead = rev(head->next);
           head->next->next = head; head->next = NULL;
           O(n) time | O(n) space
```

---

<p align="center"><i>⭐ Revise → Dry run khud karo → Code bina dekhe likho ⭐</i></p>
