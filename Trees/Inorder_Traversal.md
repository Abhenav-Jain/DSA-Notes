# 🌳 LeetCode 94 — Binary Tree Inorder Traversal

> **Difficulty:** Easy  |  **Pattern:** DFS (Recursive / Iterative Stack)  |  **Time:** O(n)  |  **Space:** O(h)

---

## 🏷️ Tags

`Tree` · `Binary Tree` · `Binary Search Tree` · `DFS` · `Stack` · `Recursion` · `Morris Traversal`

---

## ⚡ 30-Second Recap

| Cheez | Yaad rakhne wali baat |
|---|---|
| Inorder order | **Left → Root → Right** (LNR) |
| Recursive | Left jao, root visit karo, right jao |
| Iterative | Left-left jaate hue **push** karo; jab null mile to **pop + visit**, phir **right** pe jao |
| BST me | Inorder = **sorted ascending order** 🔥 |
| Space | O(h), `h` = tree height |
| Bonus | Morris traversal se O(1) extra space |

**One-liner:** *"Jitna left ja sako jao (push), null aaye to pop karo, print karo, phir right ko dekho."*

---

## 📝 1. Problem Statement

Binary tree ka `root` diya hai. Uske nodes ki values ka **inorder traversal** return karo.

**Constraints:** nodes ki sankhya `[0, 100]`, `-100 <= Node.val <= 100`

**Follow-up:** Recursive to trivial hai. **Iterative** solution karo.

### Examples

| Input (level order) | Output |
|---|---|
| `[1,null,2,3]` | `[1,3,2]` |
| `[]` | `[]` |
| `[1]` | `[1]` |

---

## 🧠 2. Core Intuition

### Inorder ka matlab

Kisi node ko **tabhi visit karo jab uska poora left subtree khatam ho chuka ho**. Uske baad right subtree.

```
        1
       / \
      2   3
     / \   \
    4   5   6

Inorder: 4 → 2 → 5 → 1 → 3 → 6
```

### 🔥 BST wala golden fact

Agar tree **Binary Search Tree** hai, to inorder traversal **sorted order** me values deta hai. Isi wajah se yeh traversal BST ke bahut saare problems me aata hai (validate, kth smallest, iterator, etc.).

### Recursive soch

```
inorder(node):
    if node is null: return
    inorder(node.left)
    visit(node)
    inorder(node.right)
```

### Iterative soch (explicit stack)

Recursion me left call ke baad "wapas aana" call stack sambhalta hai. Iterative me yeh kaam **stack** karta hai:

1. `curr` ko root pe rakho.
2. **Agar `curr != null`**: stack me push karo, `curr = curr->left` (jitna left ja sako).
3. **Agar `curr == null`**: left khatam ho gaya.
   - Stack khaali hai to **break**.
   - Warna **pop** karo, **visit** karo, aur `curr = curr->right`.

---

## 💻 3. Solution 1: Iterative (Stack)

```cpp
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> inorder;
        stack<TreeNode*> st;
        TreeNode* curr = root;
        while (true) {
            if (curr != NULL) {
                st.push(curr);          // left ki taraf badhte jao
                curr = curr->left;
            } else {
                if (st.empty()) {
                    break;              // kaam khatam
                }
                curr = st.top();
                st.pop();
                inorder.push_back(curr->val);   // visit
                curr = curr->right;             // ab right subtree
            }
        }
        return inorder;
    }
};
```

### Compact alternative (same logic)

```cpp
while (curr || !st.empty()) {
    while (curr) { st.push(curr); curr = curr->left; }
    curr = st.top(); st.pop();
    inorder.push_back(curr->val);
    curr = curr->right;
}
```

### 🔍 Dry Run (tree: `1 → (2 → (4, 5)), (3 → (null, 6))`)

| Step | `curr` | Action | Stack (bottom → top) | Output |
|---|---|---|---|---|
| 1 | 1 | push, go left | `[1]` | `[]` |
| 2 | 2 | push, go left | `[1,2]` | `[]` |
| 3 | 4 | push, go left | `[1,2,4]` | `[]` |
| 4 | null | pop 4, visit, go right (null) | `[1,2]` | `[4]` |
| 5 | null | pop 2, visit, go right (5) | `[1]` | `[4,2]` |
| 6 | 5 | push, go left | `[1,5]` | `[4,2]` |
| 7 | null | pop 5, visit, go right (null) | `[1]` | `[4,2,5]` |
| 8 | null | pop 1, visit, go right (3) | `[]` | `[4,2,5,1]` |
| 9 | 3 | push, go left | `[3]` | `[4,2,5,1]` |
| 10 | null | pop 3, visit, go right (6) | `[]` | `[4,2,5,1,3]` |
| 11 | 6 | push, go left | `[6]` | `[4,2,5,1,3]` |
| 12 | null | pop 6, visit, go right (null) | `[]` | `[4,2,5,1,3,6]` |
| 13 | null | stack empty → **break** | | |

Answer = **`[4,2,5,1,3,6]`** ✅

---

## 💻 4. Solution 2: Recursive (aapka version)

