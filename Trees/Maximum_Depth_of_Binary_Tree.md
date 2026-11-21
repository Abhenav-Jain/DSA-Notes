# 🌲 LeetCode 104 — Maximum Depth of Binary Tree

> **Difficulty:** Easy  |  **Pattern:** Bottom-Up Recursion (Postorder Thinking)  |  **Time:** O(n)  |  **Space:** O(h)

---

## 🏷️ Tags

`Tree` · `Binary Tree` · `DFS` · `BFS` · `Recursion` · `Bottom-Up` · `Foundation Problem`

---

## ⚡ 30-Second Recap

| Cheez | Yaad rakhne wali baat |
|---|---|
| Depth ki definition | Root se sabse door leaf tak **nodes ki ginti** (edges nahi) |
| Base case | `root == NULL` → **0** |
| Recurrence | `1 + max(depth(left), depth(right))` |
| Approach | **Bottom-up**: pehle children ka answer lo, phir apna banao |
| BFS alternative | Levels ginti karo |
| Space | O(h) recursion stack |

**One-liner:** *"Mera depth = 1 + (left aur right me se jo bada ho)."*

---

## 📝 1. Problem Statement

Binary tree ka `root` diya hai. Uski **maximum depth** return karo.

Maximum depth = root se sabse door wale leaf node tak ke raaste me **nodes ki sankhya**.

**Constraints:** nodes ki sankhya `[0, 10⁴]`, `-100 <= Node.val <= 100`

### Examples

| Input (level order) | Output |
|---|---|
| `[3,9,20,null,null,15,7]` | 3 |
| `[1,null,2]` | 2 |
| `[]` | 0 |

---

## 🧠 2. Core Intuition

### Recursive soch (Trust the recursion)

Maan lo mere left aur right subtree ki depth mujhe koi bata de. To meri depth kya hogi?

```
meri depth = 1 (main khud) + max(left ki depth, right ki depth)
```

Bas! Baaki recursion sambhal lega.

### Base case

`NULL` node ki depth **0** hai (koi node hi nahi). Isse leaf node ki depth automatically `1 + max(0, 0) = 1` aati hai.

```
        3              depth(3) = 1 + max(1, 2) = 3
       / \
      9   20           depth(9)  = 1 + max(0,0) = 1
         /  \          depth(20) = 1 + max(1,1) = 2
        15   7         depth(15) = depth(7) = 1
```

### 🔑 Pattern: Bottom-Up (Postorder) thinking

Parent ka answer **children ke answers se banta hai**. Isliye yeh postorder style hai: pehle left, phir right, phir apna kaam.

```
ans(node) = combine( ans(left), ans(right) ) + node ka contribution
```

Yehi template height, diameter, balanced tree, max path sum jaise problems me chalta hai.

---

## 💻 3. Solution 1: Recursive DFS (aapka version)

```cpp
class Solution {
public:
    int maxDepth(TreeNode* root) {
        if (root == NULL) {
            return 0;                       // khaali tree ki depth 0
        }
        int lh = maxDepth(root->left);      // left subtree ki depth
        int rh = maxDepth(root->right);     // right subtree ki depth
        return 1 + max(lh, rh);             // main + bada wala
    }
};
```

### ✅ Is code me kya achha hai

- **Pure function** hai: koi shared member variable nahi, to same object pe baar baar call karna safe hai.
- Base case aur recurrence dono bilkul clean.

### Ek-line version

```cpp
int maxDepth(TreeNode* root) {
    return root ? 1 + max(maxDepth(root->left), maxDepth(root->right)) : 0;
}
```

### 🔍 Dry Run (tree: `3 → (9), (20 → (15, 7))`)

| Call | Left result | Right result | Return |
|---|---|---|---|
| `maxDepth(NULL)` (9 ke left/right) | | | 0 |
| `maxDepth(9)` | 0 | 0 | `1 + 0 = 1` |
| `maxDepth(15)` | 0 | 0 | 1 |
| `maxDepth(7)` | 0 | 0 | 1 |
| `maxDepth(20)` | 1 (15) | 1 (7) | `1 + 1 = 2` |
| `maxDepth(3)` | 1 (9) | 2 (20) | `1 + 2 = 3` |

Answer = **3** ✅

---

## 💻 4. Solution 2: BFS (Levels ginti karo)

Depth = tree me **levels ki sankhya**. Level order traversal (LC 102) ka template use karo aur har level ke baad counter badhao.

```cpp
int maxDepth(TreeNode* root) {
    if (root == NULL) return 0;
    queue<TreeNode*> q;
    q.push(root);
    int depth = 0;
    while (!q.empty()) {
        int size = q.size();                 // level snapshot
        for (int i = 0; i < size; i++) {
            TreeNode* node = q.front();
            q.pop();
            if (node->left)  q.push(node->left);
            if (node->right) q.push(node->right);
        }
        depth++;                             // ek level poora hua
    }
    return depth;
}
```

