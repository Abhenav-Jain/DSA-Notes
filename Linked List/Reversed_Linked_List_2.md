# 🔄 Reverse Linked List II — LeetCode 92

> **Difficulty:** Medium
> **Topics:** Linked List · Dummy Node · In-place Reversal
> **Pattern:** *Boundary nodes pakdo → beech ka part reverse karo → dono taraf se jodo*

---

## 📌 Problem Statement

Linked list ka `head` aur do integers `left` aur `right` (`left <= right`) diye hain. Position `left` se `right` tak ke nodes ko reverse karo aur list return karo.
Positions **1-indexed** hain.

```
Input:  1 → 2 → 3 → 4 → 5,  left = 2, right = 4
Output: 1 → 4 → 3 → 2 → 5

Input:  5,  left = 1, right = 1
Output: 5
```

**Constraints:**
- Nodes: `n`, jahan `1 <= n <= 500`
- `-500 <= Node.val <= 500`
- `1 <= left <= right <= n`

**Follow-up:** Kya ek hi pass mein kar sakte ho? (Neeche bonus section dekho)

---

## 🧠 Core Intuition

List ko **teen hisson** mein dekho:

```
dummy → 1 → [2 → 3 → 4] → 5 → NULL
        ↑    └─ reverse ─┘    ↑
     before                  after
```

| Hissa | Kya karna hai |
|-------|---------------|
| `before` tak | Waisa hi rehne do |
| `left` se `right` tak | **Reverse karo** (LC 206 wala reverse, bas `k` nodes tak) |
| `after` se aage | Waisa hi rehne do |

Reverse ke baad sirf **2 connections** jodne hain:

```
before → [4 → 3 → 2] → after
   ↑       ↑       ↑     ↑
   └─ 1 ───┘       └─ 2 ─┘
```

1. `before->next = newHead` (`right` wala node, jo ab reversed part ka head hai)
2. `leftNode->next = after` (`left` wala node, jo ab reversed part ka **last** hai)

> 🎯 **Key observation:** Reverse ke baad `left` wala node **last** ban jaata hai aur `right` wala node **first**. Isliye `left` node ka pointer pehle se save karke rakhna zaroori hai.

---

## ❓ Dummy Node kyun?

Agar `left = 1` hai toh `before` kaun hoga? Head se pehle koi node hi nahi hai! 😵

Dummy lagao toh `left = 1` pe `before = dummy` ho jaata hai, aur alag se `if (left == 1)` likhne ki zarurat nahi padti.

```
left = 1:   dummy → [1 → 2 → 3] → 4
             ↑
          before
```

Aur head badal sakta hai (`left = 1` pe naya head `right` wala node banega), isliye return `dummy.next` karte hain, `head` nahi.

> Yahan `dummy.next = head` **karna zaroori hai**, kyunki existing list ko modify kar rahe hain, nayi list nahi bana rahe.

---

## ⭐ Meri Approach — Step by Step

### Step 1: Dummy setup
```cpp
ListNode dummy(0);
dummy.next = head;
```

### Step 2: `before` dhoondho (position `left-1`)
Dummy position `0` pe hai, toh `left-1` steps chalne se `left-1` wala node milta hai.
```cpp
ListNode* before = &dummy;
int cnt = 0;
while(cnt != left-1){
    before = before->next;
    cnt += 1;
}
```

### Step 3: `after` dhoondho (position `right+1`)
Dummy se `right+1` steps chalo.
```cpp
ListNode* after = &dummy;
cnt = 0;
while(cnt != right+1){
    after = after->next;
    cnt += 1;
}
```
> `right = n` ho toh `after` = `NULL` aayega. Ye safe hai, kyunki last step pe hum node `n` (jo NULL nahi hai) ka `next` le rahe hain. ✅

### Step 4: `left` node save karo
```cpp
ListNode* mark_connection = before->next;   // left wala node
```
Iska naam `mark_connection` isliye rakha kyunki reverse ke baad isi node se `after` ka connection lagana hai.

### Step 5: `k = right-left+1` nodes reverse karo
```cpp
ListNode* newHead = reverse(mark_connection, before, right-left+1);
```

### Step 6: Dono connections jodo
```cpp
before->next = newHead;            // connection 1
mark_connection->next = after;     // connection 2
return dummy.next;
```

---

## 🔄 Reverse Helper — `k` nodes tak

```cpp
ListNode* reverse(ListNode* head, ListNode* prev, int k){
    ListNode* curr = head;
    int cnt = 0;
    while(cnt != k){
        ListNode* next = curr->next;   // 1. aage ka save
        curr->next = prev;             // 2. link ulta
        prev = curr;                   // 3. prev aage
        curr = next;                   // 4. curr aage
        cnt += 1;
    }
    return prev;                       // reversed part ka naya head
}
```

