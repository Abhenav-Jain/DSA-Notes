# 🍃 LeetCode 111 — Minimum Depth of Binary Tree

> **Difficulty:** Easy  |  **Pattern:** Bottom-Up Recursion / BFS (Nearest Leaf)  |  **Time:** O(n)  |  **Space:** O(h)

---

## 🏷️ Tags

`Tree` · `Binary Tree` · `DFS` · `BFS` · `Recursion` · `Leaf Node` · `Classic Trap`

---

## ⚡ 30-Second Recap

| Cheez | Yaad rakhne wali baat |
|---|---|
| Kya nikalna hai | Root se **sabse paas wale LEAF** tak nodes ki ginti |
| 🔑 Leaf kya hai | Aisa node jiske **dono children NULL** hon |
| ⚠️ Trap | `1 + min(lh, rh)` seedha likhoge to **ek-child wale node pe galat** answer |
| Fix | Agar ek child NULL ho, to **doosre child ki taraf** jao |
| BFS | Pehla leaf milte hi `return depth`, **early exit** |
| Space | O(h) DFS, O(w) BFS |

**One-liner:** *"Min depth = nearest **leaf**, nearest NULL nahi."*

---

## 📝 1. Problem Statement

Binary tree ka `root` diya hai. Uski **minimum depth** return karo.

Minimum depth = root se **sabse paas wale leaf node** tak ke raaste me **nodes ki sankhya**.

**Leaf** = aisa node jiska koi child nahi hai.

**Constraints:** nodes ki sankhya `[0, 10⁵]`, `-1000 <= Node.val <= 1000`

### Examples

```
Example 1: answer = 2          Example 2: answer = 5

        3                      2
       / \                      \
      9   20                     3
         /  \                     \
        15   7                     4
                                     \
                                      5
                                       \
                                        6
```

| Input (level order) | Output |
|---|---|
| `[3,9,20,null,null,15,7]` | 2 (3 → 9) |
| `[2,null,3,null,4,null,5,null,6]` | 5 |
| `[]` | 0 |

---

## 🧠 2. Core Intuition

### Max Depth se tulna

| | Max Depth (104) | Min Depth (111) |
|---|---|---|
| Recurrence | `1 + max(lh, rh)` | `1 + min(lh, rh)` **(par trap hai!)** |
| NULL ka depth | 0 | 0 |
| Dikkat | Koi nahi | `NULL` ko "leaf" samajh baithte hain |

### 🚨 Trap: Ek child NULL ho to kya hota hai?

Tree: `[1, 2]` (root 1, sirf left child 2)

```
    1
   /
  2
```

Seedha formula lagao: `1 + min(minDepth(2), minDepth(NULL))` = `1 + min(1, 0)` = **1** ❌

Par root `1` leaf nahi hai (uska ek child hai). Sahi answer **2** hai (1 → 2).

**Wajah:** `NULL` ki depth 0 hai, par `NULL` **leaf nahi hai**, wo to bas "kuch nahi" hai. Isliye us side ko ignore karna hai.

### ✅ Fix

```
agar left NULL hai   → sirf right me jao:  1 + minDepth(right)
agar right NULL hai  → sirf left me jao:   1 + minDepth(left)
warna (dono hain)    → 1 + min(left, right)
```

---

## 💻 3. Solution 1: Recursive DFS (aapka version)

```cpp
class Solution {
public:
    int minDepth(TreeNode* root) {
        if (root == NULL) {
            return 0;
        }
        int lh = minDepth(root->left);
        int rh = minDepth(root->right);
        if (root->left == NULL || root->right == NULL) {
            return 1 + max(lh, rh);        // ek side NULL (ya leaf): jo side real hai wahi lo
        }
        return 1 + min(lh, rh);            // dono children hain: chhota wala lo
    }
};
```

### 🧠 Aapke code ki clever baat

Agar ek child NULL hai to uski depth `0` aati hai. Tab `max(lh, rh)` automatically **non-NULL side** ki depth utha leta hai. Aur agar **dono NULL** hain (leaf), to `max(0, 0) = 0` aur answer `1 + 0 = 1` ✅. Yani ek hi branch me **leaf aur ek-child**, dono cases handle ho gaye.

### Dusra clean style (explicit)

```cpp
int minDepth(TreeNode* root) {
    if (!root) return 0;
    if (!root->left)  return 1 + minDepth(root->right);   // sirf right hai
    if (!root->right) return 1 + minDepth(root->left);    // sirf left hai
    return 1 + min(minDepth(root->left), minDepth(root->right));
}
```

Leaf pe: `!root->left` true, `minDepth(NULL) = 0`, answer `1` ✅.

---

## 🔍 4. Dry Runs

### Example 1: `[3,9,20,null,null,15,7]` → `2`

| Call | lh | rh | Branch | Return |
|---|---|---|---|---|
| `minDepth(9)` | 0 | 0 | leaf (ek NULL) → `1 + max(0,0)` | **1** |
| `minDepth(15)` | 0 | 0 | leaf | **1** |
| `minDepth(7)` | 0 | 0 | leaf | **1** |
| `minDepth(20)` | 1 | 1 | dono children → `1 + min(1,1)` | **2** |
| `minDepth(3)` | 1 | 2 | dono children → `1 + min(1,2)` | **2** ✅ |

### Example 2: chain `2 → 3 → 4 → 5 → 6` (sirf right children) → `5`

