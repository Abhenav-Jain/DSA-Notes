# 🌳 LeetCode 144 — Binary Tree Preorder Traversal

> **Difficulty:** Easy  |  **Pattern:** DFS (Recursive / Iterative Stack)  |  **Time:** O(n)  |  **Space:** O(h)

---

## 🏷️ Tags

`Tree` · `Binary Tree` · `DFS` · `Stack` · `Recursion` · `Morris Traversal`

---

## ⚡ 30-Second Recap

| Cheez | Yaad rakhne wali baat |
|---|---|
| Preorder order | **Root → Left → Right** (NLR) |
| Recursive | Visit root, phir left, phir right |
| Iterative | Stack use karo. **Right pehle push, Left baad me push** |
| Kyun? | Stack LIFO hai, isliye last pushed (left) pehle pop hota hai |
| Space | O(h), `h` = height of tree |
| Bonus | Morris traversal se O(1) extra space |

**One-liner:** *"Pop karo, print karo, right push karo, phir left push karo."*

---

## 📝 1. Problem Statement

Binary tree ka `root` diya hai. Uske nodes ki values ka **preorder traversal** return karo.

**Constraints:** nodes ki sankhya `[0, 100]`, `-100 <= Node.val <= 100`

**Follow-up:** Recursive to trivial hai. **Iterative** solution karo.

### Examples

| Input (level order) | Output |
|---|---|
| `[1,null,2,3]` | `[1,2,3]` |
| `[]` | `[]` |
| `[1]` | `[1]` |

---

## 🧠 2. Core Intuition

### Preorder ka matlab

Kisi bhi node pe aao to **pehle us node ko visit karo**, phir poora left subtree, phir poora right subtree.

```
        1
       / \
      2   3
     / \   \
    4   5   6

Preorder: 1 → 2 → 4 → 5 → 3 → 6
```

### Recursive soch

```
preorder(node):
    if node is null: return
    visit(node)
    preorder(node.left)
    preorder(node.right)
```

Call stack khud hi "yaad" rakhta hai ki kaunsa right subtree baad me visit karna hai.

### Iterative soch (explicit stack)

Recursion ka call stack ko hum **khud ke stack** se replace karte hain:

1. Root ko stack me daalo.
2. Jab tak stack khaali nahi:
   - Top ko **pop** karo aur **visit** karo.
   - **Right child push** karo (pehle).
   - **Left child push** karo (baad me).

**Right pehle kyun?** Stack LIFO hota hai. Left baad me daala, to woh upar rahega aur **pehle pop** hoga. Preorder me left pehle chahiye, to right pehle push karo.

---

## 💻 3. Solution 1: Iterative (Stack)

```cpp
class Solution {
public:
    vector<int> preorderTraversal(TreeNode* root) {
        vector<int> preorder;
        if (root == NULL) {
            return preorder;
        }
        stack<TreeNode*> st;
        st.push(root);
        while (!st.empty()) {
            root = st.top();
            st.pop();
            preorder.push_back(root->val);       // visit
            if (root->right != NULL) {
                st.push(root->right);            // right pehle
            }
            if (root->left != NULL) {
                st.push(root->left);             // left baad me (top pe rahega)
            }
        }
        return preorder;
    }
};
```

### 🔍 Dry Run (tree: `1 → (2 → (4, 5)), (3 → (null, 6))`)

| Step | Pop | Output so far | Push (right, left) | Stack (bottom → top) |
|---|---|---|---|---|
| init | | `[]` | 1 | `[1]` |
| 1 | 1 | `[1]` | 3, 2 | `[3, 2]` |
| 2 | 2 | `[1,2]` | 5, 4 | `[3, 5, 4]` |
| 3 | 4 | `[1,2,4]` | none | `[3, 5]` |
| 4 | 5 | `[1,2,4,5]` | none | `[3]` |
| 5 | 3 | `[1,2,4,5,3]` | 6 | `[6]` |
| 6 | 6 | `[1,2,4,5,3,6]` | none | `[]` |

Answer = **`[1,2,4,5,3,6]`** ✅

---

## 💻 4. Solution 2: Recursive (aapka version)

```cpp
class Solution {
public:
    vector<int> ans;
    vector<int> preorderTraversal(TreeNode* root) {
        if (root == NULL) {
            return {};
        }
        ans.push_back(root->val);
        preorderTraversal(root->left);
        preorderTraversal(root->right);
        return ans;
    }
};
```

### ⚠️ Is version me ek hidden risk hai

1. **Member variable `ans` state share karta hai.** LeetCode har test case ke liye alag `Solution` object banata hai, isliye yahan chal jaata hai. Lekin **agar same object pe function do baar call karo**, to `ans` me purana data bacha rahega aur output galat aayega.
2. **Recursive calls ka return value ignore ho raha hai.** Kaam sirf side effect (`ans.push_back`) se ho raha hai. Samajhne me thoda confusing hai.

