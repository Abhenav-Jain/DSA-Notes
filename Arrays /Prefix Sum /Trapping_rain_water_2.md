# Trapping Rain Water — LeetCode 42

## Approach 1: Prefix & Suffix Maximums

**Idea:** Har index par trapped water left aur right ki maximum heights par depend karta hai.

- `left_tallest[i]`: Current index ke left mein maximum height.
- `right_tallest[i]`: Current index ke right mein maximum height.

**Formula:**

`water = max(0, min(leftist[i], rightest[i]) - height[i])`

### C++ Implementation

```cpp
class Solution {
public:
    vector<int> left_tallest(vector<int>& height) {
        int n = height.size();
        vector<int> left_max(n, 0);
        int left_tallest = INT_MIN;

        for (int i = 1; i < n; i++) {
            if (height[i - 1] > left_tallest) {
                left_tallest = height[i - 1];
            }
            left_max[i] = left_tallest;
        }

        return left_max;
    }

    vector<int> right_tallest(vector<int>& height) {
        int n = height.size();
        vector<int> right_max(n, 0);
        int right_tallest = INT_MIN;

        for (int i = n - 2; i >= 0; i--) {
            if (height[i + 1] > right_tallest) {
                right_tallest = height[i + 1];
            }
            right_max[i] = right_tallest;
        }

        return right_max;
    }

    int trap(vector<int>& height) {
        int n = height.size();
        vector<int> leftist = left_tallest(height);
        vector<int> rightest = right_tallest(height);

        int Total_area = 0;
        int area = 0;

        for (int i = 0; i < n; i++) {
            area = min(leftist[i], rightest[i]) - height[i];

            if (area < 0) {
                area = 0;
            }

            Total_area += area;
        }

        return Total_area;
    }
};
```

**Complexity:**
- Time: `O(n)`
- Extra Space: `O(n)`

---

## Approach 2: Two Pointers — O(1) Extra Space

**Idea:** Left aur right pointers ke saath running maximum heights maintain karte hain. Extra arrays ki zaroorat nahi hoti.

### C++ Implementation

```cpp
class Solution {
public:
    int trap(vector<int>& height) {
        int n = height.size();
        int left = 0;
        int right = n - 1;
        int left_tallest = 0;
        int right_tallest = 0;
        int Total_area = 0;

        while (left < right) {
            if (height[left] <= height[right]) {
                if (height[left] > left_tallest) {
                    left_tallest = height[left];
                } else {
                    Total_area += left_tallest - height[left];
                }
                left++;
            } else {
                if (height[right] > right_tallest) {
                    right_tallest = height[right];
                } else {
                    Total_area += right_tallest - height[right];
                }
                right--;
            }
        }

        return Total_area;
    }
};
```

**Complexity:**
- Time: `O(n)`
- Extra Space: `O(1)`

## Key Learning

| Approach | Time | Extra Space |
|---|---|---|
| Prefix/Suffix Maximums | `O(n)` | `O(n)` |
| Two Pointers | `O(n)` | `O(1)` |

**Interview tip:** Pehle apna prefix/suffix approach explain kar, phir two-pointer approach se space optimization demonstrate kar.

**Pattern:** Prefix/Suffix Maximums · Two Pointers
