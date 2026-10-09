# 🔄 Rotate List — LeetCode 61

> **Difficulty:** Medium
> **Topics:** Linked List · Two Pointers
> **Pattern:** *Length nikalo → `k % n` → naya tail dhoondho → kaato aur purane tail ko head se jodo*

---

## 📌 Problem Statement

Linked list ka `head` aur ek integer `k` diya hai. List ko **right side `k` places** rotate karo.

Right rotate = last node utha ke aage laga do, aur ye `k` baar karo.

```
Input:  1 → 2 → 3 → 4 → 5,  k = 2
Output: 4 → 5 → 1 → 2 → 3

Input:  0 → 1 → 2,  k = 4
Output: 2 → 0 → 1
```

Ek-ek rotation dekho (`k = 2`):
```
rotate 1:  5 → 1 → 2 → 3 → 4
rotate 2:  4 → 5 → 1 → 2 → 3   ✅
```

**Constraints:**
- Nodes: `0` to `500`
- `-100 <= Node.val <= 100`
- `0 <= k <= 2 * 10^9` ← 🔥 `k` bahut bada ho sakta hai!

---

## 🧠 Core Intuition

### Observation 1: Last `k` nodes aage aa jaate hain

```
Pehle:   1 → 2 → 3 | 4 → 5        (k = 2)
                   ↑
            yahan se kaato

Baad:    4 → 5 | 1 → 2 → 3
```

Rotate karne ka matlab hai **list ko do hisson mein kaato** aur **dono ki jagah badal do**:
- Last `k` nodes → aage
- Pehle `n - k` nodes → peeche

Toh ek-ek karke `k` baar rotate karne ki zarurat nahi. Bas **sahi jagah se kaato aur jodo**. 🔥

### Observation 2: `n` rotations = koi rotation nahi

Length 5 ki list ko 5 baar rotate karo → wapas same list. Isliye:

```
k = 7, n = 5   →   7 % 5 = 2   →   sirf 2 rotation ka kaam
k = 5, n = 5   →   5 % 5 = 0   →   kuch nahi karna
```

> ⚠️ `k` 2×10⁹ tak ho sakta hai. `k % n` ke bina ek-ek rotation karoge toh **TLE** pakka.

### Kaun kya banega? (1-indexed positions)

| Node | Position | Rotate ke baad |
|------|----------|----------------|
| Naya **tail** | `n - k` | iska `next = NULL` |
| Naya **head** | `n - k + 1` | return yahi |
| Purana **tail** | `n` | iska `next = purana head` |

Example `n = 5`, `k = 2`: naya tail = position 3 (`3`), naya head = position 4 (`4`).

---

## ⭐ Meri Approach — Step by Step

### Step 1: Base case

```cpp
if (head == NULL || head->next == NULL || k == 0)
    return head;
```

| Condition | Kyun |
|-----------|------|
| `head == NULL` | khaali list → rotate kya karein |
| `head->next == NULL` | 1 node → rotate karke bhi same |
| `k == 0` | koi rotation nahi |

### Step 2: Length aur last node — ek hi pass mein

```cpp
int cnt = 1;
ListNode* last_node = head;

while (last_node->next != NULL) {
    last_node = last_node->next;
    cnt++;
}
```

- `cnt = 1` se shuru kiya kyunki `head` already gin liya.
- `last_node->next != NULL` tak chalaya, toh loop ke baad `last_node` **last node pe** rukta hai (NULL pe nahi).
- Ek pass mein **do cheezein** mil gayi: `cnt` (length) aur `last_node` (tail). 🔥

### Step 3: `k` ko chhota karo

```cpp
k = k % cnt;
if (k == 0) return head;
```

`k` multiple of `cnt` tha toh list waisi hi rehti hai → seedha return.

### Step 4: Naya tail dhoondho (position `cnt - k`)

```cpp
ListNode* curr = head;
for (int i = 1; i < cnt - k; i++) {
    curr = curr->next;
}
```

`curr` position 1 pe hai. Loop `cnt - k - 1` baar chalta hai, toh `curr` **position `cnt - k`** pe pahunchta hai = naya tail. ✅

### Step 5: Kaato aur jodo

```cpp
ListNode* new_Head = curr->next;    // 1. naya head save karo (kaatne se pehle!)
curr->next = NULL;                  // 2. naya tail → NULL (list kaati)
last_node->next = head;             // 3. purana tail → purana head
return new_Head;
```

