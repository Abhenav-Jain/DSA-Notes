# 🔁 Palindrome Linked List — LeetCode 234

> **Difficulty:** Easy (lekin concepts Medium-level ke hain)
> **Topics:** Linked List · Two Pointers (Slow/Fast) · In-place Reversal
> **Pattern:** *Find Middle + Reverse Second Half + Compare*

---

## 📌 Problem Statement

Ek singly linked list ka `head` diya hai. Return `true` agar list **palindrome** hai, warna `false`.

```
Input:  1 → 2 → 2 → 1        Output: true
Input:  1 → 2                Output: false
Input:  1 → 2 → 3 → 2 → 1    Output: true
```

**Constraints:**
- Nodes: `1` to `10^5`
- `0 <= Node.val <= 9`

**Follow-up:** Kya `O(n)` time aur `O(1)` space mein kar sakte ho? ✅ (Meri approach yahi karti hai)

---

## 🧠 Core Intuition

Palindrome = aage se padho ya peeche se, same.

Array mein easy hai — `i` aage se, `j` peeche se. **Problem:** singly linked list mein peeche nahi ja sakte. ❌

**Trick:** Agar peeche nahi ja sakte, toh **second half ko hi ulta kar do!** 🔄
Phir dono halves ko aage-aage chala ke compare karo.

```
Original:   1 → 2 → 3 → 2 → 1
                    ↑ middle

First half:  1 → 2 → 3 ...
Second half (reversed): 1 → 2
Compare:     1==1 ✅  2==2 ✅  → Palindrome!
```

---

## 🪜 Approaches (Brute → Optimal)

### Approach 1: Copy to Array / Vector
- Saari values vector mein daalo, two-pointer se check karo.
- ⏱ Time: `O(n)` | 💾 Space: `O(n)`

```cpp
bool isPalindrome(ListNode* head) {
    vector<int> v;
    while (head) { v.push_back(head->val); head = head->next; }
    int i = 0, j = v.size() - 1;
    while (i < j) if (v[i++] != v[j--]) return false;
    return true;
}
```

### Approach 2: Stack
- Saari values stack mein push karo, phir list traverse karte hue pop karke compare karo (stack reverse order deta hai).
- ⏱ Time: `O(n)` | 💾 Space: `O(n)`

### Approach 3: Recursion
- Recursion ka call stack "peeche se" traverse karne deta hai. Ek global `front` pointer aage chalta hai jab recursion unwind hota hai.
- ⏱ Time: `O(n)` | 💾 Space: `O(n)` (recursion stack — `10^5` pe stack overflow ka risk ⚠️)

```cpp
ListNode* front;
bool check(ListNode* curr) {
    if (!curr) return true;
    if (!check(curr->next)) return false;
    if (curr->val != front->val) return false;
    front = front->next;
    return true;
}
bool isPalindrome(ListNode* head) { front = head; return check(head); }
```

### ⭐ Approach 4: Slow/Fast + Reverse Second Half (MERI APPROACH — Optimal)
- ⏱ Time: `O(n)` | 💾 Space: `O(1)` 🔥

---

## ⭐ Meri Approach — Step by Step

### Step 1: Middle dhoondho (Tortoise & Hare 🐢🐇)
- `slow` 1 step chalta hai, `fast` 2 steps.
- Jab `fast` end pe pahunchta hai, `slow` middle pe hota hai.

```cpp
while (fast != NULL && fast->next != NULL) {
    slow = slow->next;
    fast = fast->next->next;
}
```

**Loop khatam hone ke baad `fast` batata hai length odd hai ya even:**

| Length | Loop ke baad `fast` | `slow` kahan hai |
|--------|---------------------|------------------|
| **Odd**  (`1→2→3→2→1`) | last node pe (`!= NULL`) | exact middle (`3`) |
| **Even** (`1→2→2→1`)   | `NULL` | second half ka pehla node (second `2`) |

