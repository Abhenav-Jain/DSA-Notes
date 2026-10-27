# LeetCode 49 — Group Anagrams

**Pattern:** Hashing + String Manipulation  
**Difficulty:** Medium  
**Approaches:**
1. Sorting + HashMap
2. Character Frequency + HashMap

---

# 1. Problem Statement

Given an array of strings `strs`, group the anagrams together.

You can return the answer in any order.

### What is an Anagram?

Two strings are anagrams if they contain exactly the same characters with the same frequencies, but their order may be different.

Examples:

```cpp
"eat" and "tea"  // Anagrams
"listen" and "silent" // Anagrams
"rat" and "car"  // Not Anagrams
```

### Example 1

```cpp
Input:
strs = ["eat","tea","tan","ate","nat","bat"]

Output:
[["bat"],["nat","tan"],["ate","eat","tea"]]
```

### Example 2

```cpp
Input:
strs = [""]

Output:
[[""]]
```

### Example 3

```cpp
Input:
strs = ["a"]

Output:
[["a"]]
```

---

# 2. Understand the Actual Problem

Our task is not to check whether two strings are anagrams individually.

We need to group all strings that belong to the same anagram category.

Consider:

```cpp
strs = ["eat","tea","tan","ate","nat","bat"]
```

Observe:

| String | Characters |
|---|---|
| eat | e, a, t |
| tea | t, e, a |
| ate | a, t, e |
| tan | t, a, n |
| nat | n, a, t |
| bat | b, a, t |

The first three strings contain exactly the same characters.

Similarly, `tan` and `nat` contain the same characters.

Therefore:

```cpp
Group 1 = ["eat","tea","ate"]
Group 2 = ["tan","nat"]
Group 3 = ["bat"]
```

**Core observation:** We need a common signature or key for all strings that are anagrams of each other.

We can generate this signature using two approaches.

---

# APPROACH 1 — SORTING + HASHMAP

## 3. Core Intuition

If two strings are anagrams, sorting their characters will produce exactly the same string.

Example:

```cpp
"eat" -> "aet"
"tea" -> "aet"
"ate" -> "aet"
```

All three strings produce the same sorted representation.

This sorted representation becomes our HashMap key.

### Transformation

| Original String | Sorted String |
|---|---|
| eat | aet |
| tea | aet |
| tan | ant |
| ate | aet |
| nat | ant |
| bat | abt |

Now we can use:

```cpp
unordered_map<string, vector<string>> mp;
```

- Key = Sorted string
- Value = Group of original strings

---

## 4. Step-by-Step Algorithm

**Step 1:** Create an unordered map.

```cpp
unordered_map<string, vector<string>> mp;
```

**Step 2:** Traverse every string.

```cpp
for(int i = 0; i < n; i++)
```

**Step 3:** Copy the original string.

```cpp
string mark = strs[i];
```

Why copy?

We need to preserve the original string because the final output must contain the original words, not their sorted versions.

**Step 4:** Sort the copied string.

```cpp
sort(mark.begin(), mark.end());
```

Now `mark` is the common signature.

**Step 5:** Insert the original string into its group.

```cpp
mp[mark].push_back(strs[i]);
```

If the key already exists, the string is appended to the existing group.

If the key doesn't exist, a new group is created automatically.

**Step 6:** Extract all groups from the map.

```cpp
for(auto const& [key, val_vector] : mp) {
    result.push_back(val_vector);
}
```

---

## 5. C++ Solution — Sorting + HashMap

This is your original approach.

```cpp
class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {

        int n = strs.size();

        unordered_map<string, vector<string>> mp;
        vector<vector<string>> result;

        for(int i = 0; i < n; i++) {

            string mark = strs[i];

            sort(mark.begin(), mark.end());

            mp[mark].push_back(strs[i]);
        }

        for(auto const& [key, val_vector] : mp) {
            result.push_back(val_vector);
        }

        return result;
    }
};
```

<badge color="success">Correct solution</badge>

---

## 6. Detailed Dry Run — Sorting Approach

Input:

```cpp
strs = ["eat","tea","tan","ate","nat","bat"]
```

Initially:

```cpp
mp = {}
```

**Iteration 1**

```cpp
strs[i] = "eat"
mark = "aet"
```

Insertion:

