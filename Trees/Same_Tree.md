# 👯 LeetCode 100 — Same Tree

> **Difficulty:** Easy  |  **Pattern:** Two-Tree Simultaneous Recursion  |  **Time:** O(n)  |  **Space:** O(h)

---

## 🏷️ Tags

`Tree` · `Binary Tree` · `DFS` · `BFS` · `Recursion` · `Two-Pointer on Trees` · `Foundation Problem`

---

## ⚡ 30-Second Recap

| Cheez | Yaad rakhne wali baat |
|---|---|
| "Same" ka matlab | **Structure same** AND **values same** |
| Technique | Dono trees pe **ek saath** chalo (`p` aur `q`) |
| Check 1 | Dono `NULL` → **true** |
| Check 2 | Sirf ek `NULL` → **false** |
| Check 3 | `p->val != q->val` → **false** |
| Check 4 | `left` aur `right` dono same hone chahiye → `&&` |
| Order | Check 1 **pehle**, Check 2 **baad me** (order matter karta hai) |

**One-liner:** *"Dono NULL to true, ek NULL to false, value alag to false, warna left aur right dono same hone chahiye."*

---

## 📝 1. Problem Statement

Do binary trees `p` aur `q` diye hain. Check karo ki woh **same** hain ya nahi.

Do trees **same** hain agar woh **structurally identical** hain aur **nodes ki values bhi barabar** hain.

**Constraints:** nodes ki sankhya `[0, 100]`, `-10⁴ <= Node.val <= 10⁴`

### Examples

| `p` | `q` | Output | Kyun |
|---|---|---|---|
| `[1,2,3]` | `[1,2,3]` | `true` | Structure aur values dono same |
| `[1,2]` | `[1,null,2]` | `false` | Structure alag (2 left me vs right me) |
| `[1,2,1]` | `[1,1,2]` | `false` | Structure same, par values alag |

```
Example 2:     p:   1        q:   1
                   /                \
                  2                  2        ← structure alag, to false
```

---

## 🧠 2. Core Intuition

### Do pointers, ek saath

Ek tree ko traverse karna to aata hai. Yahan **do trees pe ek hi saath** traverse karte hain: `p` aur `q` hamesha **same position** pe hote hain. Agar kisi bhi position pe mismatch mila, to answer `false`.

### 4-Check Template (Do-Tree Problems ka standard)

```
1. Dono NULL?        → true    (dono jagah khatam, abhi tak sab match)
2. Koi ek NULL?      → false   (structure alag)
3. Values alag?      → false
4. Recurse: left same  AND  right same
```

### ⚠️ Check ka order kyun matter karta hai

- **Check 1 pehle** hona zaroori hai. Agar Check 2 (`p == NULL || q == NULL`) pehle rakha, to **dono NULL** wali situation bhi `false` de dega, jabki wahan answer `true` hona chahiye.
- **Check 3 (`->val`) baad me** hona zaroori hai. Uske pehle yeh guarantee chahiye ki dono non-NULL hain, warna **NULL pointer dereference crash**.

---

## 💻 3. Solution 1: Recursive DFS (aapka version)

```cpp
class Solution {
public:
    bool isSameTree(TreeNode* p, TreeNode* q) {
        // 1. Dono NULL → same
        if (p == NULL && q == NULL) {
            return true;
        }

        // 2. Koi ek NULL (dono NULL upar check ho chuka) → same nahi
        if (p == NULL || q == NULL) {
            return false;
        }

        // 3. Dono non-NULL hain, values compare
        if (p->val != q->val) {
            return false;
        }

        // 4. Left aur Right subtrees dono same hone chahiye
        return isSameTree(p->left, q->left) && isSameTree(p->right, q->right);
    }
};
```

### Compact version

```cpp
bool isSameTree(TreeNode* p, TreeNode* q) {
    if (!p && !q) return true;
    if (!p || !q || p->val != q->val) return false;
    return isSameTree(p->left, q->left) && isSameTree(p->right, q->right);
}
```

> ✅ `||` ki **short-circuit evaluation** ki wajah se `p->val` tabhi evaluate hota hai jab `!p || !q` false ho, yani dono non-NULL hon. Isliye yeh safe hai.

### ⚡ Short-circuit ka bonus

Last line me `&&` hai. Agar **left subtree hi mismatch** nikla, to right subtree check hi nahi hota. Early exit milta hai.

---

## 🔍 4. Dry Runs

### Example 1: `p = [1,2,3]`, `q = [1,2,3]` → `true`

| Call | p | q | Result |
|---|---|---|---|
| `isSame(1, 1)` | 1 | 1 | values barabar, recurse |
| → `isSame(2, 2)` | 2 | 2 | values barabar |
| → → `isSame(NULL, NULL)` (2 ke left) | | | **true** |
| → → `isSame(NULL, NULL)` (2 ke right) | | | **true** |
| → `isSame(3, 3)` | 3 | 3 | values barabar, dono children NULL → **true** |
| `isSame(1,1)` | | | `true && true` = **true** ✅ |

### Example 2: `p = [1,2]`, `q = [1,null,2]` → `false`

| Call | p | q | Result |
|---|---|---|---|
| `isSame(1, 1)` | 1 | 1 | values barabar, recurse |
| → `isSame(2, NULL)` (left) | 2 | NULL | Check 2: ek NULL → **false** |
| right check | | | `&&` short-circuit, **skip** |
| `isSame(1,1)` | | | **false** ✅ |

