# 🌲 LeetCode 257: Binary Tree Paths

## 📌 Problem Statement
Ek Binary Tree ka `root` diya gaya hai. Humein **root-to-leaf** tak ke saare paths kisi bhi order me return karne hain, jahan har path `"1->2->5"` format ki string ho.
* **Leaf Node:** Wo node jiske dono children `NULL` hote hain (`left == NULL && right == NULL`).

---

## 🎯 Pattern Recognition & Triggering Points
Interview ya coding test me is pattern ko kaise pehchanein?

* **Triggering Keywords:** *"All root-to-leaf paths"*, *"Return all paths as strings"*, *"Find all combinations from top to bottom"*.
* **Core Pattern:** **Top-Down DFS (Preorder Traversal: `Node -> Left -> Right`) + Path Construction**.
* **Why Top-Down?** Kyunki path hamesha `root` se shuru hota hai aur niche `leaf` ki taraf badhta hai. Humein parent se mili hui state (ab tak ka path) ko children tak pass karna hota hai.
* **Sibling Problems (Same Pattern):**
  * LeetCode 112: *Path Sum*
  * LeetCode 113: *Path Sum II*
  * LeetCode 129: *Sum Root to Leaf Numbers*
  * LeetCode 988: *Smallest String Starting From Leaf*

---

## 💡 Intuition & Core Understanding
1. **Path Building:** Jab hum kisi node par pahunchte hain, toh sabse pehle us node ki value (`root->val`) ko string me convert karke current path `s` me jodte hain.
2. **Decision Point (Leaf vs Non-Leaf):**
   * **Agar Leaf Node hai:** Path poora ho chuka hai! Iske aage `"->"` lagane ki zaroorat nahi hai. Seedha `ans` vector me `s` ko push karein aur return kar jayein.
   * **Agar Leaf Node NAHI hai:** Iska matlab rasta abhi aage jayega (left ya right). Isliye current node ke baad `"->"` append karein aur dono children ko call lagayein.
3. **State Isolation (Pass-by-Value Magic):** Jab hum `string s` ko **by-value** pass karte hain, toh Left subtree me ki gayi changes Right subtree ko affect nahi karti—har branch ko apna fresh path milta hai.

---

## 🔍 Step-by-Step Dry Run (Explanation)
Maan lijiye tree hai: `Root = 1`, `Left = 2` (jiska `Right = 5`), `Right = 3`.

1. **`fillS(1, "")`**
   * `s` becomes `"1"`
   * Leaf nahi hai → `s` becomes `"1->"`
   * Calls `fillS(2, "1->")` and `fillS(3, "1->")`
2. **`fillS(2, "1->")`**
   * `s` becomes `"1->2"`
   * Leaf nahi hai → `s` becomes `"1->2->"`
   * Calls `fillS(NULL, "1->2->")` (returns immediately)
   * Calls `fillS(5, "1->2->")`
3. **`fillS(5, "1->2->")`**
   * `s` becomes `"1->2->5"`
   * **Leaf Node!** (`left == NULL && right == NULL`) → `ans.push_back("1->2->5")` and returns.
4. **`fillS(3, "1->")`** (Iske paas abhi bhi `"1->"` hi hai!)
   * `s` becomes `"1->3"`
   * **Leaf Node!** → `ans.push_back("1->3")` and returns.
5. **Final Output:** `["1->2->5", "1->3"]`

---

## 💻 Properly Documented C++ Code

### Approach 1: Pass-by-Value (Clean & Easy to Write)
Isme har recursive call par string ki nayi copy banti hai, isliye manual backtracking ki zaroorat nahi padti.

```cpp
class Solution {
public:
    vector<string> ans;

    /**
     * @brief Performs Preorder DFS to build root-to-leaf path strings.
     * @param root Current tree node being visited.
     * @param s Path string accumulated from root up to parent of current node (passed by value).
     */
    void fillS(TreeNode* root, string s) {
        // Base Case: Empty node par kuch nahi karna
        if (root == NULL) {
            return;
        }

        // Step 1: Current node ki integer value ko string me convert karke append karein
        s.append(to_string(root->val));

        // Step 2: Check karein ki kya ye Leaf Node hai
        if (root->left == NULL && root->right == NULL) {
            ans.push_back(s); // Complete path store karein (bina trailing "->")
            return;
        }

        // Step 3: Non-leaf node ke liye arrow lagayein aur children explore karein
        s.append("->");
        fillS(root->left, s);
        fillS(root->right, s);
    }

    vector<string> binaryTreePaths(TreeNode* root) {
        string s = "";
        fillS(root, s);
        return ans;
    }
};
```

### Approach 2: Pass-by-Reference + Backtracking (Interview Space Optimization)
Agar interviewer string copying ka overhead bachane ko bole, toh `string& s` use karke `s.resize(len)` se backtrack karein:

```cpp
class Solution {
public:
    vector<string> ans;

    void fillS(TreeNode* root, string& s) {
        if (!root) return;

        int len = s.size(); // Backtracking ke liye purani length save karein
        s.append(to_string(root->val));

        if (!root->left && !root->right) {
            ans.push_back(s);
        } else {
            s.append("->");
            fillS(root->left, s);
            fillS(root->right, s);
        }

        s.resize(len); // Backtrack: added number aur "->" dono ek sath hatayein
    }

    vector<string> binaryTreePaths(TreeNode* root) {
        string s = "";
        fillS(root, s);
        return ans;
    }
};
```

---

## 📊 Code & Complexity Analysis

| Parameter | Approach 1 (Pass-by-Value) | Approach 2 (Pass-by-Reference) |
| :--- | :--- | :--- |
| **Time Complexity** | **$O(N^2)$** — Har node par string copy hoti hai (skewed tree me path length $O(N)$ tak ja sakti hai). | **$O(N^2)$** — Worst-case me saare $O(N)$ leaf paths ko `ans` me copy karne me $O(N)$ lagta hai. |
| **Auxiliary Space** | **$O(H^2)$** — Recursion stack depth $H$ aur har frame me $O(H)$ length ki string copy. | **$O(H)$** — Sirf recursion call stack aur ek single mutable string `s`. |

*(Yahan $N$ = Total nodes in tree, $H$ = Height of tree; Worst-case skewed tree me $H = N$, Balanced tree me $H = \log N$).*

---

## ⚠️ Edge Cases & Code Analysis
* **Empty Tree (`root == NULL`):** Base case turant return karega aur empty vector `[]` return hoga.
* **Single Node Tree (`[1]`):** `s` me `"1"` append hoga, leaf condition true hogi, aur `["1"]` return hoga (bina `"->"` ke).
* **Negative & Multi-Digit Values (`-100`):** `to_string(root->val)` negative sign (`'-'`) aur multiple digits ko automatically handle kar leta hai. Agar single digit trick (`val + '0'`) use karte toh `-100` par fail ho jata!

---

## ✅ Quick Revision Checkpoints (Exam/Interview se 2 Min Pehle)
- [ ] **Order of Operations:** `s.append(to_string(root->val))` hamesha leaf check se **pehle** aayega.
- [ ] **Arrow Placement:** `"->"` hamesha leaf check ke **baad** aur recursive calls se **pehle** lagega, taaki leaf node ke aage arrow na lage.
- [ ] **Int to String:** Hamesha `to_string(val)` use karein kyunki node values negative (`-100` to `100`) aur multi-digit ho sakti hain.
- [ ] **Backtracking Trick (If asked):** String me `pop_back()` ek-ek char hatata hai, lekin `"-100->"` multi-char hota hai—isliye `int len = s.size()` save karke `s.resize(len)` use karna sabse clean backtracking hai.
