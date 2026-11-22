# ⚖️ LeetCode 110 — Balanced Binary Tree

> **Difficulty:** Easy  |  **Pattern:** Bottom-Up Recursion (Height + Early Exit Sentinel)  |  **Time:** O(n) optimal  |  **Space:** O(h)

---

## 🏷️ Tags

`Tree` · `Binary Tree` · `DFS` · `Recursion` · `Height` · `Bottom-Up` · `Optimization (O(n²) → O(n))`

---

## ⚡ 30-Second Recap

| Cheez | Yaad rakhne wali baat |
|---|---|
| Balanced ki definition | **Har node** pe `|height(left) - height(right)| <= 1` |
| ⚠️ Zaruri baat | **Sirf root** nahi, **har node** check hota hai |
| Top-down (brute) | Har node pe `height()` call + recurse → **O(n²)** worst |
| Bottom-up (optimal) | Height nikalte hue hi balance check → **O(n)** |
| Sentinel trick | Imbalance mile to **`-1`** return karo, upar tak propagate karo |
| Final | `check(root) != -1` |

**One-liner:** *"Height return karo, agar kahin imbalance mila to -1 return karo, aur -1 ko upar tak failne do."*

---

## 📝 1. Problem Statement

Binary tree ka `root` diya hai. Check karo ki woh **height-balanced** hai ya nahi.

**Height-balanced** = tree jisme **har node** ke left aur right subtrees ki height ka difference **1 se zyada nahi** hota.

**Constraints:** nodes ki sankhya `[0, 5000]`, `-10⁴ <= Node.val <= 10⁴`

### Examples

```
Example 1: true                  Example 2: false

        3                                1
       / \                              / \
      9   20                           2   2
         /  \                         / \
        15   7                       3   3
                                    / \
                                   4   4
```

| Input (level order) | Output |
|---|---|
| `[3,9,20,null,null,15,7]` | `true` |
| `[1,2,2,3,3,null,null,4,4]` | `false` |
| `[]` | `true` |

---

## 🧠 2. Core Intuition

### Balanced ka matlab: har node pe check

Sirf root pe check karna kaafi **nahi** hai. Root balanced dikh sakta hai, par neeche koi subtree imbalanced ho sakta hai.

```
        r
       / \
      a   d          Root: left height = 3, right height = 3 → diff 0 ✔
     /     \         Par node 'a': left height = 2, right height = 0 → diff 2 ✘
    b       e        Isliye poora tree NOT balanced
   /         \
  c           f
```

### Height ka formula (LC 104 se)

```
height(node) = 1 + max(height(left), height(right)),   NULL → 0
```

---

## 💻 3. Solution 1: Top-Down (aapka version)

```cpp
class Solution {
public:
    int height(TreeNode* root) {
        if (root == NULL) {
            return 0;
        }
        int lh = height(root->left);
        int rh = height(root->right);
        return 1 + max(lh, rh);
    }
    bool isBalanced(TreeNode* root) {
        if (root == NULL) {
            return true;
        }
        int lh = height(root->left);
        int rh = height(root->right);
        if (abs(rh - lh) > 1) {
            return false;                                    // is node pe imbalance
        }
        return isBalanced(root->left) && isBalanced(root->right);   // neeche bhi check
    }
};
```

### ✅ Sahi hai kyunki

- Har node pe balance condition check hoti hai.
- Subtrees pe recursion se **saare nodes** cover ho jaate hain.

### ⚠️ Par slow kyun hai? (O(n²))

`isBalanced(node)` me `height()` call hota hai jo us **poore subtree** (size `s`) ko traverse karta hai. Phir `isBalanced` children pe jaake wahi kaam dobara karta hai. **Neeche ke nodes ki height baar-baar calculate hoti hai.**

| Tree type | Time |
|---|---|
| Balanced | O(n log n) |
| Skewed (worst) | `n + (n-1) + (n-2) + ... = O(n²)` |

`n ≤ 5000` hai, to chal jaata hai, par interview me interviewer **"optimize karo"** zaroor bolega.

---

## 🚀 4. Solution 2: Bottom-Up, O(n) (Optimal)

### Idea

Height nikalte hue **saath me hi** balance check kar lo. Ek hi pass me kaam khatam.

- Agar subtree **balanced** hai → uski **height** return karo (`>= 0`).
- Agar subtree **imbalanced** hai → **`-1`** return karo (sentinel).
- Jahan bhi `-1` mile, turant `-1` upar propagate karo.

Height kabhi negative nahi hoti, to `-1` ek safe "invalid" signal hai.

```cpp
class Solution {
public:
    int check(TreeNode* root) {
        if (!root) return 0;                       // NULL ki height 0

        int lh = check(root->left);
        if (lh == -1) return -1;                   // left me imbalance, turant bahar

        int rh = check(root->right);
        if (rh == -1) return -1;                   // right me imbalance, turant bahar

        if (abs(lh - rh) > 1) return -1;           // is node pe imbalance

        return 1 + max(lh, rh);                    // balanced, height return
    }
    bool isBalanced(TreeNode* root) {
        return check(root) != -1;
    }
};
```

### Do kaam ek saath

| `check(node)` ka return | Matlab |
|---|---|
| `-1` | Is subtree me kahin imbalance hai |
| `>= 0` | Subtree balanced hai, aur yeh uski height hai |

---

## 🔍 5. Dry Runs

### Example 1: `[3,9,20,null,null,15,7]` → `true`

| Call | lh | rh | Diff | Return |
|---|---|---|---|---|
| `check(9)` | 0 | 0 | 0 | 1 |
| `check(15)` | 0 | 0 | 0 | 1 |
| `check(7)` | 0 | 0 | 0 | 1 |
| `check(20)` | 1 | 1 | 0 | 2 |
| `check(3)` | 1 | 2 | 1 ✔ | 3 |

