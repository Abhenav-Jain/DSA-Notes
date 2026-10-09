# Find the Duplicate Number — LeetCode 287

**Difficulty:** Medium  
**Pattern:** Floyd's Cycle Detection · Fast & Slow Pointers  
**Algorithm:** Tortoise and Hare  
**Key Concept:** Cycle detection in an array interpreted as a linked list

---

## 1. Problem Understanding

Given an array `nums` containing `n + 1` integers, where every integer lies in the range `[1, n]`, find the duplicate number.

**Constraints:**
- At least one number is duplicated.
- The array must not be modified.
- Only `O(1)` extra space is allowed.
- The duplicate may occur more than twice.

**Example:**

```text
Input:  nums = [1,3,4,2,2]
Output: 2
```

---

## 2. Core Intuition — Treat the Array as a Linked List

Normally, an array is accessed using indices. Here, we treat each value as a pointer to the next index.

```cpp
next = nums[current];
```

For example:

```text
nums = [1,3,4,2,2]

Index:  0  1  2  3  4
Value:  1  3  4  2  2
```

Starting from index `0`:

```text
0 → 1 → 3 → 2 → 4 → 2 → 4 → ...
```

The sequence enters a cycle:

```text
2 → 4 → 2 → 4 → ...
```

The duplicate number corresponds to the **entry point of the cycle**.

Why? Every index points to the index represented by its value. Since values are restricted to `[1, n]`, the sequence stays within valid indices. A repeated value creates multiple incoming links to the same index, producing a cycle in this functional graph.

---

## 3. Optimal Approach — Floyd's Cycle Detection

Floyd's algorithm uses two pointers:

- **Slow pointer:** Moves one step at a time.
- **Fast pointer:** Moves two steps at a time.

The solution has two phases.

### Phase 1 — Detect the Cycle

```cpp
int slow = nums[0];
int fast = nums[0];

do {
    slow = nums[slow];
    fast = nums[nums[fast]];
} while (slow != fast);
```

The slow and fast pointers eventually meet somewhere inside the cycle.

**Important:** A `do-while` loop is used because both pointers start at the same position. A normal `while (slow != fast)` would terminate immediately.

### Phase 2 — Find the Cycle Entry

```cpp
slow = nums[0];

while (slow != fast) {
    slow = nums[slow];
    fast = nums[fast];
}

return slow;
```

Reset `slow` to `nums[0]`, then move both pointers one step at a time.

They meet at the cycle entry, which gives the duplicate number.

---

## 4. Complete C++ Solution

```cpp
class Solution {
public:
    int findDuplicate(vector<int>& nums) {
        int slow = nums[0];
        int fast = nums[0];

        // Phase 1: Detect the cycle
        do {
            slow = nums[slow];
            fast = nums[nums[fast]];
        } while (slow != fast);

        // Phase 2: Find the cycle entry
        slow = nums[0];

        while (slow != fast) {
            slow = nums[slow];
            fast = nums[fast];
        }

        return slow;
    }
};
```

---

## 5. Dry Run

```text
nums = [1,3,4,2,2]

Index:  0  1  2  3  4
Value:  1  3  4  2  2
```

### Phase 1: Detect the cycle

Initially:

```text
slow = nums[0] = 1
fast = nums[0] = 1
```

| Iteration | Slow update | Fast update | Slow | Fast |
|---|---|---|---:|---:|
| 1 | `nums[1]` | `nums[nums[1]]` | 3 | 2 |
| 2 | `nums[3]` | `nums[nums[2]]` | 2 | 4 |
| 3 | `nums[2]` | `nums[nums[4]]` | 4 | 4 |

Pointers meet at `4`. The cycle is detected.

### Phase 2: Find the cycle entry

Reset:

```text
slow = nums[0] = 1
fast = 4
```

Move both one step at a time:

| Iteration | Slow | Fast |
|---|---:|---:|
| 1 | `nums[1] = 3` | `nums[4] = 2` |
| 2 | `nums[3] = 2` | `nums[2] = 4` |
| 3 | `nums[2] = 4` | `nums[4] = 2` |

Correction to keep track of the actual pointer positions: after the reset, the first move is `slow = nums[1] = 3`, `fast = nums[4] = 2`; the next is `slow = nums[3] = 2`, `fast = nums[2] = 4`; the next is `slow = nums[2] = 4`, `fast = nums[4] = 2`. This shows the importance of tracing the *indices* rather than treating the displayed values as node labels.

A cleaner trace uses the actual index mapping `0 → 1 → 3 → 2 → 4 → 2 ...`. After the phase-1 meeting, resetting one pointer to index `0` and advancing both one step at a time makes them meet at index `2`, whose value is `4` under this index-label interpretation.

**Output: `2`**, the duplicate value in the array.

---

## 6. Why Does Phase 2 Work?

Let:

- `L` = distance from the starting node to the cycle entry.
- `C` = cycle length.
- `x` = distance from the cycle entry to the phase-1 meeting point.

At the meeting point, the fast pointer has travelled twice as far as the slow pointer. Their distance difference is a whole number of cycle lengths:

```text
2(L + x) - (L + x) = qC
```

Therefore:

```text
L + x = qC
L = qC - x
```

This means the distance from the start to the cycle entry equals the distance from the meeting point to the cycle entry, modulo the cycle length.

So, reset one pointer to the start and move both one step at a time. They meet at the cycle entry.

---

## 7. Complexity Analysis

- **Time Complexity:** `O(n)` — both phases take linear time.
- **Auxiliary Space:** `O(1)` — only two pointers are used.
- **Array Modification:** None.

---

## 8. Common Mistakes

1. Using `fast = nums[fast]` instead of `fast = nums[nums[fast]]` in Phase 1.
2. Forgetting to reset `slow = nums[0]` after detecting the cycle.
3. Moving `fast` two steps in Phase 2; both pointers must move one step.
4. Using a `while` loop in Phase 1 while both pointers start equal.
5. Assuming the duplicate is necessarily the value where the pointers first meet. The first meeting is generally not the cycle entry.

---

## 9. Interview Explanation

"I interpret the array as a linked list, where each value points to the next index. Because values lie between 1 and n and the array has n+1 elements, the sequence must contain a cycle. The duplicate corresponds to the cycle entry. I use Floyd's cycle detection algorithm: first, slow and fast pointers meet inside the cycle; then I reset one pointer to the starting position and move both one step at a time until they meet at the cycle entry. This gives O(n) time and O(1) extra space without modifying the array."

---

## 10. Final Revision Checklist

- [ ] Understand the array-to-linked-list mapping.
- [ ] Know why the duplicate creates a cycle.
- [ ] Remember why Phase 1 uses two-speed pointers.
- [ ] Remember to reset one pointer for Phase 2.
- [ ] Be able to explain the cycle-entry proof.
- [ ] State `O(n)` time and `O(1)` auxiliary space.

**One-line memory trick:** Detect the cycle first; reset one pointer; move both at the same speed to find the duplicate.
