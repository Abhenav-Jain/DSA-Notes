# LeetCode 26 — Remove Duplicates from Sorted Array

**Pattern:** Array + Two Pointers  
**Difficulty:** Easy  
**Importance:** ⭐⭐⭐⭐⭐  
**Core Concept:** In-place removal of duplicates from a sorted array.

---

## 1. Problem Statement

Given an integer array `nums` sorted in non-decreasing order, remove the duplicates **in-place** such that each unique element appears only once.

Return the number of unique elements.

The first `k` positions of `nums` should contain the unique elements.

### Example

Input:

    nums = [1,1,2]

Output:

    2

Modified array:

    [1,2, ...]

Only the first `2` positions matter.

---

## 2. Important Observation

The array is already **sorted**.

Therefore, all duplicates are next to each other.

Example:

    [1,1,1,2,2,3,4,4]

We can easily identify a duplicate because:

    nums[i] == nums[i-1]

If:

    nums[i] != nums[k-1]

then `nums[i]` is a new unique element.

This sorted property is the key to the entire solution.

---

## 3. Main Idea — Two Pointers

We use:

    k
    i

### `k`

`k` points to the position where the **next unique element** should be placed.

### `i`

`i` scans the array and looks for new unique elements.

Initially:

    k = 1

Why?

Because the first element is always unique.

So:

    nums[0]

is already correctly placed.

---

## 4. Core Logic

Start:

    k = 1

Then traverse from index `1`:

    for(int i = 1; i < nums.size(); i++)

For every element:

    if(nums[i] != nums[k-1])

then we found a new unique element.

Place it at `k`:

    nums[k] = nums[i]

Then move:

    k++

If:

    nums[i] == nums[k-1]

then it is a duplicate.

Simply ignore it.

---

## 5. Why Compare With `nums[k-1]`?

`k-1` represents the position of the **last unique element**.

Therefore:

    nums[k-1]

is the most recently stored unique value.

If:

    nums[i] != nums[k-1]

then the current element is different from the last unique element.

Therefore, it must be a new unique element.

---

## 6. Dry Run

Input:

    nums = [1,1,2,2,3]

Initial:

    k = 1

The first element `1` is already unique.

---

### i = 1

    nums[i] = 1
    nums[k-1] = nums[0] = 1

Comparison:

    1 == 1

Duplicate.

Do nothing.

    k = 1

---

### i = 2

    nums[i] = 2
    nums[k-1] = nums[0] = 1

Comparison:

    2 != 1

New unique element.

Place it:

    nums[1] = 2

Array:

    [1,2,2,2,3]

Then:

    k++

    k = 2

---

### i = 3

    nums[i] = 2
    nums[k-1] = nums[1] = 2

Comparison:

    2 == 2

Duplicate.

Ignore it.

    k = 2

---

### i = 4

    nums[i] = 3
    nums[k-1] = nums[1] = 2

Comparison:

    3 != 2

New unique element.

Place it:

    nums[2] = 3

Array:

    [1,2,3,2,3]

Then:

    k++

    k = 3

Final:

    k = 3

First `k` elements:

    [1,2,3]

Answer:

    3

---

## 7. Code

    class Solution {
    public:
        int removeDuplicates(vector<int>& nums) {

            // k points to the position where
            // the next unique element will be placed

            int k = 1;

            for(int i = 1; i < nums.size(); i++){

                if(nums[i] != nums[k-1]){

                    nums[k] = nums[i];

                    k++;
                }
            }

            return k;
        }
    };

---

## 8. Understanding the Code

### Step 1

    int k = 1;

The first element is always unique.

So we start placing new unique elements from index `1`.

---

### Step 2

    for(int i = 1; i < nums.size(); i++)

`i` scans every element after the first one.

---

### Step 3

    if(nums[i] != nums[k-1])

Check whether the current element is different from the last unique element.

If different:

    New unique element

If same:

    Duplicate

---

### Step 4

    nums[k] = nums[i];

Put the new unique element at the next available position.

---

### Step 5

    k++;

Increase the number of unique elements.

At the end:

    k = number of unique elements

---

## 9. What Does `k` Actually Mean?

This is extremely important.

`k` has TWO related meanings:

    1. Index where the next unique element will be placed
    2. Number of unique elements found so far

For example:

    nums = [1,1,2,2,3]

At the end:

    k = 3

and the first `3` positions contain:

    [1,2,3]

So:

    return k;

---

## 10. Why Does the Array Need to Be Sorted?