### Step 2: Second half reverse karo
- **Odd:** middle element ko compare karne ki zarurat nahi (woh khud se hi match karta hai) → `reverse(slow->next)`
- **Even:** `slow` already second half ka start hai → `reverse(slow)`

```cpp
ListNode* mad;
if (fast != NULL) mad = reverse(slow->next);   // odd
else              mad = reverse(slow);         // even
```

### Step 3: Compare dono halves
- `slow` ko wapas `head` pe le aao.
- Jab tak `mad` (reversed second half) khatam na ho, values compare karo.
- Loop `mad` pe chalate hain kyunki second half **chhota ya barabar** hota hai.

```cpp
slow = head;
while (mad != NULL) {
    if (slow->val != mad->val) return false;
    slow = slow->next;
    mad  = mad->next;
}
return true;
```

### 🔄 Reverse Helper (Iterative — 3 pointers)

```cpp
ListNode* reverse(ListNode* head) {
    ListNode* prev = NULL;
    ListNode* next = NULL;
    ListNode* curr = head;
    while (curr != NULL) {
        next = curr->next;   // 1. aage ka save karo
        curr->next = prev;   // 2. link ulta karo
        prev = curr;         // 3. prev aage badhao
        curr = next;         // 4. curr aage badhao
    }
    return prev;             // naya head
}
```

> 🧠 **Yaad rakhne ka mantra:** *"Save next, Reverse link, Move prev, Move curr"*

---

## ✅ Full Code (Meri Approach)

```cpp
class Solution {
public:
    ListNode* reverse(ListNode* head){
        ListNode* prev = NULL;
        ListNode* next = NULL;
        ListNode* curr = head;
        while(curr != NULL){
            next = curr->next;
            curr->next = prev;
            prev = curr;
            curr = next;
        }
        return prev;
    }

    bool isPalindrome(ListNode* head) {
        ListNode* slow = head;
        ListNode* fast = head;

        // Step 1: middle dhoondho
        while(fast != NULL && fast->next != NULL){
            slow = slow->next;
            fast = fast->next->next;
        }

        // Step 2: second half reverse karo
        ListNode* mad;
        if(fast != NULL){          // odd length → middle skip
            mad = reverse(slow->next);
        }
        else{                      // even length
            mad = reverse(slow);
        }

        // Step 3: compare
        slow = head;
        while(mad != NULL){
            if(slow->val != mad->val){
                return false;
            }
            slow = slow->next;
            mad = mad->next;
        }
        return true;
    }
};
```

---

## 🔍 Dry Run

### Case A: Odd — `1 → 2 → 3 → 2 → 1`

| Iteration | slow | fast |
|-----------|------|------|
| start | 1 | 1 |
| 1 | 2 | 3 |
| 2 | 3 | 1 (last) |
| stop (`fast->next == NULL`) | | |

- `fast != NULL` → **odd** → `reverse(slow->next)` = reverse(`2 → 1`) = `1 → 2`
- Compare:
  - `head=1` vs `mad=1` ✅
  - `2` vs `2` ✅
  - `mad = NULL` → **return true** 🎉

### Case B: Even — `1 → 2 → 2 → 1`

| Iteration | slow | fast |
|-----------|------|------|
| start | 1 | 1 |
| 1 | 2 (first) | 2 (second) |
| 2 | 2 (second) | NULL |

- `fast == NULL` → **even** → `reverse(slow)` = reverse(`2 → 1`) = `1 → 2`
- Compare: `1==1` ✅, `2==2` ✅ → **true** 🎉

### Case C: Not palindrome — `1 → 2`
- Loop: slow=2, fast=NULL → even → reverse(`2`) = `2`
- Compare: `1` vs `2` ❌ → **false**

---

## 🧪 Edge Cases

