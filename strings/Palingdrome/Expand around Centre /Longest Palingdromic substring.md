# 🔥 LONGEST PALINDROMIC SUBSTRING — LeetCode 5

🟢 NOTES WORTHY / MUST KEEP
📁 Store: Strings → Palindrome → Expand Around Center

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🎯 PATTERN
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

String + Palindrome + Expand Around Center

💡 Core Idea:
Har palindrome ka ek CENTER hota hai.

Example:
    "aba"
      ↑
     center = 'b'

    "abba"
      ↑↑
    center = between 'b' and 'b'

Isliye har possible center se dono directions mein expand karo
jab tak characters equal hain.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 THE BIG TRICK
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Palindrome 2 types ka hota hai:

1️⃣ ODD LENGTH
   Example: "aba", "racecar"

       a b a
         ↑
       center

   → expand(i, i)

2️⃣ EVEN LENGTH
   Example: "abba", "noon"

       a b b a
         ↑ ↑
       center

   → expand(i, i+1)

🔥 Ye dono cases handle karna MUST hai.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚙️ APPROACH
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

For every index i:

    1. Odd palindrome check karo
       expand(i, i)

    2. Even palindrome check karo
       expand(i, i+1)

    3. Dono mein se maximum length lo

    4. Agar current palindrome longest hai,
       uska starting index store karo.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔍 EXPAND FUNCTION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

expand(left, right):

    while:
        left >= 0
        right < n
        s[left] == s[right]

    tab tak:
        left--
        right++

Expansion rukne ke baad:

    right - left - 1

🔥 Why?

Suppose:

    left = -1
    right = 4

Valid palindrome indices:

    0 ... 3

Length:

    4 - (-1) - 1
    = 4

So:

    palindrome_length = right - left - 1

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
💻 CLEAN C++ SOLUTION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

class Solution {
public:

    int expand(string &s, int left, int right) {

        while(left >= 0 &&
              right < s.length() &&
              s[left] == s[right]) {

            left--;
            right++;
        }

        return right - left - 1;
    }

    string longestPalindrome(string s) {

        int n = s.length();

        int maxlen = 0;
        int start = 0;

        for(int i = 0; i < n; i++) {

            // Odd length palindrome
            int len1 = expand(s, i, i);

            // Even length palindrome
            int len2 = expand(s, i, i + 1);

            int len = max(len1, len2);

            if(len > maxlen) {

                maxlen = len;

                start = i - (len - 1) / 2;
            }
        }

        return s.substr(start, maxlen);
    }
};

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧩 MOST IMPORTANT LINE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

    start = i - (len - 1) / 2;

This calculates the starting index of the palindrome.

For odd:

    "aba"

     i = 1
     len = 3

    start = 1 - (3-1)/2
          = 1 - 1
          = 0

For even:

    "abba"

     i = 1
     len = 4

    start = 1 - (4-1)/2
          = 1 - 1
          = 0

🔥 Same formula handles BOTH odd and even cases.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧪 DRY RUN
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Input:

    "babad"

i = 0

    odd  → "b"
    even → none

    max = "b"


i = 1

    odd:
        "bab"

    even:
        none

    max = "bab"


i = 2

    odd:
        "aba"

    even:
        none

    length = 3

    Since equal length hai,
    existing "bab" hi retain ho sakta hai.


i = 3

    odd:
        "bad" ❌

    even:
        none


i = 4

    odd:
        "d"

Final:

    "bab"

Note:
"aba" bhi valid answer hai.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🚨 COMMON MISTAKES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

❌ Sirf expand(i, i) karna

→ Even length palindromes miss ho jayenge.

Example:

    "abba"

❌ Sirf expand(i, i+1) karna

→ Odd length palindromes miss ho jayenge.

❌ Expansion ke baad length ko
   right-left samajhna

Correct:

    right - left - 1

❌ Start index galat calculate karna

Correct:

    start = i - (len - 1) / 2

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏱️ COMPLEXITY
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Time:
    O(n²)

Worst case:
    "aaaaaaa..."

Har center se almost poori string expand ho sakti hai.

Space:
    O(1)

No extra DP table / hashmap required.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
💡 WHY THIS PATTERN IS IMPORTANT?
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Palindrome problems mein ek powerful thought:

    "Palindrome ko directly check karne ke bajaye
     uske CENTER se expand karo."

Ye idea future problems mein directly kaam aa sakta hai.

Think:

    Palindrome?
        ↓
    Center kya hai?
        ↓
    Left + Right expand
        ↓
    Characters equal?
        ↓
    Continue

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 INTERVIEW THOUGHT PROCESS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Question:
"Find the longest palindromic substring."

Immediately think:

    🔹 Substring → contiguous
    🔹 Palindrome → symmetric
    🔹 Symmetry → center
    🔹 Center → expand outward
    🔹 2 centers → odd + even

Therefore:

    ⭐ Expand Around Center ⭐

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚡ ONE-LINE REVISION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

"Har index ko odd center aur har adjacent pair ko
even center maan kar expand karo, aur maximum
palindrome track karo."

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🏆 MASTER TEMPLATE

for(int i = 0; i < n; i++) {

    int odd  = expand(s, i, i);
    int even = expand(s, i, i + 1);

    int len = max(odd, even);

    if(len > maxlen) {
        maxlen = len;
        start = i - (len - 1) / 2;
    }
}

🔥 PALINDROME = CENTER → EXPAND → MAX
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
