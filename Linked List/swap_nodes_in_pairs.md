# 🔀 Swap Nodes in Pairs — LeetCode 24

> **Difficulty:** Medium
> **Topics:** Linked List · Recursion · Dummy Node
> **Pattern:** *Recursion — "Pehla pair main sambhalunga, baaki list recursion sambhal lega"*

---

## 📌 Problem Statement

Linked list ke har do adjacent nodes ko swap karo aur naya head return karo.
⚠️ **Values swap karna allowed nahi hai**, sirf nodes ke links badalne hain.

```
Input:  1 → 2 → 3 → 4        Output: 2 → 1 → 4 → 3
Input:  1 → 2 → 3            Output: 2 → 1 → 3     (akela node waisa hi)
Input:  []                   Output: []
Input:  [1]                  Output: [1]
```

**Constraints:**
- Nodes: `0` to `100`
- `0 <= Node.val <= 100`

---

## 🧠 Core Intuition

List ko pairs mein dekho:

```
[1 → 2] → [3 → 4] → [5 → 6] → ...
 pair1     pair2     pair3
```

Har pair ka kaam **ekdum same** hai: pehle aur doosre node ko ulta karo, aur pair ko baaki (already swapped) list se jodo.

Jab har chhota hissa same kaam karta ho, toh **recursion** perfect fit hai:

> 🎯 **Recursion ka leap of faith:** "Main sirf pehla pair swap karunga. `head->next->next` se aage ki list ko swap karna recursion ka kaam hai, aur main maan ke chalunga ki woh sahi swapped list ka head return karega."

```
Pehle:   1 → 2 → [3 → 4 → 5 → 6]
                  └─ recursion isko 4 → 3 → 6 → 5 bana dega

Mera kaam:  2 → 1 → [4 → 3 → 6 → 5]
```

---

## ⭐ Meri Approach — Recursive

### Step 1: Base Case
```cpp
if (head == NULL || head->next == NULL) return head;
```
- **0 nodes** → swap karne ko kuch nahi
- **1 node** → pair hi nahi bana, waisa hi return karo (odd length ka last node yahin handle hota hai)

### Step 2: Doosre node ko pakdo (yahi naya head banega)
```cpp
ListNode* temp = head->next;    // node 2
```

### Step 3: Pehle node ko baaki swapped list se jodo
```cpp
head->next = swapPairs(head->next->next);   // 1 → (swapped 3,4,...)
```
Swap ke baad `head` (node 1) pair ka **second** node ban jaata hai, isliye uske aage swapped remaining list aani chahiye.

### Step 4: Doosre node ko pehle se jodo
```cpp
temp->next = head;              // 2 → 1
```

### Step 5: Naya head return karo
```cpp
return temp;                    // 2 hi is pair ka naya head hai
```

### 📐 Picture

```
Pehle:     head        temp
            ↓           ↓
            1     →     2     →    3 → 4 ...

Step 3:     1 ─────────────────→ (4 → 3 ...)    [recursion ka result]
Step 4:     2 → 1
Final:      2 → 1 → 4 → 3 ...
            ↑
          return temp
```

---

## ✅ Full Code (Meri Approach)

```cpp
class Solution {
public:
    ListNode* swapPairs(ListNode* head) {
        // Base case: 0 ya 1 node → swap ka sawaal hi nahi
        if(head == NULL || head->next == NULL){
            return head;
        }
        ListNode* temp = head->next;                // 2nd node = naya head
        head->next = swapPairs(head->next->next);   // 1st → baaki swapped list
        temp->next = head;                          // 2nd → 1st

        return temp;
    }
};
```

---

## ⚠️ Statements ka ORDER kyun matter karta hai 🔥

Step 3 aur Step 4 ko ulta likh diya toh:

```cpp
temp->next = head;                          // 2 → 1   (2 ka purana next = 3 ka link KHO GAYA!)
head->next = swapPairs(head->next->next);   // head->next ab 2 hai, 2->next ab 1 hai
                                            // → swapPairs(1) → infinite recursion 💥
```

> 🧠 **Rule:** Kisi node ka `next` overwrite karne se pehle, check karo ki uske purane `next` ki zarurat aage toh nahi. Meri order mein `head->next->next` (yaani node 3) **pehle use ho jaata hai**, phir `temp->next` badalta hai. ✅

---

## 🔍 Dry Run

### Case A: `1 → 2 → 3 → 4`

Recursion **neeche jaata hai** (calls):

| Call | head | temp | Call karta hai |
|------|------|------|----------------|
| `swapPairs(1)` | 1 | 2 | `swapPairs(3)` |
| `swapPairs(3)` | 3 | 4 | `swapPairs(NULL)` |
| `swapPairs(NULL)` | — | — | base case → **return NULL** |

Recursion **upar aata hai** (returns):

| Call | Kya hota hai | Return |
|------|--------------|--------|
| `swapPairs(3)` | `3->next = NULL`, `4->next = 3` | `4` → list: `4 → 3` |
| `swapPairs(1)` | `1->next = 4`, `2->next = 1` | `2` → list: `2 → 1 → 4 → 3` |

**Output: `2 → 1 → 4 → 3`** ✅

