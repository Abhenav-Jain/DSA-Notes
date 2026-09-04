# LeetCode 11 — Container With Most Water

**Pattern:** Array + Two Pointers  
**Difficulty:** Medium  
**Importance:** ⭐⭐⭐⭐⭐  
**Core Concept:** Two pointers + Greedy elimination

---

## 1. Problem Statement

Given an integer array `height` where:

    height[i]

represents the height of a vertical line at index `i`.

Choose two lines such that, together with the x-axis, they form a container that holds the most water.

Return the maximum amount of water.

---

## 2. Formula

For two pointers:

    left
    right

The width of the container is:

    width = right - left

The water level is limited by the shorter line:

    min(height[left], height[right])

Therefore:

    Area = width × min(height[left], height[right])

OR:

    Area = (right - left) × min(height[left], height[right])

This formula is the heart of the problem.

---

## 3. Example

Input:

    height = [1,8,6,2,5,4,8,3,7]

Take:

    left = 1
    right = 8

Heights:

    height[1] = 8
    height[8] = 7

Width:

    8 - 1 = 7

Water height:

    min(8,7) = 7

Area:

    7 × 7 = 49

Maximum answer:

    49

---

## 4. Brute Force Approach

Try every possible pair:

    for i = 0 → n-1
        for j = i+1 → n-1

Calculate:

    (j-i) × min(height[i],height[j])

Keep the maximum.

### Complexity

    Time: O(n²)
    Space: O(1)

This works but is too slow for large arrays.

We need an O(n) solution.

---

## 5. Main Idea — Two Pointers

Start with:

    left = 0
    right = n-1

So initially we have the **maximum possible width**.

At every step:

    width = right - left

    area =
        width × min(height[left], height[right])

Update:

    max_area = max(max_area, area)

Then move the pointer corresponding to the **shorter line**.

---

## 6. Most Important Question

### Why do we move the shorter pointer?

Suppose:

    height[left] < height[right]

Current area:

    width × height[left]

The shorter line is `height[left]`.

If we move `right` instead:

    width decreases

But the limiting height is still:

    height[left]

because `height[left]` is still the shorter side.

So the area **cannot increase**.

Therefore, moving the taller pointer is useless.

Instead, we move:

    left++

so that we can potentially find a taller left boundary.

---

## 7. The Golden Rule

    Move the pointer having the SMALLER height.

Because:

    Area = Width × Smaller Height

Width always decreases when we move a pointer.

Therefore, to compensate for the decreasing width, we need a chance to increase the limiting height.

So:

    Smaller height → move that pointer

---

## 8. Dry Run

Input:

    height = [1,8,6,2,5,4,8,3,7]

Initially:

    left = 0
    right = 8

Heights:

    1 and 7

Width:

    8

Area:

    8 × min(1,7)
    = 8 × 1
    = 8

Current:

    max_area = 8

Since:

    height[left] < height[right]

move:

    left++

---

### Step 2

    left = 1
    right = 8

Heights:

    8 and 7

Width:

    7

Area:

    7 × 7
    = 49

Update:

    max_area = 49

Since:

    height[right] < height[left]

move:

    right--

---

### Step 3

    left = 1
    right = 7

Heights:

    8 and 3

Width:

    6

Area:

    6 × 3
    = 18

Maximum remains:

    49

Move:

    right--

---

Continue this process until:

    left >= right

Final answer:

    49

---

## 9. Code

    class Solution {
    public:
        int maxArea(vector<int>& height) {

            int left = 0;
            int right = height.size() - 1;

            int max_height = 0;

            while(left <= right){

                int width = right - left;

                int area =
                    width * min(height[left], height[right]);

                max_height =
                    max(max_height, area);

                if(height[left] < height[right]){

                    left++;
                }
                else{

                    right--;
                }
            }

            return max_height;
        }
    };

---

## 10. Understanding the Code

### Step 1 — Initialize pointers

    int left = 0;
    int right = height.size() - 1;

We start from both ends because this gives us the maximum possible width.

---

### Step 2 — Calculate width

    int width = right - left;

Distance between the two lines is the width.

---

### Step 3 — Calculate water height

    min(height[left], height[right])

The shorter line determines the maximum water level.

---

### Step 4 — Calculate area

    width * min(height[left], height[right])

---

### Step 5 — Update maximum

    max_height =
        max(max_height, area);

---

### Step 6 — Move the shorter pointer

    if(height[left] < height[right]){

        left++;
    }
    else{

        right--;
    }