```cpp
class Solution {
public:
    vector<int> inorder;
    vector<int> inorderTraversal(TreeNode* root) {
        if (root == NULL) {
            return {};
        }
        inorderTraversal(root->left);
        inorder.push_back(root->val);
        inorderTraversal(root->right);
        return inorder;
    }
};
```

### ⚠️ Hidden risk (preorder wale notes jaisa hi)

1. **Member variable `inorder` state share karta hai.** LeetCode har test case pe naya object banata hai, isliye pass ho jaata hai. Par **same object pe do baar call** karoge to purana data bacha rahega.
2. **Recursive calls ka return value ignore** ho raha hai, kaam sirf side effect se chal raha hai.

### ✅ Safer version: helper function

```cpp
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> ans;
        dfs(root, ans);
        return ans;
    }
private:
    void dfs(TreeNode* node, vector<int>& ans) {
        if (node == NULL) return;
        dfs(node->left, ans);       // L
        ans.push_back(node->val);   // N
        dfs(node->right, ans);      // R
    }
};
```

---

## 🚀 5. Bonus: Morris Inorder (O(1) Extra Space)

Idea: Har node ke inorder predecessor ke right pointer ko temporarily current node se jod do (thread), taaki stack ki zarurat na pade.

```cpp
vector<int> inorderTraversal(TreeNode* root) {
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
                pred->right = cur;          // thread banao
                cur = cur->left;
            } else {
                pred->right = nullptr;      // thread hatao (tree restore)
                res.push_back(cur->val);    // left subtree khatam, ab visit
                cur = cur->right;
            }
        }
    }
    return res;
}
```

> 📌 **Preorder vs Inorder Morris me farq:** Preorder me visit **thread banate waqt** hota hai, inorder me visit **thread hatate waqt**.

---

## ⏱️ 6. Complexity

| Approach | Time | Space |
|---|---|---|
| Recursive | O(n) | O(h) call stack |
| Iterative (stack) | O(n) | O(h) |
| Morris | O(n) | O(1) |

`h` = height. Balanced tree me `h = log n`, skewed tree me `h = n`.

---

## 🔁 7. Teeno Traversals ka Cheat Sheet

| Traversal | Order | Iterative idea |
|---|---|---|
| **Preorder** | N → L → R | Pop, visit, push right, push left |
| **Inorder** | L → N → R | Left-left push karo; null pe pop + visit; phir right pe jao |
| **Postorder** | L → R → N | (N → R → L) karo, phir result **reverse** kar do |

**Recursive me bas visit line ki jagah badalti hai:**

```
Preorder  : visit → left → right
Inorder   : left → visit → right
Postorder : left → right → visit
```

---

## ⚠️ 8. Common Mistakes

1. **Pop karne ke baad `curr = curr->right` bhoolna.** Infinite loop ya galat output.
2. **Visit ko push ke waqt kar dena.** Woh preorder ban jaata hai.
3. **`st.empty()` check ke bina `top()` call karna.** Crash.
4. **Null push kar dena stack me.** Sirf non-null nodes push karo.
5. **Recursive me member vector reuse karna.** State leak hoti hai.
6. **Empty tree handle na karna.** Iterative me `curr = NULL` se loop seedha break hota hai, to wahan safe hai.

---

## 🧪 9. Edge Cases Checklist

- [ ] Empty tree → `[]`
- [ ] Single node → `[val]`
- [ ] Sirf left chain (skewed left)
- [ ] Sirf right chain (skewed right)
- [ ] Negative values
- [ ] BST: output sorted aana chahiye

---

## 🔗 10. Related Problems (BST wale zaroor karo)

| Problem | Link in concept |
|---|---|
| **98. Validate Binary Search Tree** | Inorder sorted strictly increasing hona chahiye |
| **230. Kth Smallest Element in a BST** | Inorder ka k-th element |
| **173. Binary Search Tree Iterator** | Iterative inorder ka hi controlled version |
| **99. Recover Binary Search Tree** | Inorder me do galat jagah swap hue elements |
| **144. Preorder Traversal** | Same template, visit position alag |
| **145. Postorder Traversal** | Reverse of (N→R→L) |

---

## 🎯 11. Interview Talking Points

1. *"Inorder me pehle left, phir root, phir right visit hota hai."*
2. *"BST me inorder sorted order deta hai."*
3. *"Iterative me stack se call stack ko simulate karta hu: left push, null pe pop aur visit, phir right."*
4. *"Time O(n), space O(h). Morris traversal se O(1) space."*
5. *"Recursive me shared member variable ki jagah helper function safer hai."*

---

## ✅ 12. Final Revision Checklist

- [ ] Inorder = Left → Root → Right
- [ ] BST + inorder = sorted
- [ ] Iterative ke 3 steps: push-left, pop+visit, go-right
- [ ] Preorder/Inorder/Postorder ka farq clear hai
- [ ] Dry run `[4,2,5,1,3,6]` bina dekhe kar sakta hu

> **Memory hook:** 🌿 *"Left tak push, null pe pop, print, phir right. BST me output hamesha sorted."*
