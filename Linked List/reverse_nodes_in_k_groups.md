# 🔁 Reverse Nodes in k-Group — LeetCode 25

> **Difficulty:** Hard
> **Topics:** Linked List · Recursion · In-place Reversal
> **Pattern:** *Check k nodes → baaki list recursion ko do → current group reverse karke baaki se jodo*

---

## 📌 Problem Statement

Linked list ka `head` aur ek integer `k` diya hai. List ke nodes ko **k-k ke groups** mein reverse karo aur modified list return karo.

- Agar end mein bache nodes **k se kam** hain, toh unhe **waisa hi** chhod do.
- Sirf nodes ke links badalne hain, **values change karna allowed nahi**.

```
Input:  1 → 2 → 3 → 4 → 5,  k = 2
Output: 2 → 1 → 4 → 3 → 5        (5 akela bacha, waisa hi)

Input:  1 → 2 → 3 → 4 → 5,  k = 3
Output: 3 → 2 → 1 → 4 → 5        (4, 5 sirf 2 nodes → waise hi)
```

**Constraints:**
- Nodes: `n`, jahan `1 <= k <= n <= 5000`
- `0 <= Node.val <= 1000`

**Follow-up:** Kya `O(1)` extra space mein kar sakte ho? (Neeche iterative section dekho)

---

## 🧠 Core Intuition

List ko groups mein dekho:

```
k = 2:   [1 → 2] → [3 → 4] → [5]
          group1    group2    k se kam → mat chhedo
```

Har group ka kaam **same** hai: apne k nodes reverse karo, aur baaki (already reversed) list se jod do. Jab har hissa same kaam kare, toh **recursion** perfect hai.

> 🎯 **Leap of faith:** "Main sirf pehla group sambhalunga. Uske baad ki list ko reverse karna recursion ka kaam hai, aur woh mujhe reversed list ka head de dega."

```
1 → 2 → [3 → 4 → 5]
         └─ recursion isko 4 → 3 → 5 bana dega, aur head "4" return karega

Mera kaam:  2 → 1 → [4 → 3 → 5]
```

### 🔗 Pichhle problems se connection

| Problem | Is problem mein kya kaam aaya |
|---------|-------------------------------|
| LC 24 Swap Pairs | Yahi problem with `k = 2` — same recursion structure |
| LC 206 Reverse List | Group ke andar ka reverse loop |
| LC 92 Reverse List II | 🔥 `prev` mein "aage wala node" dena → reversed part apne aap judta hai |

---

## ⭐ Meri Approach — Step by Step

### Step 1: Check karo ki k nodes hain ya nahi

```cpp
ListNode* temp = head;
int cnt = 0;
while(cnt < k){
    if(temp == NULL){
        return head;        // k se kam nodes → waisa hi return
    }
    cnt += 1;
    temp = temp->next;
}
```

Loop ke baad do cheezein milti hain:
- Pakka ho gaya ki **k nodes hain** ✅
- `temp` ab **(k+1)th node** pe hai, yaani **agle group ka start** (ya `NULL`)

> ⚠️ `NULL` check **pehle**, phir `temp->next`. Order ulta kiya toh crash.

### Step 2: Aage ki list recursion ko de do

```cpp
ListNode* newHead = reverseKGroup(temp, k);
```

Recursion **pehle** call ho raha hai, reverse **baad** mein. Iska matlab list ka **last group sabse pehle** reverse hota hai, aur upar aate hue har group pichhle wale se judta jaata hai.

`newHead` = baaki list (already reversed) ka head.

### Step 3: Current group reverse karo — `prev = newHead` se start

```cpp
temp = head;
cnt = 0;
while(cnt < k){
    ListNode* next = temp->next;
    temp->next = newHead;       // 👈 pehli iteration: head → baaki reversed list
    newHead = temp;
    temp = next;
    cnt += 1;
}
return newHead;                 // ab ye current group ka naya head (kth node) hai
```

Yahan `newHead` hi **`prev`** ka kaam kar raha hai. Ye wahi trick hai jo LC 92 mein dekhi thi:

> 🧠 **Reversed part ka last node usi se judta hai jo `prev` mein shuru mein diya tha.**

| Shuru mein `prev` | Group ka pehla node (reverse ke baad last) judta hai |
|-------------------|-----------------------------------------------------|
| `NULL` | `NULL` se (LC 206) |
| `newHead` (recursion ka result) | **baaki reversed list se** ✅ |

Isliye alag se koi connection jodne ki zarurat nahi padti. Pehli iteration mein hi `head->next = newHead` ho jaata hai. 🔥