```cpp
mp["aet"].push_back("eat");
```

Map:

```cpp
{
    "aet" : ["eat"]
}
```

**Iteration 2**

```cpp
strs[i] = "tea"
mark = "aet"
```

Insertion:

```cpp
mp["aet"].push_back("tea");
```

Map:

```cpp
{
    "aet" : ["eat","tea"]
}
```

**Iteration 3**

```cpp
strs[i] = "tan"
mark = "ant"
```

Map:

```cpp
{
    "aet" : ["eat","tea"],
    "ant" : ["tan"]
}
```

**Iteration 4**

```cpp
strs[i] = "ate"
mark = "aet"
```

Map:

```cpp
{
    "aet" : ["eat","tea","ate"],
    "ant" : ["tan"]
}
```

**Iteration 5**

```cpp
strs[i] = "nat"
mark = "ant"
```

Map:

```cpp
{
    "aet" : ["eat","tea","ate"],
    "ant" : ["tan","nat"]
}
```

**Iteration 6**

```cpp
strs[i] = "bat"
mark = "abt"
```

Final map:

```cpp
{
    "aet" : ["eat","tea","ate"],
    "ant" : ["tan","nat"],
    "abt" : ["bat"]
}
```

Final output:

```cpp
[
    ["eat","tea","ate"],
    ["tan","nat"],
    ["bat"]
]
```

The order of groups can differ because `unordered_map` does not guarantee iteration order.

---

## 7. Complexity Analysis — Sorting Approach

Let:

- `n` = Number of strings.
- `k` = Maximum length of a string.

### Time Complexity

For every string, we perform sorting.

Sorting one string:

$$O(k\log k)$$

For `n` strings:

$$O(nk\log k)$$

HashMap insertion also requires processing the key, which takes O(k) expected time under normal hashing assumptions.

Therefore, the overall expected time complexity is:

$$\boxed{O(nk\log k)}$$

### Space Complexity

The HashMap stores:
- Sorted string keys.
- Original strings grouped into vectors.

Total storage:

$$O(nk)$$

The result also stores the grouped strings, requiring O(nk) output space.

---

# APPROACH 2 — CHARACTER FREQUENCY + HASHMAP

## 8. Why Do We Need Another Approach?

In the sorting approach, every string is sorted.

Sorting takes:

$$O(k\log k)$$

But think about it:

Do we really need to sort the string?

Our actual requirement is only to identify whether two strings contain the same characters with the same frequencies.

For example:

```cpp
"eat"
"tea"
"ate"
```

All three have:

- a = 1
- e = 1
- t = 1

Their character frequencies are identical.

Therefore, instead of sorting, we can directly calculate character frequencies.

This avoids sorting completely.

---

## 9. Core Intuition — Frequency Array

For lowercase English letters, there are exactly 26 possible characters.

We can create:

```cpp
vector<int> freq(26, 0);
```

Each index represents one character.

Mapping:

| Character | Index |
|---|---:|
| a | 0 |
| b | 1 |
| c | 2 |
| d | 3 |
| ... | ... |
| z | 25 |

The index of a character can be calculated using:

```cpp
ch - 'a'
```

Example:

```cpp
'e' - 'a' = 4
't' - 'a' = 19
'a' - 'a' = 0
```

For the string:

```cpp
"eat"
```

Frequency:

```cpp
freq[0]  = 1;  // a
freq[4]  = 1;  // e
freq[19] = 1;  // t
```

For:

```cpp
"tea"
```

The frequency array is exactly the same.

Therefore, both strings belong to the same anagram group.

---

## 10. How Do We Convert the Frequency Array into a HashMap Key?

Our HashMap uses a string key:

```cpp
unordered_map<string, vector<string>> mp;
```

But our frequency representation is an integer array.

We need to convert it into a unique string signature.

For example:

```cpp
freq = [1,0,0,0,1,0,...,1,...]
```

We can construct a string key by appending every frequency followed by a delimiter.

Example:

```cpp
string key = "";

for(int j = 0; j < 26; j++) {
    key += to_string(freq[j]) + "#";
}
```

Why use `#`?

Consider these two sequences:

```cpp
[1, 11]
[11, 1]
```

Without delimiters, both could become:

```cpp
"111"
```

With delimiters:

