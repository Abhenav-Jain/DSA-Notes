# LeetCode 443 — String Compression

**Pattern:** Two Pointers + String + In-place Modification  
**Difficulty:** Medium  
**Approach:** Read Pointer + Write Pointer  
**Goal:** Compress the character array in-place and return the new length.

---

## 1. Problem Understanding

Given a character array `chars`, compress it using the following rules:

1. Consecutive repeating characters are replaced by the character followed by its count.
2. If a character appears only once, store only that character.
3. If the count is greater than 1, store its digits individually.
4. Modify the original array in-place.
5. Return the length of the compressed array.

### Example 1

```text
Input:
chars = ['a','a','b','b','c','c','c']

Output:
6

Modified array:
['a','2','b','2','c','3']
```

### Example 2

```text
Input:
chars = ['a']

Output:
1

Modified array:
['a']
```

### Example 3

```text
Input:
chars = ['a','b','b','b','b','b','b','b','b','b','b']

Output:
5

Modified array:
['a','b','1','0','b']
```

Notice that the count `10` is stored as two separate characters: `'1'` and `'0'`.

---

## 2. Core Intuition

The key observation is that we don't need another array to store the compressed result.

We can modify the original array itself using two pointers.

We maintain:

- `i` → Read Pointer: traverses the original character groups.
- `idx` → Write Pointer: writes the compressed result into the same array.

For every group of consecutive identical characters:

1. Store the current character at `chars[idx]`.
2. Increment `idx`.
3. Count how many times the character occurs consecutively.
4. If the count is greater than 1:
   - Convert the count into a string using `to_string()`.
   - Store each digit individually.
5. Continue with the next group.

At the end, `idx` represents the compressed array's length.

---

## 3. Accepted C++ Implementation

```cpp
class Solution {
public:
    int compress(vector<char>& chars) {
        int n = chars.size();
        int i = 0;
        int idx = 0;

        while(i < n) {
            char curr_character = chars[i];
            int count = 0;

            while(i < n && chars[i] == curr_character) {
                count++;
                i++;
            }

            chars[idx] = curr_character;
            idx++;

            if(count > 1) {
                string count_str = to_string(count);

                for(char &ch : count_str) {
                    chars[idx] = ch;
                    idx++;
                }
            }
        }

        return idx;
    }
};
```

---

## 4. Detailed Code Explanation

### Step 1: Initialize pointers

```cpp
int n = chars.size();
int i = 0;
int idx = 0;
```

- `n` stores the original array size.
- `i` reads characters from the array.
- `idx` tracks where the next compressed character should be written.

Initially, both pointers start at index `0`.

### Step 2: Process each character group

```cpp
while(i < n) {
    char curr_character = chars[i];
    int count = 0;
```

We select the current character and initialize its frequency counter.

### Step 3: Count consecutive occurrences

```cpp
while(i < n && chars[i] == curr_character) {
    count++;
    i++;
}
```

This loop:

- Checks whether we are still within array bounds.
- Checks whether the current character matches the selected character.
- Increments the count.
- Moves the read pointer forward.

When this loop ends, we have processed one complete group.

**Important:** `i` now points to the first character of the next group, or `n` if the current group was the last one.

### Step 4: Write the character

```cpp
chars[idx] = curr_character;
idx++;
```

We write the group character into the compressed portion of the original array.

### Step 5: Write the frequency

```cpp
if(count > 1) {
    string count_str = to_string(count);

    for(char &ch : count_str) {
        chars[idx] = ch;
        idx++;
    }
}
```

Why `count > 1`?

Because a single occurrence does not need a frequency suffix.

Why `to_string(count)`?

Because the frequency may contain multiple digits.

For example:

```text
count = 12

to_string(count) = "12"

Stored characters:
'1', '2'
```

### Step 6: Return compressed length

```cpp
return idx;
```

`idx` is the position immediately after the last written character.

Therefore, it is also the length of the compressed array.

---

## 5. Dry Run — Step by Step

Consider:

```text
chars = ['a','a','b','b','c','c','c']
```

Initial state:

```text
i = 0
idx = 0
```

### Iteration 1 — Character 'a'

Consecutive group:

```text
'a', 'a'
```

Therefore:

```text
curr_character = 'a'
count = 2
```

Write:

```text
chars[0] = 'a'
chars[1] = '2'
```

Pointers:

```text
i = 2
idx = 2
```

### Iteration 2 — Character 'b'

Consecutive group:

```text
'b', 'b'
```

Therefore:

```text
curr_character = 'b'
count = 2
```

Write:

```text
chars[2] = 'b'
chars[3] = '2'
```

Pointers:

```text
i = 4
idx = 4
```

### Iteration 3 — Character 'c'

Consecutive group:

```text
'c', 'c', 'c'
```

Therefore:

```text
curr_character = 'c'
count = 3
```

Write:

```text
chars[4] = 'c'
chars[5] = '3'
```

Pointers:

```text
i = 7
idx = 6
```

