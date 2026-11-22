# 🦋 LeetCode 101 — Symmetric Tree

> **Difficulty:** Easy  |  **Pattern:** Two-Tree Recursion with Cross Comparison (Mirror Check)  |  **Time:** O(n)  |  **Space:** O(h)

---

## 🏷️ Tags

`Tree` · `Binary Tree` · `DFS` · `BFS` · `Recursion` · `Mirror` · `Two-Tree Template`

---

## ⚡ 30-Second Recap

| Cheez | Yaad rakhne wali baat |
|---|---|
| Symmetric ka matlab | Root ke left aur right subtrees ek dusre ke **mirror image** hain |
| Technique | Helper `check(l, r)` jo do subtrees ko compare kare |
| 4-check template | Dono NULL → true, ek NULL → false, value alag → false, recurse |
| 🔑 Fark Same Tree se | **Cross compare**: `l->left` vs `r->right`, aur `l->right` vs `r->left` |
| Shortcut | `check(root, root)` bhi chalta hai |
| Space | O(h) recursion stack |

**One-liner:** *"Same Tree jaisa hi code, bas children ko cross karke compare karo."*

---

## 📝 1. Problem Statement

Binary tree ka `root` diya hai. Check karo ki woh **khud ka mirror** hai ya nahi (yani center ke around symmetric).

**Constraints:** nodes ki sankhya `[1, 1000]`, `-100 <= Node.val <= 100`

### Examples

```
Example 1: true               Example 2: false

        1                             1
       / \                           / \
      2   2                         2   2
     / \ / \                         \   \
    3  4 4  3                         3   3
```

| Input (level order) | Output |
|---|---|
| `[1,2,2,3,4,4,3]` | `true` |
| `[1,2,2,null,3,null,3]` | `false` |

---

## 🧠 2. Core Intuition

### Mirror ka matlab

Tree ke beech me ek aaina rakho. Left side jaisi dikhti hai, right side usi ka **ulta (mirror)** hona chahiye.

- Left subtree ka **left child** ↔ Right subtree ka **right child**
- Left subtree ka **right child** ↔ Right subtree ka **left child**

### Same Tree (LC 100) se connection

| | Same Tree (100) | Symmetric Tree (101) |
|---|---|---|
| Compare | `p->left` vs `q->left` | `l->left` vs `r->right` |
| | `p->right` vs `q->right` | `l->right` vs `r->left` |
| Type | **Parallel** compare | **Cross** compare |

Baaki sab (NULL checks, value check) **bilkul same** hain.

### 4-Check Template (wahi purana)

```
1. Dono NULL?        → true
2. Koi ek NULL?      → false
3. Values alag?      → false
4. Recurse (CROSS):  check(l.left, r.right)  AND  check(l.right, r.left)
```

---

## 💻 3. Solution 1: Recursive DFS (aapka version)

```cpp
class Solution {
public:
    bool check(TreeNode* l, TreeNode* r) {
        if (l == NULL && r == NULL) {
            return true;                        // dono khatam, sab match
        }
        if (l == NULL || r == NULL) {
            return false;                       // ek hi khatam, structure alag
        }
        if (l->val != r->val) {
            return false;                       // value alag
        }
        return check(l->left, r->right)         // bahar wale jodi
            && check(l->right, r->left);        // andar wale jodi
    }
    bool isSymmetric(TreeNode* root) {
        TreeNode* l = root->left;
        TreeNode* r = root->right;
        return check(l, r);
    }
};
```

### 📌 Robustness note

Aapka `isSymmetric` `root->left` seedha access karta hai. Is problem me `n >= 1` guaranteed hai, to `root` kabhi NULL nahi hoga aur code safe hai. Phir bhi do chhote safe tareeke:

```cpp
// Option 1: NULL guard
bool isSymmetric(TreeNode* root) {
    if (!root) return true;
    return check(root->left, root->right);
}

// Option 2: ek-line trick, root ko khud se compare karo
bool isSymmetric(TreeNode* root) {
    return check(root, root);
}
```

**Option 2 kaise kaam karta hai?** Tree ko apne mirror se compare kar rahe hain. Agar `check(root, root)` true aaye, to tree symmetric hai. Yeh NULL root ko bhi handle kar leta hai.

---

## 🔍 4. Dry Runs

### Example 1: `[1,2,2,3,4,4,3]` → `true`

```
        1
       / \
      2a  2b
     / \ / \
    3a 4a 4b 3b
```

| Call | l | r | Action |
|---|---|---|---|
| `check(2a, 2b)` | 2 | 2 | values barabar, cross recurse |
| → `check(3a, 3b)` | 3 | 3 | `l->left` vs `r->right` ✔ (children NULL, NULL → true) |
| → `check(4a, 4b)` | 4 | 4 | `l->right` vs `r->left` ✔ (children NULL, NULL → true) |
| `check(2a, 2b)` | | | `true && true` = **true** ✅ |

### Example 2: `[1,2,2,null,3,null,3]` → `false`

```
        1
       / \
      2a  2b
       \   \
        3   3
```

