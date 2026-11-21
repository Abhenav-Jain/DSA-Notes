# 🌳 LeetCode 145 — Binary Tree Postorder Traversal

> **Difficulty:** Easy  |  **Pattern:** DFS (Recursive / Iterative Stack)  |  **Time:** O(n)  |  **Space:** O(h)

---

## 🏷️ Tags

`Tree` · `Binary Tree` · `DFS` · `Stack` · `Recursion` · `Bottom-Up` · `Reverse Trick`

---

## ⚡ 30-Second Recap

| Cheez | Yaad rakhne wali baat |
|---|---|
| Postorder order | **Left → Right → Root** (LRN) |
| Recursive | Left, right, phir root visit |
| Iterative (2-step trick) | **Root → Right → Left** karo (preorder ka mirror), phir **result reverse** |
| Trick me push order | **Left pehle push, Right baad me** (preorder se ulta) |
| Iterative (two stacks) | `st1` se N→R→L order me nikalo aur `st2` me daalo; `st2` pop karo = L→R→N |
| Iterative (true one-stack) | `last` visited pointer rakho: right visit ho chuka ho tabhi root visit karo |
| Mindset | **Bottom-up**: parent ko visit karne se pehle children ki info chahiye |

**One-liner:** *"Postorder = reverse of (Root, Right, Left)."*

---

## 📝 1. Problem Statement

Binary tree ka `root` diya hai. Uske nodes ki values ka **postorder traversal** return karo.

**Constraints:** nodes ki sankhya `[0, 100]`, `-100 <= Node.val <= 100`

**Follow-up:** Recursive to trivial hai. **Iterative** solution karo.

### Examples

| Input (level order) | Output |
|---|---|
| `[1,null,2,3]` | `[3,2,1]` |
| `[]` | `[]` |
| `[1]` | `[1]` |

---

## 🧠 2. Core Intuition

### Postorder ka matlab

Node ko **sabse last me** visit karo, jab uske dono subtrees poore ho chuke hon.

```
        1
       / \
      2   3
     / \   \
    4   5   6

Postorder: 4 → 5 → 2 → 6 → 3 → 1
```

### Recursive soch

```
postorder(node):
    if node is null: return
    postorder(node.left)
    postorder(node.right)
    visit(node)
```

### 🪄 Iterative trick: Reverse of (N → R → L)

Seedha postorder iterative me mushkil hai, kyunki root ko **do baar** dekhna padta hai (ek baar neeche jaane ke liye, ek baar visit ke liye). Trick:

1. Preorder jaisa traversal karo, par **left aur right ka order ulta**: `Root → Right → Left`.
2. Is output ko **reverse** kar do: `Left → Right → Root` ✅ (yehi postorder hai).

```
N → R → L  ko reverse karo  =  L → R → N
```

**Stack me push order:** pop → visit → **left push** → **right push**.
Right top pe rahega, to **right pehle pop** hoga, yani N → R → L ban jaayega.

> 📌 Preorder me tha: right push, phir left push. Yahan **ulta**: left push, phir right push.

---

## 💻 3. Solution 1: Iterative (2-step trick, aapka version)

```cpp
class Solution {
public:
    vector<int> postorderTraversal(TreeNode* root) {
        vector<int> postorder;
        stack<TreeNode*> st;
        if (root == NULL) {
            return postorder;
        }
        st.push(root);
        while (!st.empty()) {
            root = st.top();
            st.pop();
            postorder.push_back(root->val);       // N
            if (root->left != NULL) {
                st.push(root->left);              // left pehle push
            }
            if (root->right != NULL) {
                st.push(root->right);             // right baad me (top pe), pehle pop hoga
            }
        }
        reverse(postorder.begin(), postorder.end());   // NRL → LRN
        return postorder;
    }
};
```

### 🔍 Dry Run (tree: `1 → (2 → (4, 5)), (3 → (null, 6))`)

| Step | Pop | Output (N→R→L) | Push (left, right) | Stack (bottom → top) |
|---|---|---|---|---|
| init | | `[]` | 1 | `[1]` |
| 1 | 1 | `[1]` | 2, 3 | `[2, 3]` |
| 2 | 3 | `[1,3]` | 6 | `[2, 6]` |
| 3 | 6 | `[1,3,6]` | none | `[2]` |
| 4 | 2 | `[1,3,6,2]` | 4, 5 | `[4, 5]` |
| 5 | 5 | `[1,3,6,2,5]` | none | `[4]` |
| 6 | 4 | `[1,3,6,2,5,4]` | none | `[]` |

**Reverse karo:** `[4,5,2,6,3,1]` ✅

---

## 💻 4. Solution 2: Recursive (aapka version)

