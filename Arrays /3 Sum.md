# LeetCode 15 — 3Sum

**Pattern:** Sorting + Two Pointers  
**Difficulty:** Medium  
**Time:** O(n²)  
**Space:** O(1) extra (excluding answer)

---

## 1. Problem

Array me aise **3 elements** find karo jinka sum `0` ho.

Example:

    nums = [-1,0,1,2,-1,-4]

Answer:

    [[-1,-1,2],[-1,0,1]]

Duplicate triplets allowed nahi hain.

---

## 2. Main Idea

Pehle array sort karo:

    [-4,-1,-1,0,1,2]

Phir har `i` ko fix karke remaining part me **Two Pointer** lagao.

    i = fixed element
    left = i + 1
    right = n - 1

Calculate:

    sum = nums[i] + nums[left] + nums[right]

---

## 3. Pointer Movement

### sum == 0

Triplet mil gaya.

    ans.push_back(...)

Then duplicate `left` aur `right` values skip karo.

Finally:

    left++;
    right--;

### sum < 0

Sum chhota hai → sum increase karna hai:

    left++;

### sum > 0

Sum bada hai → sum decrease karna hai:

    right--;

---

## 4. Duplicate Handling

### Duplicate `i`

Same first element dobara process nahi karna:

    if(i > 0 && nums[i] == nums[i-1])
        continue;

### Duplicate `left`

    while(left < right && nums[left] == nums[left+1])
        left++;

### Duplicate `right`

    while(left < right && nums[right] == nums[right-1])
        right--;

Ye duplicate triplets ko prevent karta hai.

---

## 5. Dry Run

    nums = [-1,0,1,2,-1,-4]

After sorting:

    [-4,-1,-1,0,1,2]

Take:

    i = 1 → -1
    left = 2 → -1
    right = 5 → 2

Sum:

    -1 + -1 + 2 = 0

Triplet:

    [-1,-1,2]

Then duplicates skip karke pointers move karo.

Next valid combination:

    [-1,0,1]

---

## 6. Code

    class Solution {
    public:
        vector<vector<int>> threeSum(vector<int>& nums) {

            int n = nums.size();
            vector<vector<int>> ans;

            sort(nums.begin(), nums.end());

            for(int i = 0; i < n - 2; i++) {

                // Skip duplicate first elements
                if(i > 0 && nums[i] == nums[i - 1])
                    continue;

                int left = i + 1;
                int right = n - 1;

                while(left < right) {

                    long long sum =
                        (long long)nums[i] +
                        nums[left] +
                        nums[right];

                    if(sum == 0) {

                        ans.push_back(
                            {nums[i], nums[left], nums[right]}
                        );

                        // Skip duplicate left values
                        while(left < right &&
                              nums[left] == nums[left + 1])
                            left++;

                        // Skip duplicate right values
                        while(left < right &&
                              nums[right] == nums[right - 1])
                            right--;

                        left++;
                        right--;
                    }

                    else if(sum < 0) {
                        left++;
                    }

                    else {
                        right--;
                    }
                }
            }

            return ans;
        }
    };

---

## 7. Why Sorting?

Sorting gives us two benefits:

1. Duplicates easily identify ho jaate hain.
2. Two Pointer movement possible hoti hai.

Because:

    sum < 0 → left++
    sum > 0 → right--

---

## 8. Complexity

Sorting:

    O(n log n)

Two-pointer traversal:

    O(n²)

Overall:

    O(n²)

Extra Space:

    O(1)

(excluding answer and sorting implementation details)

---

## 9. Key Pattern

**3Sum = Fix One Element + 2Sum using Two Pointers**

    Sort
      ↓
    Fix i
      ↓
    left + right
      ↓
    Find target = -nums[i]
      ↓
    Skip duplicates

---

## 10. One-Line Revision

> Sort the array, fix one element, use two pointers for the remaining two elements, and carefully skip duplicates.
