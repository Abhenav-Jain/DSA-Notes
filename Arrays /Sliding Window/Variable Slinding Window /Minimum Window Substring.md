# Minimum Window Substring — LeetCode 76

**Difficulty:** Hard  
**Pattern:** Variable Sliding Window + Frequency Map  
**Core Concepts:** Character Frequency, Valid Window, Greedy Shrinking  
**Approach 1:** Two Hash Maps + Matched Character Count  
**Approach 2:** Optimized Frequency Array + Single Map  
**Time Complexity:** `O(n + m)` average  
**Space Complexity:** `O(Σ)` auxiliary space for a fixed character set

---

## 1. Problem Intuition

Given two strings `s` and `t`, find the **minimum-length substring of `s` containing every character of `t`, including duplicate occurrences**.

The characters may appear in any order, and the substring can contain additional characters.

If no valid window exists, return an empty string.

### Example

```text
s = "ADOBECODEBANC"
t = "ABC"

Output = "BANC"
```

Why?

- `"BANC"` contains `B`, `A`, and `C`.
- Every character required by `t` is present.
- Its length is `4`, and no shorter valid window exists.

### Important: Character Frequency Matters

```text
s = "AAABBC"
t = "AABC"
```

A valid window must contain at least:

```text
A → 2
B → 1
C → 1
```

Simply checking whether a character exists is not enough. We must satisfy its required frequency.

---

## 2. Core Idea — Variable Sliding Window

We maintain two pointers:

- `left`: starting index of the window.
- `right`: ending index of the window.

We use frequency maps to track required and available characters.

### Your variables

| Variable | Purpose |
|---|---|
| `need` | Required frequency of each character in `t` |
| `have` | Frequency of required characters in the current window |
| `requirement` | Total number of characters in `t`, including duplicates |
| `cnt` | Number of required character occurrences currently satisfied |
| `min_len` | Length of the shortest valid window found |
| `ans` | Best substring found so far |

### Window Validity Condition

```cpp
cnt == requirement
```

Once `cnt` reaches `t.length()`, all required character occurrences have been satisfied.

At that point, we try to shrink the window from the left to find the shortest valid substring.

---

## 3. Approach 1 — Your Original Solution

```cpp
class Solution {
public:
    string minWindow(string s, string t) {
        unordered_map<char,int> need;
        unordered_map<char,int> have;

        int left = 0;
        int right = 0;
        int requirement = t.length();
        int n = s.length();
        int cnt = 0;
        int min_len = INT_MAX;
        int len = 0;
        string ans = "";

        for(char c : t) {
            need[c]++;
        }

        while(right < n) {
            if(need.find(s[right]) != need.end()) {
                have[s[right]]++;

                if(have[s[right]] <= need[s[right]]) {
                    cnt++;
                }
            }

            if(cnt == requirement) {
                // Shrink as much as possible
                while(left <= right) {
                    if(need.find(s[left]) == need.end()) {
                        left++;
                    }
                    else if(have[s[left]] > need[s[left]]) {
                        have[s[left]]--;
                        left++;
                    }
                    else {
                        break;
                    }
                }

                len = right - left + 1;

                if(len < min_len) {
                    min_len = len;
                    ans = s.substr(left, min_len);
                }
            }

            right++;
        }

        return ans;
    }
};
```

### Is your code correct?

**Yes, the core logic is correct for the standard LeetCode 76 constraints, where `t` is non-empty.**

The key reason it works is that your shrinking loop removes only:
1. Characters that are not required by `t`.
2. Extra occurrences of required characters.

It never removes an occurrence that is necessary to keep the window valid. Therefore, `cnt` does not need to decrease inside your shrinking loop.

---

## 4. Line-by-Line Explanation

### Step 1 — Build the required frequency map

```cpp
for(char c : t) {
    need[c]++;
}
```

For `t = "AABC"`:

```text
need = {
    A: 2,
    B: 1,
    C: 1
}
```

The total number of required character occurrences is `4`.

### Step 2 — Expand the window

```cpp
if(need.find(s[right]) != need.end()) {
    have[s[right]]++;
}
```

Only characters required by `t` are tracked in `have`.

Irrelevant characters can exist in the substring, but they do not contribute to satisfying the requirements.

### Step 3 — Count only useful occurrences

```cpp
if(have[s[right]] <= need[s[right]]) {
    cnt++;
}
```

Suppose `need['A'] = 2`.

- First `A` increases `cnt`.
- Second `A` increases `cnt`.
- Third `A` does not increase `cnt`, because the required frequency is already satisfied.

This prevents extra occurrences from being counted as additional requirements.

### Step 4 — Start shrinking when the window is valid

```cpp
if(cnt == requirement)
```

At this point, the current window contains every required character occurrence.

Now we greedily remove unnecessary characters from the left.

### Step 5 — Remove irrelevant characters

```cpp
if(need.find(s[left]) == need.end()) {
    left++;
}
```

For example, if the window begins with `D` but `D` is not required, removing it cannot invalidate the window.

### Step 6 — Remove excess occurrences

```cpp
else if(have[s[left]] > need[s[left]]) {
    have[s[left]]--;
    left++;
}
```

Suppose:

```text
need['A'] = 1
have['A'] = 2
```

One `A` is extra. We can remove it without losing validity.

**Important:** Your code correctly decreases `have` here. If the outgoing character is required and its frequency is already exactly what is needed, the loop stops.