```cpp
class Solution {
public:
    vector<int> postorder;
    vector<int> postorderTraversal(TreeNode* root) {
        if (root == NULL) {
            return {};
        }
        postorderTraversal(root->left);
        postorderTraversal(root->right);
        postorder.push_back(root->val);
        return postorder;
    }
};
```

### ⚠️ Hidden risk (preorder/inorder jaisa hi)

1. **Member variable `postorder` state share karta hai.** LeetCode pe har test ke liye naya object banta hai, isliye pass ho jaata hai. **Same object pe do baar call** karoge to purana data bacha rahega.
2. **Recursive calls ka return value ignore** ho raha hai, kaam side effect se chal raha hai.

### ✅ Safer version: helper function

```cpp
class Solution {
public:
    vector<int> postorderTraversal(TreeNode* root) {
        vector<int> ans;
        dfs(root, ans);
        return ans;
    }
private:
    void dfs(TreeNode* node, vector<int>& ans) {
        if (node == NULL) return;
        dfs(node->left, ans);       // L
        dfs(node->right, ans);      // R
        ans.push_back(node->val);   // N
    }
};
```

---

## 💻 4B. Solution 3: Iterative (Two Stacks)

Yeh Solution 1 ka hi cousin hai. Wahan hum output vector ko `reverse()` karte the, yahan us reverse ka kaam **doosra stack** (`st2`) karta hai. Stack me daalke nikalne se order apne aap ulta ho jaata hai.

```cpp
class Solution {
public:
    vector<int> postorderTraversal(TreeNode* root) {
        vector<int> postorder;
        stack<TreeNode*> st1;
        stack<TreeNode*> st2;
        if (root == NULL) {
            return postorder;
        }
        st1.push(root);
        while (!st1.empty()) {
            root = st1.top();
            st1.pop();
            st2.push(root);                       // st2 me N → R → L order me jaata hai
            if (root->left != NULL) {
                st1.push(root->left);             // left pehle push
            }
            if (root->right != NULL) {
                st1.push(root->right);            // right baad me, pehle pop hoga
            }
        }
        while (!st2.empty()) {                    // st2 pop = reverse = L → R → N
            root = st2.top();
            st2.pop();
            postorder.push_back(root->val);
        }
        return postorder;
    }
};
```

### 🧠 Kaise kaam karta hai?

| Stack | Kaam |
|---|---|
| `st1` | Traversal chalata hai (preorder ka mirror: N → R → L) |
| `st2` | Nodes ko usi order me collect karta hai. Baad me pop karne pe **order ulta** hota hai |

### 🔍 Dry Run (tree: `1 → (2 → (4, 5)), (3 → (null, 6))`)

| Step | Pop from `st1` | Push to `st2` | `st1` (bottom → top) | `st2` (bottom → top) |
|---|---|---|---|---|
| init | | | `[1]` | `[]` |
| 1 | 1 | 1 | `[2, 3]` | `[1]` |
| 2 | 3 | 3 | `[2, 6]` | `[1, 3]` |
| 3 | 6 | 6 | `[2]` | `[1, 3, 6]` |
| 4 | 2 | 2 | `[4, 5]` | `[1, 3, 6, 2]` |
| 5 | 5 | 5 | `[4]` | `[1, 3, 6, 2, 5]` |
| 6 | 4 | 4 | `[]` | `[1, 3, 6, 2, 5, 4]` |

Ab `st2` ko pop karo (top se): `4, 5, 2, 6, 3, 1` ✅

### ⚖️ Solution 1 vs Solution 3

| | Reverse trick (Sol 1) | Two stacks (Sol 3) |
|---|---|---|
| Idea | Vector me daalo, phir `reverse()` | `st2` me daalo, phir pop karo |
| Extra space | O(n) output (zaruri hi hai) | O(n) `st2` + O(n) output |
| Code | Chhota, simple | Thoda lamba |
| Interview | Preferred | Concept dikhane ke liye theek, par ek stack wala better |

> 💡 Dono ka logic same hai. Interview me Solution 1 likho, aur agar interviewer "stack se hi karo" bole to Solution 3 batao.

---

## 🚀 5. Bonus: True One-Stack Iterative (bina reverse ke)

Interviewer puch sakta hai: *"Reverse kiye bina karo."* Idea: stack ke top node ko tabhi visit karo jab uska **right subtree already visit ho chuka ho** (ya right hai hi nahi). Iske liye `last` pointer rakho.

```cpp
vector<int> postorderTraversal(TreeNode* root) {
    vector<int> res;
    stack<TreeNode*> st;
    TreeNode* curr = root;
    TreeNode* last = nullptr;          // last visited node
    while (curr || !st.empty()) {
        if (curr) {
            st.push(curr);             // left ki taraf badhte jao
            curr = curr->left;
        } else {
            TreeNode* top = st.top();
            if (top->right && top->right != last) {
                curr = top->right;     // right abhi baaki hai, wahan jao
            } else {
                res.push_back(top->val);   // dono side ho chuke, ab visit
                last = top;
                st.pop();
            }
        }
    }
    return res;
}
```

