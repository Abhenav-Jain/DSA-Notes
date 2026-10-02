# 🔀 143. Reorder List

![Difficulty](https://img.shields.io/badge/Difficulty-Medium-orange)
![Topic](https://img.shields.io/badge/Topic-Linked%20List-blue)
![Topic](https://img.shields.io/badge/Topic-Two%20Pointers-orange)
![Topic](https://img.shields.io/badge/Topic-Stack-lightgrey)
![Language](https://img.shields.io/badge/Language-C%2B%2B-00599C)

> **LeetCode:** [143. Reorder List](https://leetcode.com/problems/reorder-list/)

---

## 📌 Problem Statement

Linked list diya hai: `L0 → L1 → … → Ln-1 → Ln`
Isko aise reorder karna hai: `L0 → Ln → L1 → Ln-1 → L2 → Ln-2 → …`

Matlab: **ek aage se, ek peeche se, ek aage se, ek peeche se...**
Node ki **values change nahi** karni — sirf **nodes ke links** badalne hain. Function `void` hai (in-place).

| Input | Output |
|---|---|
| `1 → 2 → 3 → 4` | `1 → 4 → 2 → 3` |
| `1 → 2 → 3 → 4 → 5` | `1 → 5 → 2 → 4 → 3` |
| `1` | `1` |

**Constraints:** nodes `1` se `5 × 10⁴` tak, `1 <= Node.val <= 1000`

---

## 🧠 Core Intuition (Yaad rakhne wali line)

> **"Peeche se aage aana singly linked list me mushkil hai — toh second half ko ulta kar do! Ab dono halves ko aage se hi chalte hue ek-ek karke jodo (zipper 🤐 jaisa)."**

Ye problem **3 easy problems ka combo** hai 🧩:

| Step | Kya karna hai | Kaunsa problem |
|---|---|---|
| 1️⃣ | List ka **middle** dhundo | [876. Middle of the Linked List](https://leetcode.com/problems/middle-of-the-linked-list/) |
| 2️⃣ | Middle ke baad wala part **reverse** karo | [206. Reverse Linked List](https://leetcode.com/problems/reverse-linked-list/) |
| 3️⃣ | Dono halves ko **alternate merge** karo | [21. Merge Two Sorted Lists](https://leetcode.com/problems/merge-two-sorted-lists/) jaisa (bas compare nahi, alternate) |

```
1 → 2 → 3 → 4 → 5 → 6

Step 1 (split):     1 → 2 → 3 → 4        5 → 6
Step 2 (reverse):   1 → 2 → 3 → 4        6 → 5
Step 3 (zip):       1 → 6 → 2 → 5 → 3 → 4  ✅
```

---

## 🪜 Approach (Step by Step)

1. **Middle nikalo** — `slow` / `fast` pointers (`slow` = middle).
2. **Second half reverse karo** — `second = reverse(slow->next)`
3. **List ko todo** — `slow->next = NULL` ⚠️ *(ye bhoole toh cycle ban jaayegi)*
4. **Zip / merge karo** — `first = head`, jab tak `second != NULL`:
   - `next1 = first->next`, `next2 = second->next` *(dono ke aage wale save)*
   - `first->next = second` *(first ke baad second)*
   - `second->next = next1` *(second ke baad first ka agla)*
   - `first = next1`, `second = next2` *(dono aage badho)*

---

## 🔍 Dry Run

### Case 1 — Odd: `1 → 2 → 3 → 4 → 5`

**Step 1 — Middle:** `slow` = **3**

| | `slow` | `fast` |
|---|---|---|
| start | 1 | 1 |
| 1 | 2 | 3 |
| 2 | 3 | 5 → `fast->next == NULL` stop |

**Step 2 & 3 — Reverse + Split:**
```
first:  1 → 2 → 3 → NULL
second: 5 → 4 → NULL        (4 → 5 reversed)
```

**Step 4 — Zip:**

| Iter | `first` | `second` | `next1` | `next2` | Links bane | List ab tak |
|---|---|---|---|---|---|---|
| 1 | 1 | 5 | 2 | 4 | `1→5`, `5→2` | `1 → 5 → 2 → 3` |
| 2 | 2 | 4 | 3 | NULL | `2→4`, `4→3` | `1 → 5 → 2 → 4 → 3` |
| — | 3 | NULL | | | loop khatam | **`1 → 5 → 2 → 4 → 3`** ✅ |

### Case 2 — Even: `1 → 2 → 3 → 4 → 5 → 6`

**Middle** (second middle): `slow` = **4**

```
first:  1 → 2 → 3 → 4 → NULL
second: 6 → 5 → NULL
```

| Iter | `first` | `second` | Links bane | List ab tak |
|---|---|---|---|---|
| 1 | 1 | 6 | `1→6`, `6→2` | `1 → 6 → 2 → 3 → 4` |
| 2 | 2 | 5 | `2→5`, `5→3` | `1 → 6 → 2 → 5 → 3 → 4` |
| — | 3 | NULL | loop khatam | **`1 → 6 → 2 → 5 → 3 → 4`** ✅ |

> 💡 Last wala node (3 & 4) already sahi jagah pe hai — `first` half me hi jude hue the.

---

## ❓ Ye crash kyun nahi karta? (Important)

`fast && fast->next` condition **second middle** deti hai → `slow` first half me include hota hai.
Isliye **first half ≥ second half** (ya toh barabar+1, ya +2... hamesha bada ya barabar):

| n | first half | second half |
|---|---|---|
| 4 | 3 nodes | 1 node |
| 5 | 3 nodes | 2 nodes |
| 6 | 4 nodes | 2 nodes |

Loop `second != NULL` tak chalta hai → jab tak `second` bacha hai, `first` bhi pakka bacha hai → `first->next` pe **NULL crash nahi** ✅

---

## 💻 Code 1 (C++) — Middle + Reverse + Merge ✅ (Best)

```cpp
/**
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(nullptr) {}
 * };
 */
class Solution {
    // LC 206 — iterative reverse
    ListNode* reverse(ListNode* head) {
        ListNode* prev = nullptr;
        ListNode* curr = head;
        while (curr != nullptr) {
            ListNode* next = curr->next;
            curr->next = prev;
            prev = curr;
            curr = next;
        }
        return prev;
    }

public:
    void reorderList(ListNode* head) {
        if (!head || !head->next) return;   // 0 ya 1 node → kuch nahi karna

        // 1️⃣ Middle nikalo (LC 876)
        ListNode* slow = head;
        ListNode* fast = head;
        while (fast != nullptr && fast->next != nullptr) {
            slow = slow->next;
            fast = fast->next->next;
        }

        // 2️⃣ Second half reverse  +  3️⃣ list todo
        ListNode* second = reverse(slow->next);
        slow->next = nullptr;               // ⚠️ bhoolna mat — warna cycle

        // 4️⃣ Zip merge
        ListNode* first = head;
        while (second != nullptr) {
            ListNode* next1 = first->next;  // save
            ListNode* next2 = second->next; // save

            first->next = second;           // first → second
            second->next = next1;           // second → first ka agla

            first = next1;                  // aage
            second = next2;                 // aage
        }
    }
};
```

> 💡 Ye tumhara hi first attempt hai (aur **pass** hota hai ✅). Notes me sirf clean-up kiya:
> - `mark1 / temp_curr / temp_mark` → `second / next1 / next2` (padhne me easy)
> - `NULL` → `nullptr`
> - Empty / single node ke liye shuru me safety check

| | Value | Reason |
|---|---|---|
| **Time** | `O(n)` | Middle `n/2` + reverse `n/2` + merge `n/2` |
| **Space** | `O(1)` | Sirf pointers, sab in-place ✅ |

---

## 💻 Code 2 (C++) — Brute: Vector of Nodes (Two Pointers)

**Idea:** Saare nodes ka **address** ek vector me daal do. Ab `i` (aage se) aur `j` (peeche se) se seedha index access mil gaya → alternate jodo.

```cpp
class Solution {
public:
    void reorderList(ListNode* head) {
        vector<ListNode*> arr;
        for (ListNode* t = head; t != nullptr; t = t->next) arr.push_back(t);

        int i = 0, j = arr.size() - 1;
        while (i < j) {
            arr[i]->next = arr[j];      // aage wala → peeche wala
            i++;
            if (i == j) break;          // even case: beech me mil gaye
            arr[j]->next = arr[i];      // peeche wala → agla aage wala
            j--;
        }
        arr[i]->next = nullptr;         // ⚠️ last node ka link todo
    }
};
```

| | Value |
|---|---|
| **Time** | `O(n)` |
| **Space** | `O(n)` — vector |

> Interview me pehle ye bata sakte ho, phir bolo *"space O(1) karne ke liye middle + reverse + merge karunga"* 🚀

---

## 🔀 Variant: First middle use karein toh?

Agar `while (fast->next && fast->next->next)` (first middle) use karo, tab bhi **answer same** aata hai:

| n = 6 | first half | second (reversed) | Result |
|---|---|---|---|
| Second middle (`slow`=4) | `1 2 3 4` | `6 5` | `1 6 2 5 3 4` ✅ |
| First middle (`slow`=3) | `1 2 3` | `6 5 4` | `1 6 2 5 3 4` ✅ |

> Dono kaam karte hain kyunki first half kabhi second half se **chhota** nahi hota. Jo tumhe yaad ho wahi use karo.

---

## ⚖️ Approach Comparison

| Approach | Time | Space | Note |
|---|---|---|---|
| Har baar last node dhundh ke jodna | `O(n²)` | `O(1)` | ❌ TLE (n = 5×10⁴) |
| Vector / deque / stack of nodes | `O(n)` | `O(n)` | Easy, par extra space |
| **Middle + Reverse + Merge** | `O(n)` | `O(1)` | ⭐ Interview answer |

---

## ⚠️ Common Mistakes / Gotchas

- ❌ **`slow->next = NULL` bhool jaana** → first half ka last node abhi bhi second half ko point karega → **cycle** → infinite loop / TLE.
- ❌ **Reverse `slow` se karna** instead of `slow->next` (second-middle style me) → middle node dono halves me aa jaata hai → galat answer.
- ❌ Merge me **`next1` / `next2` save kiye bina** links badal dena → baaki list kho jaati hai.
- ❌ Merge loop `first != NULL` pe chalana → `second` NULL hone ke baad `second->next` pe **crash** 💥. Loop **`second != NULL`** pe chalao.
- ❌ Function se kuch `return` karne ki koshish — ye **`void`** hai, in-place change karna hai.
- ❌ Brute me **last node ka `next = NULL`** na karna → cycle.
- 💡 Values swap karke solve karna allowed nahi — sirf **nodes** reorder karne hain.

---

## 🧩 Pattern Recognition

> **"Linked list ko aage + peeche se saath process karna"** → **Middle + Reverse second half** trick.

Isi trick pe based problems:

- [234. Palindrome Linked List](https://leetcode.com/problems/palindrome-linked-list/) — middle + reverse + **compare** (merge ki jagah)
- [2130. Maximum Twin Sum of a Linked List](https://leetcode.com/problems/maximum-twin-sum-of-a-linked-list/) — middle + reverse + **pair sum**
- [148. Sort List](https://leetcode.com/problems/sort-list/) — middle se split + merge (21)
- [328. Odd Even Linked List](https://leetcode.com/problems/odd-even-linked-list/) — nodes ko re-link karna (in-place)
- [86. Partition List](https://leetcode.com/problems/partition-list/) — split + join

Building blocks (already done ✅): **876** (middle) · **206** (reverse) · **21** (merge)

---

## 📝 30-Second Revision Card

```
if (!head || !head->next) return

// 1. middle
slow = fast = head
while (fast && fast->next): slow = slow->next; fast = fast->next->next

// 2. reverse second half + 3. cut
second = reverse(slow->next)
slow->next = NULL                    // ⚠️ cycle avoid

// 4. zip
first = head
while (second):
    n1 = first->next; n2 = second->next
    first->next = second; second->next = n1
    first = n1; second = n2

876 + 206 + merge  →  Time O(n) | Space O(1)
```

---

<p align="center"><i>⭐ Revise → Dry run khud karo → Code bina dekhe likho ⭐</i></p>
