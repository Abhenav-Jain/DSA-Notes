# 239. Sliding Window Maximum

**Difficulty:** Hard  
**Pattern:** Sliding Window + Monotonic Deque  
**Approach:** Decreasing Deque of Indices

## 💡 Problem Statement

Given an integer array `nums` and a window size `k`, find the maximum element in every contiguous window of size `k`.

**Example:**

```text
Input:  nums = [1,3,-1,-3,5,3,6,7], k = 3
Output: [3,3,5,5,6,7]
```

## 🧠 Intuition: Why Monotonic Deque?

A brute-force solution finds the maximum in every window separately, taking `O(n * k)` time.

We can optimize this using a deque that stores **indices of useful maximum candidates**.

The deque maintains elements in decreasing order of their corresponding values:

\[
nums[dq[0]] \ge nums[dq[1]] \ge \cdots
\]

Therefore, the element at the front always represents the maximum of the current window, after expired indices are removed.

### Three key rules

1. **Remove smaller elements from the back:** If `nums[j] > nums[dq.back()]`, those smaller elements cannot be the maximum while the new element remains in the window.
2. **Remove expired indices from the front:** If `dq.front() < i`, that index is outside the current window.
3. **Read the maximum from the front:** Once the window size is `k`, add `nums[dq.front()]` to the answer.

**Why store indices instead of values?**

Indices let us determine whether an element has left the window and access its value through `nums[index]`.

## 💻 C++ Solution — My Approach

```cpp
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        deque<int> dq;
        int i = 0;
        int j = 0;
        int n = nums.size();
        vector<int> ans;

        while (j < n) {
            while (!dq.empty() &&
                   nums[j] > nums[dq.back()]) {
                dq.pop_back();
            }

            dq.push_back(j);

            while ((j - i + 1) > k) {
                i++;
            }

            while (!dq.empty() && dq.front() < i) {
                dq.pop_front();
            }

            if (!dq.empty() && (j - i + 1) == k) {
                ans.push_back(nums[dq.front()]);
            }

            j++;
        }

        return ans;
    }
};
```

## 🔍 Dry Run

**Input:** `nums = [1,3,-1,-3,5,3,6,7]`, `k = 3`

Deque entries below are **indices**, not values.

| `j` | `nums[j]` | Deque values after processing | Maximum |
|---:|---:|---|---:|
| 0 | 1 | `[1]` | — |
| 1 | 3 | `[3]` | — |
| 2 | -1 | `[3,-1]` | 3 |
| 3 | -3 | `[3,-1,-3]` | 3 |
| 4 | 5 | `[5]` | 5 |
| 5 | 3 | `[5,3]` | 5 |
| 6 | 6 | `[6]` | 6 |
| 7 | 7 | `[7]` | 7 |

**Output:** `[3,3,5,5,6,7]`

Notice that when `5` arrives, the smaller candidates `-1` and `-3` are removed. They cannot be the maximum as long as `5` remains in the window.

## ⚙️ How the Code Works

### 1. Remove dominated candidates

```cpp
while (!dq.empty() &&
       nums[j] > nums[dq.back()]) {
    dq.pop_back();
}
```

The new element is larger than the elements being removed. Those elements are no longer useful as maximum candidates.

### 2. Insert the current index

```cpp
dq.push_back(j);
```

The deque stores the index of the current element.

### 3. Shrink the window

```cpp
while ((j - i + 1) > k) {
    i++;
}
```

Move the left pointer until the window size is at most `k`.

### 4. Remove expired candidates

```cpp
while (!dq.empty() && dq.front() < i) {
    dq.pop_front();
}
```

An index smaller than `i` lies outside the current window and cannot contribute to its maximum.

### 5. Record the maximum

```cpp
if ((j - i + 1) == k) {
    ans.push_back(nums[dq.front()]);
}
```

The front index represents the maximum of the current window.

## ⚠️ Common Mistakes

- **Storing values instead of indices:** You cannot reliably identify expired elements when duplicate values occur.
- **Accessing `dq.front()` or `dq.back()` when empty:** Always check `!dq.empty()` first.
- **Forgetting to remove expired indices:** The deque could return a maximum from a previous window.
- **Removing from the front using the wrong comparison:** Use `dq.front() < i`, because index `i` is still inside the window.
- **Recording answers too early:** First insert the current index, update the window, and remove expired indices; then record the maximum when the window size is `k`.

## ⏱️ Complexity Analysis

- **Time:** `O(n)` — every index is inserted once and removed at most once.
- **Auxiliary space:** `O(k)` — the deque holds at most `k` indices.
- **Output space:** `O(n - k + 1)` for the answer vector.

## 🎯 Interview Takeaways

1. Recognize the **sliding window maximum** pattern.
2. Use a monotonic deque to avoid recalculating the maximum for every window.
3. Store indices to handle window boundaries.
4. Remove dominated elements from the back and expired elements from the front.
5. The front of the deque gives the maximum in `O(1)` time.

### Pattern Summary

**Sliding Window + Monotonic Deque = `O(n)` maximum queries**

Related concept: Next Greater Element, where a monotonic stack helps maintain useful candidates.
