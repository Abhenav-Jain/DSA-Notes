# LeetCode 238 — Product of Array Except Self

**Pattern:** Array + Prefix Product + Suffix Product  
**Difficulty:** Medium  
**Importance:** ⭐⭐⭐⭐⭐

---

## 1. Problem Statement

Given an integer array `nums`, return an array `answer` such that:

    answer[i] = product of all elements of nums except nums[i]

### Example

Input:

    nums = [1,2,3,4]

Output:

    [24,12,8,6]

Explanation:

    answer[0] = 2 × 3 × 4 = 24
    answer[1] = 1 × 3 × 4 = 12
    answer[2] = 1 × 2 × 4 = 8
    answer[3] = 1 × 2 × 3 = 6

---

## 2. Important Constraint

We are not supposed to use division.

So this approach is NOT allowed:

    total_product / nums[i]

Also, the expected solution uses:

    O(n) Time
    O(1) Extra Space

The output array itself is not considered extra space.

---

## 3. Main Idea

For every index `i`, we need:

    Product of elements on the LEFT
                    ×
    Product of elements on the RIGHT

So:

    answer[i] = prefix_product × suffix_product

For example:

    nums = [1,2,3,4]

For index 2:

    Left  = 1 × 2 = 2
    Right = 4

    answer[2] = 2 × 4 = 8

---

## 4. Prefix Product

First, traverse from LEFT → RIGHT.

Maintain:

    prefix = product of all elements before current index

For:

    nums = [1,2,3,4]

The prefix contribution becomes:

    index 0 → 1
    index 1 → 1
    index 2 → 1 × 2 = 2
    index 3 → 1 × 2 × 3 = 6

We store this in `product_array`.

After the first traversal:

    product_array = [1,1,2,6]

Notice:

    product_array[i] = product of all elements BEFORE i

---

## 5. Suffix Product

Now traverse from RIGHT → LEFT.

Maintain:

    suffix = product of all elements after current index

For:

    nums = [1,2,3,4]

Initially:

    suffix = 4

At index 2:

    product_array[2] = 2 × 4 = 8

Then update:

    suffix = 4 × 3 = 12

At index 1:

    product_array[1] = 1 × 12 = 12

Then:

    suffix = 12 × 2 = 24

At index 0:

    product_array[0] = 1 × 24 = 24

Final:

    [24,12,8,6]

---

## 6. Core Idea

Every answer is:

    LEFT PRODUCT × RIGHT PRODUCT

So:

    answer[i]
        =
    product of nums[0 ... i-1]
        ×
    product of nums[i+1 ... n-1]

We calculate the left product in the first traversal and multiply the right product during the second traversal.

---

## 7. Dry Run

Input:

    nums = [1,2,3,4]

### First Traversal — Prefix

Initially:

    prefix = 1

At index 0:

    product_array[0] = prefix
                      = 1

    prefix = prefix × nums[0]
           = 1 × 1
           = 1

At index 1:

    product_array[1] = 1

    prefix = 1 × 2
           = 2

At index 2:

    product_array[2] = 2

    prefix = 2 × 3
           = 6

At index 3:

    product_array[3] = 6

Final prefix array:

    [1,1,2,6]

---

### Second Traversal — Suffix

Initially:

    suffix = 1

At index 3:

    product_array[3] = 6 × 1
                      = 6

    suffix = 1 × 4
           = 4

At index 2:

    product_array[2] = 2 × 4
                      = 8

    suffix = 4 × 3
           = 12

At index 1:

    product_array[1] = 1 × 12
                      = 12

    suffix = 12 × 2
           = 24

At index 0:

    product_array[0] = 1 × 24
                      = 24

    suffix = 24 × 1
           = 24

Final Answer:

    [24,12,8,6]

---

## 8. Code

    class Solution {
    public:
        vector<int> productExceptSelf(vector<int>& nums) {

            int n = nums.size();

            vector<int> product_array(n, 1);

            // Prefix Product
            int prefix = nums[0];

            for(int i = 1; i < n; i++){

                product_array[i] = prefix;

                prefix = prefix * nums[i];
            }

            // Suffix Product
            int suffix = nums[n-1];

            for(int j = n-2; j >= 0; j--){

                product_array[j] =
                    product_array[j] * suffix;

                suffix = suffix * nums[j];
            }

            return product_array;
        }
    };

