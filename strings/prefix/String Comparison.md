╔══════════════════════════════════════════════════════════════╗
║        ✦ LONGEST COMMON PREFIX — LeetCode 14 ✦             ║
║              String Comparison • Prefix Pattern             ║
╚══════════════════════════════════════════════════════════════╝

🟡 NOTE LEVEL → SHORT NOTE
📁 PATH → Strings → Prefix / String Comparison


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🎯 PROBLEM
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Given an array of strings, find the longest prefix common to ALL
strings.

Example:

    ["flower", "flow", "flight"]

Common prefix:

    "fl"

Answer:

    "fl"


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 FIRST THOUGHT — WHAT DOES "COMMON PREFIX" MEAN?
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Prefix = starting portion of a string.

For example:

    "flower"
    "flow"

Both start with:

    f → l → ...

So we can compare characters at the SAME INDEX across
all strings.

🔥 Key Observation:

If even ONE string has a different character at index i,
then no character after i can be part of the common prefix.

Therefore:

    Compare → Match → Continue
                 ↓
              Mismatch
                 ↓
                STOP


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
💡 CLEANEST WAY TO THINK
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Take the first string as a REFERENCE.

For every character:

    strs[0][i]

check whether the same character exists at index i in every
other string.

Example:

    ["flower", "flow", "flight"]

Reference:

    f l o w e r
    ↑

Compare index 0:

    f = f = f  ✅

Index 1:

    l = l = l  ✅

Index 2:

    o = o = i  ❌

STOP!

Therefore:

    Answer = "fl"


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚙️ ALGORITHM
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. Take first string as reference.
2. Traverse its characters.
3. For every index i:
       Compare strs[0][i] with every other string.
4. If mismatch occurs OR another string ends:
       STOP.
5. Number of successfully matched characters = prefix length.
6. Return that prefix.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🚨 IMPORTANT EDGE CASE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Never assume every string has the same length.

Example:

    ["flower", "flow", "flight"]

Reference:

    "flower"

But:

    "flow"

ends after index 3.

So before accessing:

    strs[j][i]

make sure:

    i < strs[j].length()

Otherwise → OUT OF BOUNDS / undefined behavior.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
💻 CLEAN C++ SOLUTION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

class Solution {
public:
    string longestCommonPrefix(vector<string>& strs) {

        int n = strs.size();
        int m = strs[0].length();

        int count = 0;

        for(int i = 0; i < m; i++) {

            char check = strs[0][i];

            for(int j = 1; j < n; j++) {

                if(i >= strs[j].length() ||
                   strs[j][i] != check) {

                    return strs[0].substr(0, count);
                }
            }

            count++;
        }

        return strs[0].substr(0, count);
    }
};


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✨ YOUR DERIVED APPROACH
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Tera original thought:

    sort(strings)
         ↓
    first string ko reference
         ↓
    every character ko baaki strings se compare
         ↓
    mismatch → stop
         ↓
    substring return

🔥 The core logic is absolutely correct.

Bas ek refinement:

    sort() ki zarurat nahi hai.

Because even after sorting, tu har string ke same index
ko compare kar hi raha hai.

So sorting sirf extra work add kar rahi hai.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔍 WHY SORTING IS SOMETIMES USED?
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Ek alternative trick hai:

    Sort all strings.

After sorting:

    first string
    last string

in dono ka LCP actually poore array ka LCP determine karta hai.

Example:

    ["flower", "flow", "flight"]

After sorting:

    ["flight", "flow", "flower"]

Compare:

    first = "flight"
    last  = "flower"

Common prefix:

    "fl"

But this requires sorting.

So:

    Sorting approach → O(n log n) sorting overhead

while direct comparison:

    No sorting → simpler


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧪 DRY RUN
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Input:

    ["flower", "flow", "flight"]

Reference:

    "flower"

──────────────────────────────────────────────────────────────

i = 0

check = 'f'

    flower → f ✅
    flow   → f ✅
    flight → f ✅

count = 1


i = 1

check = 'l'

    flower → l ✅
    flow   → l ✅
    flight → l ✅

count = 2


i = 2

check = 'o'

    flower → o ✅
    flow   → o ✅
    flight → i ❌

Mismatch!

STOP.

Return:

    strs[0].substr(0, 2)

Answer:

    "fl" ✅


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏱️ COMPLEXITY
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Let:

    n = number of strings
    m = length of the shortest string

Worst case:

    Time  → O(n × m)
    Space → O(1)

Why m?

Because the answer cannot be longer than the shortest string.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️ COMMON MISTAKES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

❌ Assuming all strings have equal length.

❌ Accessing:

    strs[j][i]

without checking whether i exists.

❌ Continuing after mismatch.

Once mismatch occurs:

    common prefix ends permanently.

❌ Using sorting unnecessarily.

Sorting works, but direct comparison is cleaner here.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 INTERVIEW TRIGGER
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Whenever you see:

    "Longest Common Prefix"

Immediately think:

    SAME INDEX
       ↓
    Compare ALL strings
       ↓
    First mismatch
       ↓
    STOP


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚡ 5-SECOND REVISION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

"First string ko reference bana, har index par uska character
baaki sab strings ke same index se compare kar. Ek bhi mismatch
ya string-end → prefix wahi khatam."


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🏆 PATTERN CARD
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Pattern:
    String Traversal + Character Comparison

Core Technique:
    Reference String + Same-Index Comparison

Stop Condition:
    Mismatch OR String Ends

Complexity:
    O(n × m) time
    O(1) extra space

Difficulty:
    🟢 Easy

Note Value:
    🟡 SHORT NOTE


╔══════════════════════════════════════════════════════════════╗
║  🔥 MASTER THOUGHT                                           ║
║                                                              ║
║  "Common prefix tab tak hi zinda hai jab tak EVERY string   ║
║   same index par SAME character de rahi hai."               ║
╚══════════════════════════════════════════════════════════════╝
