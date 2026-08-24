# Letter Combinations of a Phone Number

**LeetCode:** 17 — Letter Combinations of a Phone Number
**Concept:** Recursion + Backtracking
**Difficulty:** Medium

---

## 1. Problem

Given a string containing digits from `2` to `9`, return all possible letter combinations that the digits can represent.

The mapping is the same as a telephone keypad:

```text
2 → abc
3 → def
4 → ghi
5 → jkl
6 → mno
7 → pqrs
8 → tuv
9 → wxyz
```

### Example

```text
Input:
digits = "23"

Output:
["ad","ae","af","bd","be","bf","cd","ce","cf"]
```

For digit `2`, we have 3 choices:

```text
a
b
c
```

For digit `3`, we have 3 choices:

```text
d
e
f
```

Therefore:

```text
3 × 3 = 9 combinations
```

---

# 2. Concept

## Recursion + Backtracking

The key observation is:

> **For every digit, we have multiple possible characters. We need to try every character.**

For example:

```text
digits = "23"
```

For `2`:

```text
abc
```

For every character of `"abc"`, we recursively process `3`.

```text
                 ""
              /   |   \
             a    b    c
           / | \ /|\ /|\
          ad ae af bd be bf cd ce cf
```

This is a classic **choice → recursion → undo choice** pattern.

---

# 3. How to Think About the Problem

Suppose:

```text
digits = "23"
```

We start at:

```text
idx = 0
```

Current digit:

```text
'2'
```

Its corresponding letters are:

```text
"abc"
```

So we have 3 choices:

```text
a
b
c
```

For each choice:

```text
choose character
      ↓
move to next digit
      ↓
recursively generate combinations
      ↓
remove chosen character
      ↓
try next character
```

This is exactly **backtracking**.

---

# 4. Data Structure for Digit → Letters

We need to store the mapping.

```cpp
unordered_map<int, string> mp;

mp[2] = "abc";
mp[3] = "def";
mp[4] = "ghi";
mp[5] = "jkl";
mp[6] = "mno";
mp[7] = "pqrs";
mp[8] = "tuv";
mp[9] = "wxyz";
```

### Important

`digits[idx]` is a character.

For example:

```cpp
digits[idx] = '2';
```

But our map key is:

```cpp
2
```

Therefore:

```cpp
digits[idx] - '0'
```

converts:

```text
'2' → 2
```

So we access:

```cpp
mp[digits[idx] - '0']
```

---

# 5. Recursive State

Our DFS needs:

```text
idx
temp
ans
```

### `idx`

Which digit are we currently processing?

```text
idx = 0 → first digit
idx = 1 → second digit
idx = 2 → third digit
```

### `temp`

The current combination being constructed.

Example:

```text
temp = "ad"
```

means:

> We have selected `a` from the first digit and `d` from the second digit.

### `ans`

Stores all completed combinations.

---

# 6. Base Case

When:

```cpp
idx == digits.length()
```

we have processed every digit.

Therefore:

```cpp
ans.push_back(temp);
return;
```

Example:

```text
digits = "23"
temp = "ad"
idx = 2
```

Since:

```text
idx == digits.length()
```

`"ad"` is a complete answer.

---

# 7. Core Backtracking Logic

First get the letters corresponding to the current digit:

```cpp
string letters = mp[digits[idx] - '0'];
```

Suppose:

```text
digits[idx] = '2'
```

Then:

```text
letters = "abc"
```

Now iterate through every possible choice:

```cpp
for(char el : letters)
```

For each character:

### Step 1 — Choose

```cpp
temp.push_back(el);
```

### Step 2 — Explore

```cpp
dfs(digits, idx + 1, mp, ans, temp);
```

### Step 3 — Undo

```cpp
temp.pop_back();
```

So the complete pattern is:

```text
Choose
   ↓
Explore
   ↓
Undo
```

This is one of the most important patterns in Backtracking.

---

# 8. Why `pop_back()` Is Necessary

Suppose:

```text
letters = "abc"
```