### ✅ Safer version: helper function

```cpp
class Solution {
public:
    vector<int> preorderTraversal(TreeNode* root) {
        vector<int> ans;
        dfs(root, ans);
        return ans;
    }
private:
    void dfs(TreeNode* node, vector<int>& ans) {
        if (node == NULL) return;
        ans.push_back(node->val);   // N
        dfs(node->left, ans);       // L
        dfs(node->right, ans);      // R
    }
};
```

Yahan har call ka apna fresh `ans` hai, koi shared state ka jhanjhat nahi.

---

## 🚀 5. Bonus: Morris Preorder (O(1) Extra Space)

Idea: Tree me temporary links (threads) bana ke bina stack ke traverse karo.

```cpp
vector<int> preorderTraversal(TreeNode* root) {
    vector<int> res;
    TreeNode* cur = root;
    while (cur) {
        if (!cur->left) {
            res.push_back(cur->val);        // left nahi hai, visit karo
            cur = cur->right;
        } else {
            TreeNode* pred = cur->left;     // inorder predecessor dhundo
            while (pred->right && pred->right != cur) pred = pred->right;
            if (!pred->right) {
                res.push_back(cur->val);    // pehli baar aaye: visit karo
                pred->right = cur;          // thread banao
                cur = cur->left;
            } else {
                pred->right = nullptr;      // thread hatao (tree restore)
                cur = cur->right;
            }
        }
    }
    return res;
}
```

- Time: O(n), Space: **O(1)**
- Tree ko temporarily modify karta hai, par end me restore kar deta hai.
- Interview me bonus hai, har baar zaruri nahi.

---

## ⏱️ 6. Complexity

| Approach | Time | Space |
|---|---|---|
| Recursive | O(n) | O(h) call stack |
| Iterative (stack) | O(n) | O(h) |
| Morris | O(n) | O(1) |

`h` = tree height. Balanced tree me `h = log n`, skewed tree me `h = n`.

---

## 🔁 7. Preorder vs Inorder vs Postorder (Iterative Cheat Sheet)

| Traversal | Order | Iterative idea |
|---|---|---|
| **Preorder** | N → L → R | Pop, visit, push right, push left |
| **Inorder** | L → N → R | Left-left jaate hue push karo; pop karke visit; phir right pe jao |
| **Postorder** | L → R → N | Modified preorder (N → R → L) karo, phir **result reverse** kar do |

> 💡 Teeno ka recursive code me bas **visit line ki position** badalti hai:
> Preorder = visit pehle, Inorder = visit beech me, Postorder = visit aakhir me.

---

## ⚠️ 8. Common Mistakes

1. **Left pehle push kar dena.** Output `N → R → L` ban jaata hai.
2. **Null check bhoolna** push karte waqt. Null pointer stack me daalne se crash hota hai.
3. **Empty tree (`root == NULL`) handle na karna.**
4. **Recursive me member vector use karke same object reuse karna.** State leak hoti hai.
5. **Stack pe `root` variable reuse karte waqt confusion.** (Aapke code me ho sakta hai, par sahi chal raha hai.) Readable code ke liye `node` naam alag rakhna better hai.

---

## 🧪 9. Edge Cases Checklist

- [ ] Empty tree → `[]`
- [ ] Single node → `[val]`
- [ ] Sirf left chain (skewed left)
- [ ] Sirf right chain (skewed right)
- [ ] Negative values
- [ ] Complete / perfect binary tree

---

## 🔗 10. Related Problems

| Problem | Link in concept |
|---|---|
| **94. Binary Tree Inorder Traversal** | Same template, visit position alag |
| **145. Binary Tree Postorder Traversal** | Reverse of (N→R→L) trick |
| **589. N-ary Tree Preorder Traversal** | Children ko **reverse order** me push karo |
| **102. Binary Tree Level Order Traversal** | Stack ki jagah **queue** (BFS) |

---

## 🎯 11. Interview Talking Points

1. *"Preorder me root pehle visit hota hai, phir left, phir right."*
2. *"Recursive simple hai; iterative me explicit stack use karta hu."*
3. *"Right pehle push karta hu taaki left pehle pop ho, kyunki stack LIFO hai."*
4. *"Time O(n), space O(h). Morris traversal se space O(1) ho sakta hai."*
5. *"Recursive me shared member variable ki jagah helper function use karna safer hai."*

---

## ✅ 12. Final Revision Checklist

- [ ] Preorder = Root → Left → Right
- [ ] Iterative me right pehle, left baad me push
- [ ] Recursive code me member-variable wali galti samajh aayi
- [ ] Inorder/Postorder se difference clear hai
- [ ] Dry run `[1,2,4,5,3,6]` bina dekhe kar sakta hu

> **Memory hook:** 🌲 *"Pop, print, right push, left push, bas yehi preorder hai."*
