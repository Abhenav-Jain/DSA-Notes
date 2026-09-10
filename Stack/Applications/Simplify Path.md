━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📁 LEETCODE 71 — SIMPLIFY PATH
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📌 PATTERN
Stack → Path Processing / Directory Navigation

Goal:
Given an absolute Unix-style path, usko
canonical (simplified) path me convert karna.

Example:

    "/home//foo/"

→   "/home/foo"

    "/home/user/../docs"

→   "/home/docs"


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 UNIX PATH RULES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Token:

    ""     → ignore
    "."    → current directory → ignore
    ".."   → parent directory → pop
    name   → directory → push


Example:

    /a/b/../c

Process:

    a → push
    b → push
    .. → pop b
    c → push

Stack:

    [a, c]

Final:

    /a/c


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔥 WHY STACK?
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Directory navigation naturally follows:

    current directory
          ↓
    ".." = go one level back


Agar current directory stack ke top par hai:

    /a/b/c

Stack:

    [a, b, c]

"..":

    pop c

Now:

    [a, b]

Again "..":

    pop b

Now:

    [a]


So:

    directory → push
    ".."      → pop

Exactly stack ka LIFO behavior.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧩 TOKENIZATION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Path ko '/' ke basis par split karna hai.

C++ me:

    stringstream ss(path);

    getline(ss, token, '/');

Example:

    "/home//foo/"

Tokens conceptually:

    ""
    "home"
    ""
    "foo"

Empty tokens ignore karne hain.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
💻 CODE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

class Solution {
public:

    string simplifyPath(string path) {

        stringstream ss(path);
        string token;

        stack<string> st;

        while(getline(ss, token, '/')) {

            // Empty token or current directory
            if(token == "" || token == ".") {
                continue;
            }

            // Parent directory
            else if(token == "..") {

                if(!st.empty()) {
                    st.pop();
                }
            }

            // Normal directory
            else {
                st.push(token);
            }
        }

        string ans = "";

        while(!st.empty()) {

            ans = "/" + st.top() + ans;

            st.pop();
        }

        return ans.empty() ? "/" : ans;
    }
};


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔍 CODE KA STEP-BY-STEP LOGIC
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1️⃣ Split path

    stringstream ss(path);

Then:

    getline(ss, token, '/');

har directory/component ko ek token deta hai.


2️⃣ Ignore unnecessary components

    ""
    "."

Both ka final canonical path par koi effect nahi.


3️⃣ Handle ".."

    if(!st.empty())
        st.pop();

Current directory se parent directory par move karna.


4️⃣ Normal directory

    st.push(token);


5️⃣ Stack ko reverse order me remove karke
   final path construct karo.

Example:

Stack:

    [home, user, docs]

Top:

    docs

Then:

    /docs

Then:

    /user/docs

Then:

    /home/user/docs


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧪 DRY RUN
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

path:

    "/a/./b/../../c/"

Tokens:

    ""
    "a"
    "."
    "b"
    ".."
    ".."
    "c"


Step 1:

    "" → ignore

    Stack: []


Step 2:

    "a" → push

    Stack: [a]


Step 3:

    "." → ignore

    Stack: [a]


Step 4:

    "b" → push

    Stack: [a, b]


Step 5:

    ".." → pop

    Stack: [a]


Step 6:

    ".." → pop

    Stack: []


Step 7:

    "c" → push

    Stack: [c]


Final:

    /c


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️ IMPORTANT EDGE CASE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Agar stack empty hai aur ".." milta hai:

    "/../"

then:

    nothing to pop

So simply ignore it.

Root directory se upar nahi ja sakte.


Example:

    "/../../a"

Process:

    .. → ignore
    .. → ignore
    a  → push

Answer:

    /a


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 "." VS ".."
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

".":

    Current directory

    /a/./b

    → /a/b

So:
    IGNORE


"..":

    Parent directory

    /a/b/../c

    → /a/c

So:
    POP


Memory trick:

    "."   → stay here → IGNORE
    ".."  → go back   → POP
    name  → enter     → PUSH


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️ COMMON MISTAKES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

❌ Empty tokens ko directory samajhna.

    //a///b

Multiple '/' ka matlab extra directories nahi.

Correct:

    /a/b


❌ "." ko push karna.

Correct:

    ignore


❌ ".." ko blindly pop karna.

Correct:

    if(!st.empty())
        st.pop();


❌ Relative path ki tarah behave karna.

Problem absolute Unix path deti hai,
so root se beyond nahi jaana.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
💡 FINAL STRING BUILDING
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Stack:

    [home, user, docs]

We need:

    /home/user/docs

But stack gives:

    docs → user → home

So:

    ans = "/" + st.top() + ans;

har baar front me add karta hai.

Alternatively, vector use karke reverse order me
construct bhi kar sakte ho.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏱️ COMPLEXITY
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Let:

    n = path.length()


Time:
    O(n)

Har character/token ko overall limited times
process kiya jaata hai.


Space:
    O(n)

Worst case me saare directory names stack me
store ho sakte hain.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🎯 INTERVIEW TRIGGER
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Agar problem me:

    directory navigation
    parent directory
    current directory
    undo/go back
    nested states

jaisa behavior dikhe:

    → STACK consider karo


Core mapping:

    normal directory → PUSH
    ".."             → POP
    "."              → IGNORE
    ""               → IGNORE


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔥 ONE-LINE REVISION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

"Path ko '/' se tokenize karo,
normal directories PUSH karo,
'..' par POP karo,
'.' aur empty tokens IGNORE karo."


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⭐ PATTERN CONNECTION

Stack ke different applications:

    Valid Parentheses
         ↓
    Matching / Nested Structure

    Min Stack
         ↓
    Auxiliary Information

    RPN
         ↓
    Expression Evaluation

    Simplify Path
         ↓
    Directory Navigation

    NGE / NSE
         ↓
    Monotonic Stack

    Largest Rectangle
         ↓
    PSE + NSE


Ye question especially useful hai because
ye dikhata hai ki Stack sirf parentheses/NGE ke liye
nahi hota — kisi bhi "go back / undo / previous state"
type problem me Stack naturally fit ho sakta hai.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
