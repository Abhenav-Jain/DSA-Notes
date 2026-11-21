# 🪞 LeetCode 226 — Invert Binary Tree

> **Difficulty:** Easy  |  **Pattern:** Tree Recursion (Swap Children)  |  **Time:** O(n)  |  **Space:** O(h)

---

## 🏷️ Tags

`Tree` · `Binary Tree` · `DFS` · `BFS` · `Recursion` · `In-Place` · `Mirror`

---

## ⚡ 30-Second Recap

| Cheez | Yaad rakhne wali baat |
|---|---|
| Kya karna hai | Tree ka **mirror image** banana |
| Core operation | Har node pe `left` aur `right` **swap** karo |
| Base case | `root == NULL` → `NULL` return |
| Order | Preorder ya Postorder dono chalte hain |
| ⚠️ Trap | **Inorder** (recurse → swap → recurse) me bug aata hai |
| Modification | **In-place**, naya tree nahi banta |

**One-liner:** *"Har node pe children ko swap karo, aur yehi sab subtrees ke saath bhi karo."*

---

## 📝 1. Problem Statement

Binary tree ka `root` diya hai. Tree ko **invert** karo aur uska `root` return karo.

**Constraints:** nodes ki sankhya `[0, 100]`, `-100 <= Node.val <= 100`

### Examples

```
Input:           4                 Output:          4
               /   \                              /   \
              2     7                            7     2
             / \   / \                          / \   / \
            1   3 6   9                        9   6 3   1
```

| Input | Output |
|---|---|
| `[4,2,7,1,3,6,9]` | `[4,7,2,9,6,3,1]` |
| `[2,1,3]` | `[2,3,1]` |
| `[]` | `[]` |

> 🎓 *Fun fact:* Yeh problem ek mashhoor tweet ki wajah se famous hui jisme Homebrew ke creator ne kaha tha ki Google interview me tree invert na kar paane pe reject hue.

---

## 🧠 2. Core Intuition

### Mirror ka matlab

Tree ko aaine me dekho. **Har node ke left aur right children aapas me badal jaate hain**, aur yeh badlaav **poore tree me har level pe** hota hai.

### Recursive soch (Trust the recursion)

Maan lo mere left aur right subtrees pehle se invert ho chuke hain. Ab mujhe bas **apne do children ko swap** karna hai. Done!

```
invert(node):
    if node is null: return null
    invert(node.left)         # left subtree ko invert karo
    invert(node.right)        # right subtree ko invert karo
    swap(node.left, node.right)   # ab apne children swap
    return node
```

---

## 💻 3. Solution 1: Postorder Recursive (aapka version)

```cpp
class Solution {
public:
    TreeNode* invertTree(TreeNode* root) {
        if (root == NULL) {
            return NULL;
        }
        invertTree(root->left);                 // pehle children ke subtrees invert
        invertTree(root->right);
        TreeNode* temp = root->left;            // phir apne children swap
        root->left = root->right;
        root->right = temp;
        return root;
    }
};
```

### 🔍 Dry Run (tree: `4 → (2 → (1,3)), (7 → (6,9))`)

| Step | Node | Action | Result at node |
|---|---|---|---|
| 1 | 1 | Leaf, swap(null, null) | koi badlaav nahi |
| 2 | 3 | Leaf, swap(null, null) | koi badlaav nahi |
| 3 | 2 | swap(1, 3) | `2 → (3, 1)` |
| 4 | 6 | Leaf | koi badlaav nahi |
| 5 | 9 | Leaf | koi badlaav nahi |
| 6 | 7 | swap(6, 9) | `7 → (9, 6)` |
| 7 | 4 | swap(2, 7) | `4 → (7, 2)` |

Final tree: `4 → (7 → (9,6)), (2 → (3,1))` = **`[4,7,2,9,6,3,1]`** ✅

---

## 💻 4. Solution 2: Return Values ka Use (Temp ke bina)

Aapke code me recursive calls ke return values **ignore** ho rahe hain (kaam in-place pointer swap se ho raha hai, jo theek hai). Return values use karke aur chhota likh sakte ho:

```cpp
TreeNode* invertTree(TreeNode* root) {
    if (!root) return nullptr;
    TreeNode* l = invertTree(root->left);    // inverted left subtree
    TreeNode* r = invertTree(root->right);   // inverted right subtree
    root->left  = r;                         // ulta jodo
    root->right = l;
    return root;
}
```

Ya `std::swap` ke saath:

```cpp
swap(root->left, root->right);
```

---

## 💻 5. Solution 3: Preorder Recursive (Pehle swap, phir recurse)

```cpp
TreeNode* invertTree(TreeNode* root) {
    if (!root) return nullptr;
    swap(root->left, root->right);      // pehle swap
    invertTree(root->left);             // ab jo left hai (purana right), use invert karo
    invertTree(root->right);
    return root;
}
```

