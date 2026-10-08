# 🧹 Remove Duplicates from Sorted List II — LeetCode 82

> **Difficulty:** Medium
> **Topics:** Linked List · Two Pointers · Dummy Node
> **Pattern:** *Dummy + Tail se nayi list banao, sirf "eligible" nodes jodo*

---

## 📌 Problem Statement

Ek **sorted** linked list ka `head` diya hai. Jo values **duplicate** hain, unke **saare** nodes hata do. Sirf woh values bachni chahiye jo **exactly ek baar** aayi hain. Result sorted hi rahega.

```
Input:  1 → 2 → 3 → 3 → 4 → 4 → 5     Output: 1 → 2 → 5
Input:  1 → 1 → 1 → 2 → 3             Output: 2 → 3
Input:  1 → 1                         Output: []
```

**Constraints:**
- Nodes: `0` to `300`
- `-100 <= Node.val <= 100`
- List **sorted (ascending)** hai ← yahi sabse bada hint hai

---

## ⚔️ LC 83 vs LC 82 — Confusion mat karna!

| | LC 83 (Easy) | **LC 82 (Medium)** |
|---|---|---|
| Kya karna hai | Duplicates ki **ek copy rakho** | Duplicate value ke **saare nodes hatao** |
| `1→1→2→3→3` | `1→2→3` | `2` |
| Head badal sakta hai? | ❌ Nahi | ✅ **Haan** → isliye dummy node chahiye |

---

## 🧠 Core Intuition

1. List sorted hai, toh **same values hamesha saath-saath (adjacent)** aayengi. Kisi value ko check karne ke liye sirf `curr->val == curr->next->val` dekhna kaafi hai.
2. Head khud duplicate ho sakta hai (`1→1→2`), toh answer ka head badal sakta hai. **Dummy node** lagao, taaki head ke liye alag case na likhna pade.
3. Ek nayi list banane ki tarah socho: `curr` original list pe chalta hai, aur jo node **eligible** (unique) hai usko `tail` ke peeche jod do.

```
Original:  1 → 2 → [3 → 3] → [4 → 4] → 5
                     skip      skip
Answer:    dummy → 1 → 2 → 5
```

---

## ⭐ Meri Approach — Step by Step

### Setup
```cpp
ListNode dummy(0);           // stack pe dummy (delete ki tension nahi)
ListNode* tail = &dummy;     // answer list ka last node
ListNode* curr = head;       // original list pe chalne wala pointer
```
> Yahan `dummy.next = head` **nahi** kiya, kyunki hum nayi list scratch se jod rahe hain.

### Har `curr` pe do cases

**Case 1: `curr` duplicate group ka start hai** (`curr->next` same value ka hai)
→ Uss value `v` ko yaad karo, aur jab tak `curr->val == v` hai, aage badhte raho. Poora group skip ho jaayega.

```cpp
if (curr->next != NULL && curr->val == curr->next->val) {
    int v = curr->val;
    while (curr && curr->val == v) {
        curr = curr->next;
    }
}
```

**Case 2: `curr` unique hai** → answer mein jod do.
```cpp
else {
    tail->next = curr;   // jodo
    tail = curr;         // tail aage
    curr = curr->next;   // curr aage
}
```

### End: tail ke aage ka kachra kaato
```cpp
tail->next = NULL;
return dummy.next;
```

---

## ✅ Full Code (Meri Approach)

```cpp
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        ListNode dummy(0);
        ListNode* tail = &dummy;
        ListNode* curr = head;

        while(curr){
            // Case 1: duplicate group → poora skip
            if(curr->next != NULL && curr->val == curr->next->val){
                int v = curr->val;
                while(curr && curr->val == v){
                    curr = curr->next;
                }
            }
            // Case 2: unique → answer mein jodo
            else{
                tail->next = curr;
                tail = curr;
                curr = curr->next;
            }
        }
        tail->next = NULL;   // ⚠️ bahut zaroori
        return dummy.next;
    }
};
```

---

## 🔍 Dry Run

### Case A: `1 → 2 → 3 → 3 → 4 → 4 → 5`

| curr | `curr->next` same? | Action | Answer list |
|------|-------------------|--------|-------------|
| 1 | ❌ (2) | jodo | `1` |
| 2 | ❌ (3) | jodo | `1 → 2` |
| 3 | ✅ (3) | v=3, dono 3 skip → curr=4 | `1 → 2` |
| 4 | ✅ (4) | v=4, dono 4 skip → curr=5 | `1 → 2` |
| 5 | ❌ (NULL) | jodo | `1 → 2 → 5` |
| NULL | | loop khatam | |

`tail->next = NULL` → **Output: `1 → 2 → 5`** ✅

### Case B: `1 → 1 → 1 → 2 → 3` (head khud duplicate)
- curr=1, next same → v=1, teeno 1 skip → curr=2
- 2 unique → jodo; 3 unique → jodo
- **Output: `2 → 3`** ✅ (dummy ki wajah se head badalna automatically handle hua)

### Case C: `1 → 1` (sab duplicate)
- Sab skip → curr=NULL. `tail` abhi bhi `&dummy` pe hai.
- `tail->next = NULL` → `dummy.next = NULL`
- **Output: `[]`** ✅

### Case D: `1 → 2 → 2` — `tail->next = NULL` kyun zaroori hai 🔥
- 1 unique → jodo. Tail = node 1. **Lekin node 1 ka `next` abhi bhi original wale `2` ko point kar raha hai!**
- 2, 2 skip → curr=NULL
- Agar `tail->next = NULL` nahi kiya → output `1 → 2 → 2` ❌ (galat)
- Kiya toh → `1` ✅

---

## 🧪 Edge Cases

