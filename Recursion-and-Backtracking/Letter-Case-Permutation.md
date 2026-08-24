# Letter Case Permutation

**LeetCode:** 784 — Letter Case Permutation
**Concept:** Recursion + Backtracking
**Difficulty:** Medium

---

## 1. Problem

Given a string `s` containing letters and digits, return all possible strings after changing the case of every letter.

Digits remain unchanged.

### Example

```text
Input:
s = "a1b2"

Output:
["a1b2", "a1B2", "A1b2", "A1B2"]
```

For every **letter**, we have 2 choices:

```text
lowercase
uppercase
```

For every **digit**, we have only 1 choice:

```text
digit itself
```

---

## 2. Concept

### Recursion + Backtracking

The important observation is:

> At every character, we have to make a decision.

```text
Digit
   ↓
Only one choice

Letter
   ↓
Two choices
├── lowercase
└── uppercase
```

Because we need **all possible combinations**, recursion is a natural fit.

---

## 3. Intuition

Suppose:

```text
s = "ab"
```

For `'a'`:

```text
'a'
'A'
```

For each of these, `'b'` again has two choices:

```text
        ""
       /  \
      a    A
     / \  / \
    ab aB Ab AB
```

Therefore, with `n` letters, the number of possible answers is:

```text
2^n
```

Digits do not increase the number of possibilities.

---

## 4. Approach

We maintain:

```cpp
idx
```

which tells us which character we are currently processing.

And:

```cpp
temp
```

which stores the string constructed so far.

### Case 1: Current character is a digit

Example:

```text
"3"
```

There is only one possibility.

So:

```cpp
temp.push_back(s[idx]);
solve(idx + 1, ...);
```

---

### Case 2: Current character is a letter

We have two possibilities.

#### Choice 1 — lowercase

```cpp
temp.push_back(tolower(s[idx]));
solve(idx + 1, ...);
```

#### Choice 2 — uppercase

Instead of adding another character, we replace the last character:

```cpp
temp.back() = toupper(s[idx]);
solve(idx + 1, ...);
```

Why?

Because after the first choice:

```text
temp = "a"
```

We need:

```text
"A"
```

not:

```text
"aA"
```

So `temp.back()` changes the last character.

---

## 5. Base Case

When:

```cpp
idx == s.size()
```

we have processed the complete string.

Therefore:

```cpp
ans.push_back(temp);
return;
```

The `return` is important because otherwise the function would continue and try to access:

```cpp
s[idx]
```

when `idx == s.size()`.

---

## 6. C++ Solution

```cpp
class Solution {
public:
    void solve(int idx, string s, vector<string>& ans, string temp) {
        if(idx == s.size()) {
            ans.push_back(temp);
            return;
        }

        if(isdigit(s[idx])) {
            temp.push_back(s[idx]);
            solve(idx + 1, s, ans, temp);
        }
        else {
            // Choice 1: lowercase
            temp.push_back(tolower(s[idx]));
            solve(idx + 1, s, ans, temp);

            // Choice 2: uppercase
            temp.back() = toupper(s[idx]);
            solve(idx + 1, s, ans, temp);
        }
    }

    vector<string> letterCasePermutation(string s) {
        vector<string> ans;
        string temp = "";

        solve(0, s, ans, temp);

        return ans;
    }
};
```

---

## 7. Dry Run

Consider:

```text
s = "a1"
```

### Start

```text
idx = 0
temp = ""
```

Current character:

```text
'a'
```

It is a letter, so two branches.

### Branch 1 — lowercase

```text
temp = "a"
```

Move to:

```text
idx = 1
```

Current character:

```text
'1'
```

It's a digit.

```text
temp = "a1"
```

Now:

```text
idx = 2
```

Base case:

```text
ans = ["a1"]
```

---

### Branch 2 — uppercase

After completing the first branch:

```cpp
temp.back() = toupper(s[idx]);
```

So:

```text
temp = "A"
```

Again process `'1'`:

```text
temp = "A1"
```

Base case:

```text
ans = ["a1", "A1"]
```

