# 🧬 LeetCode 572 — Subtree of Another Tree

> **Difficulty:** Easy  |  **Pattern:** Tree Traversal + Same Tree Subroutine  |  **Time:** O(m·n) worst  |  **Space:** O(h)

---

## 🏷️ Tags

`Tree` · `Binary Tree` · `DFS` · `Recursion` · `Tree Matching` · `String Matching (alt)` · `Hashing (alt)`

---

## ⚡ 30-Second Recap

| Cheez | Yaad rakhne wali baat |
|---|---|
| Subtree ki definition | Koi node **+ uske SAARE descendants** (adhura hissa nahi) |
| Idea | `root` ke **har node** pe check karo: "yahan se `subRoot` ke barabar tree shuru hota hai?" |
| Check kaise | **Same Tree (LC 100)** ko subroutine ki tarah use karo |
| Recurrence | `sameTree(root, sub)` **OR** `isSubtree(left, sub)` **OR** `isSubtree(right, sub)` |
| Base case | `root == NULL` → `false` |
| Time | O(m·n) worst, `m = |root|`, `n = |subRoot|` |
| Alternative | Serialize (NULL markers + delimiter) + substring search |

**One-liner:** *"Har node pe pucho: yahan se subRoot jaisa tree shuru hota hai? Haan to true, nahi to left aur right me dhundo."*

---

## 📝 1. Problem Statement

Do binary trees `root` aur `subRoot` diye hain. Return `true` agar `root` me aisa subtree hai jo `subRoot` ke **structure aur node values** me bilkul same ho.

`root` ka **subtree** = `root` ka koi bhi node aur **uske saare descendants**. `root` khud bhi apna subtree maana jaata hai.

**Constraints:** `root` me `[1, 2000]` nodes, `subRoot` me `[1, 1000]` nodes, `-10⁴ <= val <= 10⁴`

### Examples

```
Example 1: true                         Example 2: false

root:      3       subRoot:  4          root:      3       subRoot:  4
          / \                / \                  / \                / \
         4   5              1   2                4   5              1   2
        / \                                      / \
       1   2                                    1   2
                                                   /
                                                  0
```

| root | subRoot | Output | Kyun |
|---|---|---|---|
| `[3,4,5,1,2]` | `[4,1,2]` | `true` | Node `4` ke neeche bilkul same tree |
| `[3,4,5,1,2,null,null,null,null,0]` | `[4,1,2]` | `false` | Node `2` ke neeche extra child `0` hai |

---

## 🧠 2. Core Intuition

### "Subtree" ka matlab (important)

Subtree me node ke **saare descendants** hote hain. Example 2 me node `4` ke neeche `1`, `2` to hain, par `2` ke neeche ek **extra `0`** bhi hai. Isliye wo `[4,1,2]` ke **barabar nahi**.

### Divide into 2 sawal

1. **Kya `root` pe hi match hai?** → `sameTree(root, subRoot)`
2. **Nahi, to kya `root` ke left ya right subtree me match hai?** → `isSubtree(left)` **OR** `isSubtree(right)`

```
isSubtree(root, sub):
    if root is null: return false
    if sameTree(root, sub): return true
    return isSubtree(root.left, sub) || isSubtree(root.right, sub)
```

### Same Tree (LC 100) ka kaam

`sameTree(p, q)` check karta hai ki **p aur q se shuru hone wale poore trees bilkul same** hain ya nahi (4-check template: dono NULL → true, ek NULL → false, value alag → false, recurse left && right).

---

## 💻 3. Solution 1: Brute Force Recursion (aapka version)

