# 22. Generate Parentheses

**Platform:** LeetCode · **Difficulty:** Medium · **Topic:** Recursion, Backtracking

## Problem

Given `n` pairs of parentheses, generate **all combinations of well-formed (balanced) parentheses**.

```
Input:  n = 3
Output: ["((()))", "(()())", "(())()", "()(())", "()()()"]

Input:  n = 1
Output: ["()"]
```

**Constraints:** `1 <= n <= 8`

---

## Key Observations

- Every valid string has length `2n`: exactly `n` opening `(` and `n` closing `)` brackets.
- A string is valid if, while scanning left to right:
  1. the number of `)` **never exceeds** the number of `(` in any prefix, and
  2. at the end, the number of `(` equals the number of `)`.

---

## Approach 1: Brute Force (Generate All + Validate)

### Idea

At each of the `2n` positions there are 2 choices: `(` or `)`.
Generate **all `2^(2n)` strings** using recursion, and at the end check each one with a stack-based validity check. Keep only the valid ones.

### Recursion Tree (n = 1, length = 2)

```
                  ""
             /          \
          "("            ")"
         /    \         /    \
      "(("   "()"    ")("   "))"
       ✗      ✓       ✗      ✗
```

All 4 strings are generated; only `"()"` passes the validity check.

### Validity Check (Stack)

- On `(` → push.
- On `)` → if the stack is empty, there's no matching `(`, so it's **invalid**; otherwise pop.
- At the end → valid only if the stack is empty (no unmatched `(` left).

### Code

```cpp
class Solution {
public:
    bool valid_parentheis(string s){
        stack<char> st;
        for(char c : s){
            if(c == '('){
                st.push(c);
            }
            else if(!st.empty() && c == ')'){
                st.pop();
            }
            else if(st.empty() && c == ')'){
                return false;
            }
        }
        return st.empty();
    }
    void solve(vector<string>& ans,string temp,int n,vector<char>& marker){
        if(n == 0){
            if(valid_parentheis(temp)){
                ans.push_back(temp);
            }
            return;
        }
        temp.push_back(marker[0]);
        solve(ans,temp,n-1,marker);
        temp.pop_back();
        temp.push_back(marker[1]);
        solve(ans,temp,n-1,marker);
    }
    vector<string> generateParenthesis(int n) {
        vector<char> marker = {'(',')'};
        vector<string> ans;
        string temp = "";
        int mark = 2 * n;
        solve(ans,temp,mark,marker);
        return ans;
    }
};
```

### Complexity

| | |
|---|---|
| **Time** | `O(2^(2n) · n)`: `2^(2n)` strings, each validated in `O(n)` |
| **Space** | `O(n)` recursion depth (`2n`) + `O(n)` stack for validation (excluding output) |

### Drawback

It wastes a lot of work. A prefix like `")"` or `"())"` can **never** become valid, but we still extend it all the way to length `2n` before rejecting it.

> 💡 The stack only ever stores `(`, so it can be replaced with a simple integer counter (`balance++` / `balance--`) for `O(1)` extra space.

---

## Approach 2: Optimized Backtracking (Prune Invalid Branches)

### Idea

Instead of generating everything and validating at the end, **stop a branch early** as soon as it becomes invalid. Track two counts:

- `open`: number of `(` used so far
- `close`: number of `)` used so far

Prune (return) when:

1. `open > n`: used more `(` than allowed.
2. `close > open`: a `)` has no matching `(` (this prefix can never be fixed).

At the end (`2n` characters placed), accept the string if `open == close`.

### Why the final check is enough

When `n == 0`, the string has `2n` characters, so `open + close = 2n`. If `open == close`, both equal `n`.
Every earlier prefix already passed the `close > open` check, so the string is balanced. No stack is needed.

### Recursion Tree (n = 2, pruned)

```
                         ""
                /                 \
             "("                  ")"  ✗ (close > open)
           /      \
        "(("       "()"
       /    \      /    \
   "((("✗  "(()"  "()("  "())"✗
   open>n   |      |
          "(())" "()()"
            ✓      ✓
```

Invalid branches are cut off immediately instead of being expanded.

### Code

```cpp
class Solution {
public:
    void solve(vector<string>& ans,string temp,int n,vector<char>& marker,int open,int close,int nole){
        if(n == 0){
            if(open == close){
                ans.push_back(temp);
            }
            return;
        }
        if(open > nole){
            return;
        }
        if(close > open){
            return;
        }
        temp.push_back(marker[0]);
        solve(ans,temp,n-1,marker,open+1,close,nole);
        temp.pop_back();
        temp.push_back(marker[1]);
        solve(ans,temp,n-1,marker,open,close+1,nole);
    }
    vector<string> generateParenthesis(int n) {
        vector<char> marker = {'(',')'};
        vector<string> ans;
        string temp = "";
        int mark = 2 * n;
        int open = 0;
        int close = 0;
        int nole = n;
        solve(ans,temp,mark,marker,open,close,nole);
        return ans;
    }
};
```

### Complexity

| | |
|---|---|
| **Time** | `O(4^n / √n)`: proportional to the number of valid results (the n-th Catalan number) times their length |
| **Space** | `O(n)` recursion depth (excluding output) |

---

## Cleaner Version (Same Logic)

Small refinements:

- Check **before** recursing, so dead branches are never called.
- Pass `temp` **by reference** to avoid copying the string on every call.
- `marker` and the `n` countdown aren't needed (`temp.size()` tells us the length).

```cpp
class Solution {
public:
    void solve(vector<string>& ans, string& temp, int open, int close, int n) {
        if (temp.size() == 2 * n) { ans.push_back(temp); return; }
        if (open < n)     { temp.push_back('('); solve(ans, temp, open + 1, close, n); temp.pop_back(); }
        if (close < open) { temp.push_back(')'); solve(ans, temp, open, close + 1, n); temp.pop_back(); }
    }
    vector<string> generateParenthesis(int n) {
        vector<string> ans;
        string temp;
        solve(ans, temp, 0, 0, n);
        return ans;
    }
};
```

---

## Comparison

| | Brute Force | Optimized Backtracking |
|---|---|---|
| Strategy | Generate all, validate at end | Prune invalid prefixes early |
| Strings explored | All `2^(2n)` | Only valid prefixes |
| Validation | Stack, `O(n)` per string | `open` / `close` counters, `O(1)` |
| Time | `O(2^(2n) · n)` | `O(4^n / √n)` |
| Space | `O(n)` | `O(n)` |

## Takeaways

- **Brute force → backtracking:** if you can tell a partial answer is already invalid, stop exploring it.
- Rules for placing brackets:
  - add `(` only if `open < n`
  - add `)` only if `close < open`
- Use counters instead of a stack when there's only one kind of bracket.
