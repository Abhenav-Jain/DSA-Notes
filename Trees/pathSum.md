# 🛤️ LeetCode 112 — Path Sum

> **Difficulty:** Easy  |  **Pattern:** Top-Down DFS (Carry Info Downward)  |  **Time:** O(n)  |  **Space:** O(h)

---

## 🏷️ Tags

`Tree` · `Binary Tree` · `DFS` · `BFS` · `Recursion` · `Root-to-Leaf Path` · `Top-Down`

---

## ⚡ 30-Second Recap

| Cheez | Yaad rakhne wali baat |
|---|---|
| Path | **Root se LEAF** tak (beech me ruk nahi sakte) |
| 🔑 Leaf | Dono children `NULL` |
| Technique A | Neeche jaate hue **sum accumulate** karo, leaf pe `sum == target` |
| Technique B (cleaner) | Neeche jaate hue **target me se value ghatao**, leaf pe `target == root->val` |
| Combine | Left `||` Right (koi bhi ek path mil jaye) |
| `NULL` pe | **false** (NULL leaf nahi hai) |
| Space | O(h) |

**One-liner:** *"Root se leaf tak jaao, har step pe target me se current value ghatao, leaf pe target bacha hua node ki value ke barabar ho to true."*

---

## 📝 1. Problem Statement

Binary tree ka `root` aur ek integer `targetSum` diya hai. Return `true` agar tree me **root-to-leaf path** hai jiske nodes ki values ka **sum `targetSum`** ke barabar ho.

**Leaf** = jiska koi child nahi hai.

**Constraints:** nodes `[0, 5000]`, `-1000 <= Node.val <= 1000`, `-1000 <= targetSum <= 1000`

### Examples

```
Tree:        5
            / \
           4   8
          /   / \
         11  13  4
        /  \      \
       7    2      1
```

| Input | targetSum | Output | Path |
|---|---|---|---|
| tree upar wala | 22 | `true` | `5 → 4 → 11 → 2` |
| `[1,2,3]` | 5 | `false` | Paths: `1→2` (3), `1→3` (4) |
| `[]` | 0 | `false` | Koi path hi nahi |

---

## 🧠 2. Core Intuition

### Top-Down vs Bottom-Up

Pichhle problems (height, diameter, balanced) **bottom-up** the: children se answer lo, parent me combine karo.

Yahan **top-down** hai: **jaankari neeche ki taraf le jaao** (ab tak ka sum, ya bacha hua target), aur **leaf pe** decide karo.

| | Bottom-Up (104, 110, 543) | Top-Down (112) |
|---|---|---|
| Info ka flow | Children → Parent (return value) | Parent → Children (parameter) |
| Decision kahan | Parent pe | **Leaf pe** |

### Technique A: Sum accumulate

```
helper(node, sumSoFar):
    sumSoFar += node.val
    if leaf: return sumSoFar == target
    return helper(left, sumSoFar) || helper(right, sumSoFar)
```

### Technique B: Target ghatao (cleaner)

Har step pe "mujhe ab kitna aur chahiye" track karo.

```
hasPathSum(node, remaining):
    if leaf: return remaining == node.val
    return hasPathSum(left, remaining - node.val) || hasPathSum(right, remaining - node.val)
```

Isme alag `target` variable ya helper ki zarurat nahi.

---

## 💻 3. Solution 1: Accumulate Sum (aapka pehla version)

```cpp
class Solution {
public:
    int target;
    bool helper(TreeNode* root, int sum) {
        if (root == NULL) {
            return false;                       // NULL leaf nahi, path nahi
        }
        sum += root->val;                       // ab tak ka sum
        if (root->left == NULL && root->right == NULL) {
            return sum == target;               // leaf pe decide
        }
        bool lans = helper(root->left, sum);
        bool rans = helper(root->right, sum);
        return lans || rans;
    }
    bool hasPathSum(TreeNode* root, int targetSum) {
        target = targetSum;
        return helper(root, 0);
    }
};
```

### 📝 Is version ke baare me

- ✅ **Member `target` safe hai**, kyunki har `hasPathSum` call pe wo dobara set hota hai. Purani state leak nahi hoti.
- ⚠️ **Early exit nahi hota.** `lans` aur `rans` **dono pehle compute** ho jaate hain, phir `||` lagta hai. Matlab left me path mil bhi jaaye, right bhi poora traverse hota hai.

---

## 💻 4. Solution 2: Target Ghatao (aapka doosra version) ⭐

```cpp
class Solution {
public:
    bool hasPathSum(TreeNode* root, int targetSum) {
        if (root == NULL) {
            return false;
        }
        if (root->left == NULL && root->right == NULL) {
            return targetSum == root->val;                 // leaf: bacha hua target = meri value?
        }
        return hasPathSum(root->left,  targetSum - root->val)
            || hasPathSum(root->right, targetSum - root->val);
    }
};
```

### ✅ Isme kya achha hai

- **Koi helper ya member variable nahi.** Pure function.
- **Short-circuit `||`**: left me `true` mil gaya to right ko dekhta hi nahi. Average case me tez.
- Code chhota aur interview me explain karna aasan.

---

## 🔍 5. Dry Runs

### Solution 2 on tree upar wale, `targetSum = 22`

| Call | `targetSum` | Leaf? | Action |
|---|---|---|---|
| `has(5, 22)` | 22 | Nahi | left: `has(4, 17)` |
| `has(4, 17)` | 17 | Nahi | left: `has(11, 13)` |
| `has(11, 13)` | 13 | Nahi | left: `has(7, 2)` |
| `has(7, 2)` | 2 | **Haan** | `2 == 7`? **false** |
| `has(2, 2)` (11 ka right) | 2 | **Haan** | `2 == 2` ✔ **true** |
| `has(11, 13)` | | | `false \|\| true` = **true** |
| `has(4, 17)` | | | `true` (right skip) |
| `has(5, 22)` | | | `true` (right skip) ✅ |

