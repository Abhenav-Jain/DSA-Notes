# 🎲 138. Copy List with Random Pointer

![Difficulty](https://img.shields.io/badge/Difficulty-Medium-orange)
![Topic](https://img.shields.io/badge/Topic-Linked%20List-blue)
![Topic](https://img.shields.io/badge/Topic-Hash%20Table-yellow)
![Language](https://img.shields.io/badge/Language-C%2B%2B-00599C)

> **LeetCode:** [138. Copy List with Random Pointer](https://leetcode.com/problems/copy-list-with-random-pointer/)

---

## 📌 Problem Statement

Ek linked list di hai jisme har node ke paas **2 pointers** hain:
- `next` → agla node
- `random` → list ka **koi bhi node** ya `NULL`

Is list ki **deep copy** banani hai — yani **bilkul naye nodes**, jinke `next` aur `random` bhi **naye nodes** ko hi point karein (purani list ka koi node copy me nahi hona chahiye).

```cpp
class Node {
public:
    int val;
    Node* next;
    Node* random;
    Node(int _val) { val = _val; next = NULL; random = NULL; }
};
```

| Input `[val, random_index]` | Output |
|---|---|
| `[[7,null],[13,0],[11,4],[10,2],[1,0]]` | Same structure, naye nodes |
| `[[1,1],[2,1]]` | Same structure, naye nodes |
| `[]` | `[]` |

**Constraints:** nodes `0` se `1000` tak, `-10⁴ <= Node.val <= 10⁴`

---

## 🧠 Core Intuition (Yaad rakhne wali line)

> **"Problem ye hai ki `random` kisi aise node ko point kar sakta hai jo abhi bana hi nahi. Toh pehle saare naye nodes bana lo, aur ek 'phone directory' 📒 (hash map) rakho: purana node → uski copy. Phir directory dekh ke random jod do."**

- `next` jodna easy hai — line se banate jao.
- `random` mushkil hai — wo **aage** ke node ko bhi point kar sakta hai (jo pass 1 me abhi bana nahi).
- **Solution:** `unordered_map<Node*, Node*> m` → `m[old] = new`
  - Pass 1: saare nodes banao + `next` jodo + map bharo
  - Pass 2: `new->random = m[old->random]` ✅

🔑 Map **pointer (address)** se pointer map karta hai, **value** se nahi — kyunki values duplicate ho sakti hain.

---

## 🪜 Approach (Step by Step)

1. `head == NULL` → `return NULL`
2. Head ki copy banao: `newHead`, aur `m[head] = newHead`
3. **Pass 1 (next connections):** `old_curr = head->next` se chalo
   - naya node banao → `m[old_curr] = new_copy`
   - `new_curr->next = new_copy`, dono pointers aage
4. **Pass 2 (random connections):** dono lists ko start se saath chalao
   - `new_curr->random = m[old_curr->random]`
5. Return `newHead`

---

## 🔍 Dry Run

**List:** `A(1) → B(2) → C(3) → NULL`
**Random:** `A → C`, `B → A`, `C → NULL`

```
   ┌──────random──────┐
   │                  ▼
  A(1) ──→ B(2) ──→ C(3) ──→ NULL
   ▲        │        │
   └─random─┘        └─random→ NULL
```

### Pass 1 — Copies + `next` + map

| Step | `old_curr` | Naya node | Map | Copy list |
|---|---|---|---|---|
| init | — | `A'` (newHead) | `A→A'` | `A'` |
| 1 | B | `B'` | `B→B'` | `A' → B'` |
| 2 | C | `C'` | `C→C'` | `A' → B' → C'` |
| 3 | NULL | loop khatam | | |

### Pass 2 — `random` connections

| `old_curr` | `old_curr->random` | `m[...]` | Set |
|---|---|---|---|
| A | C | `C'` | `A'->random = C'` |
| B | A | `A'` | `B'->random = A'` |
| C | NULL | `nullptr` *(neeche dekho ⚠️)* | `C'->random = NULL` |

```
Result:  A'(1) → B'(2) → C'(3) → NULL
         A'→C'   B'→A'   C'→NULL     ✅ bilkul naye nodes
```

---

## 💻 Code (C++) — Hash Map, Two Pass ✅

```cpp
class Solution {
public:
    Node* copyRandomList(Node* head) {
        if (head == NULL) {
            return NULL;
        }
        Node* newHead = new Node(head->val);
        Node* old_curr = head->next;
        Node* new_curr = newHead;
        unordered_map<Node*, Node*> m;   // old node → new node
        m[head] = newHead;

        // Pass 1: basic connections (next) + map bharo
        while (old_curr != NULL) {
            Node* new_copy = new Node(old_curr->val);
            m[old_curr] = new_copy;
            new_curr->next = new_copy;
            new_curr = new_curr->next;
            old_curr = old_curr->next;
        }

        // Pass 2: random connections
        old_curr = head;
        new_curr = newHead;
        while (old_curr != NULL) {
            new_curr->random = m[old_curr->random];   // NULL → nullptr (neeche dekho)
            new_curr = new_curr->next;
            old_curr = old_curr->next;
        }
        return newHead;
    }
};
```

| | Value | Reason |
|---|---|---|
| **Time** | `O(n)` | 2 passes, map lookup average `O(1)` |
| **Space** | `O(n)` | Map me n entries (output list count nahi hoti) |

---

## ⚠️ `m[NULL]` — Ye kaam kyun karta hai? (Interview me poochenge!)

Jab `old_curr->random == NULL`, tab `m[NULL]` chalta hai. `NULL` map me daala hi nahi tha, phir bhi crash nahi hota. Kyun?

> `unordered_map` ka **`operator[]`** missing key milne pe **chupke se** ek naya entry bana deta hai → default value (pointer ke liye `nullptr`) ke saath → aur wahi return karta hai.

Toh `m[NULL]` → `nullptr` ✅ — answer sahi aata hai, par ye **side-effect** se ho raha hai.

**Intention clear karne ke 2 tareeke** (code same rehta hai, bas ek line add):

```cpp
m[NULL] = NULL;   // map banane ke baad shuru me hi
```
ya
```cpp
new_curr->random = (old_curr->random != NULL) ? m[old_curr->random] : NULL;
```

| Function | Missing key pe kya hota hai |
|---|---|
| `m[key]` | Naya entry bana ke default (`nullptr` / `0`) return ⚠️ |
| `m.at(key)` | `std::out_of_range` exception throw 💥 |
| `m.find(key)` / `m.count(key)` | Sirf check, kuch insert nahi ✅ |

---

## 🚀 Follow-up: `O(1)` Extra Space — Interleaving Trick

Map ki jagah har copy ko uske **original ke theek baad** rakh do:

```
Original:      A → B → C
Interleaved:   A → A' → B → B' → C → C'
```

Ab `A'` ka random = `A->random->next` (kyunki har node ki copy uske just aage hai) — **map ki zarurat hi nahi!**

**3 steps:**
1. **Interleave:** har node ke baad uski copy daalo
2. **Random:** `c->next->random = c->random ? c->random->next : NULL`
3. **Separate:** dono lists alag karo (original ko bhi wapas theek karo)

```cpp
Node* copyRandomList(Node* head) {
    for (Node* c = head; c; c = c->next->next) {          // 1. interleave
        Node* copy = new Node(c->val);
        copy->next = c->next;
        c->next = copy;
    }
    for (Node* c = head; c; c = c->next->next)            // 2. random
        if (c->random) c->next->random = c->random->next;

    Node dummy(0); Node* tail = &dummy;                   // 3. separate
    for (Node* c = head; c; c = c->next) {
        Node* copy = c->next;
        c->next = copy->next;    // original restore
        tail->next = copy;
        tail = copy;
    }
    return dummy.next;
}
```

| | Value |
|---|---|
| **Time** | `O(n)` — 3 passes |
| **Space** | `O(1)` extra |

---

## ⚖️ Approach Comparison

| Approach | Time | Space | Note |
|---|---|---|---|
| Har random ke liye index dhundh ke copy me utna chalna | `O(n²)` | `O(1)` | ❌ Slow |
| **Hash Map (mera code)** | `O(n)` | `O(n)` | ⭐ Pehla answer — simple & safe |
| **Interleaving** | `O(n)` | `O(1)` | Follow-up / optimize bole tab |

---

## ⚠️ Common Mistakes / Gotchas

- ❌ **Ek hi pass me random jodna** → `random` aage wale node ko point kare toh uski copy abhi bani hi nahi → map me nahi milega.
- ❌ **`new_curr->random = old_curr->random`** → copy **purani list** ke node ko point karegi → deep copy nahi, **shallow** ho gayi. LeetCode reject karega.
- ❌ Map me **`val`** ko key banana → duplicate values pe galat node milega. **Pointer** key banao.
- ❌ `m.at(old->random)` use karna → random NULL pe **exception** 💥.
- ❌ Empty list check bhoolna → `head->val` pe crash (tumhare code me handled ✅).
- ❌ Interleaving me **original list restore na karna** → LeetCode "original list modified" error deta hai.

---

## 🧩 Pattern Recognition

> **"Kisi structure ki deep copy banani hai jisme arbitrary links hain"** → **Hash map: old → new** (clone pattern).

Isi pattern pe based problems:

- [133. Clone Graph](https://leetcode.com/problems/clone-graph/) — same map trick, graph pe (BFS/DFS)
- [1485. Clone Binary Tree With Random Pointer](https://leetcode.com/problems/clone-binary-tree-with-random-pointer/) — tree version
- [1490. Clone N-ary Tree](https://leetcode.com/problems/clone-n-ary-tree/)
- [160. Intersection of Two Linked Lists](https://leetcode.com/problems/intersection-of-two-linked-lists/) — pointer (address) ko key/identity maanna
- [141. Linked List Cycle](https://leetcode.com/problems/linked-list-cycle/) — hash set of node pointers wala brute

---

## 📝 30-Second Revision Card

```
if (!head) return NULL
map<Node*,Node*> m          // old → new  (pointer key, val nahi!)

Pass 1: har node ki copy banao, next jodo, m[old] = new
Pass 2: new->random = m[old->random]
        (m[NULL] → nullptr apne aap; ya m[NULL] = NULL pehle daalo)

Time O(n) | Space O(n)

O(1) follow-up: A→A'→B→B'  →  A'->random = A->random->next
                → phir dono lists alag (original restore!)
```

---

<p align="center"><i>⭐ Revise → Dry run khud karo → Code bina dekhe likho ⭐</i></p>