LC 206 se sirf ek farak: `curr != NULL` ki jagah **`k` nodes** tak chalta hai.

### 🧠 `prev` parameter ka secret

> **Reversed part ka last node (yaani pehla node) hamesha usi se judta hai jo `prev` mein shuru mein diya tha.**

| Shuru mein `prev` | Pehla node ka `next` ban jaata hai | Kab use hota hai |
|-------------------|-----------------------------------|------------------|
| `NULL` | `NULL` | LC 206 (poori list reverse) |
| `before` (meri approach) | `before` ❌ galat link, baad mein fix | Step 6 mein `mark_connection->next = after` se theek |
| `after` | `after` ✅ seedha sahi | Fix-up line ki zarurat nahi |

Meri approach mein `prev = before` pass kiya, toh reverse ke andar `2->next = 1` (galat) ban jaata hai. Lekin Step 6 ki line `mark_connection->next = after` use overwrite karke sahi kar deti hai. Dono connections end mein clearly dikhte hain, isliye ye version samajhne mein easy hai. 👍

---

## ✅ Full Code (Meri Approach)

```cpp
class Solution {
public:
    ListNode* reverse(ListNode* head , ListNode* prev , int k){
        ListNode* curr = head;
        int cnt = 0;
        while(cnt != k){
            ListNode* next = curr->next;
            curr->next = prev;
            prev = curr;
            curr = next;
            cnt += 1;
        }
        return prev;
    }

    ListNode* reverseBetween(ListNode* head, int left, int right) {
        ListNode dummy(0);
        dummy.next = head;

        // left-1 wala node
        ListNode* before = &dummy;
        int cnt = 0;
        while(cnt != left-1){
            before = before->next;
            cnt += 1;
        }

        // right+1 wala node
        ListNode* after = &dummy;
        cnt = 0;
        while(cnt != right+1){
            after = after->next;
            cnt += 1;
        }

        ListNode* mark_connection = before->next;    // left wala node
        ListNode* newHead = reverse(mark_connection, before, right-left+1);

        before->next = newHead;           // before → reversed part
        mark_connection->next = after;    // reversed part → after
        return dummy.next;
    }
};
```

---

## 🔍 Dry Run

### Case A: `1 → 2 → 3 → 4 → 5`, `left = 2`, `right = 4`

**Pointers dhoondhna:**

| Pointer | Steps from dummy | Node |
|---------|------------------|------|
| `before` | `left-1 = 1` | `1` |
| `after` | `right+1 = 5` | `5` |
| `mark_connection` | `before->next` | `2` |
| `k` | `right-left+1` | `3` |

**`reverse(2, prev=1, k=3)`:**

| cnt | curr | `curr->next = prev` | prev | curr (next) |
|-----|------|---------------------|------|-------------|
| 0 | 2 | `2 → 1` (galat, baad mein fix) | 2 | 3 |
| 1 | 3 | `3 → 2` | 3 | 4 |
| 2 | 4 | `4 → 3` | 4 | 5 |
| 3 | stop | | | |

Return `newHead = 4`. Ab reversed part: `4 → 3 → 2`

**Connections:**
- `before->next = 4` → `1 → 4 → 3 → 2`
- `mark_connection->next = 5` → `2 → 5`

**Output: `1 → 4 → 3 → 2 → 5`** ✅

### Case B: `left = 1` — `1 → 2 → 3`, `left = 1`, `right = 2`
- `before = dummy` (0 steps), `after = 3`, `mark_connection = 1`
- Reverse 2 nodes → `2 → 1`
- `dummy.next = 2`, `1->next = 3`
- **Output: `2 → 1 → 3`** ✅ (head badal gaya, dummy ne sambhala)

### Case C: `right = n` — `1 → 2 → 3`, `left = 2`, `right = 3`
- `before = 1`, `after = NULL` (4 steps: dummy→1→2→3→NULL), `mark_connection = 2`
- Reverse → `3 → 2`
- `1->next = 3`, `2->next = NULL`
- **Output: `1 → 3 → 2`** ✅

---

## 🧪 Edge Cases

| Input | left, right | Output | Kaise handle hua |
|-------|-------------|--------|------------------|
| `[5]` | 1, 1 | `[5]` | `before = dummy`, `after = NULL`, 1 node reverse = same |
| `[1,2,3]` | 2, 2 | `[1,2,3]` | `k = 1`, ek node ka reverse kuch nahi badalta |
| `[1,2,3]` | 1, 3 | `[3,2,1]` | poori list reverse, `before = dummy`, `after = NULL` |
| `[1,2,3]` | 1, 2 | `[2,1,3]` | head badla → `dummy.next` return |
| `[1,2,3]` | 2, 3 | `[1,3,2]` | `after = NULL` |

---

## ⏱ Complexity

