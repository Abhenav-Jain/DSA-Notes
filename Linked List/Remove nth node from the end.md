# ✂️ 19. Remove Nth Node From End of List

![Difficulty](https://img.shields.io/badge/Difficulty-Medium-orange)
![Topic](https://img.shields.io/badge/Topic-Linked%20List-blue)
![Topic](https://img.shields.io/badge/Topic-Two%20Pointers-orange)
![Language](https://img.shields.io/badge/Language-C%2B%2B-00599C)

> **LeetCode:** [19. Remove Nth Node From End of List](https://leetcode.com/problems/remove-nth-node-from-end-of-list/)

---

## 📌 Problem Statement

Linked list ka `head` aur ek number `n` diya hai. List ke **end se n-th node** ko delete karke `head` return karna hai.

| Input | `n` | Output | Note |
|---|---|---|---|
| `1 → 2 → 3 → 4 → 5` | 2 | `1 → 2 → 3 → 5` | End se 2nd = `4` |
| `1` | 1 | `[]` | Akela node (head) hi delete |
| `1 → 2` | 1 | `1` | Last node delete |
| `1 → 2` | 2 | `2` | **Head** delete |

**Constraints:** nodes `sz`, `1 <= sz <= 30`, `1 <= n <= sz` *(n hamesha valid hai)*

**Follow-up:** **One pass** me karo.

---

## 🧠 Core Intuition (Yaad rakhne wali line)

> **"Do pointers ke beech `n` ka fixed gap bana do. Phir dono ko saath chalao — jab aage wala last node pe pahunchega, piche wala target ke theek pehle khada hoga."**

- Delete karne ke liye hume target node **nahi**, balki uske **pichhle node** tak pahunchna hai (taaki `prev->next = prev->next->next` kar sakein).
- Length gine bina end se position kaise pata kare? → **Gap trick** 📏
  - `fast` ko pehle **`n` steps aage** bhejo.
  - Ab `slow` aur `fast` dono **1-1 step** chalo jab tak `fast` **last node** pe na aa jaaye.
  - Gap `n` hai → `slow` end se **(n+1)-th** node pe hoga = **target ke just pehle** ✅

🔑 **Dummy node kyun?** Agar delete karne wala node **head** hi ho (`n == length`), toh uske "pehle" koi node nahi hai. Dummy node head ke pehle ek fake node de deta hai → **head delete karna bhi normal case** ban jaata hai.

---

## 🪜 Approach (Step by Step)

1. `dummy` banao, `dummy->next = head`
2. `slow = dummy`, `fast = dummy`
3. `fast` ko **`n` steps** aage le jao
4. Jab tak `fast->next != NULL`: `slow` aur `fast` dono **1 step** aage
5. Ab `slow` target ke pehle hai → `slow->next = slow->next->next` *(target skip)*
6. Return `dummy->next` *(head nahi! — head delete ho gaya ho sakta hai)*

---

## 🔍 Dry Run

### Case 1 — `1 → 2 → 3 → 4 → 5`, `n = 2`

```
D → 1 → 2 → 3 → 4 → 5 → NULL      (D = dummy)
```

**Phase 1 — `fast` ko n = 2 steps aage:**

| i | `fast` |
|---|---|
| start | D |
| 0 | 1 |
| 1 | 2 |

**Phase 2 — dono saath chalao (jab tak `fast->next != NULL`):**

| Step | `slow` | `fast` | `fast->next` |
|---|---|---|---|
| start | D | 2 | 3 ✅ |
| 1 | 1 | 3 | 4 ✅ |
| 2 | 2 | 4 | 5 ✅ |
| 3 | 3 | 5 | NULL ❌ stop |

```
D → 1 → 2 → 3 → 4 → 5 → NULL
            🐢  ❌  🐇
           slow  target  fast

slow->next = slow->next->next   →   3 → 5
Result: 1 → 2 → 3 → 5 ✅
```

### Case 2 — `1`, `n = 1` (Head delete — dummy ka asli kaam 💪)

| Phase | `slow` | `fast` | Note |
|---|---|---|---|
| fast n=1 step | D | 1 | |
| loop | D | 1 | `fast->next == NULL` → loop chala hi nahi |
| delete | D | | `D->next = 1->next = NULL` |

```
Return dummy->next = NULL  →  []  ✅
```

> Agar dummy na hota toh `slow` ko node `1` ke "pehle" kahan rakhte? 🤷 — yahi wajah hai dummy ki.

---

## 💻 Code 1 (C++) — One Pass, Two Pointers + Dummy ✅ (Best)

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
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        ListNode dummy(0, head);     // stack pe dummy → delete ki tension nahi
        ListNode* slow = &dummy;
        ListNode* fast = &dummy;

        // 1. fast ko n steps aage → slow aur fast me n ka gap
        for (int i = 0; i < n; i++) {
            fast = fast->next;
        }

        // 2. dono saath chalao jab tak fast last node pe na pahunche
        while (fast->next != nullptr) {
            slow = slow->next;
            fast = fast->next;
        }

        // 3. slow target ke just pehle hai → target skip karo
        ListNode* target = slow->next;
        slow->next = target->next;
        delete target;               // memory free (good practice)

        return dummy.next;           // head nahi — head delete ho sakta hai
    }
};
```

> 💡 Tumhara original code **bilkul sahi** hai. Yahan sirf 2 clean-ups hain:
> 1. `new ListNode(0)` heap pe banta hai aur kabhi `delete` nahi hota → **memory leak**. Stack pe `ListNode dummy(0, head);` banao — apne aap free.
> 2. Hataya gaya node bhi `delete` kar diya. (LeetCode pe zaroori nahi, par interview me bonus point.)

| | Value | Reason |
|---|---|---|
| **Time** | `O(L)` | `fast` list ek baar traverse karta hai (L = length) → **one pass** ✅ |
| **Space** | `O(1)` | Sirf 2 pointers + dummy |

---

## 💻 Code 2 (C++) — Two Pass (Length gino)

**Idea:** End se `n`-th node = start se `(L - n + 1)`-th node. Toh dummy se **`L - n` steps** chalo → target ke just pehle pahunch jaoge.

```cpp
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        int L = 0;
        for (ListNode* t = head; t != nullptr; t = t->next) L++;   // pass 1: length

        ListNode dummy(0, head);
        ListNode* prev = &dummy;
        for (int i = 0; i < L - n; i++) prev = prev->next;         // pass 2: L-n steps

        ListNode* target = prev->next;
        prev->next = target->next;
        delete target;
        return dummy.next;
    }
};
```

> `1→2→3→4→5`, `n=2` → `L=5`, `L-n = 3` steps: D→1→2→**3** → `3->next = 5` ✅

| | Value |
|---|---|
| **Time** | `O(L)` — par **2 passes** |
| **Space** | `O(1)` |

---

## 🔀 Variant: Same logic, alag loop style

Kai log `fast` ko **`n + 1`** steps aage bhejte hain aur `while (fast != NULL)` chalate hain. Dono **same** hain:

| Style | `fast` kitna aage | Loop condition | `slow` kahan rukta hai |
|---|---|---|---|
| **Tumhara** | `n` | `fast->next != NULL` | target ke pehle ✅ |
| Alternate | `n + 1` | `fast != NULL` | target ke pehle ✅ |

> Rule: **`slow` aur `fast` ke beech gap + jahan `fast` rukta hai** — dono ko milake `slow` target ke pehle aana chahiye. Ek ko change karo toh doosra bhi adjust karo.

---

## ⚖️ Approach Comparison

| Approach | Time | Space | Note |
|---|---|---|---|
| Nodes ko vector me store, `arr[L-n-1]` se link badlo | `O(L)` | `O(L)` | Extra space + head case alag |
| Two pass (length gino) | `O(L)` (2 pass) | `O(1)` | Simple, theek hai |
| **One pass — Gap of n + Dummy** | `O(L)` (1 pass) | `O(1)` | ⭐ Interview answer (follow-up) |

---

## ⚠️ Common Mistakes / Gotchas

- ❌ **Dummy na lagana** → `n == length` (head delete) pe `slow` ke paas "pichhla node" nahi hoga → wrong answer / crash.
- ❌ **`return head`** karna → agar head hi delete hua tha toh deleted node return ho jaayega. Hamesha **`dummy->next`** return karo.
- ❌ `slow`/`fast` ko `head` se start karna aur dummy bhi lagana → **off-by-one**, galat node delete hoga. Dono **dummy** se start karo.
- ❌ Loop condition `fast != NULL` rakhna jab `fast` sirf `n` aage gaya ho → `slow` **target pe** ruk jaayega, uske pehle nahi.
- ❌ `new ListNode` dummy ko delete na karna → memory leak (stack dummy use karo).
- 💡 `n` hamesha valid hai (constraint), isliye `fast = fast->next` loop me NULL crash nahi hoga. Real code me check lagana safe hai.

---

## 🧩 Pattern Recognition

> **"End se k-th / list ki length bina gine position"** → **Two pointers with fixed gap**.
> **"Head delete / change ho sakta hai"** → **Dummy node**.

Isi pattern pe based problems:

- [876. Middle of the Linked List](https://leetcode.com/problems/middle-of-the-linked-list/) — slow/fast, alag speed
- [141. Linked List Cycle](https://leetcode.com/problems/linked-list-cycle/) — slow/fast, alag speed
- [61. Rotate List](https://leetcode.com/problems/rotate-list/) — end se k-th node pe list todo
- [1721. Swapping Nodes in a Linked List](https://leetcode.com/problems/swapping-nodes-in-a-linked-list/) — start se k-th & end se k-th (same gap trick)
- [2095. Delete the Middle Node of a Linked List](https://leetcode.com/problems/delete-the-middle-node-of-a-linked-list/) — delete + slow/fast
- [203. Remove Linked List Elements](https://leetcode.com/problems/remove-linked-list-elements/) — dummy node se delete
- [82. Remove Duplicates from Sorted List II](https://leetcode.com/problems/remove-duplicates-from-sorted-list-ii/) — dummy + delete

---

## 📝 30-Second Revision Card

```
dummy->next = head; slow = fast = dummy
for i in 0..n-1:  fast = fast->next          // gap = n
while (fast->next):                          // fast last node tak
    slow = slow->next; fast = fast->next
slow->next = slow->next->next                // target skip
return dummy->next                           // head nahi!

Gap n + fast last pe → slow = target ke PEHLE
Dummy → head delete bhi normal case
Time O(L) one pass | Space O(1)
```

---

<p align="center"><i>⭐ Revise → Dry run khud karo → Code bina dekhe likho ⭐</i></p>
