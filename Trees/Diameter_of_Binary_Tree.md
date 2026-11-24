# 📏 LeetCode 543 — Diameter of Binary Tree

> **Difficulty:** Easy  |  **Pattern:** Bottom-Up Height + Global Max  |  **Time:** O(n) optimal  |  **Space:** O(h)

---

## 🏷️ Tags

`Tree` · `Binary Tree` · `DFS` · `Recursion` · `Height` · `Global Variable` · `Optimization (O(n²) → O(n))`

---

## ⚡ 30-Second Recap

| Cheez | Yaad rakhne wali baat |
|---|---|
| Diameter | Tree ke **do kisi bhi nodes ke beech sabse lamba path** (**edges** me ginti) |
| Path root se **guzre ya na guzre** | Zaroori nahi ki root se guzre ⚠️ |
| Kisi node pe longest path | `height(left) + height(right)` |
| Answer | Saare nodes pe is value ka **maximum** |
| Top-down (brute) | Har node pe `height()` call → **O(n²)** worst |
| Bottom-up (optimal) | Height nikalte hue hi `lh + rh` update → **O(n)** |
| 🔑 Core insight | **Return** kuch aur hota hai (height), **record** kuch aur (lh + rh) |

**One-liner:** *"Har node ko 'bend point' maano: wahan se guzarne wala sabse lamba path = lh + rh. Saare nodes me se max lo."*

---

## 📝 1. Problem Statement

Binary tree ka `root` diya hai. Tree ka **diameter** return karo.

Diameter = tree ke kisi bhi **do nodes ke beech sabse lambe path ki length**. Yeh path **root se guzre bhi sakta hai, nahi bhi**. Length = path ke **edges** ki sankhya.

**Constraints:** nodes ki sankhya `[1, 10⁴]`, `-100 <= Node.val <= 100`

### Examples

```
Example 1: answer = 3                Example 2: answer = 1

        1                                1
       / \                              /
      2   3                            2
     / \
    4   5
```

| Input | Output | Longest path |
|---|---|---|
| `[1,2,3,4,5]` | 3 | `4 → 2 → 1 → 3` (ya `5 → 2 → 1 → 3`) |
| `[1,2]` | 1 | `1 → 2` |

---

## 🧠 2. Core Intuition

### Kisi bhi node pe "bend" wala path

Har lamba path ka ek **sabse upar wala node** hota hai (jahan se path neeche dono taraf jaata hai). Us node pe longest path:

```
path through node = (left taraf ki gehraai) + (right taraf ki gehraai)
                  = height(left) + height(right)
```

(Height yahan nodes me hai, aur node se neeche jaate waqt nodes ki ginti = edges ki ginti. Isliye `lh + rh` seedha **edges** deta hai.)

### ⚠️ Diameter root se guzre, zaroori nahi

```
        1
       /
      2                  Root pe: lh = 3, rh = 0 → 3
     / \
    3   4                Node 2 pe: lh = 2, rh = 2 → 4 ✔ (yehi diameter hai)
   /     \
  5       6              Path: 5 → 3 → 2 → 4 → 6  (4 edges)
```

Isliye **har node** pe `lh + rh` nikalna padta hai aur max rakhna padta hai.

### 3 cases (aapke comments se)

Diameter (longest path) ya to:
1. **Root se guzre:** `lh + rh`
2. **Left subtree ke andar** poori ho
3. **Right subtree ke andar** poori ho

---

## 💻 3. Solution 1: Top-Down (aapka pehla version)

```cpp
class Solution {
public:
    int max_height = 0;
    int height(TreeNode* root) {
        if (root == NULL) {
            return 0;
        }
        int lh = height(root->left);
        int rh = height(root->right);
        return max(lh, rh) + 1;
    }
    int diameterOfBinaryTree(TreeNode* root) {
        if (root == NULL) {
            return 0;
        }
        // Case 1: path root se guzre
        int lh = height(root->left);
        int rh = height(root->right);
        max_height = max(max_height, lh + rh);

        // Case 2: path left subtree me
        diameterOfBinaryTree(root->left);

        // Case 3: path right subtree me
        diameterOfBinaryTree(root->right);

        return max_height;
    }
};
```

