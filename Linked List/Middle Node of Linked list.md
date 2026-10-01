# 🎯 876. Middle of the Linked List

![Difficulty](https://img.shields.io/badge/Difficulty-Easy-brightgreen)
![Topic](https://img.shields.io/badge/Topic-Linked%20List-blue)
![Topic](https://img.shields.io/badge/Topic-Two%20Pointers-orange)
![Language](https://img.shields.io/badge/Language-C%2B%2B-00599C)

> **LeetCode:** [876. Middle of the Linked List](https://leetcode.com/problems/middle-of-the-linked-list/)

---

## 📌 Problem Statement

Singly linked list ka `head` diya hai. **Middle node** return karna hai.
Agar **2 middle** hain (even length), toh **second middle** return karo.

| Input | Output | Note |
|---|---|---|
| `1 → 2 → 3 → 4 → 5` | `3 → 4 → 5` | Odd → ek hi middle |
| `1 → 2 → 3 → 4 → 5 → 6` | `4 → 5 → 6` | Even → middles 3 & 4, return **4** |
| `1` | `1` | Single node |

**Constraints:** nodes `1` se `100` tak, `1 <= Node.val <= 100`

---

## 🧠 Core Intuition (Yaad rakhne wali line)

> **"Ek kachhua 🐢 (1 step), ek khargosh 🐇 (2 steps). Jab khargosh end pe pahunchega, kachhua theek beech me hoga."**

- **`slow`** ek baar me **1 step** chalta hai.
- **`fast`** ek baar me **2 steps** chalta hai.
- `fast` ki speed double hai → jab `fast` ne poori list cover ki, `slow` ne **aadhi** cover ki = **middle** ✅

🔑 **Key insight:** List ki length pehle se jaanne ki zarurat nahi → **one pass** me kaam ho jaata hai.

---

## 🪜 Approach (Step by Step)

1. `slow = head`, `fast = head`
2. Jab tak `fast != NULL` **aur** `fast->next != NULL`:
   - `slow = slow->next` *(1 step)*
   - `fast = fast->next->next` *(2 steps)*
3. Return `slow`.

### ❓ Condition me dono check kyun?

| Check | Kab kaam aata hai |
|---|---|
| `fast != NULL` | **Even** length — `fast` list ke bahar (NULL) nikal jaata hai |
| `fast->next != NULL` | **Odd** length — `fast` last node pe ruk jaata hai |

> ⚠️ Order important hai! Pehle `fast != NULL` check karo, warna `fast->next` pe **NULL dereference → crash** 💥

---

## 🔍 Dry Run

### Case 1 — Odd: `1 → 2 → 3 → 4 → 5`

| Step | `slow` | `fast` | Condition (`fast && fast->next`) |
|---|---|---|---|
| Start | 1 | 1 | ✅ true |
| 1 | 2 | 3 | ✅ true |
| 2 | 3 | 5 | ❌ `fast->next == NULL` → stop |
| **Return** | **3** ✅ | | |

```
1 → 2 → 3 → 4 → 5 → NULL
        🐢      🐇
```

### Case 2 — Even: `1 → 2 → 3 → 4 → 5 → 6`

| Step | `slow` | `fast` | Condition |
|---|---|---|---|
| Start | 1 | 1 | ✅ true |
| 1 | 2 | 3 | ✅ true |
| 2 | 3 | 5 | ✅ true |
| 3 | 4 | NULL | ❌ `fast == NULL` → stop |
| **Return** | **4** ✅ (second middle) | | |

```
1 → 2 → 3 → 4 → 5 → 6 → NULL
            🐢           🐇
```

---

## 💻 Code 1 (C++) — Slow & Fast Pointer ✅ (Best)

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
    ListNode* middleNode(ListNode* head) {
        ListNode* slow = head;   // 🐢 1 step
        ListNode* fast = head;   // 🐇 2 steps

        while (fast != nullptr && fast->next != nullptr) {
            slow = slow->next;
            fast = fast->next->next;
        }
        return slow;  // slow = middle (even me second middle)
    }
};
```

| | Value | Reason |
|---|---|---|
| **Time** | `O(n)` | `fast` list ek baar traverse karta hai (~n/2 iterations) |
| **Space** | `O(1)` | Sirf 2 pointers |

---

## 💻 Code 2 (C++) — Count Approach (Brute / Two Pass)

**Idea:** Pehle length `n` gino, phir `n/2` steps chalo.

```cpp
class Solution {
public:
    ListNode* middleNode(ListNode* head) {
        int n = 0;
        for (ListNode* t = head; t != nullptr; t = t->next) n++;  // pass 1: count

        ListNode* curr = head;
        for (int i = 0; i < n / 2; i++) curr = curr->next;         // pass 2: n/2 steps
        return curr;
    }
};
```

> `n = 5` → 2 steps → node 3 ✅ &nbsp;|&nbsp; `n = 6` → 3 steps → node 4 ✅ (second middle)

| | Value |
|---|---|
| **Time** | `O(n)` — par **2 passes** |
| **Space** | `O(1)` |

---

## 🔀 Variant: FIRST Middle chahiye ho toh?

Kai problems (jaise *Palindrome Linked List*, *Sort List*, *Reorder List*) me even length pe **first middle** chahiye hota hai. Bas condition badlo:

```cpp
// head non-null maan ke chal rahe hain
ListNode* slow = head;
ListNode* fast = head;
while (fast->next != nullptr && fast->next->next != nullptr) {
    slow = slow->next;
    fast = fast->next->next;
}
return slow;  // 1→2→3→4→5→6 pe 3 return karega
```

| Condition | Even list `1..6` ka result |
|---|---|
| `fast && fast->next` | **4** (second middle) — LC 876 |
| `fast->next && fast->next->next` | **3** (first middle) — split / merge sort ke liye |

---

## ⚖️ Approach Comparison

| Approach | Time | Space | Note |
|---|---|---|---|
| Array / vector me nodes store karke `arr[n/2]` | `O(n)` | `O(n)` | Extra space waste |
| Count + n/2 steps | `O(n)` (2 pass) | `O(1)` | Theek hai |
| **Slow & Fast (Tortoise–Hare)** | `O(n)` (1 pass) | `O(1)` | ⭐ Interview answer |

---

## ⚠️ Common Mistakes / Gotchas

- ❌ Condition ka **order ulta** likhna: `fast->next != NULL && fast != NULL` → even case me NULL dereference → **crash**.
- ❌ Sirf `fast->next != NULL` check karna → even list me `fast` NULL ho jaayega aur agle check pe crash.
- ❌ Sirf `fast != NULL` check karna → odd list me `fast->next->next` pe crash.
- ❌ First vs Second middle confuse karna — problem dhyan se padho kaunsa chahiye.
- 💡 C++ me `NULL` ki jagah **`nullptr`** prefer karo.

---

## 🧩 Pattern Recognition

> **"Linked list me middle / cycle / nth-from-end / length bina gine position"** → **Slow & Fast pointers** socho.

Isi pattern pe based problems:

- [141. Linked List Cycle](https://leetcode.com/problems/linked-list-cycle/) — fast aur slow mile toh cycle hai
- [142. Linked List Cycle II](https://leetcode.com/problems/linked-list-cycle-ii/) — cycle ka starting node
- [234. Palindrome Linked List](https://leetcode.com/problems/palindrome-linked-list/) — middle + reverse (206) + compare
- [143. Reorder List](https://leetcode.com/problems/reorder-list/) — middle + reverse + merge
- [148. Sort List](https://leetcode.com/problems/sort-list/) — merge sort me middle se split
- [2095. Delete the Middle Node of a Linked List](https://leetcode.com/problems/delete-the-middle-node-of-a-linked-list/)
- [19. Remove Nth Node From End of List](https://leetcode.com/problems/remove-nth-node-from-end-of-list/) — fast ko n steps aage bhejo
- [202. Happy Number](https://leetcode.com/problems/happy-number/) — linked list nahi, par same cycle-detection idea

---

## 📝 30-Second Revision Card

```
slow = fast = head
while (fast && fast->next):      // order matters!
    slow = slow->next            // 🐢 1 step
    fast = fast->next->next      // 🐇 2 steps
return slow

Odd  → exact middle
Even → SECOND middle
First middle chahiye → while (fast->next && fast->next->next)

Time O(n) | Space O(1) | One pass
```

---

<p align="center"><i>⭐ Revise → Dry run khud karo → Code bina dekhe likho ⭐</i></p>