**Kab visit?** Jab `top->right == null` ya `top->right == last` (right se wapas aaye hain).

> 📌 Inorder jaisa hi structure hai, bas pop karne se pehle **check** hota hai ki right subtree ho chuka hai ya nahi.

(Morris se bhi postorder ho sakta hai, par woh complex hai aur interviews me rarely maanga jaata hai.)

---

## ⏱️ 6. Complexity

| Approach | Time | Space |
|---|---|---|
| Recursive | O(n) | O(h) call stack |
| Iterative (reverse trick) | O(n) | O(h) stack + O(n) output |
| Iterative (one-stack, `last` pointer) | O(n) | O(h) |
| Iterative (two stacks) | O(n) | O(n) (`st2` me saare nodes aate hain) |

`h` = height. Balanced tree me `h = log n`, skewed me `h = n`.

---

## 🔁 7. Teeno Traversals ka Cheat Sheet

| Traversal | Order | Iterative idea |
|---|---|---|
| **Preorder** | N → L → R | Pop, visit, push **right**, push **left** |
| **Inorder** | L → N → R | Left-left push; null pe pop + visit; phir right |
| **Postorder** | L → R → N | Pop, visit, push **left**, push **right**; phir **reverse** |

```
Preorder  : visit → left → right
Inorder   : left → visit → right
Postorder : left → right → visit
```

> 💡 **Pre vs Post (iterative):** Dono ka code lagbhag same hai. Farq sirf **push order** (right-left vs left-right) aur **end me reverse** ka hai.

---

## 🧩 8. Postorder kab socho? (Bottom-Up Problems)

Jab parent ka answer **children ke answers par depend** kare, to postorder thinking lagao:

- Tree ki **height / depth** nikalna
- **Diameter** nikalna
- **Maximum path sum**
- **Subtree delete / free** karna (pehle children, phir parent)
- **Lowest Common Ancestor**

Pattern: *"Pehle left se maango, right se maango, phir apna answer banao."*

---

## ⚠️ 9. Common Mistakes

1. **Push order preorder wala hi rakhna** (right, left). Reverse ke baad `N → L → R` jaisa kuch ulta-pulta output milta hai.
2. **`reverse` call bhoolna.** Output `N → R → L` reh jaata hai.
3. **Null nodes stack me push karna.** Crash ya garbage.
4. **One-stack version me `last` update bhoolna.** Infinite loop.
5. **Recursive me member vector reuse karna.** State leak hoti hai.

---

## 🧪 10. Edge Cases Checklist

- [ ] Empty tree → `[]`
- [ ] Single node → `[val]`
- [ ] Sirf left chain (skewed left)
- [ ] Sirf right chain (skewed right)
- [ ] Negative values
- [ ] Root hamesha **last** me aana chahiye

---

## 🔗 11. Related Problems

| Problem | Link in concept |
|---|---|
| **94. Binary Tree Inorder Traversal** | Same family |
| **144. Binary Tree Preorder Traversal** | Same family, mirror trick ka base |
| **590. N-ary Tree Postorder Traversal** | Children ko order me push karke reverse |
| **104. Maximum Depth of Binary Tree** | Bottom-up (postorder) soch |
| **543. Diameter of Binary Tree** | Bottom-up, heights combine karo |
| **124. Binary Tree Maximum Path Sum** | Postorder DP |
| **236. Lowest Common Ancestor of a Binary Tree** | Postorder style recursion |

---

## 🎯 12. Interview Talking Points

1. *"Postorder me root sabse last me visit hota hai, left aur right ke baad."*
2. *"Recursive simple hai. Iterative me N → R → L karke reverse kar deta hu, jo L → R → N ban jaata hai."*
3. *"Agar reverse allowed nahi, to `last` pointer ke saath ek-stack approach use karunga."*
4. *"Time O(n), space O(h)."*
5. *"Postorder bottom-up problems ke liye natural hai, jaise height aur diameter."*

---

## ✅ 13. Final Revision Checklist

- [ ] Postorder = Left → Right → Root
- [ ] Reverse trick: N → R → L ko reverse karo
- [ ] Push order: left pehle, right baad me (preorder se ulta)
- [ ] `last` pointer wala one-stack version samajh aaya
- [ ] Dry run `[4,5,2,6,3,1]` bina dekhe kar sakta hu

> **Memory hook:** 🔄 *"Postorder = Preorder ka mirror, phir reverse. Ya `last` pointer se seedha."*