| Call | l | r | Action |
|---|---|---|---|
| `check(2a, 2b)` | 2 | 2 | values barabar, cross recurse |
| → `check(2a->left, 2b->right)` | **NULL** | **3** | Check 2: ek NULL → **false** |
| `&&` short-circuit | | | right half skip |
| result | | | **false** ✅ |

> 💡 Dono 2 ke neeche `3` **right** side pe hai. Mirror hota to ek ka `3` left me aur doosre ka right me hota.

---

## 💻 5. Solution 2: Iterative BFS (Queue me Pairs)

```cpp
bool isSymmetric(TreeNode* root) {
    if (!root) return true;
    queue<TreeNode*> q;
    q.push(root->left);
    q.push(root->right);
    while (!q.empty()) {
        TreeNode* a = q.front(); q.pop();
        TreeNode* b = q.front(); q.pop();
        if (!a && !b) continue;                       // dono NULL, aage badho
        if (!a || !b || a->val != b->val) return false;
        q.push(a->left);  q.push(b->right);           // bahar wali jodi
        q.push(a->right); q.push(b->left);            // andar wali jodi
    }
    return true;
}
```

**Idea:** Queue me hamesha **do-do karke** nodes daalo (ek jodi). Pop karte waqt dono ek saath nikalo aur compare karo.

(Iterative DFS ke liye queue ki jagah stack use karo, logic same rehta hai.)

---

## 🚫 6. Galat Soch: Inorder Palindrome

> *"Inorder traversal nikalo aur dekho ki palindrome hai ya nahi."*

**Problem:** Kuch non-symmetric trees ka bhi inorder palindrome aa jaata hai.

```
        1
       / \
      2   2
     /   /
    2   2

Inorder: [2, 2, 1, 2, 2]  ← palindrome hai, par tree symmetric NAHI hai
```

Dono taraf ka `2` **left child** pe hai. Mirror me ek ka child right pe hona chahiye tha. Isliye hamesha **simultaneous cross comparison** use karo.

---

## ⏱️ 7. Complexity

| Approach | Time | Space |
|---|---|---|
| Recursive DFS | O(n) | O(h) call stack |
| Iterative BFS | O(n) | O(w) queue |

- Har node ek baar visit hota hai. Mismatch milte hi early exit.
- Balanced: `h = log n`. Skewed: `h = n`.

---

## ⚠️ 8. Common Mistakes

1. **Cross ki jagah parallel compare karna** (`l->left` vs `r->left`). Wo Same Tree (100) hai.
2. **Sirf values compare karna, structure ignore karna.**
3. **Inorder palindrome se check karna** (upar counter-example).
4. **Check 1 aur Check 2 ka order ulta karna.** Dono NULL pe `false` aa jaata hai.
5. **`root` NULL hone pe `root->left` access karna.** Is problem me guaranteed safe hai, par general code me guard lagao.
6. **`&&` ki jagah `||` likhna.**

---

## 🧪 9. Edge Cases Checklist

- [ ] Single node → `true`
- [ ] Root ke sirf ek child → `false`
- [ ] Do level, dono children equal → `true`
- [ ] Do level, dono children alag values → `false`
- [ ] Structure symmetric, par values alag (deep neeche)
- [ ] Values same, par structure asymmetric (upar wala counter-example)
- [ ] Skewed tree → `false`

---

## 🔗 10. Related Problems

| Problem | Link in concept |
|---|---|
| **100. Same Tree** | Same template, **parallel** compare |
| **226. Invert Binary Tree** | Mirror banana. Symmetric ⇔ `invert(left)` same as `right` |
| **951. Flip Equivalent Binary Trees** | Kuch nodes pe swap allowed, phir equality |
| **572. Subtree of Another Tree** | Har node pe Same Tree check |
| **617. Merge Two Binary Trees** | Do trees pe ek saath recursion |
| **104. Maximum Depth of Binary Tree** | Bottom-up recursion basics |

### 🧩 Conceptual link: Invert + Same Tree

```
Tree symmetric  ⇔  isSameTree( invertTree(root->left), root->right )
```

Yeh idea samajhne ke liye achha hai, par practically tree ko modify karna padta hai, isliye `check(l, r)` wala direct approach better hai.

---

## 🎯 11. Interview Talking Points

1. *"Symmetric ka matlab left aur right subtrees ek dusre ke mirror hain."*
2. *"Helper `check(l, r)` banata hu jo do subtrees ko **cross** compare karta hai."*
3. *"Same Tree jaisa hi hai, bas `l->left` ko `r->right` se aur `l->right` ko `r->left` se compare karta hu."*
4. *"Time O(n), space O(h). Mismatch pe early exit."*
5. *"Inorder palindrome galat hai, kyunki structure alag hone pe bhi palindrome aa sakta hai."*

---

## ✅ 12. Final Revision Checklist

- [ ] 4-check template yaad hai
- [ ] **Cross** compare (`l->left` vs `r->right`) samajh aaya
- [ ] Same Tree (100) se difference clear hai
- [ ] `check(root, root)` trick pata hai
- [ ] Inorder palindrome kyun galat hai, example yaad hai
- [ ] Dry run `[1,2,2,null,3,null,3]` → `false` bina dekhe kar sakta hu

> **Memory hook:** 🦋 *"Same Tree + Cross: bahar wale bahar se, andar wale andar se."*