```cpp
"1#11#"
"11#1#"
```

They remain distinguishable.

This ensures that different frequency arrays don't accidentally produce the same key.

---

## 11. Step-by-Step Algorithm

**Step 1:** Create the HashMap.

```cpp
unordered_map<string, vector<string>> mp;
```

**Step 2:** Traverse every string.

```cpp
for(auto &s : strs)
```

**Step 3:** Create a frequency array.

```cpp
vector<int> freq(26, 0);
```

**Step 4:** Count each character.

```cpp
for(char ch : s) {
    freq[ch - 'a']++;
}
```

**Step 5:** Convert frequency array into a string key.

```cpp
string key = "";

for(int j = 0; j < 26; j++) {
    key += to_string(freq[j]) + "#";
}
```

**Step 6:** Group the original string.

```cpp
mp[key].push_back(s);
```

**Step 7:** Extract all groups into the result.

---

## 12. C++ Solution — Frequency + HashMap

```cpp
class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {

        unordered_map<string, vector<string>> mp;

        for(auto &s : strs) {

            vector<int> freq(26, 0);

            // Count character frequencies
            for(char ch : s) {
                freq[ch - 'a']++;
            }

            // Generate unique frequency signature
            string key = "";

            for(int j = 0; j < 26; j++) {
                key += to_string(freq[j]) + "#";
            }

            // Group original strings
            mp[key].push_back(s);
        }

        vector<vector<string>> result;

        for(auto const& [key, val_vector] : mp) {
            result.push_back(val_vector);
        }

        return result;
    }
};
```

<badge color="success">Optimized approach</badge>

---

## 13. Detailed Dry Run — Frequency Approach

Input:

```cpp
strs = ["eat","tea","tan","ate","nat","bat"]
```

Consider the first string:

```cpp
s = "eat"
```

Initial frequency array:

```cpp
freq = [0,0,0,0,0,...]
```

Process characters:

```cpp
'e' -> freq[4]++
'a' -> freq[0]++
't' -> freq[19]++
```

Final non-zero frequencies:

```cpp
a = 1
e = 1
t = 1
```

Generated key:

```cpp
"1#0#0#0#1#0#0#...#1#..."
```

Insertion:

```cpp
mp[key].push_back("eat");
```

Now process:

```cpp
s = "tea"
```

Its frequency array is identical.

Therefore, its key is identical.

Insertion:

```cpp
mp[key].push_back("tea");
```

Both strings go into the same group.

Similarly:

```cpp
"ate" -> Same key
"tan" -> Different key
"nat" -> Same key as tan
"bat" -> Different key
```

Final grouping:

```cpp
[
    ["eat","tea","ate"],
    ["tan","nat"],
    ["bat"]
]
```

---

## 14. Complexity Analysis — Frequency Approach

Let:

- `n` = Number of strings.
- `k` = Maximum length of a string.
- `26` = Fixed alphabet size.

### Time Complexity

For each string:

1. Build frequency array: O(k).
2. Generate key: O(26).
3. HashMap insertion: expected O(26) key processing plus hashing.

For all strings:

$$O(n(k+26))$$

Since 26 is constant:

$$\boxed{O(nk)}$$

### Space Complexity

Frequency array:

$$O(26)=O(1)$$

HashMap keys and grouped strings:

$$O(nk)$$

Therefore:

- Auxiliary space including the map: O(nk).
- Output space: O(nk).

---

# 15. Both Approaches — Comparison

| Parameter | Sorting + HashMap | Frequency + HashMap |
|---|---|---|
| Key | Sorted string | Character frequencies |
| Sorting required | Yes | No |
| Time | O(nk log k) | O(nk) |
| Extra key construction | Sorted copy | Frequency signature |
| Alphabet dependency | No | Yes, in this implementation |
| Implementation | Easier | Slightly more involved |
| Best use case | General character sets | Fixed small alphabet |

### Which one should you use?

**Sorting approach:**

Use it when you want a simple and clean solution, especially when the character set is not restricted to lowercase English letters.

**Frequency approach:**

Use it when the input alphabet is fixed and small, such as lowercase English letters.

It avoids sorting and gives linear time in the total input length, treating the alphabet size as constant.

---

# 16. Important Edge Cases

### Case 1 — Empty string

