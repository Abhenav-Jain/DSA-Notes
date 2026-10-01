# 🔁 141. Linked List Cycle

![Difficulty](https://img.shields.io/badge/Difficulty-Easy-brightgreen)
![Topic](https://img.shields.io/badge/Topic-Linked%20List-blue)
![Topic](https://img.shields.io/badge/Topic-Two%20Pointers-orange)
![Topic](https://img.shields.io/badge/Topic-Hash%20Table-yellow)
![Language](https://img.shields.io/badge/Language-C%2B%2B-00599C)

> **LeetCode:** [141. Linked List Cycle](https://leetcode.com/problems/linked-list-cycle/)

---

## 📌 Problem Statement

Linked list ka `head` diya hai. Batana hai ki list me **cycle** hai ya nahi.
Cycle tab hoti hai jab koi node ka `next` **pichhle kisi node** ko point kare, aur list kabhi `NULL` pe khatam hi na ho.

| Input | `pos` (cycle kahan judti hai) | Output |
|---|---|---|
| `3 → 2 → 0 → -4` | 1 (`-4 → 2`) | `true` |
| `1 → 2` | 0 (`2 → 1`) | `true` |
| `1` | -1 (no cycle) | `false` |

> `pos` sirf samjhane ke liye hai, function ko nahi milta.

**Constraints:** nodes `0` se `10⁴` tak

**Follow-up:** `O(1)` memory me solve karo.

---

## 🧠 Core Intuition (Yaad rakhne wali line)

> **"Circular race track 🏟️ pe agar ek tez runner aur ek dheema runner daude, toh tez wala dheeme ko piche se aakar zaroor pakdega. Seedhi road pe kabhi nahi milenge."**

- **`slow`** 🐢 → 1 step
- **`fast`** 🐇 → 2 steps
- **Cycle nahi hai** → `fast` end (`NULL`) tak pahunch jaayega → `false`
- **Cycle hai** → dono cycle me phas jaayenge, aur `fast` ek din `slow` ko pakad lega → `true`

🔑 **Floyd's Cycle Detection Algorithm** (Tortoise & Hare) — ye naam yaad rakho, interview me bolna.

---

## 🤔 Wo pakka milenge kyun? (Proof — simple)

Maan lo dono cycle ke andar hain aur `fast`, `slow` se **`d` steps piche** hai.

- Har iteration me `slow` +1 chalta hai, `fast` +2 chalta hai
- Toh gap har baar **exactly 1 se kam** hota hai: `d → d-1 → d-2 → ... → 0`
- Gap 1-1 karke ghat raha hai → `fast` slow ko **jump karke skip nahi kar sakta** → gap **0** hona hi hai ✅

> Isliye speed **2** rakhte hain. Speed 3 rakhoge toh gap 2-2 se ghatega aur kabhi-kabhi skip ho sakta hai (phir bhi milenge, par baad me — 2 sabse clean hai).

---

## 🪜 Approach (Step by Step)

1. `slow = head`, `fast = head`
2. Jab tak `fast != NULL` **aur** `fast->next != NULL`:
   - `slow = slow->next`
   - `fast = fast->next->next`
   - **Move karne ke BAAD** check: `slow == fast` → `return true`
3. Loop se bahar aaye matlab `fast` ne end dekh liya → `return false`

> ⚠️ Check **move ke baad** karna hai. Shuru me dono `head` pe hain, toh pehle check karoge toh har list pe `true` aa jaayega! ❌

---

## 🔍 Dry Run

### Case 1 — Cycle hai: `3 → 2 → 0 → -4 → (back to 2)`

```
3 → 2 → 0 → -4
    ↑        |
    └────────┘
```

| Step | `slow` | `fast` | `slow == fast`? |
|---|---|---|---|
| Start | 3 | 3 | (check nahi karte) |
| 1 | 2 | 0 | ❌ |
| 2 | 0 | 2 *(0 → -4 → 2)* | ❌ |
| 3 | -4 | -4 *(2 → 0 → -4)* | ✅ **Mil gaye!** |
| **Return** | | | **`true`** ✅ |

### Case 2 — Cycle nahi: `1 → 2 → 3 → NULL`

| Step | `slow` | `fast` | Condition (`fast && fast->next`) |
|---|---|---|---|
| Start | 1 | 1 | ✅ |
| 1 | 2 | 3 | ✅ check: 2 ≠ 3 → aage |
| — | | | ❌ `fast->next == NULL` → loop khatam |
| **Return** | | | **`false`** ✅ |

---

## 💻 Code 1 (C++) — Floyd's Slow & Fast ✅ (Best)

```cpp
/**
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    bool hasCycle(ListNode *head) {
        ListNode* slow = head;   // 🐢 1 step
        ListNode* fast = head;   // 🐇 2 steps

        while (fast != nullptr && fast->next != nullptr) {
            slow = slow->next;
            fast = fast->next->next;

            if (slow == fast) return true;  // move ke BAAD check
        }
        return false;  // fast ne NULL dekh liya → no cycle
    }
};
```

| | Value | Reason |
|---|---|---|
| **Time** | `O(n)` | No cycle → `fast` n/2 steps me end. Cycle → `slow` cycle me enter karne ke baad max 1 round me pakda jaata hai |
| **Space** | `O(1)` | Sirf 2 pointers ✅ (Follow-up done) |

---

## 💻 Code 2 (C++) — Hash Set (Brute)

**Idea:** Har visited node ka **address** set me daalo. Koi node dobara mila → cycle.

```cpp
class Solution {
public:
    bool hasCycle(ListNode *head) {
        unordered_set<ListNode*> seen;
        ListNode* curr = head;

        while (curr != nullptr) {
            if (seen.count(curr)) return true;  // pehle dekh chuke → cycle
            seen.insert(curr);
            curr = curr->next;
        }
        return false;
    }
};
```

> ⚠️ Set me **node ka pointer** (`ListNode*`) store karo, **`val` nahi** — values duplicate ho sakti hain bina cycle ke (e.g. `1 → 1 → 1`).

| | Value |
|---|---|
| **Time** | `O(n)` |
| **Space** | `O(n)` — set me n nodes |

---

## ⚖️ Approach Comparison

| Approach | Time | Space | Note |
|---|---|---|---|
| Hash set of node addresses | `O(n)` | `O(n)` | Easy, par extra memory |
| Node values modify karna (marker) | `O(n)` | `O(1)` | ❌ Input change karta hai — avoid |
| **Floyd's Tortoise & Hare** | `O(n)` | `O(1)` | ⭐ Interview answer |

---

## ⚠️ Common Mistakes / Gotchas

- ❌ **`slow == fast` check loop ke shuru me** karna → dono `head` pe hain → har baar `true`. Check **move ke baad** karo.
- ❌ Condition ka order ulta: `fast->next != NULL && fast != NULL` → NULL dereference **crash** 💥
- ❌ Sirf `fast != NULL` check karna → `fast->next->next` pe crash.
- ❌ Hash set approach me **`val`** store karna instead of **pointer**.
- ❌ `while(true)` likh ke sirf `slow == fast` pe rely karna → no-cycle list pe crash.
- 💡 Empty list (`head == NULL`) → loop chalega hi nahi → `false` ✅ (apne aap handle).
- 💡 `NULL` ki jagah **`nullptr`** prefer karo.

---

## 🚀 Next Level: Cycle kahan se shuru hoti hai? (LC 142)

Ye **141 ka direct follow-up** hai — interview me aksar poochte hain.

**Trick:** Jab `slow == fast` mile, ek pointer ko wapas `head` pe bhejo. Ab **dono 1-1 step** chalo. Jahan milenge wahi **cycle ka start** hai.

```cpp
ListNode *detectCycle(ListNode *head) {
    ListNode* slow = head;
    ListNode* fast = head;
    while (fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;
        if (slow == fast) {
            slow = head;                 // ek ko head pe bhejo
            while (slow != fast) {       // dono 1-1 step
                slow = slow->next;
                fast = fast->next;
            }
            return slow;                 // cycle ka start
        }
    }
    return nullptr;
}
```

> **Why?** Agar `head` se cycle start tak distance `a` hai, aur meeting point se cycle start tak (aage) `c`, toh math se `a = c + (k-1)·L` (L = cycle length). Matlab head se aur meeting point se ek saath chaloge toh cycle start pe hi miloge.

---

## 🧩 Pattern Recognition

> **"Cycle / loop detect karna, ya repeated state dhundna"** → **Floyd's slow & fast pointers**.

Isi pattern pe based problems:

- [142. Linked List Cycle II](https://leetcode.com/problems/linked-list-cycle-ii/) — cycle ka start node (upar dekha)
- [876. Middle of the Linked List](https://leetcode.com/problems/middle-of-the-linked-list/) — same slow/fast setup
- [202. Happy Number](https://leetcode.com/problems/happy-number/) — numbers ka sequence, cycle detect
- [287. Find the Duplicate Number](https://leetcode.com/problems/find-the-duplicate-number/) — array ko linked list maan ke Floyd's
- [457. Circular Array Loop](https://leetcode.com/problems/circular-array-loop/)
- [160. Intersection of Two Linked Lists](https://leetcode.com/problems/intersection-of-two-linked-lists/) — two pointer meeting idea

---

## 📝 30-Second Revision Card

```
slow = fast = head
while (fast && fast->next):        // order matters!
    slow = slow->next              // 🐢 1
    fast = fast->next->next        // 🐇 2
    if (slow == fast) return true  // check AFTER move
return false

Why meet? Gap har step 1 se ghatta hai → skip impossible
Time O(n) | Space O(1)   — Floyd's Tortoise & Hare

LC 142 (start of cycle): meet ke baad slow = head,
                          dono 1-1 step → jahan mile = start
```

---

<p align="center"><i>⭐ Revise → Dry run khud karo → Code bina dekhe likho ⭐</i></p>