`3 != -1` → **true** ✅

### Example 2: `[1,2,2,3,3,null,null,4,4]` → `false`

| Call | lh | rh | Diff | Return |
|---|---|---|---|---|
| `check(4)` (dono leaf) | 0 | 0 | 0 | 1 |
| `check(3)` (left, 2 children) | 1 | 1 | 0 | 2 |
| `check(3)` (right, leaf) | 0 | 0 | 0 | 1 |
| `check(2)` (left) | 2 | 1 | 1 ✔ | 3 |
| `check(2)` (right, leaf) | 0 | 0 | 0 | 1 |
| `check(1)` (root) | 3 | 1 | **2** ✘ | **-1** |

`-1` → **false** ✅

(Top-down me bhi root pe hi pakda jaata: `height(left)=3`, `height(right)=1`, diff `2 > 1`.)

---

## ⏱️ 6. Complexity

| Approach | Time | Space |
|---|---|---|
| Top-down (aapka) | O(n²) worst, O(n log n) balanced | O(h) |
| **Bottom-up (-1 sentinel)** | **O(n)** | O(h) |

- Bottom-up me har node **exactly ek baar** visit hota hai.
- Imbalance milte hi **early exit** (baaki tree skip).

---

## 🧩 7. Teen Tareeke Bottom-Up Info Return Karne ke

| Technique | Kaise |
|---|---|
| **Sentinel (`-1`)** | Height ke saath invalid signal ek hi int me (upar wala) |
| **Pair return** | `pair<bool, int>` (balanced?, height) |
| **Global/reference flag** | `bool& ok` pass karo, imbalance pe `false` kar do |

```cpp
// Reference flag version
int dfs(TreeNode* root, bool& ok) {
    if (!root || !ok) return 0;
    int lh = dfs(root->left, ok);
    int rh = dfs(root->right, ok);
    if (abs(lh - rh) > 1) ok = false;
    return 1 + max(lh, rh);
}
bool isBalanced(TreeNode* root) {
    bool ok = true;
    dfs(root, ok);
    return ok;
}
```

---

## 🔁 8. "Height + Extra Info" Pattern (Bahut common)

Yeh template tab use hota hai jab height nikalte hue **kuch aur bhi track** karna ho:

| Problem | Height ke saath kya track kare |
|---|---|
| **110. Balanced Binary Tree** | Imbalance flag (`-1`) |
| **543. Diameter of Binary Tree** | `lh + rh` ka global max |
| **124. Binary Tree Maximum Path Sum** | Max path sum ka global max |
| **563. Binary Tree Tilt** | `|sum(left) - sum(right)|` ka total |

---

## ⚠️ 9. Common Mistakes

1. **Sirf root pe height diff check karna.** Neeche ke nodes bhi check karne hain.
2. **Top-down me `height()` baar-baar call** karke O(n²) bana dena (optimize na karna).
3. **Bottom-up me `-1` propagate na karna.** `-1` ko normal height ki tarah use kar liya to galat answer.
4. **`abs(lh - rh) > 1` ki jagah `>= 1` likhna.** Diff 1 allowed hai.
5. **Empty tree ko `false` return karna.** Empty tree balanced hai (`true`).
6. **Sirf left aur right subtrees ko alag-alag balanced check karke root ka diff bhool jaana.**

---

## 🧪 10. Edge Cases Checklist

- [ ] Empty tree → `true`
- [ ] Single node → `true`
- [ ] Root ke sirf ek child (diff 1) → `true`
- [ ] Root ke ek side 2-level chain (diff 2) → `false`
- [ ] Root balanced, par neeche koi subtree imbalanced → `false`
- [ ] Perfect binary tree → `true`
- [ ] Skewed tree (3+ nodes) → `false`

---

## 🔗 11. Related Problems

| Problem | Link in concept |
|---|---|
| **104. Maximum Depth of Binary Tree** | Height ka base |
| **111. Minimum Depth of Binary Tree** | Leaf wali trap |
| **543. Diameter of Binary Tree** | Height + global max |
| **124. Binary Tree Maximum Path Sum** | Height jaisa recursion, global max |
| **108. Convert Sorted Array to BST** | Balanced BST **banana** |
| **1382. Balance a Binary Search Tree** | Unbalanced BST ko balanced banana |

---

## 🎯 12. Interview Talking Points

1. *"Balanced ka matlab har node pe height diff <= 1, sirf root pe nahi."*
2. *"Pehle simple approach: har node pe `height()` nikalo aur recurse. Yeh O(n²) worst case hai."*
3. *"Optimize: bottom-up, height ke saath balance check. Imbalance pe -1 return karke upar propagate."*
4. *"Ab har node ek hi baar visit hota hai, O(n) time, O(h) space."*
5. *"Yehi 'height + extra info' pattern diameter aur max path sum me bhi chalta hai."*

> 💡 **Interview strategy:** Pehle apna top-down solution batao (correct hai), phir khud bolo *"isko O(n) me optimize kar sakte hain"* aur bottom-up dikhao. Yeh strong signal hai.

---

## ✅ 13. Final Revision Checklist

- [ ] Balanced = **har node** pe diff <= 1
- [ ] Top-down kyun O(n²) hai, samajh aaya
- [ ] Bottom-up `-1` sentinel trick yaad hai
- [ ] Imbalance milte hi turant `-1` return (early exit)
- [ ] Empty tree = `true`
- [ ] Dry run example 2 (`[1,2,2,3,3,null,null,4,4]` → `false`) bina dekhe kar sakta hu

> **Memory hook:** ⚖️ *"Height return karo, imbalance mile to -1, aur -1 ko upar tak jaane do."*
