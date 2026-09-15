━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧩 LEETCODE 131 — PALINDROME PARTITIONING
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📌 PATTERN
Backtracking + Variable-Length Cut + Palindrome Check

Goal:
String s ke saare possible tareeke se partition karne hain
jisme HAR piece ek palindrome ho.

Example:

    s = "aab"

    Output:
    [["a","a","b"], ["aa","b"]]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 CORE IDEA
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Subsets / Combination Sum mein har index par sirf 2 choices
hoti hain (take / don't take, ek element at a time).

Yahan choice thodi different hai:

    "idx se lekar string ke end tak, KITNA BADA piece lena hai?"

idx = ab tak string fix ho chuka, agla piece yahin se
start hoga.

For-loop ek variable-length "cut point" try karta hai:

    i = idx, idx+1, idx+2 ... s.length()-1

Har i par ek candidate substring banta hai:

    temp = s[idx..i]

Agar wo palindrome hai — tabhi use ek valid "piece" maan ke
aage badhte hain. Agar palindrome nahi hai — us particular
length ko turant reject kar dete hain (aage explore hi
nahi hota, seedha next i try hota hai).

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔑 MOST IMPORTANT PATTERN
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

For every idx, for every i from idx to n-1:

        temp = s[idx..i]
              |
        palindrome? ──── NO ──→ skip, i++ (aage try karo)
              |
             YES
              |
           CHOOSE
              |
        curr.push_back(temp)
              |
           EXPLORE
              |
        solve(idx = i + 1)
              |
          UN-CHOOSE
              |
        curr.pop_back()
              |
        loop continues, i++

⚠️ Ye Subsets / Combination Sum se alag hai — wahan har
element ka fixed decision hota hai (le lo ya chhodo). Yahan
HAR possible LENGTH ka piece try hota hai, aur sirf
palindrome wale hi aage badhte hain.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔄 YOUR APPROACH
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

    class Solution {
    public:
        bool is_palingdrome(string& s){
            int left = 0;
            int right = s.length()-1;
            while(left <= right){
                if(s[left] != s[right]){
                    return false;
                }
                left++;
                right--;
            }
            return true;
        }

        void solve(string& s , vector<vector<string>>& result , int idx , vector<string>& curr){
            if(idx == s.length()){
                result.push_back(curr);
                return;
            }
            for(int i = idx ; i < s.length() ; i++){
                string temp = s.substr(idx, i - idx + 1);

                if(is_palingdrome(temp)){
                    curr.push_back(temp);
                    solve(s, result, i + 1, curr);
                    curr.pop_back();
                }
            }
        }

        vector<vector<string>> partition(string s) {
            vector<vector<string>> result;
            int idx = 0;
            vector<string> curr;
            solve(s,result,idx,curr);
            return result;
        }
    };

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧩 is_palingdrome() — DO POINTER CHECK
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

left string ke start se, right end se shuru hota hai:

    left → → →     ← ← ← right

Jahan bhi s[left] != s[right] mile:
    turant false (mismatch mila, palindrome nahi)

Dono pointers cross ho jaayein bina kisi mismatch ke:
    true (poora string palindrome hai)

Naam mein chhota sa typo hai — "is_palingdrome" hona chahiye
tha "is_palindrome" — par compiler ke liye ye sirf ek
identifier hai, spelling se code ke kaam karne par koi farak
nahi padta.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🎯 BACKTRACKING INVARIANT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

At every recursive call solve(idx):

    curr = ab tak choose kiye gaye palindrome pieces

Loop ke andar har i ke liye:

    CHOOSE    → curr.push_back(temp)
    EXPLORE   → solve(s, result, i + 1, curr)
    UN-CHOOSE → curr.pop_back()

pop_back() zaroori hai — warna previous branch ka piece
curr mein reh jaayega jab next i try hoga.

BASE CASE:

    idx == s.length()
        → poora string cover ho gaya
        → curr ek VALID partition hai
        → result.push_back(curr)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧪 EXAMPLE — DRY RUN
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

s = "aab"

solve(idx=0, curr=[])

    i=0 → temp="a" → palindrome ✅
        curr=["a"] → solve(idx=1)

            i=1 → temp="a" → palindrome ✅
                curr=["a","a"] → solve(idx=2)

                    i=2 → temp="b" → palindrome ✅
                        curr=["a","a","b"] → solve(idx=3)

                            idx==3==length → BASE CASE
                            result += ["a","a","b"]

                    pop_back() → curr=["a","a"]

                pop_back() → curr=["a"]

            i=2 → temp="ab" → palindrome ❌ (a != b) → skip

        pop_back() → curr=[]

    i=1 → temp="aa" → palindrome ✅
        curr=["aa"] → solve(idx=2)

            i=2 → temp="b" → palindrome ✅
                curr=["aa","b"] → solve(idx=3)

                    idx==3==length → BASE CASE
                    result += ["aa","b"]

            pop_back() → curr=["aa"]

        pop_back() → curr=[]

    i=2 → temp="aab" → palindrome ❌ (a != b) → skip

Final result:

    [["a","a","b"], ["aa","b"]]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚔️ PALINDROME PARTITIONING vs SUBSETS / COMBINATION SUM
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Subsets / Combination Sum:
    Har element → take / don't take (fixed, 1 element at a time)

Palindrome Partitioning:
    Har idx → for-loop se VARIABLE LENGTH piece try karo
    (1 char, 2 chars, 3 chars ... jitna bhi palindrome bane)

Common cheez sabme:

    CHOOSE → EXPLORE → UN-CHOOSE
    (push_back → recurse → pop_back)

Farak sirf itna hai ki "choice" kya represent karti hai —
element include karna, ya kitna bada substring lena hai.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏱️ COMPLEXITY
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Let n = s.length()

Partitions ke possible ways:
    up to 2^(n-1)   (har gap par cut ya no-cut)

Har candidate ke liye:
    is_palingdrome() → O(n)
    substr()         → O(n) (naya string copy hota hai)

Time:
    O(n * 2^n)   worst case

Space:
    O(n)         recursion depth (call stack)
  + O(n)         curr vector
  + O(2^n * n)   result storage (worst case, jaise "aaaa")

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🚀 OPTIMIZATION IDEA
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Ye solution har baar is_palingdrome() ko SCRATCH se check
karta hai — same substring baar-baar re-check ho sakta hai.

Better: shuru mein hi ek dp[i][j] table bana lo (O(n^2) time)
jo batata hai "s[i..j] palindrome hai ya nahi" — phir loop
ke andar check O(1) ho jaata hai.

    dp[i][j] = (s[i] == s[j]) && (j - i < 2 || dp[i+1][j-1])

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🚨 COMMON MISTAKES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

❌ Base case ko for-loop ke andar check karna
    → idx == s.length() function ke SHURU mein hona chahiye.

❌ pop_back() bhool jaana
    → previous branch ka piece curr mein reh jayega.

❌ Non-palindrome substring ko bhi push kar dena
    → is_palingdrome() check pass kiye bina push_back mat
      karo.

❌ i ko idx se shuru na karna
    → substring hamesha idx se start hona chahiye, warna
      string ke beech ke characters miss ho jaayenge.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 ONE-LINE INTERVIEW EXPLANATION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

"At every index, try every possible cut length — if that
substring is a palindrome, take it as a valid piece and
recurse on the remaining string; when the whole string is
consumed, the current set of pieces is one valid partition."

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔥 PATTERN TO REMEMBER
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

PALINDROME PARTITIONING

for i = idx to n-1
  ↓
temp = s[idx..i]
  ↓
palindrome?
  ↓ yes                    ↓ no
CHOOSE (push)             skip, i++
  ↓
EXPLORE solve(i+1)
  ↓
UN-CHOOSE (pop)
  ↓
loop continues, i++

⭐ Key Rule:
"Loop ka har i ek NAYI cut-length hai — palindrome check
pass karne par hi us length ko choose karo aur aage recurse
karo, warna seedha skip karke agla i try karo."

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔗 SIMILAR QUESTIONS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. Palindrome Partitioning II — LeetCode 132 (min cuts, DP)
2. Subsets — LeetCode 78
3. Subsets II — LeetCode 90
4. Combination Sum — LeetCode 39
5. Word Break — LeetCode 139 (similar idx + for-loop cut pattern)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
