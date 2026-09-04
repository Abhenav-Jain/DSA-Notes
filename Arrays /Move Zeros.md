# LeetCode 283 — Move Zeroes

**Pattern:** Array + Two Pointers  
**Difficulty:** Easy  
**Importance:** ⭐⭐⭐⭐⭐  
**Core Concept:** Move all zeroes to the end while maintaining the relative order of non-zero elements.

---

## 1. Problem Statement

Given an integer array `nums`, move all `0`s to the end of the array while maintaining the **relative order of the non-zero elements**.

The operation must be performed **in-place**.

### Example

Input:

    nums = [0,1,0,3,12]

Output:

    [1,3,12,0,0]

The relative order of non-zero elements remains:

    1 → 3 → 12

Only the zeroes are moved to the end.

---

## 2. Main Idea — Two Pointers

We use two pointers:

    left
    right

### Meaning of pointers

`left` points to the position where the **next non-zero element should be placed**.

`right` is used to **scan the array** and find non-zero elements.

Initially:

    left = 0
    right = 0

---

## 3. Core Logic

We scan the array using `right`.

### Case 1 — Current element is non-zero

If:

    nums[right] != 0

then this element belongs at the next available position:

    swap(nums[left], nums[right])

Then move both pointers:

    left++
    right++

### Case 2 — Current element is zero

If:

    nums[right] == 0

then we don't move `left`.

Only:

    right++

This allows `right` to search for the next non-zero element.

---

## 4. Why Does This Work?

`left` always represents the position where the next non-zero element should go.

Example:

    nums = [0,1,0,3,12]

Initially:

    left = 0
    right = 0

`right` finds `1`.

So:

    swap(nums[0], nums[1])

Array becomes:

    [1,0,0,3,12]

Now:

    left = 1
    right = 2

`right` finds `3`.

Swap:

    [1,3,0,0,12]

Then `right` finds `12`.

Swap:

    [1,3,12,0,0]

Final answer:

    [1,3,12,0,0]

---

## 5. Important Invariant

At any point:

    nums[0 ... left-1]

contains all the non-zero elements found so far, in their original relative order.

And:

    left

points to the next position where a non-zero element should be placed.

This is the most important thing to remember.

---

## 6. Dry Run

Input:

    nums = [0,1,0,3,12]

Initial:

    left = 0
    right = 0

### Step 1

    nums[right] = 0

So:

    right++

Array:

    [0,1,0,3,12]

Pointers:

    left = 0
    right = 1

---

### Step 2

    nums[right] = 1

Non-zero found.

Swap:

    swap(nums[left], nums[right])

Array:

    [1,0,0,3,12]

Move:

    left++
    right++

Now:

    left = 1
    right = 2

---

### Step 3

    nums[right] = 0

Only move:

    right++

Now:

    left = 1
    right = 3

---

### Step 4

    nums[right] = 3

Swap:

    swap(nums[1], nums[3])

Array:

    [1,3,0,0,12]

Move:

    left++
    right++

Now:

    left = 2
    right = 4

---

### Step 5

    nums[right] = 12

Swap:

    swap(nums[2], nums[4])

Array:

    [1,3,12,0,0]

Final:

    [1,3,12,0,0]

---

## 7. Code

    class Solution {
    public:
        void moveZeroes(vector<int>& nums) {

            int left = 0;
            int right = 0;

            while(right <= nums.size() - 1){

                if(nums[left] == 0 && nums[right] != 0){

                    swap(nums[left], nums[right]);

                    left++;
                    right++;
                }

                else if(nums[left] != 0 && nums[right] != 0){

                    left++;
                    right++;
                }

                else{

                    right++;
                }
            }
        }
    };

---

## 8. Understanding the Submitted Code

The submitted solution handles three situations.

### Case 1

    nums[left] == 0
    nums[right] != 0

We found a non-zero element that can replace the zero at `left`.

So:

    swap(nums[left], nums[right])

Then:

    left++
    right++

---

### Case 2

    nums[left] != 0
    nums[right] != 0

Both positions already contain non-zero elements.