---

## 💻 5. Solution 3: Iterative DFS (Stack me depth ke saath)

Har node ke saath uski depth store karo.

```cpp
int maxDepth(TreeNode* root) {
    if (root == NULL) return 0;
    stack<pair<TreeNode*, int>> st;
    st.push({root, 1});
    int best = 0;
    while (!st.empty()) {
        pair<TreeNode*, int> cur = st.top();
        st.pop();
        best = max(best, cur.second);                    // depth update
        if (cur.first->left)  st.push({cur.first->left,  cur.second + 1});
        if (cur.first->right) st.push({cur.first->right, cur.second + 1});
    }
    return best;
}
```

---

## ⏱️ 6. Complexity

| Approach | Time | Space |
|---|---|---|
| Recursive DFS | O(n) | O(h) call stack |
| BFS | O(n) | O(w) queue (w = max width) |
| Iterative DFS | O(n) | O(h) stack |

- Balanced tree: `h = log n`
- Skewed tree: `h = n` (is problem me `n ≤ 10⁴`, to recursion depth safe hai)

---

## 🆚 7. Depth vs Height (Terminology)

| Term | Matlab |
|---|---|
| **Depth of a node** | Root se **us node tak** ka distance |
| **Height of a node** | Us node se **sabse door leaf tak** ka distance |
| **Max depth of tree** | = Height of root (is problem me **nodes** count hote hain) |

> ⚠️ LeetCode yahan **nodes ginta hai**. Kuch textbooks **edges** ginte hain, to wahan answer 1 kam hota hai. Single node tree: LeetCode me `1`, edges me `0`.

---

## ⚠️ 8. Common Mistakes

1. **Base case me `return 1` likhna.** Null ke liye 0 hi hona chahiye, warna answer 1 zyada aata hai.
2. **`+1` bhool jaana.** Tab answer hamesha 0 aayega.
3. **`max` ki jagah `min` ya `+` use karna.** `+` karoge to total nodes jaisa kuch aayega, `min` karoge to shortest path.
4. **Min Depth (LC 111) me yehi code copy karna.** Wahan agar ek child NULL ho to `min` galat answer deta hai (neeche dekho).
5. **Edges vs nodes ka confusion.**

---

## 🧪 9. Edge Cases Checklist

- [ ] Empty tree → **0**
- [ ] Single node → **1**
- [ ] Sirf left chain (skewed) → n
- [ ] Sirf right chain (skewed) → n
- [ ] Complete / perfect tree → `log₂(n+1)`
- [ ] Ek side bahut gehri, doosri chhoti

---

## 🔗 10. Related Problems (Isi Template ke Variations)

| Problem | Variation |
|---|---|
| **111. Minimum Depth of Binary Tree** | Sabse paas wala **leaf**. ⚠️ Ek child NULL ho to usse ignore karna hai |
| **110. Balanced Binary Tree** | Har node pe `|lh - rh| <= 1` check; height return karo, imbalance pe `-1` |
| **543. Diameter of Binary Tree** | Height nikalte hue `lh + rh` ka global max rakho |
| **124. Binary Tree Maximum Path Sum** | Height jaisa recursion, par values sum hoti hain |
| **559. Maximum Depth of N-ary Tree** | Sab children pe loop karke max lo |
| **226. Invert Binary Tree** | Bottom-up recursion ka aur ek example |

### ⚠️ Min Depth ki trap (111)

```cpp
// ❌ Galat: [1,2] jaise tree pe 1 dega (jabki sahi answer 2)
return 1 + min(minDepth(left), minDepth(right));

// ✅ Sahi: agar ek child NULL ho, to doosre child ki taraf jao
if (!root->left)  return 1 + minDepth(root->right);
if (!root->right) return 1 + minDepth(root->left);
return 1 + min(minDepth(root->left), minDepth(root->right));
```

---

## 🎯 11. Interview Talking Points

1. *"Depth = 1 + max(left depth, right depth), base case NULL → 0."*
2. *"Yeh bottom-up recursion hai: children ka answer pehle, phir parent ka."*
3. *"Time O(n), space O(h). Skewed tree me worst O(n) call stack."*
4. *"BFS se bhi ho sakta hai: levels ginta hu."*
5. *"Yehi pattern height, diameter, balanced check, max path sum me kaam aata hai."*

---

## ✅ 12. Final Revision Checklist

- [ ] Base case: `NULL → 0`
- [ ] Recurrence: `1 + max(lh, rh)`
- [ ] Postorder / bottom-up soch samajh aayi
- [ ] BFS (levels count) alternative yaad hai
- [ ] Min Depth (111) ki trap yaad hai
- [ ] Dry run (`[3,9,20,null,null,15,7]` → 3) bina dekhe kar sakta hu

> **Memory hook:** 🌲 *"NULL = 0. Baaki sab: 1 + max(left, right)."*