| Input | Kya hota hai | Output |
|-------|--------------|--------|
| `[]` (empty) | `fast == NULL` → `reverse(NULL)` = NULL → loop nahi chalta | `true` |
| `[5]` (single) | loop nahi chalta, `fast != NULL` → `reverse(NULL)` | `true` |
| `[1,1]` | even, reverse(`1`) → `1==1` | `true` |
| `[1,2]` | `1 != 2` | `false` |
| `[1,2,1]` | odd, middle `2` skip | `true` |

✅ Meri code saare edge cases sahi handle karti hai — `NULL` checks pehle se built-in hain.

---

## ⏱ Complexity

| | Value | Kyun |
|---|---|---|
| **Time** | `O(n)` | Middle `n/2` + Reverse `n/2` + Compare `n/2` |
| **Space** | `O(1)` | Sirf kuch pointers, koi extra structure nahi |

---

## ⚠️ Common Mistakes / Traps

1. **Loop condition galat order mein:** `fast->next != NULL && fast != NULL` ❌ → NULL dereference crash. Hamesha `fast != NULL` **pehle** check karo (short-circuit).
2. **Compare loop `slow` pe chalana:** First half ka last node abhi bhi second half se connected hai (reverse ke baad bhi), toh `slow` pe loop chalaoge toh extra nodes compare honge. ✅ Hamesha **`mad` (reversed half)** pe loop chalao.
3. **Reverse mein `next` save karna bhoolna** → list toot jaati hai.
4. **Odd/even confusion** — table yaad rakho: loop ke baad `fast != NULL` ⇒ odd.

---

## 💡 Pro Tips (Tagda Level 🔥)

### Tip 1: Odd/Even branch hata sakte ho
Actually `reverse(slow)` **dono cases** mein kaam karta hai! Odd case mein middle element compare loop ke end mein khud se compare ho jaata hai (`3 == 3`), jo hamesha true hai.
```cpp
ListNode* mad = reverse(slow);   // works for both!
```
Lekin meri wali approach (odd mein skip) **zyada clear aur intention-revealing** hai — interview mein explain karna easy hai. 👍

### Tip 2: List ko restore karna (Interview Brownie Points ⭐)
Meri approach input list ko **modify** kar deti hai (second half ulta reh jaata hai). Real-world mein caller ki list kharab karna bad practice hai. Interviewer pooch sakta hai:
> *"Can you restore the list after checking?"*

```cpp
ListNode* secondHead = reverse(...);   // save karo
ListNode* p = head, *q = secondHead;
bool ans = true;
while (q) {
    if (p->val != q->val) { ans = false; break; }
    p = p->next; q = q->next;
}
reverse(secondHead);   // wapas seedha kar do 🔄
return ans;
```
> Note: `return false` turant mat karo — pehle restore karo, phir return.

### Tip 3: Naming
`mad` ki jagah `secondHalf` / `revHead` naam do — interview mein readability matter karti hai.

### Tip 4: `NULL` vs `nullptr`
Modern C++ mein `nullptr` prefer karo — type-safe hai.

---

## 🧩 Pattern Recognition

Yeh question **3 building blocks** ka combo hai — teeno alag se bhi bahut aate hain:

| Building Block | Related Problems |
|----------------|------------------|
| 🐢🐇 Find Middle (Slow/Fast) | LC 876 Middle of Linked List, LC 141/142 Linked List Cycle |
| 🔄 Reverse List | LC 206 Reverse Linked List, LC 92 Reverse Linked List II, LC 25 Reverse Nodes in k-Group |
| 🔗 Middle + Reverse + Merge/Compare | **LC 143 Reorder List**, LC 2130 Maximum Twin Sum of a Linked List |

> 🎯 **LC 143 Reorder List** ekdum isi template pe hai — next wahi karo!

---

## 📝 One-Line Revision

> **"Slow-fast se middle nikaalo → second half reverse karo → head aur reversed half ko saath chala ke compare karo. O(n) time, O(1) space."**