### ✅ Sahi hai kyunki

Teeno cases cover hote hain, aur har node pe root-through-path check ho jaata hai.

### ⚠️ Par slow: O(n²) worst

Har node pe `height()` poora subtree traverse karta hai, phir children pe wahi dobara. Skewed tree me `n + (n-1) + ... = O(n²)`. (Balanced tree me O(n log n).) **Balanced Binary Tree (LC 110) wali exact problem.**

---

## 🚀 4. Solution 2: Bottom-Up, O(n) (aapka doosra version)

### Idea

Height nikalte hue **saath me hi** har node pe `lh + rh` ko global max me update karo. Ek hi pass.

```cpp
class Solution {
public:
    int max_diameter = 0;
    int height(TreeNode* root) {
        if (root == NULL) {
            return 0;
        }
        int lh = height(root->left);
        int rh = height(root->right);
        max_diameter = max(max_diameter, lh + rh);   // 📝 record: is node pe bend wala path
        return max(lh, rh) + 1;                      // 📤 return: parent ko height
    }
    int diameterOfBinaryTree(TreeNode* root) {
        height(root);
        return max_diameter;
    }
};
```

### 🔑 Return vs Record (sabse important insight)

| | Kya | Kyun |
|---|---|---|
| **Return karte hain** | `1 + max(lh, rh)` | Parent ko sirf **ek taraf ka** lamba chain chahiye (path aage badhne ke liye) |
| **Record karte hain** | `lh + rh` | Is node pe **dono taraf** ka path (bend), jo **upar extend nahi** ho sakta |

Agar parent ko `lh + rh` bhej do, to path "Y" shape ban jaayega (do branches), jo valid path nahi hai. Isliye alag-alag.

---

## 🔍 5. Dry Runs

### Example 1: `[1,2,3,4,5]` → `3`

| Call | lh | rh | `lh + rh` | `max_diameter` | Return (height) |
|---|---|---|---|---|---|
| `height(4)` | 0 | 0 | 0 | 0 | 1 |
| `height(5)` | 0 | 0 | 0 | 0 | 1 |
| `height(2)` | 1 | 1 | 2 | **2** | 2 |
| `height(3)` | 0 | 0 | 0 | 2 | 1 |
| `height(1)` | 2 | 1 | 3 | **3** | 3 |

Answer = **3** ✅

### Example 3 (root se na guzre): neeche wala tree

```
        1
       /
      2
     / \
    3   4
   /     \
  5       6
```

| Call | lh | rh | `lh + rh` | `max_diameter` | Return |
|---|---|---|---|---|---|
| `height(5)`, `height(6)` | 0 | 0 | 0 | 0 | 1 |
| `height(3)` | 1 | 0 | 1 | 1 | 2 |
| `height(4)` | 0 | 1 | 1 | 1 | 2 |
| `height(2)` | 2 | 2 | **4** | **4** | 3 |
| `height(1)` | 3 | 0 | 3 | 4 (nahi badla) | 4 |

Answer = **4** ✅ (root pe sirf 3 aata, galat hota)

---

## ⏱️ 6. Complexity

| Approach | Time | Space |
|---|---|---|
| Top-down (Sol 1) | O(n²) worst, O(n log n) balanced | O(h) |
| **Bottom-up (Sol 2)** | **O(n)** | O(h) |

- Bottom-up me har node **exactly ek baar** visit hota hai.

---

## 🛡️ 7. Member Variable ka Risk (aur safe version)

Aapke dono solutions me `max_height` / `max_diameter` **member variable** hai. LeetCode har test ke liye naya `Solution` object banata hai, isliye pass ho jaata hai. Lekin **same object pe doosri baar call** karoge to pichhla max bacha rahega aur chhote tree pe galat (bada) answer aayega.

### ✅ Safer: reference ke saath helper