> 💡 Dekho: right subtree (`8`) kabhi visit hi nahi hua. Yeh short-circuit ka fayda hai.

### Solution 1 (same tree): `sum` ka flow

`5 → 9 → 20 → 27` (leaf 7: `27 != 22`) · `5 → 9 → 20 → 22` (leaf 2: `22 == 22` ✔)

---

## 🚨 6. Traps (Zaroor yaad rakho)

### Trap 1: Leaf ka matlab dono children NULL

```
    1
   /
  2        targetSum = 1
```

Root `1` ka sum 1 hai, par root **leaf nahi** (left child hai). Valid path `1 → 2` hai, jiska sum 3. Answer **`false`**.

❌ Galat: `if (root == NULL) return sum == target;`
Isse `1`'s right child (NULL) pe sum 1 match ho jaayega aur galat `true` aayega.

✅ Sahi: **NULL pe hamesha false**, aur check **leaf pe** (dono children NULL).

### Trap 2: Negative values

Values negative ho sakti hain, to `sum > target` hone pe **prune mat karo**. Aage koi negative node sum ko wapas kam kar sakta hai.

### Trap 3: Empty tree

`root == NULL` aur `targetSum == 0` ho tab bhi answer **`false`** (koi path hi nahi hai).

---

## 💻 7. Solution 3: Iterative DFS (Stack)

```cpp
bool hasPathSum(TreeNode* root, int targetSum) {
    if (!root) return false;
    stack<pair<TreeNode*, int>> st;
    st.push({root, root->val});                         // (node, ab tak ka sum)
    while (!st.empty()) {
        pair<TreeNode*, int> cur = st.top();
        st.pop();
        TreeNode* node = cur.first;
        int sum = cur.second;
        if (!node->left && !node->right && sum == targetSum) {
            return true;                                // leaf pe match
        }
        if (node->right) st.push({node->right, sum + node->right->val});
        if (node->left)  st.push({node->left,  sum + node->left->val});
    }
    return false;
}
```

(BFS ke liye `stack` ki jagah `queue` use karo, logic same.)

---

## ⏱️ 8. Complexity

| Approach | Time | Space |
|---|---|---|
| Recursive (Sol 1) | O(n) | O(h) |
| Recursive (Sol 2) | O(n), aksar kam (early exit) | O(h) |
| Iterative DFS | O(n) | O(h) |

- Balanced: `h = log n`. Skewed: `h = n`.
- Sum ka range: max `5000 × 1000 = 5×10⁶`, `int` me safe.

---

## ⚠️ 9. Common Mistakes

1. **NULL pe sum match karke `true` return karna.** Leaf check zaruri hai (Trap 1).
2. **Internal node pe hi sum match hone pe `true` de dena.** Path **leaf** pe khatam hona chahiye.
3. **Negative values ke bawajood pruning karna.**
4. **`||` ki jagah `&&` likhna.** Koi bhi ek path kaafi hai.
5. **Empty tree pe `targetSum == 0` ko `true` maan lena.**
6. **Subtract karte waqt galat order** (`root->val - targetSum`).

---

## 🧪 10. Edge Cases Checklist

- [ ] Empty tree → `false`
- [ ] Single node, `val == target` → `true`
- [ ] Single node, `val != target` → `false`
- [ ] Root non-leaf par `sum == target` (par path leaf tak nahi) → `false`
- [ ] Negative values (`[-2,null,-3]`, target `-5` → `true`)
- [ ] Sum zero wale paths (`[1,-2,1]` jaise)
- [ ] Skewed tree

---

## 🔗 11. Related Problems

| Problem | Variation |
|---|---|
| **113. Path Sum II** | Saare valid paths **collect** karo (backtracking) |
| **437. Path Sum III** | Path kisi bhi node se shuru/khatam (neeche ki taraf). Prefix sum + hashmap |
| **257. Binary Tree Paths** | Saare root-to-leaf paths strings me |
| **129. Sum Root to Leaf Numbers** | Path ko number banao aur sab ka sum |
| **111. Minimum Depth** | Wahi "leaf" wali trap |
| **124. Binary Tree Maximum Path Sum** | Bottom-up, kisi bhi node se kisi bhi node tak |

> 💡 **Path Sum II (113)** me bas ek `vector<int> path` rakho, node pe push, wapas aate waqt pop (backtrack), aur leaf pe match hone par copy store karo.

---

## 🎯 12. Interview Talking Points

1. *"Root se leaf tak ke paths dekhne hain, to DFS."*
2. *"Har step pe target me se current node ki value ghata deta hu. Leaf pe bacha target node ki value ke barabar hona chahiye."*
3. *"NULL pe false return karta hu, aur leaf (dono children NULL) pe hi match check karta hu."*
4. *"`||` short-circuit se match milte hi ruk jaata hu."*
5. *"Time O(n), space O(h). Negative values ki wajah se pruning nahi kar sakta."*

---

## ✅ 13. Final Revision Checklist

- [ ] Path = root se **leaf** tak
- [ ] Leaf = dono children NULL
- [ ] NULL pe `false`
- [ ] Target ghatane wala trick (`targetSum - root->val`) yaad hai
- [ ] Negative values ki wajah se pruning nahi
- [ ] Top-down vs bottom-up ka farak samajh aaya
- [ ] Dry run (`5→4→11→2`, target 22) bina dekhe kar sakta hu

> **Memory hook:** 🛤️ *"Target ghatate chalo, leaf pe pucho: bacha hua target meri value hai?"*