### Case B: Odd — `1 → 2 → 3`
- `swapPairs(1)`: temp=2, calls `swapPairs(3)`
- `swapPairs(3)`: `3->next == NULL` → base case → **return 3**
- Back: `1->next = 3`, `2->next = 1` → return 2
- **Output: `2 → 1 → 3`** ✅

---

## 🧪 Edge Cases

| Input | Output | Kaise handle hua |
|-------|--------|------------------|
| `[]` | `[]` | `head == NULL` → base case |
| `[1]` | `[1]` | `head->next == NULL` → base case |
| `[1,2]` | `[2,1]` | ek swap, recursion `NULL` return karta hai |
| `[1,2,3]` | `[2,1,3]` | last akela node base case se bach gaya |
| `[1,2,3,4,5,6]` | `[2,1,4,3,6,5]` | 3 levels deep recursion |

> ⚠️ Base case mein `head->next == NULL` check **zaroori** hai. Uske bina odd length pe `head->next->next` → NULL ka `->next` → **crash**.

---

## ⏱ Complexity

| | Value | Kyun |
|---|---|---|
| **Time** | `O(n)` | Har pair pe ek call, har call `O(1)` kaam |
| **Space** | `O(n)` | Recursion stack — `n/2` calls, jo `O(n)` hi hai |

> Interview mein seedha bol do: *"Recursive solution clean hai lekin `O(n)` stack space leta hai. `O(1)` chahiye toh iterative dummy-node approach use karunga."* 🔥

---

## 🔁 Alternative: Iterative with Dummy Node (O(1) Space)

Head badalne wala hai (1 ki jagah 2 head banega), isliye **`dummy.next = head`** karna padega.

```cpp
ListNode* swapPairs(ListNode* head) {
    ListNode dummy(0, head);        // dummy.next = head
    ListNode* prev = &dummy;        // har pair se pehle wala node

    while (prev->next && prev->next->next) {
        ListNode* a = prev->next;       // pair ka 1st
        ListNode* b = a->next;          // pair ka 2nd

        a->next = b->next;              // 1. a → aage ki list
        b->next = a;                    // 2. b → a
        prev->next = b;                 // 3. prev → b

        prev = a;                       // a ab pair ka last hai → agle pair ka prev
    }
    return dummy.next;
}
```

### 📐 Ek pair ka swap (3 links badalte hain)

```
Pehle:   prev → a → b → next...
Baad:    prev → b → a → next...
```

| Step | Link | Kyun pehle |
|------|------|-----------|
| 1 | `a->next = b->next` | `b->next` overwrite hone se pehle save/use karna hai |
| 2 | `b->next = a` | ab b ko a se jodo |
| 3 | `prev->next = b` | pichhli list ko naye pair-head se jodo |

### Recursive vs Iterative

| | Recursive (meri) | Iterative |
|---|---|---|
| Code length | 🔥 5 lines | ~12 lines |
| Space | `O(n)` stack | **`O(1)`** |
| Dummy chahiye? | ❌ (return value hi naya head hai) | ✅ |
| Samajhna | Leap of faith chahiye | Pointer diagram chahiye |
| Interview | Pehle yeh likho | Follow-up mein yeh do |

---

## 🚫 Value Swap wala Cheat (mat karna!)

```cpp
swap(head->val, head->next->val);   // ❌ problem mein mana hai
```
Problem explicitly bolti hai ki node values modify mat karo. Real-world mein node ke andar bada data ho sakta hai, isliye links badalna hi sahi tarika hai.

---

## ⚠️ Common Mistakes / Traps

1. **Base case mein sirf `head == NULL` check** → odd length pe crash. Dono check chahiye.
2. **Statements ka order galat** → link kho jaata hai ya infinite recursion (upar dekho).
3. **`head` return kar dena `temp` ki jagah** → swap ke baad `head` pair ka *second* node hai, naya head `temp` hai.
4. **Iterative mein `prev = b` kar dena** → swap ke baad pair ka last node `a` hai, `b` nahi. `prev = a` ✅
5. **Iterative mein dummy ke bina** → pehle pair ke liye alag code likhna padega.

---

## 🧩 Pattern Recognition

| Problem | Connection |
|---------|-----------|
| LC 206 Reverse Linked List | Recursion se link ulta karna — same "trust the recursion" soch |
| **LC 25 Reverse Nodes in k-Group** (Hard) | 🔥 Yahi problem ka generalization — `k = 2` ho toh exactly Swap Pairs! |
| LC 92 Reverse Linked List II | `prev` pointer + range ke andar links badalna |
| LC 1721 Swapping Nodes in a Linked List | Do nodes swap karna (yahan values allowed hain) |

> 🎯 Next target: **LC 25** — wahi recursive template, bas pair ki jagah `k` nodes reverse karne hain.

```cpp
// LC 25 ka skeleton (same soch)
ListNode* reverseKGroup(ListNode* head, int k) {
    // 1. check karo k nodes hain ya nahi → nahi toh head return
    // 2. pehle k nodes reverse karo
    // 3. purana head (ab k-group ka last) ->next = reverseKGroup(remaining, k)
    // 4. naya head return karo
}
```

---

## 📝 One-Line Revision

> **"Base case: 0 ya 1 node → head return. Warna `temp = head->next`, `head->next = swapPairs(head->next->next)`, `temp->next = head`, `return temp`. Order mat badalna! Time O(n), space O(n) stack. O(1) chahiye toh dummy + prev se iterative."**