Yeh bhi bilkul sahi hai.

---

## 🚨 6. THE TRAP: Inorder (Recurse → Swap → Recurse)

```cpp
// ❌ GALAT
invertTree(root->left);
swap(root->left, root->right);
invertTree(root->right);     // ⚠️ yeh ab purana LEFT subtree hai!
```

**Kyun galat?**

1. `invertTree(root->left)` purana left invert karta hai.
2. `swap` ke baad `root->right` ab **wahi purana left subtree** hai (jo already invert ho chuka).
3. Dobara `invertTree(root->right)` us **same subtree ko do baar invert** kar deta hai (wapas original), aur **purana right subtree kabhi invert hi nahi hota**.

**Fix:** Swap ke baad dono baar `root->left` ko invert karo:

```cpp
invertTree(root->left);
swap(root->left, root->right);
invertTree(root->left);      // ✅ ab purana right wahan hai
```

> 💡 **Rule:** Jo bhi order use karo, ensure karo ki **har subtree exactly ek baar invert ho**.

---

## 💻 7. Solution 4: BFS (Queue)

```cpp
TreeNode* invertTree(TreeNode* root) {
    if (!root) return nullptr;
    queue<TreeNode*> q;
    q.push(root);
    while (!q.empty()) {
        TreeNode* node = q.front();
        q.pop();
        swap(node->left, node->right);       // is node ke children swap
        if (node->left)  q.push(node->left);
        if (node->right) q.push(node->right);
    }
    return root;
}
```

### Iterative DFS (Stack)

Upar wale code me bas `queue` ki jagah `stack` use karo (`top()` / `pop()`). Order se farq nahi padta, kyunki **har node pe swap independent** hai.

---

## ⏱️ 8. Complexity

| Approach | Time | Space |
|---|---|---|
| Recursive DFS | O(n) | O(h) call stack |
| BFS (queue) | O(n) | O(w) max width |
| Iterative DFS (stack) | O(n) | O(h) |

- Har node ko exactly ek baar touch karte hain.
- Balanced: `h = log n`. Skewed: `h = n` (yahan `n ≤ 100`, safe).

---

## ⚠️ 9. Common Mistakes

1. **Inorder me double invert** (upar wali trap).
2. **Swap karna hi bhool jaana.** Sirf recursion karke return kar dena.
3. **Sirf values swap karna, pointers nahi.** Chalega nahi agar children ke subtrees alag shape ke hon. Pointers swap karo.
4. **`root == NULL` check bhoolna.** Null pe `->left` se crash.
5. **Naya tree banane ki koshish.** Problem in-place bolti hai (naya bhi chalta hai, par zyada memory).

---

## 🧪 10. Edge Cases Checklist

- [ ] Empty tree → `NULL`
- [ ] Single node → same tree
- [ ] Sirf left child wala node (swap ke baad right ho jaata hai)
- [ ] Sirf right child wala node
- [ ] Skewed tree (left chain ↔ right chain ban jaati hai)
- [ ] Perfect binary tree

---

## 🔗 11. Related Problems

| Problem | Link in concept |
|---|---|
| **101. Symmetric Tree** | Tree khud ka mirror hai ya nahi (invert + compare ka hi idea) |
| **100. Same Tree** | Do trees ko simultaneously traverse karna |
| **951. Flip Equivalent Binary Trees** | Kuch nodes pe swap allowed, phir equal ho sakte hain? |
| **617. Merge Two Binary Trees** | Do trees pe ek saath recursion |
| **104. Maximum Depth of Binary Tree** | Bottom-up recursion ka basic |
| **572. Subtree of Another Tree** | Same Tree ka extension |

---

## 🎯 12. Interview Talking Points

1. *"Har node pe left aur right children swap karne hain, aur yehi subtrees pe bhi."*
2. *"Preorder ya postorder dono chalte hain. Bas har subtree exactly ek baar invert hona chahiye."*
3. *"Inorder me careful rehna padta hai, kyunki swap ke baad `right` ab purana left ban jaata hai."*
4. *"Time O(n), space O(h). BFS se O(w)."*
5. *"In-place pointer swap hai, naya tree allocate nahi karta."*

---

## ✅ 13. Final Revision Checklist

- [ ] Har node pe `left ↔ right` swap
- [ ] Base case `NULL → NULL`
- [ ] Preorder aur postorder dono sahi, **inorder me trap**
- [ ] BFS alternative yaad hai
- [ ] Dry run `[4,2,7,1,3,6,9]` → `[4,7,2,9,6,3,1]` bina dekhe kar sakta hu

> **Memory hook:** 🪞 *"Har node pe children swap. Order koi bhi, bas inorder se bacho."*
