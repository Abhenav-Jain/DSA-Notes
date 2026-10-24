# LeetCode 1832 — Check if the Sentence Is Pangram

**Pattern:** String + Array + Character-to-Index Mapping  
**Difficulty:** Easy  
**Topic:** Character Frequency / Presence Tracking

---

## 1. Problem Understanding

Given a string `sentence`, determine whether it is a pangram.

A pangram is a sentence containing every letter of the English alphabet at least once.

There are 26 lowercase English letters:

```text
a, b, c, d, ..., x, y, z
```

Return:
- `true` if all 26 letters are present.
- `false` if at least one letter is missing.

### Example 1

```text
Input:
sentence = "thequickbrownfoxjumpsoverthelazydog"

Output:
true
```

All 26 English alphabets are present.

### Example 2

```text
Input:
sentence = "leetcode"

Output:
false
```

Several alphabets are missing.

---

## 2. Core Intuition

We need to check whether all 26 English alphabets occur in the given string.

Instead of repeatedly searching for each character, we can maintain an array of size 26.

The most important concept is:

**Character-to-Index Mapping**

```cpp
index = character - 'a';
```

Mapping:

| Character | Index |
|---|---:|
| a | 0 |
| b | 1 |
| c | 2 |
| d | 3 |
| ... | ... |
| z | 25 |

For example:

```cpp
'a' - 'a' = 0
'b' - 'a' = 1
'c' - 'a' = 2
'z' - 'a' = 25
```

This allows us to represent all 26 alphabets using a fixed-size array.

There are two approaches.

---

# APPROACH 1 — Frequency Array

## 3. Intuition

Maintain an integer array of size 26.

Initially, every index contains zero.

Whenever a character appears, increment its corresponding frequency.

After traversing the string, check whether every alphabet has a non-zero frequency.

If any frequency is zero, the sentence is not a pangram.

## 4. C++ Implementation

```cpp
class Solution {
public:
    bool checkIfPangram(string sentence) {

        vector<int> freq(26, 0);

        for(char c : sentence) {
            freq[c - 'a']++;
        }

        for(int i = 0; i < 26; i++) {
            if(freq[i] == 0) {
                return false;
            }
        }

        return true;
    }
};
```

**Note:** Your original implementation uses `vector<char>`. It works under the given problem constraints, but `vector<int>` is preferable for frequency counting because frequencies are integer values.

## 5. Code Explanation

### Step 1: Initialize frequency array

```cpp
vector<int> freq(26, 0);
```

Creates an array containing 26 zeroes.

Each index represents one English alphabet.

### Step 2: Traverse the string

```cpp
for(char c : sentence) {
    freq[c - 'a']++;
}
```

For each character:
1. Convert it into an index.
2. Increment its frequency.

For example:

```text
sentence = "aabbc"

freq[0] = 2
freq[1] = 2
freq[2] = 1
```

### Step 3: Check all alphabets

```cpp
for(int i = 0; i < 26; i++) {
    if(freq[i] == 0) {
        return false;
    }
}
```

If any alphabet has zero frequency, it is missing.

### Step 4: Return true

```cpp
return true;
```

If all 26 frequencies are greater than zero, the sentence is a pangram.

---

# APPROACH 2 — Boolean Presence Array

## 6. Intuition

In this problem, we don't actually need to know how many times a character appears.

We only need to know whether it has appeared at least once.

Therefore, instead of storing frequencies, we can store boolean values.

- `false` → Character has not appeared.
- `true` → Character has appeared.

This avoids maintaining unnecessary frequency information.

## 7. C++ Implementation

```cpp
class Solution {
public:
    bool checkIfPangram(string sentence) {

        vector<bool> seen(26, false);

        for(char c : sentence) {
            seen[c - 'a'] = true;
        }

        for(bool present : seen) {
            if(!present) {
                return false;
            }
        }

        return true;
    }
};
```

## 8. Code Explanation

### Step 1: Initialize boolean array

```cpp
vector<bool> seen(26, false);
```

Initially, all alphabets are marked as absent.

### Step 2: Mark encountered characters

```cpp
for(char c : sentence) {
    seen[c - 'a'] = true;
}
```

Whenever a character appears, mark its corresponding index as `true`.

For example:

```text
sentence = "aabbc"

seen[0] = true
seen[1] = true
seen[2] = true
```

Repeated characters do not change the result.

### Step 3: Check all positions