This is the most important line of the entire solution.

---

## 11. Why Not Move the Taller Pointer?

Suppose:

    left height = 3
    right height = 8

Current area:

    width × 3

If we move the right pointer:

    new width < old width

The left height is still:

    3

Therefore:

    new area ≤ old width × 3

So there is no possibility of improving the area by moving the taller side.

But if we move the shorter side:

    left++

we may find:

    5
    7
    10
    ...

Then the limiting height may increase.

Therefore, only the shorter side has a chance to improve the answer.

---

## 12. What If Both Heights Are Equal?

Suppose:

    height[left] == height[right]

Then either pointer can be moved.

Our code does:

    else{
        right--;
    }

because the condition:

    height[left] < height[right]

is false.

We could also write:

    if(height[left] <= height[right])
        left++;
    else
        right--;

Both are valid.

---

## 13. Visual Mental Model

Think:

    left wall             right wall
       |                       |
       |                       |
       |                       |
       |_______________________|

Water is limited by the shorter wall.

Therefore:

    Water Height = shorter wall

And:

    Water Width = distance between walls

So:

    Area = shorter wall × distance

---

## 14. Why Starting From Both Ends?

Because:

    width = right - left

The maximum possible width is obtained when:

    left = 0
    right = n-1

If we start from inside, we immediately lose some width without gaining anything.

So we begin with:

    widest possible container

Then gradually reduce the width while searching for taller boundaries.

---

## 15. Greedy Insight

This is a **Greedy + Two Pointer** problem.

At each step:

    Keep the taller boundary
    Remove the shorter boundary

Why?

Because the shorter boundary is the bottleneck.

The only way to potentially improve the area is to find a taller boundary on the shorter side.

---

## 16. Important Invariant

At every step:

    left < right

and we calculate the best area possible for the current pair.

When the shorter boundary is discarded, every container using that boundary and a pointer farther inward has smaller width.

Since that boundary was already the limiting height, those possibilities cannot produce a better result.

Therefore, safely eliminate the shorter side.

---

## 17. Complexity

### Time Complexity

    O(n)

Each pointer moves only toward the other pointer.

Together they perform at most `n` movements.

### Space Complexity

    O(1)

Only:

    left
    right
    width
    area
    max_height

are used.

No extra data structure is required.

---

## 18. Common Mistakes

### ❌ Mistake 1 — Moving the taller pointer

Wrong:

    if(height[left] > height[right])
        left++;

This can skip the only side that needs improvement.

Correct:

    Move the smaller height.

---

### ❌ Mistake 2 — Using the taller height for area

Wrong:

    width × max(height[left],height[right])

Correct:

    width × min(height[left],height[right])

Because water spills over the shorter wall.

---

### ❌ Mistake 3 — Width calculation

Correct:

    right - left

Not:

    right - left + 1

Because the lines are located at indices, and distance between them is:

    right - left

---

### ❌ Mistake 4 — Starting both pointers at 0

We want maximum initial width.

Correct:

    left = 0
    right = n-1

---

## 19. Comparison With Other Two-Pointer Problems

### Move Zeroes

    left  → next position
    right → scanner

### Remove Duplicates

    k → next unique position
    i → scanner

### Container With Most Water

    left  → left boundary
    right → right boundary

The pointer movement logic is different, but all three use:

    TWO POINTERS

---

## 20. Pattern Recognition

Whenever you see:

> Two ends / boundaries + maximize area/value + array

Think:

    Two Pointers

For Container With Most Water:

    Start from both ends
            ↓
    Calculate current area
            ↓
    Keep maximum
            ↓
    Move smaller boundary
            ↓
    Repeat

---

## 21. One-Line Revision

> Start with two pointers at both ends, calculate `width × min(leftHeight,rightHeight)`, and always move the pointer with the smaller height because only that side can potentially increase the limiting height.

---

## 22. Interview Explanation

If interviewer asks:

### "Why does the two-pointer approach work?"

Answer:

> The area is limited by the shorter of the two lines. When we move the taller line, the width decreases while the limiting height cannot increase, so that move cannot produce a better area. Therefore, we always move the shorter line to search for a taller boundary.

This is the key proof of the algorithm.

---

**LeetCode:** 11 — Container With Most Water  
**Pattern:** Two Pointers + Greedy  
**Core Formula:** `(right-left) × min(height[left],height[right])`  
**Pointer Rule:** Move the shorter height  
**Time:** O(n)  
**Space:** O(1)  
**Key Insight:** Shorter boundary is the bottleneck