```cpp
class Solution {
public:
    bool sameTree(TreeNode* p, TreeNode* q) {
        if (p == NULL && q == NULL) {
            return true;
        }
        if (p == NULL || q == NULL) {
            return false;
        }
        if (p->val != q->val) {
            return false;
        }
        return sameTree(p->left, q->left) && sameTree(p->right, q->right);
    }
    bool isSubtree(TreeNode* root, TreeNode* subRoot) {
        if (root == NULL) {
            return false;                          // root khatam, match nahi mila
        }
        if (sameTree(root, subRoot)) {
            return true;                           // is node se match
        }
        return isSubtree(root->left, subRoot)      // left me dhundo
            || isSubtree(root->right, subRoot);    // right me dhundo
    }
};
```

### ✅ Kya achha hai

- Pure functions, koi shared state nahi.
- `||` short-circuit se match milte hi ruk jaata hai.
- `sameTree` mismatch milte hi return kar deta hai (early exit), to practically kaafi tez.

---

## 🔍 4. Dry Runs

### Example 1: `root = [3,4,5,1,2]`, `subRoot = [4,1,2]` → `true`

| Call | Action | Result |
|---|---|---|
| `isSubtree(3)` | `sameTree(3, 4)`: `3 != 4` | false → left me jao |
| `isSubtree(4)` | `sameTree(4, 4)`: values barabar | aage |
| → `sameTree(1, 1)` | barabar, dono ke children NULL | true |
| → `sameTree(2, 2)` | barabar, dono ke children NULL | true |
| `sameTree(4,4)` | `true && true` | **true** ✅ |

### Example 2: `root = [3,4,5,1,2,null,null,null,null,0]`, `subRoot = [4,1,2]` → `false`

| Call | Action | Result |
|---|---|---|
| `isSubtree(3)` | `sameTree(3, 4)`: `3 != 4` | false, left jao |
| `isSubtree(4)` | `sameTree(4, 4)` → left `(1,1)` true, right `(2,2)` | aage |
| → `sameTree(2, 2)` | values barabar, phir `sameTree(0, NULL)`: **ek NULL** | **false** |
| `sameTree(4,4)` | `true && false` | false |
| `isSubtree(1)`, `isSubtree(2)`, `isSubtree(0)` | Har jagah `sameTree(·, 4)` fail | false |
| `isSubtree(5)` | `sameTree(5, 4)`: `5 != 4` | false |
| Final | | **false** ✅ |

---

## ⏱️ 5. Complexity

| | Value | Reason |
|---|---|---|
| **Time** | O(m · n) worst | `root` ke `m` nodes me se har ek pe `sameTree` max `n` steps (subRoot ka size) |
| **Space** | O(h) | Recursion stack (`h` = root ki height; `sameTree` ki depth subRoot ki height tak) |

- **Worst case example:** `root` ek lamba chain jisme saari values same (jaise sab `1`), aur `subRoot` bhi chain jisme sab `1` par aakhir me alag. Har node pe `sameTree` lamba chalta hai.
- **Practical:** `sameTree` pehle mismatch pe ruk jaata hai, to average case bahut behtar hota hai.

---

## 🚀 6. Alternative: Serialization + Substring Search

Idea: Dono trees ko **string** me convert karo (NULL markers ke saath), phir check karo ki `subRoot` ki string `root` ki string ke andar **substring** hai ya nahi.

```cpp
class Solution {
public:
    void serialize(TreeNode* node, string& s) {
        if (!node) {
            s += ",#";                        // NULL marker
            return;
        }
        s += "," + to_string(node->val);      // delimiter + value
        serialize(node->left, s);
        serialize(node->right, s);
    }
    bool isSubtree(TreeNode* root, TreeNode* subRoot) {
        string a, b;
        serialize(root, a);
        serialize(subRoot, b);
        return a.find(b) != string::npos;
    }
};
```

### ⚠️ Dono cheezein zaruri hain

| Cheez | Na ho to kya galat hoga |
|---|---|
| **NULL marker (`#`)** | Alag structure ke trees ki string same ho sakti hai (Same Tree notes ka `[1,2]` vs `[1,null,2]` wala issue) |
| **Delimiter (`,`) value se pehle** | `"12"` ke andar `"2"` mil jaata hai, jabki dono alag values hain |

