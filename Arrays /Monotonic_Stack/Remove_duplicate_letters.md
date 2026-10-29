# LeetCode 316 — Remove Duplicate Letters

## Greedy + Monotonic Stack + Last Occurrence Tracking

**Difficulty:** Medium  
**Topics:** String, Greedy, Stack, Monotonic Stack  
**Time Complexity:** O(n)  
**Auxiliary Space:** O(n)

Problem: https://leetcode.com/problems/remove-duplicate-letters/

---

# 1. Problem Statement

Given a string `s`, remove duplicate letters so that every letter appears exactly once.

The resulting string must be the **smallest in lexicographical order** among all possible valid results.

### Example 1

```cpp
s = "bcabc";
Output = "abc";
```

Explanation:

- Every distinct character appears exactly once.
- `"abc"` is lexicographically smaller than other valid arrangements.

### Example 2

```cpp
s = "cbacdcbc";
Output = "acdb";
```

Explanation:

The distinct characters are:

```text
a, b, c, d
```

The result must contain all four characters exactly once, in the smallest possible lexicographical order.

## Important Conditions

There are two requirements:

1. Every distinct character must appear exactly once.
2. Among all valid answers, return the lexicographically smallest string.

**Important:** Simply sorting the distinct characters is not a valid general solution because the answer must be a subsequence of the original string. Relative character order must be preserved.

---

# 2. Understanding Lexicographical Order

Lexicographical order is similar to dictionary order.

For example:

```text
"abc" < "acb"
"acdb" < "adbc"
"bac" > "abc"
```

The first position where two strings differ determines which one is lexicographically smaller.

Our objective:

- Keep every unique character.
- Preserve the original relative order.
- Make the result lexicographically smallest.

---

# 3. Core Observation — Greedy + Monotonic Stack

Consider:

```cpp
s = "cbacdcbc";
```

Suppose our current result is:

```text
c
```

And the next character is:

```text
b
```

Should we keep `c` before `b`?

Since:

```text
b < c
```

We would prefer `b` before `c` to obtain a lexicographically smaller result.

But can we safely remove `c`?

Only if another occurrence of `c` exists later in the string.

This gives us the central greedy rule:

**Remove the previous character if:**

1. The previous character is lexicographically greater than the current character.
2. The previous character appears again later.

Mathematically:

$$result.back() > currentChar$$

And:

$$lastIndex[result.back()] > i$$

If both conditions hold, we can safely remove the previous character.

This is the foundation of the algorithm.

---

# 4. Three Important Data Structures

Our optimized solution uses three components.

## A. Last Occurrence Array

```cpp
vector<int> last_index(26);
```

Stores the last occurrence index of every character.

For example:

```cpp
s = "cbacdcbc";
```

| Character | Last occurrence |
|---|---:|
| a | 2 |
| b | 6 |
| c | 7 |
| d | 4 |

Why do we need this?

Before removing a character from the result, we must know whether it will appear again.

If it does not appear again, removing it would make it impossible to include that character in the final answer.

## B. Taken Array

```cpp
vector<bool> taken(26, false);
```

Tracks whether a character is already present in the result.

For example:

```text
taken['a'] = true
taken['b'] = false
```

Why?

Every distinct character must appear exactly once.

If a character is already included, we skip its subsequent occurrences.

## C. Result String as a Stack

```cpp
string result;
```

We use:

```cpp
result.back();
result.pop_back();
result.push_back(ch);
```

The string behaves like a stack.

It maintains a lexicographically increasing order whenever the greedy conditions allow it.

Note: The result is not guaranteed to be globally sorted. We only remove elements when doing so is safe and beneficial.

---

# 5. Optimized Algorithm — Step-by-Step

## Step 1: Calculate Last Occurrence

```cpp
int n = s.length();

vector<int> last_index(26);

for(int i = 0; i < n; i++) {
    char ch = s[i];
    last_index[ch - 'a'] = i;
}
```

Every character's last occurrence is updated while traversing the string.

## Step 2: Initialize Tracking Structures

```cpp
string result;
vector<bool> taken(26, false);
```

Initially:

- Result is empty.
- No character has been selected.

## Step 3: Traverse the String

```cpp
for(int i = 0; i < n; i++) {

    char ch = s[i];
    int idx = ch - 'a';
```

Process characters from left to right.

## Step 4: Skip Already Selected Characters

```cpp
if(taken[idx] == true) {
    continue;
}
```

If the character is already present, adding it again would violate the uniqueness condition.

## Step 5: Apply the Greedy Monotonic Stack Condition

```cpp
while(result.length() > 0 &&
      result.back() > ch &&
      last_index[result.back() - 'a'] > i) {

    taken[result.back() - 'a'] = false;

    result.pop_back();
}
```