> ⚠️ `new_Head` **pehle** save karo. `curr->next = NULL` karne ke baad naya head ka pata kho jaayega.

---

## ✅ Full Code (Meri Approach)

```cpp
class Solution {
public:
    ListNode* rotateRight(ListNode* head, int k) {
        // Step 1: base case
        if (head == NULL || head->next == NULL || k == 0)
            return head;

        // Step 2: length + last node ek pass mein
        int cnt = 1;
        ListNode* last_node = head;
        while (last_node->next != NULL) {
            last_node = last_node->next;
            cnt++;
        }

        // Step 3: extra rotations hatao
        k = k % cnt;
        if (k == 0) return head;

        // Step 4: naya tail (position cnt - k)
        ListNode* curr = head;
        for (int i = 1; i < cnt - k; i++) {
            curr = curr->next;
        }

        // Step 5: kaato aur jodo
        ListNode* new_Head = curr->next;
        curr->next = NULL;
        last_node->next = head;

        return new_Head;
    }
};
```

---

## 🔍 Dry Run

### Case A: `1 → 2 → 3 → 4 → 5`, `k = 2`

**Step 2 — length:**

| last_node | cnt |
|-----------|-----|
| 1 | 1 |
| 2 | 2 |
| 3 | 3 |
| 4 | 4 |
| 5 | 5 (ruk gaya, `5->next == NULL`) |

**Step 3:** `k = 2 % 5 = 2`

**Step 4:** `cnt - k = 3` → loop `i = 1, 2` (2 baar)

| i | curr |
|---|------|
| start | 1 |
| 1 | 2 |
| 2 | 3 ✅ naya tail |

**Step 5:**
```
new_Head = 4
3->next = NULL         →   1 → 2 → 3       4 → 5
5->next = 1            →   4 → 5 → 1 → 2 → 3
```

**Output: `4 → 5 → 1 → 2 → 3`** ✅

### Case B: `0 → 1 → 2`, `k = 4` (k > n)

- `cnt = 3`, `last_node = 2`
- `k = 4 % 3 = 1`
- `cnt - k = 2` → `curr` 1 step → `curr = 1` (naya tail)
- `new_Head = 2`, `1->next = NULL`, `2->next = 0`

**Output: `2 → 0 → 1`** ✅

### Case C: `1 → 2 → 3`, `k = 3` (k == n)

- `cnt = 3`, `k = 3 % 3 = 0` → **return head**

**Output: `1 → 2 → 3`** ✅ (poora chakkar lag ke wapas)

### Case D: `1 → 2`, `k = 1`

- `cnt = 2`, `k = 1`
- `cnt - k = 1` → loop chalega hi nahi → `curr = 1`
- `new_Head = 2`, `1->next = NULL`, `2->next = 1`

**Output: `2 → 1`** ✅

---

## 🧪 Edge Cases

| Input | k | Output | Kaise handle hua |
|-------|---|--------|------------------|
| `[]` | 3 | `[]` | `head == NULL` → base case |
| `[1]` | 99 | `[1]` | `head->next == NULL` → base case |
| `[1,2,3]` | 0 | `[1,2,3]` | `k == 0` → base case |
| `[1,2,3]` | 3 | `[1,2,3]` | `k % cnt == 0` |
| `[1,2,3]` | 2000000000 | `[3,1,2]` | `2e9 % 3 = 2` → bina TLE ke |
| `[1,2]` | 1 | `[2,1]` | loop 0 baar, `curr = head` |

---

## ⏱ Complexity

| | Value | Kyun |
|---|---|---|
| **Time** | `O(n)` | Ek pass length ke liye + max ek pass naya tail dhoondhne ke liye |
| **Space** | `O(1)` | Sirf pointers |

> `k` kitna bhi bada ho, `k % n` ki wajah se time pe koi asar nahi. ✅

---

## ⚠️ Common Mistakes / Traps

