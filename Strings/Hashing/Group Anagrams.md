# 🧩 Group Anagrams — LeetCode 49

## 💡 Pattern
Hashing + Sorting

## 🎯 Idea
Anagrams ke letters same hote hain, bas order different hota hai.

Example:
    "eat" → "aet"
    "tea" → "aet"
    "ate" → "aet"

So, har string ko sort karke usse ek common KEY bana do.
Same key wali strings ko ek hi group me daal do.

## 🔑 Approach

1. unordered_map<string, vector<string>> use karo.
2. Har string `str` ke liye:
      - `temp = str`
      - temp ko sort karo
      - `temp` ko key bana do
      - original `str` ko map[temp] me push karo.
3. Map ke saare values ko answer me daal do.

## 🧠 Example

Input:
["eat", "tea", "tan", "ate", "nat", "bat"]

Keys:
    eat → aet
    tea → aet
    tan → ant
    ate → aet
    nat → ant
    bat → abt

Map:
    "aet" → ["eat", "tea", "ate"]
    "ant" → ["tan", "nat"]
    "abt" → ["bat"]

Answer:
[
    ["eat","tea","ate"],
    ["tan","nat"],
    ["bat"]
]

## 💻 Code

class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {

        unordered_map<string, vector<string>> mp;

        for(string str : strs) {
            string temp = str;

            sort(temp.begin(), temp.end());

            mp[temp].push_back(str);
        }

        vector<vector<string>> result;

        for(auto it : mp) {
            result.push_back(it.second);
        }

        return result;
    }
};

## ⏱️ Complexity

Let:
    n = number of strings
    k = maximum length of a string

Sorting each string:
    O(k log k)

For n strings:
    Time → O(n × k log k)
    Space → O(n × k)

## ⭐ Key Takeaway

"Anagram grouping" → SORTED STRING as HASH KEY

Whenever multiple strings need to be grouped based on
same characters/frequency, think:

    String → Normalize → HashMap → Group

Alternative:
Instead of sorting, character-frequency array can be used
as the key → O(n × k), but sorting approach is simpler.