### Step 4: Return
Loop ke baad `newHead` group ke **kth node** pe hai, jo ab reversed group ka head hai. Wahi return karo.

---

## ✅ Full Code (Meri Approach)

```cpp
class Solution {
public:
    ListNode* reverseKGroup(ListNode* head, int k) {
        // Step 1: check karo ki k nodes hain ya nahi
        ListNode* temp = head;
        int cnt = 0;
        while(cnt < k){
            if(temp == NULL){
                return head;            // k se kam → waisa hi chhod do
            }
            cnt += 1;
            temp = temp->next;
        }
        // temp = agle group ka start

        // Step 2: aage ki list recursively reverse karo
        ListNode* newHead = reverseKGroup(temp,k);

        // Step 3: current group reverse karo, prev = newHead
        temp = head;
        cnt = 0;
        while(cnt < k){
            ListNode* next = temp->next;
            temp->next = newHead;
            newHead = temp;
            temp = next;
            cnt += 1;
        }
        return newHead;                 // kth node = naya head
    }
};
```

---

## 🔍 Dry Run

### Case A: `1 → 2 → 3 → 4 → 5`, `k = 2`

**Recursion neeche jaata hai (Step 1 + Step 2):**

| Call | k nodes hain? | `temp` (agla group) | Call karta hai |
|------|---------------|---------------------|----------------|
| `rev(1)` | ✅ (1, 2) | 3 | `rev(3)` |
| `rev(3)` | ✅ (3, 4) | 5 | `rev(5)` |
| `rev(5)` | ❌ (sirf 5) | — | **return 5** (waisa hi) |

**Recursion upar aata hai (Step 3):**

`rev(3)` mein: `newHead = 5`

| cnt | temp | `temp->next = newHead` | newHead |
|-----|------|------------------------|---------|
| 0 | 3 | `3 → 5` 👈 baaki list se juda | 3 |
| 1 | 4 | `4 → 3` | 4 |

Return `4` → list: `4 → 3 → 5`

`rev(1)` mein: `newHead = 4`

| cnt | temp | `temp->next = newHead` | newHead |
|-----|------|------------------------|---------|
| 0 | 1 | `1 → 4` 👈 baaki list se juda | 1 |
| 1 | 2 | `2 → 1` | 2 |

Return `2` → **Output: `2 → 1 → 4 → 3 → 5`** ✅

### Case B: `1 → 2 → 3 → 4 → 5`, `k = 3`

| Call | k nodes? | temp | Action |
|------|----------|------|--------|
| `rev(1)` | ✅ (1,2,3) | 4 | `rev(4)` call |
| `rev(4)` | ❌ (4,5 → NULL pe ruk gaya) | — | **return 4** |

`rev(1)`: `newHead = 4` → reverse `1,2,3`:
- `1 → 4`, `2 → 1`, `3 → 2`

**Output: `3 → 2 → 1 → 4 → 5`** ✅

### Case C: `k = 1`
Har group mein 1 node, uska reverse wahi node → **list same rehti hai** ✅

---

## 🧪 Edge Cases

| Input | k | Output | Kaise handle hua |
|-------|---|--------|------------------|
| `[1]` | 1 | `[1]` | 1 node ka reverse = same |
| `[1,2,3]` | 1 | `[1,2,3]` | har node khud ek group |
| `[1,2,3]` | 3 | `[3,2,1]` | poori list ek group, `rev(NULL)` → `NULL` |
| `[1,2,3,4]` | 2 | `[2,1,4,3]` | exact groups, koi bacha nahi |
| `[1,2,3,4,5]` | 3 | `[3,2,1,4,5]` | last 2 nodes waise hi |

> `rev(NULL)` case: Step 1 mein `cnt = 0`, `temp == NULL` → `return head` (jo `NULL` hai). Ye base case ka kaam karta hai. ✅

---

## ⏱ Complexity

| | Value | Kyun |
|---|---|---|
| **Time** | `O(n)` | Har node 2 baar touch hota hai: ek baar count mein, ek baar reverse mein |
| **Space** | `O(n/k)` | Har group ke liye ek recursive call ka stack frame |

> Interview line: *"Recursive solution clean hai, lekin `O(n/k)` stack space leta hai. Follow-up `O(1)` maangta hai, uske liye iterative dummy approach use karunga."*

---

## ⚠️ Common Mistakes / Traps