| Input | Output | Kaise handle hua |
|-------|--------|------------------|
| `[]` | `[]` | `while(curr)` chalega hi nahi, `dummy.next = NULL` |
| `[5]` | `[5]` | `curr->next == NULL` → else branch → jud gaya |
| `[1,1]` | `[]` | sab skip, tail = dummy |
| `[1,1,2]` | `[2]` | head change → dummy ne bachaya |
| `[1,2,2]` | `[1]` | last group duplicate → `tail->next = NULL` ne bachaya |
| `[1,2,3]` | `[1,2,3]` | kuch skip nahi hua |

---

## ⏱ Complexity

| | Value | Kyun |
|---|---|---|
| **Time** | `O(n)` | Har node pe `curr` sirf ek baar aata hai (inner loop bhi aage hi badhta hai, toh total n steps) |
| **Space** | `O(1)` | Dummy + 2 pointers, nayi nodes nahi banayi |

> ⚠️ Nested `while` dekh ke `O(n²)` mat bol dena! Inner loop `curr` ko hi aage badhata hai, toh dono loops milake bhi har node ek hi baar visit hota hai.

---

## ⚠️ Common Mistakes / Traps

1. **`tail->next = NULL` bhoolna** → last group duplicate ho toh extra nodes output mein aa jaate hain (Case D).
2. **`curr->next` NULL check ke bina access** → `curr->next->val` pe crash. Condition mein `curr->next != NULL` **pehle** likho.
3. **Inner loop mein `curr &&` bhoolna** → duplicate group list ke end mein ho toh `curr` NULL ho jaata hai, phir `curr->val` crash.
4. **Sirf ek duplicate skip karna** (`curr = curr->next->next`) → `1→1→1` mein ek `1` bach jaayega. ✅ Value `v` save karke **poora group** skip karo.
5. **Dummy ke bina karna** → head duplicate wale case (`1→1→2`) ke liye alag messy code likhna padega.
6. **`v` save na karna** aur `curr->val == curr->next->val` pe hi loop chalana → group ka **last node** bach jaata hai.

---

## 🔁 Alternative Approach: `prev` pointer (Classic Editorial Style)

Yahan `dummy->next = head` **kiya jaata hai** kyunki list ko in-place modify kar rahe hain.

```cpp
ListNode* deleteDuplicates(ListNode* head) {
    ListNode dummy(0, head);         // dummy.next = head
    ListNode* prev = &dummy;         // last confirmed unique node
    while (head) {
        if (head->next && head->val == head->next->val) {
            while (head->next && head->val == head->next->val)
                head = head->next;   // group ke last node tak jao
            prev->next = head->next; // poora group bypass
        } else {
            prev = prev->next;       // head unique hai, prev aage
        }
        head = head->next;
    }
    return dummy.next;
}
```

| | Meri approach (tail build) | Prev approach (bypass) |
|---|---|---|
| Soch | Eligible nodes **jodo** | Duplicate groups ko **bypass** karo |
| `dummy.next = head`? | ❌ | ✅ |
| End mein `tail->next = NULL`? | ✅ zaroori | ❌ nahi chahiye (bypass se already link sahi hai) |
| Reusable? | 🔥 Bahut — kisi bhi "filter" problem pe chalta hai | Is problem specific |

> Meri approach ka **"filter template"** zyada general hai — LC 203, LC 86 (Partition List), LC 328 (Odd Even) sab isi se bante hain.

---

## 🔁 Bonus: Recursive (sirf samajhne ke liye)

```cpp
ListNode* deleteDuplicates(ListNode* head) {
    if (!head || !head->next) return head;
    if (head->val == head->next->val) {
        int v = head->val;
        while (head && head->val == v) head = head->next;
        return deleteDuplicates(head);          // poora group chhod do
    }
    head->next = deleteDuplicates(head->next);  // head rakho
    return head;
}
```
Space `O(n)` (recursion stack), isliye iterative better hai.

---

## 💡 Pro Tips (Tagda Level 🔥)

1. **Memory leak:** Skipped nodes kahin se point nahi ho rahe, lekin `delete` bhi nahi hue. LeetCode pe chalega, interview mein bol do: *"Production mein skip karte waqt `delete` karunga."*
   ```cpp
   while (curr && curr->val == v) {
       ListNode* del = curr;
       curr = curr->next;
       delete del;
   }
   ```
2. **Stack dummy (`ListNode dummy(0);`)** use kiya hai — `delete dummy` ki zarurat nahi. Dot (`dummy.next`) lagta hai, arrow nahi. 👍
3. Agar list **sorted nahi** hoti → hashmap se frequency count karo, phir filter karo (`O(n)` space).

---

## 🧩 Pattern: "Filter Template" (Dummy + Tail)

```cpp
ListNode dummy(0);
ListNode* tail = &dummy;
for (curr = head; curr; ...) {
    if (eligible(curr)) { tail->next = curr; tail = curr; }
}
tail->next = NULL;
return dummy.next;
```

| Problem | Eligible kaun? |
|---------|----------------|
| LC 83 Remove Duplicates I | Har group ka pehla node |
| **LC 82 Remove Duplicates II** | Sirf woh node jiska group size = 1 |
| LC 203 Remove Linked List Elements | `val != target` |
| LC 86 Partition List | Do dummies: `< x` aur `>= x` |
| LC 328 Odd Even Linked List | Do tails: odd index, even index |

---

## 📝 One-Line Revision

> **"Sorted list → duplicates adjacent. Dummy + tail lo; agar `curr` aur `curr->next` same hain toh value save karke poora group skip karo, warna `curr` ko tail se jodo. End mein `tail->next = NULL` karke `dummy.next` return karo. O(n) time, O(1) space."**