```cpp
for(bool present : seen) {
    if(!present) {
        return false;
    }
}
```

If even one position is false, at least one alphabet is missing.

Otherwise, return true.

---

# 9. Dry Run — Boolean Approach

Consider:

```text
sentence = "thequickbrownfoxjumpsoverthelazydog"
```

Initially:

```text
seen = [false, false, false, ..., false]
```

When we encounter `'a'`:

```cpp
seen['a' - 'a'] = true;
seen[0] = true;
```

When we encounter `'b'`:

```cpp
seen['b' - 'a'] = true;
seen[1] = true;
```

Similarly, every encountered alphabet is marked true.

After processing the complete sentence:

```text
a → true
b → true
c → true
d → true
...
z → true
```

All positions are true.

Therefore:

```text
Output = true
```

---

# 10. Difference Between Both Approaches

| Parameter | Frequency Array | Boolean Array |
|---|---|---|
| Data stored | Number of occurrences | Presence/absence |
| Data type | `int` | `bool` |
| Initialization | 0 | false |
| Update | `freq[index]++` | `seen[index] = true` |
| Repeated characters | Frequency increases | No change |
| Best suited for | Frequency-based problems | Presence-check problems |

### Which approach should we prefer?

For this particular problem, **Boolean Presence Array** is conceptually better because we only need to check whether every alphabet exists.

However, Frequency Array is more reusable for other string problems.

---

# 11. Complexity Analysis

Let `n` be the length of the sentence.

### Frequency Array

- First traversal: O(n)
- Checking all 26 alphabets: O(26)
- Total: O(n + 26) = O(n)
- Auxiliary Space: O(26) = O(1)

### Boolean Array

- First traversal: O(n)
- Checking all 26 positions: O(26)
- Total: O(n + 26) = O(n)
- Auxiliary Space: O(26) = O(1)

Both approaches have the same asymptotic time and space complexity.

---

# 12. Common Interview Mistakes

1. **Incorrect character mapping**

   Incorrect:
   ```cpp
   freq[c]++;
   ```

   Correct:
   ```cpp
   freq[c - 'a']++;
   ```

2. **Incorrect array size**

   For lowercase English alphabets, we need exactly 26 positions.

3. **Checking only string length**

   A string can have 26 or more characters and still miss some alphabets.

4. **Confusing frequency with presence**

   This problem requires presence, not exact character counts.

5. **Returning true too early**

   Finding all alphabets is necessary. We must process the entire string or have a valid early-termination condition.

6. **Ignoring character constraints**

   The expression `c - 'a'` assumes lowercase English letters, as guaranteed by this problem.

---

# 13. Interview Follow-up Questions

**Q1. Why do we use `c - 'a'`?**

To convert a lowercase character into a zero-based index between 0 and 25.

**Q2. Why is the array size 26?**

Because the English alphabet contains 26 lowercase letters.

**Q3. Why is a boolean array sufficient?**

Because we only need to determine whether a character has appeared, not how many times.

**Q4. When should we use a frequency array instead?**

When the problem asks for character counts, duplicate detection, anagram checking, or frequency comparisons.

**Q5. Can we solve this using an unordered_set?**

Yes. Insert each character into a set and check whether the set contains all 26 distinct characters. However, a fixed-size array is simpler and has constant space with lower overhead.

**Q6. Can we return early?**

Yes. In the boolean approach, after marking a newly encountered character, we can maintain a counter of distinct characters. Once it reaches 26, we can return true.

---

# 14. Related String Problems

These problems use similar character mapping, frequency, or presence-tracking concepts:

1. LC 242 — Valid Anagram
2. LC 383 — Ransom Note
3. LC 387 — First Unique Character in a String
4. LC 389 — Find the Difference
5. LC 409 — Longest Palindrome
6. LC 438 — Find All Anagrams in a String
7. LC 567 — Permutation in String

---

# FINAL REVISION CHEAT SHEET

```cpp
// Frequency Array
vector<int> freq(26, 0);

for(char c : sentence) {
    freq[c - 'a']++;
}

// Boolean Presence Array
vector<bool> seen(26, false);

for(char c : sentence) {
    seen[c - 'a'] = true;
}
```

**Core Concept:** Character-to-Index Mapping

**Frequency Array:** How many times did a character occur?

**Boolean Array:** Has the character appeared?

**Time Complexity:** O(n)

**Auxiliary Space:** O(1)

**Pattern:** String + Fixed-size Array + Hashing-style Character Mapping