Because duplicates are adjacent.

Example:

    [1,1,2,2,3,3]

We can simply compare the current element with the last unique element.

But for:

    [1,2,1,3,2]

the same logic would not work because duplicates are not adjacent.

Therefore, this specific two-pointer solution relies on:

    SORTED ARRAY

---

## 11. Important Invariant

At every point during the traversal:

    nums[0 ... k-1]

contains the unique elements found so far.

And they remain in sorted order.

Also:

    k

always points to the next position where a new unique element should be placed.

This is the most important invariant to remember.

---

## 12. Why We Don't Actually Delete Elements

The problem says to remove duplicates **in-place**.

We don't physically shrink the vector.

Instead, we overwrite the beginning of the array with unique elements.

Example:

    Original:

    [1,1,2,2,3]

After processing:

    [1,2,3,2,3]

The entire vector still exists, but only the first `k` elements matter:

    [1,2,3]
     ↑─────↑
      valid

Therefore:

    return k

---

## 13. Visual Representation

Think of the array as:

    [ UNIQUE | DUPLICATES / UNPROCESSED ]

Initially:

    [1 | 1,2,2,3]

When `2` is found:

    [1,2 | 2,2,3]

When `3` is found:

    [1,2,3 | 2,3]

So the left side continuously stores the answer.

---

## 14. Edge Cases

### Case 1 — Empty Array

LeetCode's constraints for this problem generally allow handling the empty case.

A robust implementation can use:

    if(nums.empty()){
        return 0;
    }

Then:

    int k = 1;

---

### Case 2 — One Element

    nums = [5]

Output:

    1

There is only one unique element.

---

### Case 3 — All Elements Same

    nums = [2,2,2,2]

Output:

    1

Modified beginning:

    [2]

---

### Case 4 — No Duplicates

    nums = [1,2,3,4]

Output:

    4

Nothing needs to be changed.

---

## 15. Complexity

### Time Complexity

    O(n)

We traverse the array once.

### Space Complexity

    O(1)

Only two variables are used:

    i
    k

No extra array or data structure is required.

---

## 16. Common Mistakes

### ❌ Mistake 1 — Using a Set

You could use:

    set<int>

but that would require extra space.

The optimal solution is:

    O(1) Extra Space

---

### ❌ Mistake 2 — Comparing With `nums[i-1]`

You may think:

    if(nums[i] != nums[i-1])

This can sometimes identify whether the current element is different from the immediately previous element.

However, the cleaner two-pointer invariant is:

    nums[i] != nums[k-1]

because `nums[k-1]` explicitly represents the **last unique element stored in the answer portion**.

---

### ❌ Mistake 3 — Returning `k-1`

Wrong:

    return k-1;

Correct:

    return k;

Because `k` represents the **number of unique elements**, not the last index.

Example:

    [1,2,3]

Last index = `2`

But number of unique elements = `3`

Therefore:

    return 3

---

## 17. Comparison With Move Zeroes

Both problems use the **Two Pointer** pattern.

### Move Zeroes

    left  → position for next non-zero
    right → scanner

### Remove Duplicates

    k → position for next unique element
    i → scanner

Common pattern:

    Scanner Pointer
          ↓
    Find useful element
          ↓
    Place useful element
          ↓
    Move write pointer

This is an important reusable array pattern.

---

## 18. General Two-Pointer Pattern

Whenever you need to modify an array **in-place** while keeping only useful elements:

    read pointer
          ↓
    scan the array

    write pointer
          ↓
    place the useful element

General structure:

    write = 0

    for(read = 0; read < n; read++){

        if(element is useful){

            nums[write] = nums[read];

            write++;
        }
    }

    return write;

This same pattern appears in many array problems.

---

## 19. Interview Pattern

Whenever you see:

> "Sorted array + remove duplicates + in-place"

Immediately think:

    Two Pointers
          ↓
    Read Pointer
          +
    Write Pointer
          ↓
    O(n) Time
          +
    O(1) Space

For this problem:

    i → scanner
    k → write position

---

## 20. One-Line Revision

> Since the array is sorted, duplicates are adjacent; use one pointer to scan the array and another pointer to place each newly found unique element at the front.

---

**LeetCode:** 26 — Remove Duplicates from Sorted Array  
**Pattern:** Array → Two Pointers → Read/Write  
**Core Concept:** Maintain unique elements in the first `k` positions  
**Time:** O(n)  
**Space:** O(1)  
**Key Condition:** Array is sorted
