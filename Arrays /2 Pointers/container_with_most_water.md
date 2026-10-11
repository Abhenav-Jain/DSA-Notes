# Container With Most Water — LC 11

**Pattern:** Two Pointers  
**Difficulty:** Medium

## Intuition
Choose two vertical lines that can hold the maximum water. Width decreases as pointers move inward, so we must improve the limiting height to potentially increase the area.

## Core Formula
```cpp
area = min(height[left], height[right]) * (right - left);
```

- `min(height[left], height[right])` → water ki maximum height.
- `right - left` → width between the lines.

## Approach
1. Initialize `left = 0` and `right = n - 1`.
2. Calculate the area and update `maxWater`.
3. Move the pointer pointing to the **shorter line**.
4. Repeat until `left >= right`.

## Why Move the Shorter Pointer?
The shorter line limits the water level. Moving the taller pointer only reduces width while the shorter line still limits the height. Moving the shorter pointer is the only move that can potentially improve the limiting height.

## C++ Code
```cpp
class Solution {
public:
    int maxArea(vector<int>& height) {
        int left = 0;
        int right = height.size() - 1;
        int maxWater = 0;

        while (left < right) {
            int width = right - left;
            int h = min(height[left], height[right]);

            maxWater = max(maxWater, width * h);

            if (height[left] < height[right]) {
                left++;
            } else {
                right--;
            }
        }

        return maxWater;
    }
};
```

## Complexity
- **Time:** O(n)
- **Space:** O(1)

## Interview Traps
- Use `min()`, not `max()`, for the limiting height.
- Width is `right - left`.
- Move the shorter pointer, not randomly either pointer.
- This is a two-pointer optimization, not a fixed sliding-window problem.

**Key takeaway:** Maximum area = limiting height × width; discard the shorter boundary.
