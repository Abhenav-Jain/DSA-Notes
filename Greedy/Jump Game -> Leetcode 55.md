# Jump Game — LeetCode 55

**Pattern:** Greedy + Backward Traversal + Reachability  
**Difficulty:** Medium  
**Core Concept:** Maintain the leftmost position from which the destination is reachable.

## 1. Problem Statement

You are given an integer array `nums`.

- You start at index `0`.
- `nums[i]` represents the maximum jump length from index `i`.
- You can jump any distance from `1` to `nums[i]` (or stay at the same position).
- Return `true` if you can reach the last index; otherwise, return `false`.

**Example 1**

```cpp
nums = [2,3,1,1,4]
Output: true
```

One possible path: `0 → 1 → 4`

**Example 2**

```cpp
nums = [3,2,1,0,4]
Output: false
```

You can reach index `3`, but cannot move beyond it to index `4`.

---

## 2. Greedy Intuition

Instead of starting from index `0` and trying to find a path to the end, reverse the question:

**Which positions can reach the last index?**

Initially, the last index is our target because reaching it is the goal.

Now traverse from right to left.

For every index `i`, check:

```cpp
i + nums[i] >= target
```

If this condition is true, index `i` can reach the current target.

Therefore, update:

```cpp
target = i;
```

Now our new goal is to reach index `i`, because reaching it guarantees that the original destination can also be reached.

At the end:
- If `target == 0`, the starting index can reach the last index.
- Otherwise, it cannot.

## 3. Optimized Approach — Backward Greedy

### C++ Code

```cpp
class Solution {
public:
    bool canJump(vector<int>& nums) {
        int n = nums.size();

        int final_position = n - 1;

        for(int i = n - 2; i >= 0; i--) {

            if(i + nums[i] >= final_position) {
                final_position = i;
            }

        }

        return final_position == 0;
    }
};
```

### 4. Dry Run — Example 1

```cpp
nums = [2,3,1,1,4]
```

Initially:

```cpp
final_position = 4;
```

| Index `i` | `nums[i]` | `i + nums[i]` | Target before | Target after |
|---:|---:|---:|---:|---:|
| 3 | 1 | 4 | 4 | 3 |
| 2 | 1 | 3 | 3 | 2 |
| 1 | 3 | 4 | 2 | 1 |
| 0 | 2 | 2 | 1 | 0 |

Final:

```cpp
final_position = 0;
return true;
```

**Why?**

- Index 3 can reach index 4.
- Index 2 can reach index 3.
- Index 1 can reach index 2.
- Index 0 can reach index 1.

Therefore, index 0 can reach the last index.

### 5. Dry Run — Example 2

```cpp
nums = [3,2,1,0,4]
```

Initially:

```cpp
final_position = 4;
```

| Index `i` | `nums[i]` | `i + nums[i]` | Target before | Target after |
|---:|---:|---:|---:|---:|
| 3 | 0 | 3 | 4 | 4 |
| 2 | 1 | 3 | 4 | 4 |
| 1 | 2 | 3 | 4 | 4 |
| 0 | 3 | 3 | 4 | 4 |

Final:

```cpp
final_position = 4;
return false;
```

No index can reach the current target, which remains index 4.

---

## 6. Why Does This Greedy Work?

The key is that we do not need to find an exact path.

We only need to determine whether the starting position can reach the destination.

Suppose the current target is `target`.

If:

```cpp
i + nums[i] >= target
```

then index `i` can jump to the target or beyond it.

Since the target is already known to be able to reach the original destination, index `i` can also reach the original destination.

Thus, moving the target backward to `i` is safe.

If the condition is false, index `i` cannot directly reach the current target, so it cannot become the new target.

By processing every index from right to left, we eventually determine whether index 0 can reach the destination.

## 7. Complexity Analysis

Let `N` be the size of the array.

| Complexity | Value | Reason |
|---|---|---|
| Time | O(N) | Each index is visited once |
| Auxiliary Space | O(1) | Only a few variables are used |

This is optimal: in the worst case, we may need to inspect every element.

## 8. Edge Cases

| Input | Output | Explanation |
|---|---|---|
| `[0]` | `true` | Start is already the destination |
| `[1,0]` | `true` | One jump reaches the end |
| `[0,1]` | `false` | Cannot leave index 0 |
| `[2,0,0]` | `true` | Index 0 can directly reach the end |
| `[3,2,1,0,4]` | `false` | Stuck before the destination |

**Important:** A single-element array must return `true`. The given implementation handles it automatically because the loop does not execute and `final_position` is already 0.

---

## 9. Common Mistakes

1. **Assuming every jump must be exactly `nums[i]`.**  
   No. It is the maximum jump length, so shorter jumps are allowed.

2. **Updating the target without checking reachability.**  
   Only update when `i + nums[i] >= final_position`.

3. **Traversing in the wrong direction.**  
   This particular approach uses backward traversal, from `n-2` to `0`.

4. **Confusing the target with the maximum reachable index.**  
   Here, `final_position` represents the leftmost index currently known to reach the destination, not the farthest index reachable from the start.

5. **Forgetting the final condition.**  
   We need to reach index 0 as the final target, hence `final_position == 0`.

---

## 10. Another Important Approach — Forward Greedy

The same problem can also be solved using forward traversal.

Maintain the farthest index reachable from the starting position.

```cpp
class Solution {
public:
    bool canJump(vector<int>& nums) {
        int maxReach = 0;

        for(int i = 0; i < nums.size(); i++) {

            if(i > maxReach)
                return false;

            maxReach = max(maxReach, i + nums[i]);

            if(maxReach >= nums.size() - 1)
                return true;
        }

        return true;
    }
};
```

**Forward Greedy:** Maintain the maximum reachable index.

**Backward Greedy:** Maintain the earliest index that can reach the destination.

Both approaches take O(N) time and O(1) auxiliary space.

For this problem, remember both, but understand your backward approach particularly well.

---

## 11. Interview Follow-up

**Q1. Why do we start from `n-2` instead of `n-1`?**

Because the last index is already the initial target. We check whether earlier indices can reach it.

**Q2. Why is `>=` used instead of `==`?**

Because a jump can go beyond the target. We only need to reach or cross it.

**Q3. Can we solve this without modifying the input array?**

Yes. The implementation only reads `nums` and uses a separate target variable.

**Q4. Is sorting required?**

No. The array order represents jump positions and must remain unchanged.

---

## 12. Pattern Recognition — When to Think of This Approach

Look for these clues:

- You need to determine whether a destination is reachable.
- Each position provides a maximum movement range.
- You can reason about which earlier positions can reach a known valid position.
- You need only a boolean answer, not the actual path.

Think:

**Destination → Find its reachable predecessors → Move backward → Check whether the start becomes reachable.**

## 13. Quick Revision Cheat Sheet

```cpp
int target = nums.size() - 1;

for(int i = nums.size() - 2; i >= 0; i--) {
    if(i + nums[i] >= target) {
        target = i;
    }
}

return target == 0;
```

**One-line intuition:**

Keep moving the target backward whenever the current index can reach it. If the target eventually becomes index 0, the last index is reachable.

**Remember:** Jump Game (LC 55) is a fundamental Greedy Reachability problem and prepares you for Jump Game II (LC 45), where the goal changes from simply reaching the destination to finding the minimum number of jumps.