1. **`k % n` bhoolna** ❌ → `k = 2×10⁹` pe ek-ek rotation = **TLE**, ya index calculation galat.
2. **`k % n` ke baad `k == 0` check na karna** ❌ → `cnt - k = cnt` hoga, `curr` last node pe pahunchega, `new_Head = NULL` return ho jaayega 💥 (aur `last_node->next = head` se cycle bhi ban jaayegi).
3. **Length ginte waqt `while (last_node != NULL)`** ❌ → loop ke baad `last_node` NULL hoga, tail ka pata nahi chalega. `last_node->next != NULL` tak chalao.
4. **`cnt = 0` se shuru karke `->next` wala loop** ❌ → length ek kam aayegi.
5. **`new_Head` save kiye bina `curr->next = NULL`** ❌ → naya head kho gaya.
6. **Off-by-one naye tail mein** → naya tail position `n - k` pe hai, `n - k + 1` pe nahi. Chhota example (`n = 5, k = 2` → tail `3`) se hamesha verify karo.
7. **Left vs right rotate confuse karna** → right rotate mein **last** `k` nodes aage aate hain.

---

## 🔁 Alternative: Pehle Circle Banao, Phir Kaato

Pehle tail ko head se jod do (list **circular** ban gayi), phir sahi jagah se kaato.

```cpp
ListNode* rotateRight(ListNode* head, int k) {
    if (head == NULL || head->next == NULL || k == 0) return head;

    int cnt = 1;
    ListNode* tail = head;
    while (tail->next != NULL) {
        tail = tail->next;
        cnt++;
    }

    tail->next = head;              // 🔄 circle bana diya

    k = k % cnt;
    int steps = cnt - k;            // tail se itne steps → naya tail
    ListNode* newTail = tail;
    while (steps > 0) {
        newTail = newTail->next;
        steps--;
    }

    ListNode* newHead = newTail->next;
    newTail->next = NULL;           // ✂️ circle toda
    return newHead;
}
```

```
Circle:   1 → 2 → 3 → 4 → 5
          ↑_______________│

Tail (5) se 3 steps → 3 pe kaato → 4 → 5 → 1 → 2 → 3
```

**Fayda:** `k % cnt == 0` ka alag check nahi chahiye. `steps = cnt` hoga, `newTail` poora chakkar lagake wapas `tail` pe aayega aur wahi kaatega → list waisi hi. ✅

| | Meri approach (kaato + jodo) | Circle approach |
|---|---|---|
| Kaam ka order | Pehle kaato, phir jodo | Pehle jodo, phir kaato |
| `k == 0` check | ✅ zaroori | ❌ apne aap handle |
| Samajhna | 🔥 Easy, step by step | Circle imagine karna padta hai |
| Time / Space | O(n) / O(1) | O(n) / O(1) |

---

## 💡 Pro Tips (Tagda Level 🔥)

1. **Left rotate chahiye toh?** Naya tail position `k` pe hoga (`n - k` ki jagah). Baaki code same.
   ```
   Left rotate by k  =  Right rotate by (n - k)
   ```
2. **Array rotate (LC 189) se connection:** array mein elements shift karne padte hain (O(n) moves), linked list mein sirf **2 links** badalte hain. Yahi linked list ka fayda hai.
3. **Interview line:** *"Right rotate by k ka matlab hai last k nodes aage aana. Main length nikalunga, `k % n` karunga, phir position `n - k` pe list kaat ke purane tail ko head se jod dunga. O(n) time, O(1) space."*

---

## 🧩 Pattern Recognition

| Problem | Connection |
|---------|-----------|
| **LC 61 Rotate List** | Length → `k % n` → kaato aur jodo |
| LC 189 Rotate Array | Same `k % n` trick; array mein 3 reverse wali trick |
| LC 19 Remove Nth Node From End | "End se k-th node" dhoondhna — length se ya two pointers (gap `k`) se |
| LC 876 Middle of the Linked List | Length / position based traversal |
| LC 796 Rotate String | Rotation = string ko khud se jodo (`s + s`) — circle wali soch |

> 🎯 **Bonus:** Naya tail (end se `k+1`th node) **two pointers** se bhi mil sakta hai: `fast` ko `k` steps aage bhejo, phir dono saath chalao jab tak `fast->next != NULL`. Lekin `k % n` ke liye length chahiye hi, toh meri approach hi simple hai.

---

## 📝 One-Line Revision

> **"Base case (0/1 node ya k = 0) → return. Ek pass mein length `cnt` aur `last_node` nikalo. `k %= cnt`, `k == 0` → return. Head se `cnt - k - 1` steps → naya tail. `new_Head = curr->next`, `curr->next = NULL`, `last_node->next = head`. Return `new_Head`. O(n) time, O(1) space."**