So there is nothing to swap.

Simply move both:

    left++
    right++

---

### Case 3

Everything else

The useful non-zero element has not been found yet.

So only:

    right++

This is particularly important when:

    nums[right] == 0

because we want `right` to keep searching for a non-zero element.

---

## 9. Simpler Version of the Same Idea

The same two-pointer idea can be written more cleanly:

    class Solution {
    public:
        void moveZeroes(vector<int>& nums) {

            int left = 0;

            for(int right = 0; right < nums.size(); right++){

                if(nums[right] != 0){

                    swap(nums[left], nums[right]);

                    left++;
                }
            }
        }
    };

This is the preferred implementation because it directly expresses the idea:

    Find non-zero
        ↓
    Put it at left
        ↓
    Move left

---

## 10. Why `left` Doesn't Move When We Find Zero?

Suppose:

    nums = [0,0,3]

Initially:

    left = 0
    right = 0

`right` finds zero.

If we moved `left`, we would lose the position where `3` needs to be placed.

So:

    zero → only right moves

When `right` finds `3`:

    left = 0
    right = 2

We swap:

    [3,0,0]

Therefore:

    left = position for next non-zero
    right = scanner

---

## 11. Relative Order Is Maintained

Consider:

    nums = [0,1,0,2,0,3]

Non-zero elements are:

    1 → 2 → 3

Our algorithm places them in exactly the same order:

    [1,2,3,0,0,0]

This happens because `right` scans from left to right and `left` places each non-zero element sequentially.

---

## 12. Why Two Pointers?

A brute-force approach could create another array:

    [1,3,12,0,0]

But the problem asks us to modify the array **in-place**.

Two pointers allow us to rearrange the same array without creating another array.

---

## 13. Complexity

### Time Complexity

    O(n)

`right` traverses the array once.

Even though we use `swap`, each operation is O(1).

Therefore:

    O(n)

### Space Complexity

    O(1)

Only pointer variables are used.

No extra array is created.

---

## 14. Important Edge Cases

### No Zeroes

    nums = [1,2,3]

Output:

    [1,2,3]

Nothing changes.

---

### All Zeroes

    nums = [0,0,0]

Output:

    [0,0,0]

---

### Zero at Beginning

    nums = [0,1,2]

Output:

    [1,2,0]

---

### Zero at End

    nums = [1,2,0]

Output:

    [1,2,0]

Already correct.

---

### Multiple Consecutive Zeroes

    nums = [1,0,0,2,0,3]

Output:

    [1,2,3,0,0,0]

---

## 15. Common Mistake

### ❌ Moving `left` when current element is zero

Wrong idea:

    if(nums[right] == 0){
        left++;
    }

This is wrong because `left` represents the next position for a non-zero element.

---

### ❌ Changing the relative order

For example:

    [0,1,2,3]

should become:

    [1,2,3,0]

not:

    [3,2,1,0]

The order of non-zero elements must remain unchanged.

---

## 16. Mental Model

Think of `left` as a **slot**.

Whenever `right` finds a non-zero:

    right finds non-zero
             ↓
    put it in left slot
             ↓
          left++

Whenever `right` finds zero:

    zero found
       ↓
    ignore it
       ↓
    right++

So:

    left  → next non-zero position
    right → scanner

---

## 17. Interview Pattern

Whenever you see:

> "Move/shift elements in an array while maintaining order and doing it in-place."

Think:

    Two Pointers
        ↓
    One pointer scans
        ↓
    One pointer maintains the correct position
        ↓
    O(n) Time
        +
    O(1) Space

For Move Zeroes:

    right → find non-zero
    left  → place non-zero

---

## 18. One-Line Revision

> Use two pointers where `right` scans the array and `left` tracks the next position for a non-zero element; whenever a non-zero is found, swap it with `nums[left]` and increment `left`.

---

**LeetCode:** 283 — Move Zeroes  
**Pattern:** Array → Two Pointers  
**Core Concept:** In-place rearrangement  
**Time:** O(n)  
**Space:** O(1)  
**Key Rule:** Preserve relative order of non-zero elements
