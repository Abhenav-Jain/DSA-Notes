━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧩 LEETCODE 22 — GENERATE PARENTHESES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📌 PATTERN
Recursion + Backtracking + Constraint Pruning

Goal:
n pairs of parentheses ke saare valid combinations
generate karne hain.

Example:

n = 3

Output:

    ((()))
    (()())
    (())()
    ()(())
    ()()()


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 VALID PARENTHESES KI CONDITION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

For any prefix:

    number of ')' 
    kabhi bhi
    number of '(' se zyada nahi hona chahiye.

And final string me:

    open = n
    close = n


Example:

    (()())

Valid because har prefix me:

    open >= close


Invalid:

    ())(

At some point:

    close > open

So invalid.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🥉 APPROACH 1 — BRUTE FORCE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Idea:

    Har position par:

        '('
        OR
        ')'

dono try karo.

So total possible strings:

    2^(2n)

because total length = 2n.

Har generated string ko end me:

    valid_parenthesis()

se check karo.


Flow:

    Generate ALL possible strings
            ↓
       2^(2n) candidates
            ↓
       Validate each
            ↓
       Keep valid ones


Ye correct approach hai,
but bahut saari invalid strings unnecessarily
generate hoti hain.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
💻 APPROACH 1
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

class Solution {
public:

    bool valid_parenthesis(string brackets) {

        int count = 0;

        for(char& ch : brackets) {

            if(ch == '(') {
                count++;
            }

            if(ch == ')') {
                count--;
            }

            // More ')' than '('
            if(count < 0) {
                return false;
            }
        }

        return count == 0;
    }


    void solve(
        string curr,
        int n,
        vector<string>& ans
    ) {

        if(curr.size() == 2 * n) {

            if(valid_parenthesis(curr)) {
                ans.push_back(curr);
            }

            return;
        }

        curr.push_back('(');
        solve(curr, n, ans);
        curr.pop_back();

        curr.push_back(')');
        solve(curr, n, ans);
        curr.pop_back();
    }


    vector<string> generateParenthesis(int n) {

        string curr = "";

        vector<string> ans;

        solve(curr, n, ans);

        return ans;
    }
};


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔍 APPROACH 1 KA MAIN PROBLEM
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Suppose:

    n = 3

Ek branch:

    ())(

already invalid ho gayi.

But brute-force approach:

    uske baad bhi recursively
    complete length 6 tak banayega.

Then:

    valid_parenthesis()

finally usko reject karega.


Meaning:

    Invalid branch ko pehle hi identify
    kar sakte the,

but humne usko unnecessarily generate kiya.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🚀 APPROACH 2 — OPTIMIZED BACKTRACKING ⭐
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Instead of:

    "Generate everything → validate"

we do:

    "Generate only valid possibilities"


We maintain:

    open
    close


Meaning:

    open  = kitne '(' already use kiye
    close = kitne ')' already use kiye


Rules:

    1. open < n

       → '(' add kar sakte ho.


    2. close < open

       → ')' add kar sakte ho.


That's it.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔥 RULE 1 — `open < n`
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Total n opening brackets hi use karne hain.

So:

    if(open < n)

then:

    '('

add kar sakte hain.


If:

    open == n

then:

    ❌ aur '(' nahi laga sakte.


Example:

    n = 3
    open = 3

Already:

    ((()

3 opening brackets use ho chuke.

So another '(' invalid because total
opening brackets 3 hi allowed hain.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔥 RULE 2 — `close < open`
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Ye sabse important condition hai.

Closing bracket tabhi add kar sakte hain
jab currently available opening bracket ho.

Condition:

    close < open


Why?

Because agar:

    close == open

then saare currently opened brackets
already close ho chuke hain.

Agar ab ')' add kiya:

    close > open

which makes the sequence invalid.


Example:

    curr = "(("

    open  = 2
    close = 0

')' allowed.

After:

    "(()"

    open  = 2
    close = 1

Another ')' allowed.

After:

    "(())"

    open  = 2
    close = 2

Another ')' ❌

because:

    close < open

false.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
💻 APPROACH 2 — OPTIMIZED
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

class Solution {
public:

    void solve(
        string& curr,
        int n,
        vector<string>& ans,
        int open,
        int close
    ) {

        // Complete valid sequence
        if(curr.size() == 2 * n) {

            ans.push_back(curr);

            return;
        }


        // Add '('
        if(open < n) {

            curr.push_back('(');

            solve(
                curr,
                n,
                ans,
                open + 1,
                close
            );

            curr.pop_back();
        }


        // Add ')'
        if(close < open) {

            curr.push_back(')');

            solve(
                curr,
                n,
                ans,
                open,
                close + 1
            );

            curr.pop_back();
        }
    }


    vector<string> generateParenthesis(int n) {

        string curr = "";

        vector<string> ans;

        solve(
            curr,
            n,
            ans,
            0,
            0
        );

        return ans;
    }
};


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 MOST IMPORTANT OPTIMIZATION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Approach 1:

    Build:
        '('
        ')'

    blindly

    ↓

    Validate later


Approach 2:

    '(' only if:

        open < n


    ')' only if:

        close < open


    ↓

    Invalid branches are NEVER generated.


This is called:

    PRUNING


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🌳 RECURSION TREE INTUITION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

At every point:

              curr
             /    \
           '('    ')'
            ↓       ↓
        if valid  if valid


But Approach 2 me:

    ')' branch tabhi create hogi
    jab:

        close < open


So invalid branches
tree se immediately cut ho jaati hain.


Example:

    ""

    ↓

    "("

    ↓

    "(("

    ↓

    "(()"

    ↓

    "(())"

    ↓

    "(())("

    ↓

    "(())()"

Valid result.


But:

    "())"

ke baad:

    ")"

branch kabhi generate hi nahi hoti.

That's pruning.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔄 BACKTRACKING KA ROLE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Pattern:

    choose
      ↓
    recurse
      ↓
    undo


For '(':

    curr.push_back('(')

    solve(...)

    curr.pop_back()


For ')':

    curr.push_back(')')

    solve(...)

    curr.pop_back()


This:

    push → recurse → pop

is the standard backtracking template.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 WHY `curr` REFERENCE HAI?
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Approach 2:

    string& curr

Use kar raha hai.

So same string ko modify karke:

    push
    recurse
    pop

kiya ja raha hai.

This avoids creating a new string
on every recursive call.

Approach 1 me:

    string curr

pass by value hai.

So every recursive call me string copy hoti hai.


Therefore Approach 2 is cleaner and more
efficient in terms of recursion-state handling.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️ APPROACH 2 ME VALIDATION KYU NAHI HAI?
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Because constraints already guarantee validity.

We only allow:

    '(' when open < n

and:

    ')' when close < open


Therefore every generated complete string
automatically satisfies:

    open = n
    close = n

and:

    close never exceeds open.


So:

    valid_parenthesis()

ki zarurat nahi.


This is the core optimization.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧪 DRY RUN — n = 2
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Start:

    curr = ""
    open = 0
    close = 0


Only '(' allowed because:

    open < n

So:

    "("


Now:

    open = 1
    close = 0


Both possible:

    '('
    ')'


Take '(':

    "(("

    open = 2
    close = 0


Cannot add '(':

    open < n ❌


Can add ')':

    "(()"

    open = 2
    close = 1


Again ')':

    "(())"

    open = 2
    close = 2


Length = 4

→ add answer.


Backtrack and explore:

    "()"


Then:

    "()()"

Second answer.


Final:

    ["(())", "()()"]


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📌 KEY INVARIANT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

At EVERY recursive call:

    0 <= close <= open <= n


This single invariant guarantees
that generated sequences remain valid.


Think:

    open
      ↓
    cannot exceed n

    close
      ↓
    cannot exceed open


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚖️ APPROACH 1 VS APPROACH 2
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

APPROACH 1 — BRUTE FORCE

    Generate every possible
    parenthesis string

        ↓

    2^(2n) possibilities

        ↓

    Validate each


    ❌ Generates invalid states
    ❌ Validation required
    ❌ More work


APPROACH 2 — BACKTRACKING + PRUNING

    Generate '(' only when:

        open < n

    Generate ')' only when:

        close < open

        ↓

    Only valid states generated


    ✅ No final validation
    ✅ Invalid branches pruned early
    ✅ Much cleaner


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏱️ COMPLEXITY
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Number of valid answers is the
nth Catalan number:

    Cn = 1/(n+1) × C(2n,n)


So any solution that outputs all valid
strings must spend at least:

    O(Cn × n)

time just to produce the output.


Approach 2 therefore has output-sensitive
complexity roughly:

    O(Cn × n)


Space:

    Recursion depth:
        O(n)

    Current string:
        O(n)

    Output:
        O(Cn × n)


If output storage is excluded:

    Auxiliary space → O(n)


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🎯 INTERVIEW TRIGGER
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Agar problem bole:

    "Generate all valid combinations"

and kuch constraints diye hon jo invalid
choices ko identify kar sakte hain:

    → Backtracking
    → Maintain state
    → Prune invalid branches


For this problem:

    open < n
        → add '('


    close < open
        → add ')'


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔥 ONE-LINE MEMORY TRICK
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

"Open ko n tak le jao,
but close ko kabhi open se zyada mat hone do."


Or:

    '(' → open < n
    ')' → close < open


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🏆 PATTERN CONNECTION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Brute Force:

    Generate
       ↓
    Validate


Optimized Backtracking:

    Choose
       ↓
    Check constraint
       ↓
    Recurse
       ↓
    Undo


This is the bigger lesson:

    ❌ Generate invalid possibilities
       and reject later

    ✅ Reject invalid choices BEFORE recursion


That is:

    BACKTRACKING + PRUNING


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⭐ FINAL VERDICT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Approach 1:
    🟡 Useful for understanding brute force
    ✅ Correct
    ❌ Not optimal


Approach 2:
    🟢 MUST KNOW
    ✅ Correct
    ✅ Backtracking
    ✅ State tracking
    ✅ Pruning
    ✅ Interview preferred


Main concept to remember:

    open < n
    close < open

        ↓

    Every generated string is valid.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