### Example 3: `p = [1,2,1]`, `q = [1,1,2]` → `false`

`isSame(2, 1)` (left children) → Check 3: `2 != 1` → **false** ✅

---

## 💻 5. Solution 2: Iterative BFS (Queue of Pairs)

```cpp
bool isSameTree(TreeNode* p, TreeNode* q) {
    queue<pair<TreeNode*, TreeNode*>> qu;
    qu.push({p, q});
    while (!qu.empty()) {
        pair<TreeNode*, TreeNode*> cur = qu.front();
        qu.pop();
        TreeNode* a = cur.first;
        TreeNode* b = cur.second;
        if (!a && !b) continue;                 // dono NULL, aage badho
        if (!a || !b) return false;             // structure mismatch
        if (a->val != b->val) return false;     // value mismatch
        qu.push({a->left,  b->left});
        qu.push({a->right, b->right});
    }
    return true;
}
```

Yahan NULL pairs ko bhi queue me daalna theek hai, kyunki upar `continue` / `return false` unhe handle kar leta hai.

---

## 🚫 6. Galat Soch: Sirf Traversal Compare Karna

> *"Dono ka preorder/inorder nikalo aur compare karo."*

**Problem:** Agar NULL markers na daalo, to **alag structure ke trees ka bhi traversal same** aa sakta hai.

```
p:   1          q:   1
    /                  \
   2                    2

Preorder (bina NULL): p → [1,2]   q → [1,2]   ← same dikha, par trees alag!
```

**Fix:** Traversal me **NULL markers** bhi serialize karo (jaise `[1,2,#,#,#]`). Tab compare sahi hoga. Par direct simultaneous recursion zyada simple aur memory-efficient hai.

---

## ⏱️ 7. Complexity

| Approach | Time | Space |
|---|---|---|
| Recursive DFS | O(n) | O(h) call stack |
| Iterative BFS | O(n) | O(w) queue |

- `n` = chhote tree ke nodes tak (mismatch milte hi ruk jaata hai).
- Balanced: `h = log n`. Skewed: `h = n`.

---

## ⚠️ 8. Common Mistakes

1. **Check 1 aur Check 2 ka order ulta karna.** Dono NULL pe `false` aa jaata hai.
2. **NULL check ke bina `->val` access karna.** Crash.
3. **Sirf values compare karna, structure ignore karna.**
4. **`&&` ki jagah `||` likhna** last line me. Tab ek side same hone pe bhi `true` aa jaayega.
5. **Sirf preorder/inorder compare karna bina NULL markers ke.**
6. **Left ko right se compare karna** (`p->left` vs `q->right`). Wo Symmetric Tree (101) hai, yahan nahi.

---

## 🧪 9. Edge Cases Checklist

- [ ] Dono empty → `true`
- [ ] Ek empty, ek non-empty → `false`
- [ ] Dono single node, same value → `true`
- [ ] Dono single node, alag value → `false`
- [ ] Same shape, ek value alag (deep neeche)
- [ ] Same values, alag shape (left vs right child)
- [ ] Ek tree doosre ka prefix/subtree ho (par poora same nahi)

---

## 🔗 10. Related Problems

| Problem | Link in concept |
|---|---|
| **101. Symmetric Tree** | Same template, par **mirror** compare: `(a->left, b->right)` aur `(a->right, b->left)` |
| **572. Subtree of Another Tree** | Har node pe `isSameTree` call karo |
| **617. Merge Two Binary Trees** | Do trees pe ek saath recursion |
| **226. Invert Binary Tree** | Tree ka mirror banana |
| **951. Flip Equivalent Binary Trees** | Do trees compare, par kuch jagah swap allowed |
| **1367. Linked List in Binary Tree** | Do structures ko ek saath match karna |

### 🔁 Symmetric Tree (101) ka quick link

```cpp
bool isMirror(TreeNode* a, TreeNode* b) {
    if (!a && !b) return true;
    if (!a || !b || a->val != b->val) return false;
    return isMirror(a->left, b->right) && isMirror(a->right, b->left);   // cross compare
}
```

Bas same template, children ko **cross** compare karte hain.

---

## 🎯 11. Interview Talking Points

1. *"Dono trees pe ek saath recursion chalata hu."*
2. *"Dono NULL to true, ek NULL to false, value alag to false, warna left aur right dono check."*
3. *"Order important hai: pehle dono-NULL, phir ek-NULL, tab hi `->val` safe hai."*
4. *"Time O(n), space O(h). Mismatch pe early exit hota hai."*
5. *"Sirf traversal compare karna galat hai bina NULL markers ke, kyunki structure alag ho sakta hai."*

---

## ✅ 12. Final Revision Checklist

- [ ] 4-check template yaad hai (NULL-NULL, ek NULL, value, recurse)
- [ ] Check ka order aur uska reason samajh aaya
- [ ] `&&` short-circuit samajh aaya
- [ ] Symmetric Tree (101) se difference clear hai (cross compare)
- [ ] Dry run `[1,2]` vs `[1,null,2]` bina dekhe kar sakta hu

> **Memory hook:** 👯 *"Dono NULL true, ek NULL false, value alag false, warna left && right."*