This is the most important part.

Let's understand each condition.

### Condition 1

```cpp
result.length() > 0
```

We cannot access the top element if the stack is empty.

### Condition 2

```cpp
result.back() > ch
```

The current character is lexicographically smaller than the previous character.

Removing the previous character may improve the result.

### Condition 3

```cpp
last_index[result.back() - 'a'] > i
```

The previous character appears again later.

Therefore, removing it now does not permanently eliminate it from the final answer.

### Why update `taken`?

```cpp
taken[result.back() - 'a'] = false;
```

Once we remove a character from the result, we must allow it to be inserted again later.

### Why use `while` instead of `if`?

Multiple previous characters may need to be removed.

For example, if the result is:

```text
bca
```

And the current character is:

```text
a
```

We may need to remove more than one character, depending on their future availability.

Therefore, we continue popping until the greedy condition becomes false.

## Step 6: Insert Current Character

```cpp
result.push_back(ch);
taken[idx] = true;
```

The current character is inserted and marked as selected.

---

# 6. Complete C++ Solution

This is the implementation using the same approach.

```cpp
class Solution {
public:
    string removeDuplicateLetters(string s) {

        int n = s.length();

        string result;

        vector<bool> taken(26, false);
        vector<int> last_index(26);

        // Step 1: Calculate last occurrence
        for(int i = 0; i < n; i++) {

            char ch = s[i];

            last_index[ch - 'a'] = i;
        }

        // Step 2: Process the string
        for(int i = 0; i < n; i++) {

            char ch = s[i];

            int idx = ch - 'a';

            // Skip if already present
            if(taken[idx] == true) {
                continue;
            }

            // Greedy + Monotonic Stack
            while(result.length() > 0 &&
                  result.back() > ch &&
                  last_index[result.back() - 'a'] > i) {

                taken[result.back() - 'a'] = false;

                result.pop_back();
            }

            // Insert current character
            result.push_back(ch);

            taken[idx] = true;
        }

        return result;
    }
};
```

---

# 7. Detailed Dry Run

Input:

```cpp
s = "cbacdcbc";
```

Last occurrence:

| Character | Last index |
|---|---:|
| a | 2 |
| b | 6 |
| c | 7 |
| d | 4 |

Initially:

```text
result = ""
taken = all false
```

### Iteration 1 — i = 0, ch = 'c'

Result is empty.

Push `c`.

```text
result = "c"
```

### Iteration 2 — i = 1, ch = 'b'

Current:

```text
b < c
```

Check whether `c` appears again:

```text
last_index[c] = 7 > 1
```

Both conditions are true.

Remove `c`, mark it as not taken, and push `b`.

```text
result = "b"
```

### Iteration 3 — i = 2, ch = 'a'

Current:

```text
a < b
```

And:

```text
last_index[b] = 6 > 2
```

Remove `b` and push `a`.

```text
result = "a"
```

### Iteration 4 — i = 3, ch = 'c'

`c` is not taken.

Push `c`.

```text
result = "ac"
```

### Iteration 5 — i = 4, ch = 'd'

`d` is greater than `c`.

No popping.

Push `d`.

```text
result = "acd"
```

### Iteration 6 — i = 5, ch = 'c'

`c` is already taken.

Skip it.

```text
result = "acd"
```

### Iteration 7 — i = 6, ch = 'b'

`b` is not taken.

Check the top:

```text
d > b
```

But:

```text
last_index[d] = 4
```

And:

```text
4 > 6  // false
```

We cannot remove `d`, because it will never appear again.

Push `b`.

```text
result = "acdb"
```

### Iteration 8 — i = 7, ch = 'c'

`c` is already taken.

Skip.

Final result:

```cpp
"acdb"
```

---

# 8. Dry Run Summary Table

| i | Current | Action | Result |
|---:|---|---|---|
| 0 | c | Push | c |
| 1 | b | Pop c, push b | b |
| 2 | a | Pop b, push a | a |
| 3 | c | Push | ac |
| 4 | d | Push | acd |
| 5 | c | Skip | acd |
| 6 | b | Push | acdb |
| 7 | c | Skip | acdb |

Answer:

```text
acdb
```

---

# 9. Why Is This Algorithm Correct?

The algorithm maintains three important invariants.

### Invariant 1 — Uniqueness

`taken` ensures that a character is never inserted twice.

If a character is removed from the result, its `taken` status becomes false, allowing a later occurrence to be selected.

### Invariant 2 — Safe Removal

A character is removed only when it has another occurrence later.

Therefore, we never permanently lose a distinct character.

### Invariant 3 — Lexicographical Improvement

If the current character is smaller than the result's top character, and the top character occurs again later, placing the current character earlier improves the lexicographical order without sacrificing feasibility.