Since `i == n`, the outer loop terminates.

Final result:

```text
Compressed array:
['a','2','b','2','c','3']

Returned length = 6
```

---

## 6. Dry Run Table

| Group | Character | Count | Written | Read Pointer `i` | Write Pointer `idx` |
|---|---|---:|---|---:|---:|
| 1 | a | 2 | a, 2 | 2 | 2 |
| 2 | b | 2 | b, 2 | 4 | 4 |
| 3 | c | 3 | c, 3 | 7 | 6 |

---

## 7. Important Edge Cases

### Case 1: Single character

```text
Input: ['a']
Output: 1
Result: ['a']
```

Count is 1, so no frequency is written.

### Case 2: No consecutive duplicates

```text
Input: ['a','b','c']
Output: 3
Result: ['a','b','c']
```

Every character is written individually.

### Case 3: All characters identical

```text
Input: ['a','a','a','a']
Output: 2
Result: ['a','4']
```

Only one group exists.

### Case 4: Two-digit frequency

```text
Input: ['a','a','a','a','a','a','a','a','a','a']

Output: 3

Result: ['a','1','0']
```

The count is converted into a string and each digit is written separately.

### Case 5: Alternating characters

```text
Input: ['a','b','a','b']

Output: 4
Result: ['a','b','a','b']
```

Each character forms its own group.

---

## 8. Why Is In-place Modification Safe?

A common interview question:

**Q: We are reading and writing in the same array. Can writing overwrite unread characters?**

The important invariant is:

```text
idx <= i
```

At the beginning of each outer iteration, `idx` never exceeds `i`.

Why?

- The read pointer consumes an entire group before writing its compressed representation.
- A group of length 1 writes exactly 1 character.
- A group of length greater than 1 writes the character plus the digits of its count.
- For a group of length `c >= 2`, the number of written characters is `1 + number_of_digits(c)`, which is never greater than `c`.

Therefore, the write pointer never moves ahead of the read pointer.

This ensures that unread characters are not overwritten.

---

## 9. Complexity Analysis

Let `n` be the original array length.

**Time Complexity: O(n)**

- The read pointer `i` visits every character exactly once.
- Each group is processed once.
- Writing the compressed characters takes time proportional to the number of characters written.
- Total work remains linear.

**Auxiliary Space: O(1)**

The algorithm modifies the original array directly.

Strict technical note: `to_string(count)` creates a temporary string. Its size is at most the number of digits in `n`, i.e. O(log n). Under the usual interview convention, this is treated as O(1) auxiliary space for this problem because it is temporary count-conversion storage, not an additional result array.

---

## 10. Common Interview Mistakes

1. **Using `count >= 1` instead of `count > 1`:** Single occurrences should not have a count suffix.
2. **Writing the entire integer directly into `vector<char>`:** The count must be converted into individual digit characters.
3. **Forgetting to increment `idx`:** Every written character must advance the write pointer.
4. **Incorrect boundary condition:** Always check `i < n` before accessing `chars[i]`.
5. **Using an extra vector:** The problem explicitly requires in-place modification.
6. **Returning `n`:** The answer must be the compressed length, `idx`.
7. **Assuming count is always one digit:** Counts can be greater than 9.

---

## 11. Interview Follow-up Questions

**Q1. Why do we need two pointers?**

One pointer reads the original groups, while the other writes the compressed representation.

**Q2. Why do we use `to_string()`?**

Because counts may have multiple digits, and each digit must be stored as a separate character.

**Q3. Why is the algorithm O(n)?**

Each input character is read once, and the total number of written characters cannot exceed the original length.

**Q4. Why does the write pointer never overtake the read pointer?**

Compression never increases the length of a group. A group of length `c` produces at most `c` output characters.

**Q5. What is the purpose of `idx`?**

It tracks the next position where a compressed character should be written and ultimately stores the compressed length.

**Q6. Can we use an additional string?**

We could, but that would violate the in-place requirement and use additional result storage.

---

## 12. Pattern Recognition

Whenever a problem asks you to:

- Modify an array in-place.
- Remove or compress elements.
- Maintain a read position and a write position.
- Process consecutive groups.

Think about the **Two Pointers — Read/Write Pointer Pattern**.

Related problems:

- LC 26 — Remove Duplicates from Sorted Array
- LC 27 — Remove Element
- LC 80 — Remove Duplicates from Sorted Array II
- LC 283 — Move Zeroes
- LC 443 — String Compression

---

## Final Revision Cheat Sheet

```text
Pattern: Two Pointers + In-place Modification

i   = Read Pointer
idx = Write Pointer

For every consecutive group:
1. Store current character.
2. Count consecutive occurrences.
3. Write the character.
4. If count > 1, convert count to string.
5. Write every digit individually.

Return idx.

Time: O(n)
Auxiliary Space: O(1) under interview convention
```

**Key invariant:** `idx <= i`

**Most important idea:** Read a complete group, then write its compressed representation into the original array.