```cpp
class Solution {
public:
    int diameterOfBinaryTree(TreeNode* root) {
        int diameter = 0;
        height(root, diameter);
        return diameter;
    }
private:
    int height(TreeNode* node, int& diameter) {
        if (!node) return 0;
        int lh = height(node->left, diameter);
        int rh = height(node->right, diameter);
        diameter = max(diameter, lh + rh);
        return 1 + max(lh, rh);
    }
};
```

Har call ka apna fresh `diameter` hai. (Ya member variable ko `diameterOfBinaryTree` ke shuru me `0` pe reset karo.)

---

## 🔁 8. "Height + Global Answer" Pattern

Yeh template tab use hota hai jab **har node pe ek local answer** banta ho, par **parent ko sirf ek-taraf wala chain** bhejna ho:

| Problem | Return (parent ko) | Record (global) |
|---|---|---|
| **543. Diameter** | `1 + max(lh, rh)` | `lh + rh` |
| **124. Max Path Sum** | `node + max(0, max(l, r))` | `node + max(0,l) + max(0,r)` |
| **687. Longest Univalue Path** | Same-value chain length | Dono side ka chain sum |
| **110. Balanced Binary Tree** | Height (ya `-1`) | Imbalance flag |

---

## ⚠️ 9. Common Mistakes

1. **Sirf root pe `lh + rh` check karna.** Diameter root se na guzre to galat answer.
2. **Nodes aur edges me confusion.** LeetCode **edges** maangta hai. Agar nodes maange to `+ 1`.
3. **Parent ko `lh + rh` return karna.** Return height hona chahiye, "Y" path nahi.
4. **Top-down me O(n²) pe ruk jaana.** Optimize karna chahiye.
5. **Member variable reset na karna** (same object reuse karne par).
6. **Empty tree ka case.** (Is problem me `n ≥ 1`, par general me `0` return hota.)

---

## 🧪 10. Edge Cases Checklist

- [ ] Single node → `0`
- [ ] Do nodes (`[1,2]`) → `1`
- [ ] Skewed tree (chain) → `n - 1`
- [ ] Diameter root se **na guzre** (upar wala example)
- [ ] Perfect binary tree → `2 × (h - 1)`
- [ ] Ek side bahut gehri, doosri side khaali

---

## 🔗 11. Related Problems

| Problem | Link in concept |
|---|---|
| **110. Balanced Binary Tree** | Same height-based template, `-1` sentinel |
| **104. Maximum Depth of Binary Tree** | Height ka base |
| **124. Binary Tree Maximum Path Sum** | Diameter ka weighted (values wala) version |
| **687. Longest Univalue Path** | Diameter, par sirf same-value nodes |
| **1522. Diameter of N-ary Tree** | Top 2 child heights ka sum |
| **563. Binary Tree Tilt** | Subtree sums ke saath same pattern |

---

## 🎯 12. Interview Talking Points

1. *"Kisi bhi node pe longest path `height(left) + height(right)` hota hai."*
2. *"Diameter root se guzre, zaroori nahi, isliye har node pe check karta hu."*
3. *"Pehle simple approach: har node pe `height()`. O(n²) worst."*
4. *"Optimize: height nikalte hue hi global max update karta hu. O(n)."*
5. *"Parent ko height return karta hu (ek chain), aur global me `lh + rh` record karta hu (bend path)."*
6. *"Yehi pattern max path sum jaise problems me kaam aata hai."*

> 💡 **Interview strategy:** Aapka pehla solution (top-down) batao, phir bolo *"yeh O(n²) hai, ek pass me kar sakte hain"* aur doosra solution dikhao.

---

## ✅ 13. Final Revision Checklist

- [ ] Node pe longest path = `lh + rh`
- [ ] Diameter root se na guzre, iska example yaad hai
- [ ] Return (height) vs Record (`lh + rh`) ka farak samajh aaya
- [ ] Top-down kyun O(n²) hai, aur bottom-up kaise O(n)
- [ ] Edges me ginti (nodes me nahi)
- [ ] Dry run `[1,2,3,4,5]` → `3` bina dekhe kar sakta hu

> **Memory hook:** 📏 *"Har node pe bend ka path = lh + rh (record). Parent ko sirf height bhejo (return)."*