Initially:

```text
temp = ""
```

### Choose `a`

```text
temp = "a"
```

After recursion returns:

```text
temp = "a"
```

Now we want to try `b`.

Without `pop_back()`:

```text
temp.push_back('b')
```

would produce:

```text
"ab"
```

❌ Wrong.

We need:

```text
"b"
```

So:

```cpp
temp.pop_back();
```

changes:

```text
"a" → ""
```

Then:

```cpp
temp.push_back('b');
```

gives:

```text
"b"
```

Therefore:

```text
push_back()
    ↓
choose

recursive call
    ↓
explore

pop_back()
    ↓
undo
```

---

# 9. Dry Run

Consider:

```text
digits = "23"
```

### Initial State

```text
idx = 0
temp = ""
```

Current digit:

```text
'2'
```

Mapping:

```text
"abc"
```

---

### Choice 1 — `a`

```text
temp = "a"
```

Move to:

```text
idx = 1
```

Current digit:

```text
'3'
```

Mapping:

```text
"def"
```

#### Choose `d`

```text
temp = "ad"
```

`idx = 2`

Base case:

```text
ans = ["ad"]
```

Undo:

```text
temp = "a"
```

#### Choose `e`

```text
temp = "ae"
```

Answer:

```text
ans = ["ad", "ae"]
```

Undo:

```text
temp = "a"
```

#### Choose `f`

```text
temp = "af"
```

Answer:

```text
ans = ["ad", "ae", "af"]
```

Undo:

```text
temp = "a"
```

---

### Choice 2 — `b`

First undo `a`:

```text
temp = ""
```

Choose `b`:

```text
temp = "b"
```

Then generate:

```text
bd
be
bf
```

---

### Choice 3 — `c`

Similarly:

```text
cd
ce
cf
```

Final answer:

```text
[
    "ad",
    "ae",
    "af",
    "bd",
    "be",
    "bf",
    "cd",
    "ce",
    "cf"
]
```

---

# 10. Recursion Tree

For:

```text
digits = "23"
```

```text
                       ""
                  /     |     \
                 a      b      c
               / | \  / | \  / | \
              ad ae af bd be bf cd ce cf
```

Notice:

```text
Digit 2 → 3 choices
Digit 3 → 3 choices
```

Therefore:

```text
3 × 3 = 9
```

combinations.

If the digit is `7` or `9`, there are 4 choices instead of 3.

---

# 11. Code

```cpp
class Solution {
public:
    void dfs(string digits,
             int idx,
             unordered_map<int, string> mp,
             vector<string>& ans,
             string temp) {

        int n = digits.length();

        if(idx == n) {
            ans.push_back(temp);
            return;
        }

        string letters = mp[digits[idx] - '0'];

        for(char el : letters) {
            temp.push_back(el);

            dfs(digits, idx + 1, mp, ans, temp);

            temp.pop_back();
        }
    }

    vector<string> letterCombinations(string digits) {

        if(digits.empty())
            return {};

        unordered_map<int, string> mp;

        mp[2] = "abc";
        mp[3] = "def";
        mp[4] = "ghi";
        mp[5] = "jkl";
        mp[6] = "mno";
        mp[7] = "pqrs";
        mp[8] = "tuv";
        mp[9] = "wxyz";

        vector<string> ans;
        string temp = "";

        dfs(digits, 0, mp, ans, temp);

        return ans;
    }
};
```

---

# 12. One Small Improvement

In the current implementation:

```cpp
unordered_map<int, string> mp
```

is passed by value into every recursive call.

That means the map can be copied repeatedly.

A better version is:

```cpp
unordered_map<int, string>& mp
```

So the function becomes:

```cpp
void dfs(string digits,
         int idx,
         unordered_map<int, string>& mp,
         vector<string>& ans,
         string temp)
```

This avoids unnecessary map copies.

An even cleaner approach is to make the digit-to-letter mapping a class member or use a fixed array/string table.

---

# 13. Important Edge Case

If:

```text
digits = ""
```