1. **Pehle reverse kar dena, baad mein check** ❌ → last group mein k se kam nodes hue toh woh bhi reverse ho jaayega. **Hamesha pehle count karo.**
2. **Count loop mein `NULL` check baad mein** → `temp->next` pe crash.
3. **Reverse loop `temp != NULL` tak chalana** ❌ → poori list reverse ho jaayegi. Sirf **k nodes** tak chalao (`cnt < k`).
4. **`prev = NULL` se reverse karna** ❌ → group ka last node `NULL` pe point karega, baaki list kho jaayegi. `prev = newHead` dena zaroori hai.
5. **`head` return karna** ❌ → reverse ke baad `head` group ka **last** node hai. Return `newHead` (kth node) karo.

---

## 💡 Recursion order kyun kaam karta hai?

Mera code **pehle recursion call** karta hai, **phir** apna group reverse karta hai:

```
rev(1) ─── call ───→ rev(3) ─── call ───→ rev(5)
                                            │ return 5
                     rev(3) ←───────────────┘
                     reverse 3,4 → 4→3→5
                     │ return 4
rev(1) ←─────────────┘
reverse 1,2 → 2→1→4→3→5
```

Fayda: jab current group reverse hota hai, tab tak **aage wala hissa ready** hota hai. Isliye `newHead` ko seedha `prev` mein use kar sakte hain.

> Reverse pehle karke baad mein recursion call bhi kar sakte ho, lekin tab group ka last node yaad rakhna padega aur alag se jodna padega. Meri order zyada clean hai. 👍

---

## 🔁 Alternative: Iterative with Dummy (O(1) Space — Follow-up 🔥)

LC 92 wali soch har group pe baar-baar lagao: `groupPrev`, `kth`, `groupNext`.

```cpp
ListNode* reverseKGroup(ListNode* head, int k) {
    ListNode dummy(0, head);
    ListNode* groupPrev = &dummy;

    while (true) {
        // 1. groupPrev se k steps → kth node
        ListNode* kth = groupPrev;
        int cnt = 0;
        while (cnt < k && kth != NULL) {
            kth = kth->next;
            cnt += 1;
        }
        if (kth == NULL) break;              // k nodes nahi bache

        ListNode* groupNext = kth->next;     // agle group ka start

        // 2. group reverse karo, prev = groupNext (LC 92 wali trick)
        ListNode* prev = groupNext;
        ListNode* curr = groupPrev->next;
        while (curr != groupNext) {
            ListNode* next = curr->next;
            curr->next = prev;
            prev = curr;
            curr = next;
        }

        // 3. pichhli list ko naye group head (kth) se jodo
        ListNode* first = groupPrev->next;   // reverse se pehle ka first = ab last
        groupPrev->next = kth;
        groupPrev = first;                   // agle group ka groupPrev
    }
    return dummy.next;
}
```

### Ek group ka picture (`k = 2`)

```
Pehle:   groupPrev → 1 → 2 → 3 ...
                     ↑   ↑   ↑
                  first kth groupNext

Baad:    groupPrev → 2 → 1 → 3 ...
                              ↑
                     agla groupPrev = 1 (first)
```

### Recursive vs Iterative

| | Recursive (meri) | Iterative |
|---|---|---|
| Code | 🔥 Chhota, saaf | Lamba, zyada pointers |
| Space | `O(n/k)` stack | **`O(1)`** ✅ |
| Dummy chahiye? | ❌ | ✅ |
| Last group connect | `prev = newHead` se automatic | `prev = groupNext` se automatic |
| Interview | Pehle yahi likho | Follow-up pe |

---

## 🧩 Pattern Recognition — Reverse Family

| Problem | Kya reverse karna hai | Key trick |
|---------|----------------------|-----------|
| LC 206 Reverse Linked List | Poori list | `prev = NULL` |
| LC 92 Reverse Linked List II | `left` se `right` tak | `before` / `after` boundary |
| LC 24 Swap Nodes in Pairs | Har 2 nodes | `k = 2` wala LC 25 |
| **LC 25 Reverse Nodes in k-Group** | Har k nodes | Count → recurse → reverse with `prev = newHead` |
| LC 2074 Reverse Nodes in Even Length Groups | Group size 1, 2, 3... sirf even wale | LC 25 + variable group size |

> 🎯 Ab tumhare paas **poori reverse family** ho gayi: 206 → 92 → 24 → 25. Ek hi reverse loop, bas `prev` mein kya dena hai aur kitne nodes tak chalana hai, wahi badalta hai.

---

## 📝 One-Line Revision

> **"Pehle check karo k nodes hain ya nahi (nahi → head return). `temp` agle group pe hoga, `newHead = reverseKGroup(temp, k)` se aage ki list reverse karwa lo. Phir current group ke k nodes ko `prev = newHead` se reverse karo, taaki group apne aap baaki list se jud jaaye. Return `newHead`. Time O(n), space O(n/k)."**