### Step 7 — Record the shortest window

```cpp
len = right - left + 1;

if(len < min_len) {
    min_len = len;
    ans = s.substr(left, min_len);
}
```

Once shrinking stops, the current window is the shortest valid window ending at `right`.

If it is shorter than the previous best, save it.

---

## 5. Dry Run

```text
s = "ADOBECODEBANC"
t = "ABC"
```

Required frequencies:

```text
A → 1
B → 1
C → 1
requirement = 3
```

| Window | What happens? | Valid? |
|---|---|---|
| `"A"` | `A` matched | No |
| `"ADOB"` | `A` and `B` matched | No |
| `"ADOBEC"` | `A`, `B`, `C` matched | Yes |
| `"DOBEC"` | Remove extra/unneeded prefix | No longer valid after removing `A` |
| `"CODEBA"` | Still missing `C` in this particular window | No |
| `"BANC"` | All requirements satisfied | Yes |

The algorithm keeps exploring until it discovers the shortest valid window.

Final answer:

```text
"BANC"
```

*Note:* The table illustrates the concept rather than every pointer movement; the code processes every character and shrinks whenever the window is valid.

---

## 6. Optimization — One Frequency Map

Your current implementation uses two hash maps. We can reduce the bookkeeping by maintaining one map of remaining character requirements.

The idea:

- Positive frequency means a character is still needed.
- Zero or negative frequency means the requirement for that character has been satisfied, possibly with extras.
- `missing` tracks the number of required character occurrences not yet covered.

### Optimized C++ Code

```cpp
class Solution {
public:
    string minWindow(string s, string t) {
        if (t.empty() || s.size() < t.size()) {
            return "";
        }

        unordered_map<char, int> need;

        for (char c : t) {
            need[c]++;
        }

        int left = 0;
        int missing = t.size();
        int min_len = INT_MAX;
        int start = 0;

        for (int right = 0; right < s.size(); right++) {
            char c = s[right];

            if (need[c] > 0) {
                missing--;
            }

            need[c]--;

            while (missing == 0) {
                int len = right - left + 1;

                if (len < min_len) {
                    min_len = len;
                    start = left;
                }

                char outgoing = s[left];
                need[outgoing]++;

                if (need[outgoing] > 0) {
                    missing++;
                }

                left++;
            }
        }

        return min_len == INT_MAX
            ? ""
            : s.substr(start, min_len);
    }
};
```

### How this version works

**Expand the window:**

```cpp
if (need[c] > 0) {
    missing--;
}

need[c]--;
```

If the current character is still required, decrease `missing`. Then decrement its frequency to indicate that the window has consumed that character.

**Shrink the valid window:**

```cpp
need[outgoing]++;

if (need[outgoing] > 0) {
    missing++;
}
```

When a character leaves the window, restore its requirement. If its frequency becomes positive, that character is now missing, so the window is no longer valid.

### Example of frequency values

For `t = "AABC"`:

```text
Initially:
A → 2, B → 1, C → 1
missing = 4
```

After the window includes `"AA"`:

```text
A → 0
missing = 2
```

After it also includes `B` and `C`:

```text
A → 0, B → 0, C → 0
missing = 0
```

The window is valid and can be shrunk.

**Note:** `need[c]` uses `operator[]`, which creates an entry with value zero if the character was not already present. This is safe in this implementation: such a character gets a negative count when included, and returning it to zero when removed does not increase `missing`.

---

## 7. Complexity Analysis

Let `n = s.length()` and `m = t.length()`.

### Your two-map solution

- **Time:** `O(n + m)` average.
- **Auxiliary space:** `O(Σ)` for the two maps, where `Σ` is the number of distinct characters tracked.

Both pointers only move forward. The inner shrinking loop does not make the overall algorithm quadratic because `left` advances at most `n` times.

### One-map optimized solution

- **Time:** `O(n + m)` average.
- **Auxiliary space:** `O(Σ)`.

The asymptotic complexity is the same, but the one-map approach has less bookkeeping and can be easier to optimize.

For a fixed-size character set, such as ASCII, auxiliary space is bounded by a constant.

---

## 8. Common Mistakes

1. Using `cnt` to count distinct characters rather than required character occurrences.
2. Ignoring duplicate characters in `t`.
3. Shrinking the window without restoring the outgoing character's frequency.
4. Counting extra occurrences as additional matches.
5. Forgetting to update the minimum length and starting index.
6. Returning the entire current window rather than the shortest one found.
7. Assuming a valid window must contain only characters from `t`. Extra characters are allowed.
8. Forgetting that no valid window means returning `""`.

---

## 9. Pattern Recognition

Use this pattern when a problem asks for:

- A minimum-length substring.
- Every character of a target string must be covered.
- Character frequencies matter.
- You can expand the window until valid and then shrink it greedily.

### Related Problems

- LC 567 — Permutation in String
- LC 438 — Find All Anagrams in a String
- LC 3 — Longest Substring Without Repeating Characters
- LC 424 — Longest Repeating Character Replacement

---

## One-Line Revision

Expand the right pointer until all required character occurrences are covered, then greedily shrink from the left while preserving validity and keep the shortest window.

**Interview takeaway:** Your two-map solution is a valid approach. The one-map `missing` technique is a useful optimization to learn because it combines frequency tracking and validity checking in a single structure.