there are no digits to process.

Expected result:

```text
[]
```

Therefore we handle it before starting DFS:

```cpp
if(digits.empty())
    return {};
```

Otherwise, the base case would incorrectly add an empty string to `ans`.

---

# 14. Complexity

Let:

```text
n = number of digits
```

Each digit can produce at most 4 choices.

Therefore, in the worst case:

```text
Number of combinations = 4^n
```

Every combination has length `n`.

### Time Complexity

```text
O(n × 4^n)
```

### Auxiliary Space

Recursion depth + current `temp`:

```text
O(n)
```

### Output Space

There can be:

```text
4^n
```

strings, each of length `n`.

Therefore:

```text
O(n × 4^n)
```

for storing the final answer.

---

# 15. Pattern Recognition

This question teaches an extremely important backtracking pattern.

When you see:

> **For every element, there are multiple choices and we need all possible combinations.**

Think:

```text
Backtracking
```

General structure:

```cpp
for(each possible choice) {

    choose

    dfs(next state)

    undo choice
}
```

For this problem:

```cpp
for(char el : letters) {

    temp.push_back(el);       // Choose

    dfs(...);                 // Explore

    temp.pop_back();          // Undo
}
```

---

# 16. Difference From Letter Case Permutation

### Letter Case Permutation

For every letter:

```text
2 choices
```

```text
'a' → a / A
'b' → b / B
```

### Letter Combinations of Phone Number

For every digit:

```text
3 or 4 choices
```

```text
2 → a / b / c
7 → p / q / r / s
```

But the underlying pattern is the same:

```text
Current element
      ↓
Generate possible choices
      ↓
Choose one
      ↓
Recursively process next element
      ↓
Undo choice
```

This is the important connection between the two questions.

---

# 17. Mistakes / Learning

### Mistake 1 — Treating the entire mapped string as one character

Wrong idea:

```cpp
temp.push_back(mp[digits[idx]]);
```

`mp[digits[idx]]` is a `string`, not a `char`.

Correct idea:

```cpp
string letters = mp[digits[idx] - '0'];

for(char el : letters) {
    ...
}
```

---

### Mistake 2 — Forgetting character-to-integer conversion

```cpp
digits[idx]
```

is:

```text
'2'
```

while the map key is:

```text
2
```

Therefore:

```cpp
digits[idx] - '0'
```

is required.

---

### Mistake 3 — Understanding `pop_back()`

Initially it may look unnecessary.

But it is what allows us to reuse the same `temp` for different branches.

```text
temp = "a"

explore "ad"
    ↓
pop 'd'
    ↓
temp = "a"

explore "ae"
```

Then after finishing all choices for `a`:

```text
pop 'a'
```

and move to:

```text
b
```

---

# 18. Key Takeaways

### 🔑 Takeaway 1

**Backtracking = Choose → Explore → Undo**

```cpp
temp.push_back(choice);
dfs(...);
temp.pop_back();
```

---

### 🔑 Takeaway 2

When a value maps to multiple possibilities, loop over those possibilities.

```cpp
for(char ch : letters)
```

---

### 🔑 Takeaway 3

`temp` represents the **current path**.

```text
temp = current combination
```

`ans` represents:

```text
all completed combinations
```

---

### 🔑 Takeaway 4

The recursion tree directly represents the number of choices.

```text
2 choices × 3 choices × 4 choices ...
```

---

# ⭐ One-Line Revision

> **For every digit, try every mapped letter, recursively process the next digit, and remove the chosen letter after returning — Choose → Explore → Undo.**

---

# 🔗 Similar Backtracking Questions

After this question, good questions to connect with this pattern:

1. Letter Case Permutation — LeetCode 784
2. Subsets — LeetCode 78
3. Permutations — LeetCode 46
4. Combination Sum — LeetCode 39
5. Combination Sum II — LeetCode 40
6. Generate Parentheses — LeetCode 22
7. N-Queens — LeetCode 51

These gradually increase the complexity of the same **choice → recursion → backtracking** idea.