Final answer:

```text
["a1", "A1"]
```

---

## 8. Recursion Tree

For:

```text
s = "ab"
```

```text
                    ""
                  /    \
                "a"    "A"
               /  \    /  \
             "ab" "aB" "Ab" "AB"
```

Every letter creates **2 branches**.

Therefore:

```text
Number of leaves = 2^n
```

where `n` = number of letters.

---

## 9. Why `temp.back()`?

This was an important learning point in this question.

Suppose:

```cpp
temp = "a";
```

If we do:

```cpp
temp.push_back('A');
```

we get:

```text
"aA"
```

But we actually want:

```text
"A"
```

So:

```cpp
temp.back() = 'A';
```

changes the last character:

```text
"a" → "A"
```

### Remember

```text
push_back()
    ↓
Adds a new character

back()
    ↓
Accesses the last character
```

---

## 10. Mistakes I Made

### Mistake 1 — Using wrong function names

Initially:

```cpp
is_num()
to_lowercase()
to_uppercase()
```

These are not the standard C++ functions.

Correct:

```cpp
isdigit()
tolower()
toupper()
```

---

### Mistake 2 — Forgetting `return` in base case

Wrong:

```cpp
if(idx == s.size()) {
    ans.push_back(temp);
}
```

Correct:

```cpp
if(idx == s.size()) {
    ans.push_back(temp);
    return;
}
```

---

### Mistake 3 — Adding uppercase after lowercase

Wrong:

```cpp
temp.push_back(tolower(s[idx]));
solve(...);

temp.push_back(toupper(s[idx]));
solve(...);
```

This can produce:

```text
"aA"
```

instead of:

```text
"A"
```

Correct:

```cpp
temp.push_back(tolower(s[idx]));
solve(...);

temp.back() = toupper(s[idx]);
solve(...);
```

---

### Mistake 4 — Confusing `string` and `vector<string>`

`temp` represents **one currently constructed string**.

Therefore:

```cpp
string temp
```

not:

```cpp
vector<string> temp
```

`ans` is the collection of all answers:

```cpp
vector<string>& ans
```

So:

```text
temp → one answer being constructed
ans  → all completed answers
```

---

## 11. Complexity

Let `L` be the number of letters in the string.

There are:

```text
2^L
```

possible answers.

Each answer has length `n`.

Therefore:

### Time Complexity

```text
O(n × 2^L)
```

### Space Complexity

Recursion depth:

```text
O(n)
```

But storing all answers requires:

```text
O(n × 2^L)
```

So considering the output:

```text
O(n × 2^L)
```

---

## 12. Pattern Recognition

### How do I recognize this pattern?

If a question says:

> Every element/character can have multiple choices and I need to generate **all possible combinations**, think:

```text
Recursion / Backtracking
```

For this question:

```text
Digit  → 1 choice
Letter → 2 choices
```

So the recursion tree naturally appears.

---

## 13. Key Takeaways

### 🔑 1.

When every element gives multiple choices:

```text
Choice 1
Choice 2
...
```

think **recursion tree**.

### 🔑 2.

If we need **all possible answers**, recursion/backtracking is often involved.

### 🔑 3.

`temp` represents the current path.

```text
temp = current answer being built
```

### 🔑 4.

`ans` represents all completed paths.

```text
ans = all answers
```

### 🔑 5.

`push_back()` adds.

```text
"a" → "aA"
```

`back()` lets us modify the last character.

```text
"a" → "A"
```

---

## 14. Similar Questions

After understanding this problem, good follow-up questions are:

1. **Subsets** — LeetCode 78
2. **Subsets II** — LeetCode 90
3. **Permutations** — LeetCode 46
4. **Permutations II** — LeetCode 47
5. **Combination Sum** — LeetCode 39
6. **Combination Sum II** — LeetCode 40

These will strengthen the same **choice → recursion → explore all possibilities** pattern.

---

## ⭐ One-Line Revision

> **If every element gives me multiple choices and I need every possible result, build a recursion tree and recursively explore each choice.**
