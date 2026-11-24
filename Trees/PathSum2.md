# 🌳 LeetCode 113: Path Sum II

## 📌 Problem Statement
Ek Binary Tree ka `root` aur ek integer `targetSum` diya gaya hai. Humein **root-to-leaf** tak ke saare aise paths return karne hain jinke nodes ka sum `targetSum` ke barabar ho.
* **Note:** Leaf node wo hota hai jiske koi children nahi hote (`left == NULL && right == NULL`).

---

## 💡 Intuition & Approach (DFS + Recursion)
1. Hum **Top-Down DFS (Preorder Traversal)** use karenge: `Node -> Left -> Right`.
2. Har step par current node ki value ko running `sum` me add karenge aur current `path` (`temp` vector) me push karenge.
3. Jaise hi kisi **Leaf Node** par pahunchenge, check karenge ki `sum == targetSum` hai ya nahi. Agar hai, toh `temp` ko `ans` me store kar lenge.
4. Agar leaf nahi hai, toh pehle left subtree aur phir right subtree explore karenge.

---

## 💻 C++ Code (Pass-by-Value Approach — Aapka Working Code)
Is approach me `temp` ko **by-value** (`vector<int> temp`) pass kiya gaya hai, isliye har recursive call ke paas apni alag copy hoti hai aur manual `pop_back()` ki zaroorat nahi padti.

```cpp
class Solution {
public:
    vector<vector<int>> ans;

    void check(TreeNode* root, int sum, vector<int> temp, int& targetSum) {
        if (root == NULL) return;

        // 1. Current node ko sum aur path me include karein
        sum += root->val;
        temp.push_back(root->val);

        // 2. Leaf node check karein
        if (root->left == NULL && root->right == NULL) {
            if (sum == targetSum) {
                ans.push_back(temp);
            }
            return;
        }

        // 3. Left aur Right subtree explore karein
        check(root->left, sum, temp, targetSum);
        check(root->right, sum, temp, targetSum);
    }

    vector<vector<int>> pathSum(TreeNode* root, int targetSum) {
        vector<int> temp;
        check(root, 0, temp, targetSum);
        return ans;
    }
};
```

---

## ⚡ Interview Optimization (Pass-by-Reference + Backtracking)
Upar wala code bilkul sahi kaam karta hai, lekin har recursive call par `vector<int> temp` copy hota hai. Interview me **Space/Time optimize** karne ke liye `vector<int>& temp` (by-reference) aur `pop_back()` use karna best maana jata hai:

```cpp
void check(TreeNode* root, int sum, vector<int>& temp, int targetSum) {
    if (!root) return;

    sum += root->val;
    temp.push_back(root->val);

    if (!root->left && !root->right && sum == targetSum) {
        ans.push_back(temp);
    } else {
        check(root->left, sum, temp, targetSum);
        check(root->right, sum, temp, targetSum);
    }

    temp.pop_back(); // Backtrack: wapas parent par jaate waqt current node hatayein
}
```

---

## 📊 Complexity Analysis

| Metric | Pass-by-Value (Current) | Pass-by-Reference (Backtracking) |
| :--- | :--- | :--- |
| **Time Complexity** | $O(N^2)$ (har node par vector copy hone ki wajah se) | $O(N^2)$ worst-case (jab saare valid paths copy hon) |
| **Space Complexity** | $O(H^2)$ (har stack frame me vector ki copy) | $O(H)$ (sirf recursion stack + ek single `temp` vector) |

*(Yahan $N$ = total nodes, $H$ = height of the tree)*

---

## ⚠️ Common Mistakes to Avoid (Revision Checklist)
- [ ] **Leaf Check se pehle Add karna:** `sum += root->val` aur `temp.push_back()` hamesha leaf check se **pehle** hona chahiye, warna leaf node ki value chhoot jayegi.
- [ ] **Negative Numbers:** Tree me negative values ho sakti hain (e.g., `targetSum = -5`), isliye beech raste me `if (sum > targetSum) return;` jaisi pruning **nahi** karni hai.
- [ ] **Strict Leaf Condition:** Sirf `root == NULL` par sum check mat karna, warna ek hi path do baar (`left == NULL` aur `right == NULL` ke liye) add ho jayega. Hamesha `root->left == NULL && root->right == NULL` check karein.
