# Product of Array Except Self — LeetCode 238

**Pattern:** Prefix & Suffix Product  
**Difficulty:** Medium

## 1. Problem
Har index `i` ke liye array ke baaki sabhi elements ka product return karna hai, bina `nums[i]` ko include kiye.

**Example:**
```cpp
Input:  [1, 2, 3, 4]
Output: [24, 12, 8, 6]
```

## 2. Optimal Approach — Prefix + Suffix

**Prefix:** Current index ke left ke sabhi elements ka product.

**Suffix:** Current index ke right ke sabhi elements ka product.

**Answer:**
`ans[i] = prefix product × suffix product`

Separate prefix/suffix arrays banane ke bajaye `ans` mein hi prefix store karte hain, phir right-to-left traversal mein suffix se multiply karte hain.

## 3. C++ Code

```cpp
class Solution {
public:
    vector<int> productExceptSelf(vector<int>& nums) {
        int n = nums.size();
        vector<int> ans(n, 1);

        // Step 1: Store prefix products
        int pre = 1;

        for (int i = 1; i < n; i++) {
            ans[i] = pre * nums[i - 1];
            pre *= nums[i - 1];
        }

        // Step 2: Multiply suffix products
        int suf = 1;

        for (int i = n - 2; i >= 0; i--) {
            suf *= nums[i + 1];
            ans[i] *= suf;
        }

        return ans;
    }
};
```

## 4. Dry Run

`nums = [1, 2, 3, 4]`

**Prefix pass:**

`ans = [1, 1, 2, 6]`

**Suffix pass:**

- `i = 2`: `suf = 4`, `ans[2] = 2 × 4 = 8`
- `i = 1`: `suf = 12`, `ans[1] = 1 × 12 = 12`
- `i = 0`: `suf = 24`, `ans[0] = 1 × 24 = 24`

**Final:** `[24, 12, 8, 6]`

## 5. Why initialize `ans` with 1?

```cpp
vector<int> ans(n, 1);
```

`1` multiplicative identity hai. Isse prefix aur suffix products ko initialize karna easy hota hai. First index ka left product aur last index ka right product dono `1` hote hain.

## 6. Edge Cases

- **Single element:** `[5]` → `[1]`
- **Zero present:** `[1, 2, 0, 4]` → `[0, 0, 8, 0]`
- **Multiple zeros:** `[0, 2, 0, 4]` → `[0, 0, 0, 0]`
- **Negative values:** `[-1, 2, -3]` → `[-6, 3, -2]`

Zero ko separately handle karne ki zaroorat nahi hai; prefix-suffix approach naturally handle karta hai.

## 7. Complexity

- **Time:** `O(n)` — two traversals.
- **Auxiliary Space:** `O(1)` — output array ko exclude karke.

## 8. Interview Takeaways

- Division use nahi karni; problem ki constraint isse prohibit karti hai.
- Prefix aur suffix ke separate arrays banana unnecessary hai.
- Left-to-right pass prefix products store karta hai.
- Right-to-left pass suffix product maintain karke `ans` update karta hai.
- Ye solution `O(n)` time aur `O(1)` auxiliary space achieve karta hai.

**Revision mantra:** Prefix store karo → suffix maintain karo → answer multiply karke banao.