---

## 9. Understanding the Code

### Prefix Part

    int prefix = nums[0];

    for(int i = 1; i < n; i++){

        product_array[i] = prefix;

        prefix = prefix * nums[i];
    }

At index `i`, `prefix` contains:

    Product of all elements before i

So we store:

    product_array[i] = prefix

Then include the current element in the prefix:

    prefix = prefix × nums[i]

---

### Suffix Part

    int suffix = nums[n-1];

    for(int j = n-2; j >= 0; j--){

        product_array[j] =
            product_array[j] * suffix;

        suffix = suffix * nums[j];
    }

At index `j`, `suffix` contains:

    Product of all elements after j

Therefore:

    product_array[j]
        =
    prefix_product × suffix_product

Then we update:

    suffix = suffix × nums[j]

---

## 10. Why Initialize `product_array` With 1?

We use:

    vector<int> product_array(n,1);

because `1` is the multiplicative identity:

    x × 1 = x

Also, for the first element there is no left side:

    Left Product = 1

And for the last element there is no right side:

    Right Product = 1

For:

    nums = [1,2,3,4]

We conceptually have:

    Index 0:
    Left = 1

    Index 3:
    Right = 1

---

## 11. Handling Zeroes

This approach automatically handles zeroes.

Example:

    nums = [1,2,0,4]

Output:

    [0,0,8,0]

Because:

    answer[2] = 1 × 2 × 4
              = 8

Every other position has the zero included in its product.

This is one major advantage over the division approach.

---

## 12. Why Not Use Division?

A tempting solution:

    total = product of all elements

Then:

    answer[i] = total / nums[i]

But this fails when:

    nums[i] = 0

Example:

    nums = [1,2,0,4]

Division by zero is not possible.

The prefix + suffix approach works without division.

---

## 13. Complexity

### Time Complexity

    O(n)

We traverse the array twice:

    Prefix traversal → O(n)
    Suffix traversal → O(n)

Therefore:

    O(n) + O(n) = O(n)

### Space Complexity

    O(1) Extra Space

Only `prefix` and `suffix` variables are used apart from the output array.

The output array:

    product_array

is not considered extra space according to the problem's requirement.

---

## 14. Important Pattern

Whenever you see:

> Product/Sum of all elements except current element

Think:

    LEFT CONTRIBUTION
           +
    RIGHT CONTRIBUTION

For product:

    Prefix Product × Suffix Product

For sum:

    Prefix Sum + Suffix Sum

---

## 15. Most Important Insight

At index `i`:

    answer[i]
        =
    elements before i
        ×
    elements after i

Instead of calculating these products separately for every index, calculate them incrementally.

This changes:

    O(n²)

into:

    O(n)

---

## 16. Common Mistake

### ❌ Mistake 1

Using:

    total_product / nums[i]

Division is not allowed and zero creates a problem.

### ❌ Mistake 2

Including `nums[i]` in the prefix.

Prefix should contain only:

    nums[0 ... i-1]

### ❌ Mistake 3

Including `nums[i]` in the suffix.

Suffix should contain only:

    nums[i+1 ... n-1]

### ❌ Mistake 4

Updating prefix before storing it.

Correct order:

    product_array[i] = prefix

    prefix = prefix × nums[i]

---

## 17. Mental Model

For every index:

    [ LEFT ] [ CURRENT ] [ RIGHT ]

We need:

    LEFT × RIGHT

Example:

    [1, 2, 3, 4]
          ↑
       index 2

    LEFT  = 1 × 2
    RIGHT = 4

    Answer = 2 × 4 = 8

The current element `3` is completely ignored.

---

## 18. Interview Pattern

Whenever you see:

> "Return product of array except self"

Immediately think:

    Product Except Self
            ↓
    Prefix Product
            +
    Suffix Product
            ↓
    O(n) Time
            +
    O(1) Extra Space

---

## 19. One-Line Revision

> For every index, calculate the product of all elements to its left using a prefix product and multiply it by the product of all elements to its right using a suffix product.

---

**LeetCode:** 238 — Product of Array Except Self  
**Pattern:** Array → Prefix Product + Suffix Product  
**Core Concept:** Left Product × Right Product  
**Time:** O(n)  
**Extra Space:** O(1)  
**Important:** No Division