### Complexity

- Serialization: O(m + n)
- `string::find`: naive me O(m·n) worst, **KMP** se O(m + n).

> 💡 Interview me pehle brute force (Solution 1) batao. Phir bolo *"serialization + KMP se O(m+n) bhi ho sakta hai"*. Isse depth dikhti hai.

### (Bonus) Aur optimizations

- **Height filter:** `sameTree` tabhi call karo jab `root` ke us node ki height `subRoot` ki height ke barabar ho. Bahut saari faltu calls bach jaati hain.
- **Tree hashing (Merkle hash):** Har subtree ka hash nikalo, `subRoot` ke hash se compare karo. O(m + n).

---

## ⚠️ 7. Common Mistakes

1. **Sirf `root` pe `sameTree` check karna** aur left/right me recurse na karna. Subtree kahin bhi ho sakta hai.
2. **`||` ki jagah `&&` likhna** recursive part me.
3. **Subtree ko "path" ya "subset" samajhna.** Subtree me node ke **saare descendants** hote hain.
4. **Base case `root == NULL` pe `true` return karna.** `subRoot` kam se kam 1 node ka hai, to NULL me wo nahi mil sakta, `false`.
5. **Serialization me NULL markers ya delimiter bhool jaana.**
6. **`sameTree` me NULL check ka order galat** (Same Tree notes dekho).

> ℹ️ **Convention:** Agar `subRoot` NULL ho (is problem me nahi hota), to usually `true` maana jaata hai (khaali tree har tree ka subtree hai). Aapka code us case me `false` dega, par constraints me `subRoot` ≥ 1 node hai, to koi problem nahi.

---

## 🧪 8. Edge Cases Checklist

- [ ] `root` aur `subRoot` dono single node, same value → `true`
- [ ] Dono single node, alag value → `false`
- [ ] `subRoot == root` (poora tree) → `true`
- [ ] `subRoot` leaf node ho aur root me kahin wo value ho → `true`
- [ ] `subRoot` ka structure match par neeche extra node (Example 2) → `false`
- [ ] Values same par structure alag
- [ ] `subRoot` `root` se bada ho → `false`
- [ ] Duplicate values wale trees (`[1,1,1]` jaise)

---

## 🔗 9. Related Problems

| Problem | Link in concept |
|---|---|
| **100. Same Tree** | Yahan ka subroutine |
| **101. Symmetric Tree** | Same template, cross compare |
| **226. Invert Binary Tree** | Tree transform |
| **652. Find Duplicate Subtrees** | Serialization se subtrees ko identify karna |
| **951. Flip Equivalent Binary Trees** | Do trees compare, swap allowed |
| **1367. Linked List in Binary Tree** | "Structure ko tree me dhundo" wala same idea |

---

## 🎯 10. Interview Talking Points

1. *"Subtree ka matlab node aur uske saare descendants."*
2. *"`root` ke har node pe Same Tree check karta hu, aur nahi mila to left aur right subtrees me dhundta hu."*
3. *"Time O(m·n) worst case, space O(h)."*
4. *"Optimize: serialization (NULL markers + delimiter) aur KMP se O(m+n), ya tree hashing se."*
5. *"Same Tree ko alag helper bana ke reuse kiya, isse code clean aur testable hai."*

---

## ✅ 11. Final Revision Checklist

- [ ] Subtree = node + **saare** descendants
- [ ] `isSubtree` = `sameTree` OR left OR right
- [ ] Base case `root == NULL → false`
- [ ] `sameTree` ka 4-check template yaad hai
- [ ] Serialization me NULL marker aur delimiter dono kyun chahiye
- [ ] Dry run Example 2 (`false` kyun) bina dekhe kar sakta hu

> **Memory hook:** 🧬 *"Har node pe Same Tree try karo. Nahi mila to left, phir right."*