```cpp
Input: [""]
Output: [[""]]
```

Both approaches handle this correctly.

In frequency approach, all 26 frequencies remain zero.

### Case 2 — Single character

```cpp
Input: ["a"]
Output: [["a"]]
```

### Case 3 — Duplicate strings

```cpp
Input: ["abc","abc","bca"]
Output: [["abc","abc","bca"]]
```

All three have the same signature.

### Case 4 — Different character frequencies

```cpp
Input: ["aab","abb"]
```

Frequency signatures differ:

```cpp
"aab" -> a:2, b:1
"abb" -> a:1, b:2
```

They must belong to separate groups.

### Case 5 — Same characters, different order

```cpp
Input: ["abc","cab","bca","acb"]
```

All strings belong to the same group.

---

# 17. Common Mistakes

**Mistake 1: Sorting the original string directly**

```cpp
sort(strs[i].begin(), strs[i].end());
```

This modifies the original input string.

Your approach correctly uses a copy:

```cpp
string mark = strs[i];
sort(mark.begin(), mark.end());
```

**Mistake 2: Using only character presence instead of frequency**

```cpp
"abb"
"ab"
```

Both contain `a` and `b`, but they are not anagrams.

Frequency matters, not just character existence.

**Mistake 3: Forgetting the delimiter in frequency keys**

Always encode counts unambiguously.

```cpp
key += to_string(freq[j]) + "#";
```

**Mistake 4: Assuming unordered_map returns groups in a fixed order**

It does not guarantee insertion order.

The problem allows any output order.

**Mistake 5: Using `ch - 'a'` without checking character constraints**

This indexing method assumes lowercase English letters.

For uppercase letters, Unicode, or arbitrary characters, the key-generation method must be adapted.

---

# 18. Interview Follow-up Questions

**Q1. Why does sorting work for Group Anagrams?**

Because anagrams contain the same characters with the same frequencies. Sorting puts those characters in the same order, creating an identical key.

**Q2. Why does the frequency method work?**

Because two strings are anagrams if and only if their character frequency counts are identical.

**Q3. Which approach is more efficient?**

For lowercase English letters, frequency counting takes O(nk), whereas sorting takes O(nk log k).

**Q4. Why use `unordered_map<string, vector<string>>`?**

Because one signature can correspond to multiple original strings. We need a vector to store all strings belonging to that group.

**Q5. Why not use `unordered_map<string, string>`?**

Because a group can contain more than one string.

**Q6. Does unordered_map preserve the order of groups?**

No. The order of groups is unspecified, and that's acceptable for this problem.

**Q7. What happens if two strings have identical characters but different frequencies?**

They receive different keys and are placed in different groups.

**Q8. Can we use an array as a key directly?**

A `std::array<int,26>` can be used as an unordered_map key if we provide a custom hash function. Alternatively, an ordered `map` can use an array because it has lexicographical comparison.

---

# 19. Pattern Recognition — When Should You Use This?

Whenever a problem asks you to:

- Group anagrams.
- Compare strings based on character frequencies.
- Find strings with identical character distributions.
- Group objects using a canonical representation.
- Avoid repeatedly comparing every pair of strings.

Think about:

**Canonical Key + HashMap**

Possible canonical keys:

1. Sorted string.
2. Character frequency array.
3. Other normalized representations depending on the problem.

This is a broader hashing technique, not limited to anagrams.

---

# 20. Final Revision Cheat Sheet

### Approach 1 — Sorting

```cpp
string key = s;
sort(key.begin(), key.end());

mp[key].push_back(s);
```

Time:

$$O(nk\log k)$$

### Approach 2 — Frequency

```cpp
vector<int> freq(26, 0);

for(char ch : s) {
    freq[ch-'a']++;
}

string key = "";

for(int count : freq) {
    key += to_string(count) + "#";
}

mp[key].push_back(s);
```

Time:

$$O(nk)$$

### Core Takeaway

**Anagrams = Same characters + Same frequencies.**

- Sorting converts anagrams into the same sorted string.
- Frequency counting converts anagrams into the same frequency signature.
- HashMap groups strings using that common signature.

**Interview recommendation:** First explain sorting because it is intuitive and easy to derive. Then mention frequency counting as the optimization for a fixed alphabet.