| | Value | Kyun |
|---|---|---|
| **Time** | `O(n)` | `before` tak + `after` tak + `k` nodes reverse — sab milake `O(n)` |
| **Space** | `O(1)` | Sirf pointers, dummy stack pe |

> Technically list ko ek se zyada baar traverse kiya (`before` aur `after` dono dummy se). Follow-up "one pass" maangta hai, uske liye neeche dekho.

---

## ⚠️ Common Mistakes / Traps

1. **Dummy ke bina** → `left = 1` pe `before` hi nahi milega, alag case likhna padega.
2. **`head` return karna `dummy.next` ki jagah** → `left = 1` pe purana head ab beech mein hai, galat answer.
3. **`left` node save na karna** → reverse ke baad pata hi nahi chalega ki reversed part ka last node kaunsa hai.
4. **Off-by-one steps** → `before` ke liye `left-1` steps, `after` ke liye `right+1` steps (dummy se). Dummy ko position `0` maan ke socho.
5. **`mark_connection->next = after` bhoolna** → `2->next = 1` reh jaayega aur **cycle** ban jaayegi (`1 → 4 → 3 → 2 → 1 → ...`) 💥
6. **Reverse ke andar `next` save kiye bina link ulta karna** → list toot jaati hai.

---

## 🔁 Alternative 1: `prev = after` pass karo (fix-up line nahi chahiye)

```cpp
before->next = reverse(before->next, after, right - left + 1);
return dummy.next;
```

Reverse ki pehli iteration mein hi `leftNode->next = after` ho jaata hai, kyunki `prev` shuru mein `after` hai. Compact hai, lekin connection "chhupa" hua hai, isliye pehle meri wali approach samjho.

---

## 🔁 Alternative 2: One Pass — Head Insertion (Follow-up 🔥)

Idea: `before` ko fix rakho. `left` wala node (`curr`) bhi fix rehta hai. Har baar `curr` ke **theek baad wala node** uthao aur `before` ke **theek baad** daal do. Ye `right-left` baar karo.

```cpp
ListNode* reverseBetween(ListNode* head, int left, int right) {
    ListNode dummy(0, head);
    ListNode* before = &dummy;
    int cnt = 0;
    while (cnt != left - 1) {
        before = before->next;
        cnt += 1;
    }

    ListNode* curr = before->next;      // left node — ye hamesha reversed part ka last rahega
    cnt = 0;
    while (cnt != right - left) {
        ListNode* move = curr->next;    // jise uthana hai
        curr->next = move->next;        // 1. move ko list se nikalo
        move->next = before->next;      // 2. move ko front pe lagao
        before->next = move;            // 3. before → move
        cnt += 1;
    }
    return dummy.next;
}
```

### Dry run: `1 → 2 → 3 → 4 → 5`, `left = 2`, `right = 4`
`before = 1`, `curr = 2`

| Step | move | List after |
|------|------|-----------|
| start | | `1 → 2 → 3 → 4 → 5` |
| 1 | 3 | `1 → 3 → 2 → 4 → 5` |
| 2 | 4 | `1 → 4 → 3 → 2 → 5` ✅ |

> `curr` (node 2) kabhi nahi hilta — wo dheere-dheere peeche khisakta jaata hai aur end mein reversed part ka last node ban jaata hai.

### Teeno approaches compare

| | Meri (before + after + fix) | `prev = after` | Head Insertion |
|---|---|---|---|
| Passes | 2+ | 2+ | **1** ✅ |
| Samajhna | 🔥 Sabse easy | Medium | Diagram chahiye |
| Reverse helper reuse | ✅ (LC 206 se) | ✅ | ❌ |
| Interview | Pehle yahi likho | — | Follow-up pe |

---

## 🧩 Pattern Recognition

| Problem | Connection |
|---------|-----------|
| LC 206 Reverse Linked List | Base — `prev = NULL` se poora reverse |
| **LC 92 Reverse Linked List II** | Beech ka hissa reverse — `before` / `after` boundary |
| LC 24 Swap Nodes in Pairs | `k = 2` groups reverse |
| **LC 25 Reverse Nodes in k-Group** (Hard) 🔥 | LC 92 ko baar-baar lagao — har group ke liye `before`, `after`, reverse, connect |
| LC 234 Palindrome Linked List | Second half reverse karke compare |
| LC 143 Reorder List | Middle + reverse + merge |

> 🎯 **LC 25** ab tumhare paas saare tools hain: is problem ka `reverse(head, prev, k)` helper wahan seedha kaam aayega.

---

## 📝 One-Line Revision

> **"Dummy lagao. `before` = dummy se `left-1` steps, `after` = dummy se `right+1` steps, `leftNode = before->next` save karo. `k = right-left+1` nodes reverse karo. Phir `before->next = newHead` aur `leftNode->next = after`. Return `dummy.next`. O(n) time, O(1) space."**
