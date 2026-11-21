# 🌊 LeetCode 102 — Binary Tree Level Order Traversal

> **Difficulty:** Medium  |  **Pattern:** BFS (Queue + Level Size Snapshot)  |  **Time:** O(n)  |  **Space:** O(w)

---

## 🏷️ Tags

`Tree` · `Binary Tree` · `BFS` · `Queue` · `Level Order` · `Template Problem`

---

## ⚡ 30-Second Recap

| Cheez | Yaad rakhne wali baat |
|---|---|
| Traversal type | **BFS** (level by level, upar se neeche, left se right) |
| Data structure | **Queue** (FIFO) |
| Key trick | Har level shuru hone pe `size = q.size()` ka **snapshot** lo |
| Inner loop | Exactly `size` baar chalao, wahi ek level hai |
| Output | `vector<vector<int>>`, har level ek vector |
| Space | O(w), `w` = tree ki maximum width |

**One-liner:** *"Queue me root daalo. Har round me current queue ka size note karo, utne nodes pop karo, unke children push karo. Ek round = ek level."*

---

## 📝 1. Problem Statement

Binary tree ka `root` diya hai. Uske nodes ki values ka **level order traversal** return karo (left se right, level by level).

**Constraints:** nodes ki sankhya `[0, 2000]`, `-1000 <= Node.val <= 1000`

### Examples

| Input (level order) | Output |
|---|---|
| `[3,9,20,null,null,15,7]` | `[[3],[9,20],[15,7]]` |
| `[1]` | `[[1]]` |
| `[]` | `[]` |

---

## 🧠 2. Core Intuition

### DFS vs BFS

| | DFS (pre/in/post) | BFS (level order) |
|---|---|---|
| Data structure | Stack / Recursion | **Queue** |
| Direction | Pehle gehra jao | **Pehle chaudai (width) me jao** |
| Order | Ek branch poori khatam, phir agli | Ek level poora khatam, phir agla |

### Queue kyun?

Level 1 ke nodes pehle aaye, to unke children bhi pehle process hone chahiye, **pehle aao pehle jao (FIFO)**. Yahi queue ka kaam hai.

### 🔑 Level Size Snapshot Trick

Problem: Queue me sab levels ke nodes mix hote hain. Kaise pata chale ki kahan ek level khatam hua?

Solution: Jis moment level shuru ho, `q.size()` **tab** exactly us level ke nodes ki sankhya hota hai (kyunki abhi tak sirf usi level ke nodes queue me hain). Isko `size` variable me **freeze** kar lo, aur loop exactly `size` baar chalao. Is dauran jo children push honge woh **agle level** ke honge.

```
Queue at level start:  [9, 20]      size = 2
  pop 9  → no children
  pop 20 → push 15, 7
Queue after loop:      [15, 7]      ← agla level
```

---

## 🧩 3. Algorithm

```
if root is null: return []
q = [root]
while q is not empty:
    size = q.size()              # snapshot
    level = []
    repeat size times:
        node = q.pop_front()
        level.add(node.val)
        if node.left:  q.push(node.left)
        if node.right: q.push(node.right)
    ans.add(level)
return ans
```

---

## 💻 4. Solution 1: BFS (Queue) — aapka version

```cpp
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        queue<TreeNode*> q;
        vector<vector<int>> ans;
        if (root == NULL) {
            return ans;
        }
        q.push(root);
        while (!q.empty()) {
            int size = q.size();                 // is level ke nodes ki sankhya
            vector<int> level;
            for (int i = 0; i < size; i++) {
                TreeNode* root = q.front();      // ⚠️ shadowing, neeche dekho
                q.pop();
                if (root->left != NULL) {
                    q.push(root->left);
                }
                if (root->right != NULL) {
                    q.push(root->right);
                }
                level.push_back(root->val);
            }
            ans.push_back(level);
        }
        return ans;
    }
};
```

### 📌 Chhota readability suggestion

Andar `TreeNode* root = q.front();` function parameter `root` ko **shadow** karta hai. Yeh legal hai aur kaam karta hai (kyunki inner scope hai), par confusing ho sakta hai. Behtar naam: `node`.

```cpp
TreeNode* node = q.front();
q.pop();
level.push_back(node->val);
if (node->left)  q.push(node->left);
if (node->right) q.push(node->right);
```

### 🔍 Dry Run (tree: `3 → (9), (20 → (15, 7))`)

```
        3
       / \
      9   20
         /  \
        15   7
```

| Round | `size` | Pop | Push | `level` | Queue (front → back) |
|---|---|---|---|---|---|
| init | | | 3 | | `[3]` |
| **Level 1** | 1 | 3 | 9, 20 | `[3]` | `[9, 20]` |
| **Level 2** | 2 | 9 | none | | `[20]` |
| | | 20 | 15, 7 | `[9, 20]` | `[15, 7]` |
| **Level 3** | 2 | 15 | none | | `[7]` |
| | | 7 | none | `[15, 7]` | `[]` |

Queue khaali, loop khatam. Answer = **`[[3],[9,20],[15,7]]`** ✅

---

## 💻 5. Solution 2: DFS with Depth (Recursive)

Level order ko DFS se bhi kar sakte ho. Har node ke saath uski **depth** pass karo, aur `ans[depth]` me value daalo.