Repeatedly applying this greedy decision produces the lexicographically smallest valid subsequence.

---

# 10. Complexity Analysis

Let `n` be the length of the input string.

### Time Complexity

**First traversal:**

```cpp
for(int i = 0; i < n; i++)
```

O(n), for last occurrence calculation.

**Second traversal:**

```cpp
for(int i = 0; i < n; i++)
```

O(n) iterations.

Although there is a nested `while` loop, every character can be pushed into the result at most once and popped at most once.

Therefore, the total number of stack operations is O(n).

Total:

$$O(n) + O(n) = O(n)$$

**Overall Time Complexity: O(n)**

### Space Complexity

- `last_index`: O(26)
- `taken`: O(26)
- `result`: O(26) characters at most, because there are only 26 lowercase English letters.

Therefore, for the problem's fixed lowercase alphabet:

**Auxiliary Space: O(1)**

If the alphabet size is represented by `A`, the space complexity is O(A).

---

# 11. Common Mistakes

### Mistake 1 — Removing a Character Without Checking Its Last Occurrence

Incorrect idea:

```cpp
if(result.back() > ch) {
    result.pop_back();
}
```

Why is this dangerous?

The removed character may never appear again.

We could lose a required distinct character.

Correct condition:

```cpp
last_index[result.back() - 'a'] > i
```

### Mistake 2 — Forgetting to Reset `taken`

After popping a character:

```cpp
taken[result.back() - 'a'] = false;
```

If this update is missed, the algorithm may incorrectly skip the character's future occurrence.

### Mistake 3 — Using `if` Instead of `while`

One pop may not be enough.

Multiple previous characters may be larger than the current character and safely removable.

### Mistake 4 — Forgetting Duplicate Detection

Without `taken`, duplicate characters may enter the result.

### Mistake 5 — Sorting the Characters

Sorting loses the original subsequence ordering and does not solve the actual problem.

---

# 12. Important Edge Cases

| Input | Expected output | Reason |
|---|---|---|
| `"bcabc"` | `"abc"` | Standard example |
| `"cbacdcbc"` | `"acdb"` | Multiple greedy removals |
| `"aaaa"` | `"a"` | All characters identical |
| `"abcd"` | `"abcd"` | Already increasing |
| `"dcba"` | `"dcba"` | No earlier character can be safely removed |
| `"abacb"` | `"abc"` | Duplicate handling |
| `"bbcaac"` | `"bac"` | Multiple repeated characters |

---

# 13. Interview Follow-up Questions

**Q1. Why do we need the last occurrence array?**

To determine whether a character can safely be removed and selected again later.

**Q2. Why do we need the `taken` array?**

To ensure every distinct character appears exactly once.

**Q3. Why do we use a monotonic stack?**

To remove larger characters from the end whenever a smaller character arrives and the removed characters remain available later.

**Q4. Why is the algorithm greedy?**

At every position, we make the lexicographically beneficial choice whenever it is safe to do so.

**Q5. Why is the nested while loop still O(n)?**

Each character enters and leaves the result at most once.

**Q6. Is the final result always sorted?**

No. It is the lexicographically smallest valid subsequence, not necessarily a sorted string.

---

# 14. Pattern Recognition — When Should You Think of This Approach?

Look for these signals:

- You need a lexicographically smallest or largest subsequence.
- Some elements can be removed to improve the answer.
- Every distinct element must still be represented.
- You need to know whether an element appears again in the future.
- A stack can maintain the currently selected subsequence.

Think:

```text
Greedy decision
      +
Last occurrence tracking
      +
Monotonic stack
      +
Duplicate tracking
```

---

# 15. Final Revision Cheat Sheet

```text
Problem: LC 316 — Remove Duplicate Letters

Goal:
Every distinct character exactly once.
Lexicographically smallest valid subsequence.

Structures:
1. last_index[26]
2. taken[26]
3. result string as stack

For each character ch:

1. If taken[ch]:
       continue

2. While result is not empty
   AND result.back() > ch
   AND last_index[result.back()] > current_index:

       taken[result.back()] = false
       pop_back()

3. Push ch
4. taken[ch] = true

Return result.

Time: O(n)
Auxiliary Space: O(1) for fixed lowercase alphabet.
```

## Final Learning

LC 316 teaches an important variation of the Monotonic Stack pattern.

In traditional NSE/PSE problems, we often remove elements based on numerical comparisons.

Here, removal depends on two things:

1. **Greedy comparison:** Is the current character lexicographically smaller?
2. **Future availability:** Can the removed character be selected again later?

That combination of Greedy + Monotonic Stack + Last Occurrence Tracking is the real takeaway.

**Your Monotonic Stack curated roadmap is now complete: 9/9 questions.**