| Call | lh | rh | Branch | Return |
|---|---|---|---|---|
| `minDepth(6)` | 0 | 0 | leaf → `1 + max(0,0)` | 1 |
| `minDepth(5)` | 0 | 1 | left NULL → `1 + max(0,1)` | 2 |
| `minDepth(4)` | 0 | 2 | left NULL → `1 + max(0,2)` | 3 |
| `minDepth(3)` | 0 | 3 | left NULL → `1 + max(0,3)` | 4 |
| `minDepth(2)` | 0 | 4 | left NULL → `1 + max(0,4)` | **5** ✅ |

(Galat formula `1 + min` yahan 1 deta, jabki sahi 5 hai.)

---

## 💻 5. Solution 2: BFS (Pehla Leaf = Answer)

Level order me jo **pehla leaf** milega, wahi sabse paas wala hoga. Turant return kar do.

```cpp
int minDepth(TreeNode* root) {
    if (!root) return 0;
    queue<TreeNode*> q;
    q.push(root);
    int depth = 1;
    while (!q.empty()) {
        int size = q.size();                          // level snapshot
        for (int i = 0; i < size; i++) {
            TreeNode* node = q.front();
            q.pop();
            if (!node->left && !node->right) {
                return depth;                         // 🎯 pehla leaf mil gaya
            }
            if (node->left)  q.push(node->left);
            if (node->right) q.push(node->right);
        }
        depth++;
    }
    return depth;
}
```

### DFS vs BFS: kab kaun?

| | DFS | BFS |
|---|---|---|
| Visit | Saare nodes (worst case hamesha) | Pehla leaf milte hi ruk jaata hai |
| Time | O(n) | O(n) worst, par aksar **kam** (early exit) |
| Space | O(h) | O(w) |
| Skewed deep tree | Recursion depth `n` tak ja sakti hai | Safe |
| Wide, shallow tree | Sab visit karta hai | **Fast** |

> ⚠️ Is problem me `n` `10⁵` tak ja sakta hai. Poora skewed tree hone pe recursion depth bhi `10⁵` hoga, jo kuch environments me **stack overflow** kara sakta hai. LeetCode pe aam taur par chal jaata hai, par **BFS zyada safe** hai.

---

## ⏱️ 6. Complexity

| Approach | Time | Space |
|---|---|---|
| Recursive DFS | O(n) | O(h) call stack |
| BFS | O(n) worst, aksar kam | O(w) queue |

- Balanced: `h = log n`. Skewed: `h = n`.

---

## ⚠️ 7. Common Mistakes

1. **Seedha `1 + min(lh, rh)` likhna.** Ek child wale node pe `NULL` ko leaf maan leta hai.
2. **`NULL` ko leaf samajhna.** Leaf wo hai jiske **dono children NULL** hon.
3. **Max Depth ka code copy karke `max` ko `min` kar dena.** Yehi sabse common galti.
4. **Leaf case bhoolna** (dono NULL). Aapke code me `max(0,0)=0` se handle ho gaya, par explicit version me alag se socho.
5. **BFS me `depth++` ko galat jagah rakhna.** Level ke poore hone ke baad hi badhao.

---

## 🧪 8. Edge Cases Checklist

- [ ] Empty tree → **0**
- [ ] Single node → **1**
- [ ] Root ke sirf left child (`[1,2]`) → **2**
- [ ] Root ke sirf right child (`[1,null,2]`) → **2**
- [ ] Skewed tree → **n**
- [ ] Ek side gehri, doosri side me shallow leaf
- [ ] Complete tree → `log₂(n+1)`

---

## 🔗 9. Related Problems

| Problem | Link in concept |
|---|---|
| **104. Maximum Depth of Binary Tree** | Bhai problem, koi trap nahi |
| **112. Path Sum** | Root-to-**leaf** path, wahi "leaf" ka concept |
| **257. Binary Tree Paths** | Root-to-leaf saare paths |
| **110. Balanced Binary Tree** | Height compare karna |
| **543. Diameter of Binary Tree** | Height + global max |
| **102. Level Order Traversal** | BFS template jo yahan bhi use hua |

> 💡 **Leaf wale concept** (112, 257, 111) me hamesha yaad rakho: **leaf = dono children NULL**.

---

## 🎯 10. Interview Talking Points

1. *"Max depth jaisa dikhta hai, par trap hai: NULL leaf nahi hota."*
2. *"Agar ek child NULL ho to doosre child ki taraf jaata hu, dono hon to min lega."*
3. *"BFS me pehla leaf milte hi return kar sakta hu, to average case me tez hai."*
4. *"Time O(n), space O(h) DFS me aur O(w) BFS me."*
5. *"Skewed tree me deep recursion se bachne ke liye BFS ya iterative approach safe hai."*

---

## ✅ 11. Final Revision Checklist

- [ ] Leaf = dono children NULL
- [ ] `1 + min(lh, rh)` ki trap aur example `[1,2]` yaad hai
- [ ] Ek child NULL ho to doosri taraf jao
- [ ] BFS ka early exit samajh aaya
- [ ] Max Depth (104) se difference clear hai
- [ ] Dry run `[2,null,3,null,4,null,5,null,6]` → 5 bina dekhe kar sakta hu

> **Memory hook:** 🍃 *"Nearest leaf, nearest NULL nahi. Ek side NULL to doosri side pakdo."*