```cpp
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> ans;
        dfs(root, 0, ans);
        return ans;
    }
private:
    void dfs(TreeNode* node, int depth, vector<vector<int>>& ans) {
        if (node == NULL) return;
        if (depth == ans.size()) {
            ans.push_back({});           // naya level mila
        }
        ans[depth].push_back(node->val);
        dfs(node->left, depth + 1, ans);   // left pehle, taaki order sahi rahe
        dfs(node->right, depth + 1, ans);
    }
};
```

**Kyun sahi?** Left pehle visit hota hai, to har level me left-to-right order automatically bana rehta hai.

| | BFS (queue) | DFS (depth) |
|---|---|---|
| Time | O(n) | O(n) |
| Space | O(w) width | O(h) height (call stack) |
| Intuition | Natural, level-by-level | Thoda tricky, par short |
| Interview | **Preferred** | Alternative dikhane ke liye |

---

## ⏱️ 6. Complexity

| | Value | Reason |
|---|---|---|
| **Time** | O(n) | Har node exactly ek baar push aur ek baar pop hota hai |
| **Space** | O(w) | Queue me ek time par zyada se zyada ek-do levels. `w` = max width (complete tree me ~ n/2) |

> 💡 Skewed tree me `w = 1`, to space O(1) jaisa hota hai. Complete/full tree me last level ~ n/2 nodes, to O(n).

---

## 🔁 7. Poora Tree-Traversal Map (Revision ke liye)

| Traversal | Type | Structure | Order |
|---|---|---|---|
| Preorder (144) | DFS | Stack | N → L → R |
| Inorder (94) | DFS | Stack | L → N → R |
| Postorder (145) | DFS | Stack | L → R → N |
| **Level Order (102)** | **BFS** | **Queue** | Level 0 → Level 1 → ... |

> Stack se **depth** me jaate hain, Queue se **width** me.

---

## 🧰 8. BFS Template (Yehi copy-paste karke bahut saare problems solve hote hain)

```cpp
queue<TreeNode*> q;
if (root) q.push(root);
while (!q.empty()) {
    int size = q.size();                 // level snapshot
    for (int i = 0; i < size; i++) {
        TreeNode* node = q.front(); q.pop();
        // 👉 yahan node ke saath kuch bhi karo
        if (node->left)  q.push(node->left);
        if (node->right) q.push(node->right);
    }
    // 👉 yahan level khatam hone pe kuch karo
}
```

---

## ⚠️ 9. Common Mistakes

1. **`size` snapshot na lena** aur loop me `i < q.size()` likhna. Loop ke dauran queue badhti hai, to size badalta rehta hai aur levels mix ho jaate hain.
2. **`vector<int> level` ko `while` ke bahar declare karna.** Har level ke liye fresh vector chahiye.
3. **Null children ko queue me push karna.** Null check zaruri hai, warna `->val` pe crash.
4. **`root == NULL` check bhoolna.** Empty tree pe `q.push(NULL)` se crash.
5. **Variable shadowing** (`root` ko andar re-declare karna). Chalta hai, par confusing.
6. **`ans.push_back(level)` loop ke andar kar dena** (har node ke baad). Level poora hone ke baad karna hai.

---

## 🧪 10. Edge Cases Checklist

- [ ] Empty tree → `[]`
- [ ] Single node → `[[val]]`
- [ ] Sirf left chain (skewed left)
- [ ] Sirf right chain (skewed right)
- [ ] Complete binary tree (last level bhara hua)
- [ ] Missing children beech me (null gaps)
- [ ] Negative values

---

## 🔗 11. Related Problems (Same BFS Template)

| Problem | Variation |
|---|---|
| **107. Level Order Traversal II** | Result ko reverse karo (bottom-up) |
| **103. Zigzag Level Order Traversal** | Alternate levels reverse karo |
| **199. Binary Tree Right Side View** | Har level ka **last** node |
| **637. Average of Levels in Binary Tree** | Har level ka average |
| **515. Find Largest Value in Each Tree Row** | Har level ka max |
| **104. Maximum Depth of Binary Tree** | Levels ki **count** |
| **111. Minimum Depth of Binary Tree** | Pehla leaf kis level pe mila |
| **429. N-ary Tree Level Order Traversal** | Same, children ki loop ke saath |
| **116 / 117. Populating Next Right Pointers** | Level ke nodes ko aapas me jodna |
| **662. Maximum Width of Binary Tree** | Level ke andar index tracking |

---

## 🎯 12. Interview Talking Points

1. *"Level order BFS hai, queue use karunga."*
2. *"Har round ki shuruaat me `q.size()` snapshot leta hu, wahi us level ke nodes ki sankhya hai."*
3. *"Us size tak pop karta hu, children agle level ke liye push hote hain."*
4. *"Time O(n), space O(w) width ke hisaab se."*
5. *"DFS se bhi ho sakta hai: depth pass karke `ans[depth]` me daalo."*
6. *"Yeh template right side view, zigzag, average of levels jaise problems me directly kaam aata hai."*

---

## ✅ 13. Final Revision Checklist

- [ ] BFS = Queue, DFS = Stack
- [ ] `size = q.size()` snapshot lena yaad hai aur uska reason bhi
- [ ] `level` vector har round me naya banta hai
- [ ] Null children push nahi karne
- [ ] DFS-with-depth alternative samajh aaya
- [ ] Dry run `[[3],[9,20],[15,7]]` bina dekhe kar sakta hu

> **Memory hook:** 🌊 *"Queue me root, size note karo, utne pop karo, children push karo. Ek round = ek level."*
