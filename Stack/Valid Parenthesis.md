# 🔥 Valid Parentheses — LeetCode 20

> 🟢 MUST KEEP
> 📁 Stack → Parentheses / Matching
> 🧠 Pattern: Stack + Matching Pairs + LIFO


## 🎯 Problem

Given a string containing:

    ( )   { }   [ ]

Check whether all brackets are valid.

A valid string must satisfy:

    1. Every opening bracket has a closing bracket.
    2. Every bracket is closed by the correct type.
    3. Brackets must close in the correct order.

Examples:

    "()"       → ✅ Valid
    "()[]{}"   → ✅ Valid
    "([{}])"   → ✅ Valid
    "(]"       → ❌ Invalid
    "([)]"     → ❌ Invalid
    "((("      → ❌ Invalid


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## 🧠 Core Insight — Why Stack?
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

The most important observation is:

    LAST OPENED → FIRST CLOSED

Example:

    ([{}])

Opening order:

    (
    [
    {

Now which bracket must close first?

    }

Because `{` was opened LAST.

Then:

    ]
    )

This is exactly the property of a Stack:

    LIFO
    Last In → First Out

🔥 Therefore:

    Nested / Matching Brackets
              ↓
           Think STACK


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## 📦 Basic Logic
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Whenever we see an OPENING bracket:

    (  {  [

        ↓

    PUSH into stack


Whenever we see a CLOSING bracket:

    )  }  ]

        ↓

    Check stack TOP
        ↓
    Does it contain the matching opener?
       /              \
     YES              NO
      ↓                ↓
    POP              FALSE


At the end:

    Stack EMPTY → Valid
    Stack NOT EMPTY → Invalid


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## 🔗 Matching Pairs
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

    '('  ↔  ')'
    '{'  ↔  '}'
    '['  ↔  ']'

So:

    ')' requires '(' on top

    '}' requires '{' on top

    ']' requires '[' on top


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## ⚙️ Algorithm
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

For every character s[i]:

    STEP 1:
    If it is an opening bracket:

        st.push(s[i])


    STEP 2:
    If it is a closing bracket:

        Check whether stack is empty.

        If empty:
            return false

        Otherwise check st.top().

        If top matches:
            st.pop()

        Else:
            return false


    STEP 3:
    After processing the complete string:

        return st.empty();


🔥 Why final empty check?

Because extra opening brackets may still be present.

Example:

    "((("

After processing:

    Stack = (( (

So it is NOT valid.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## 💻 Clean C++ Solution
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

class Solution {
public:
    bool isValid(string s) {

        stack<char> st;

        for(int i = 0; i < s.length(); i++) {

            // Opening brackets
            if(s[i] == '(' ||
               s[i] == '{' ||
               s[i] == '[') {

                st.push(s[i]);
            }

            // Closing ')'
            else if(!st.empty() &&
                    s[i] == ')' &&
                    st.top() == '(') {

                st.pop();
            }

            // Closing '}'
            else if(!st.empty() &&
                    s[i] == '}' &&
                    st.top() == '{') {

                st.pop();
            }

            // Closing ']'
            else if(!st.empty() &&
                    s[i] == ']' &&
                    st.top() == '[') {

                st.pop();
            }

            // Invalid case
            else {
                return false;
            }
        }

        return st.empty();
    }
};


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## 🧪 Dry Run — Valid Case
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Input:

    "({[]})"


Read '(':

    PUSH

    Stack:
    (


Read '{':

    PUSH

    Stack:
    ( {


Read '[':

    PUSH

    Stack:
    ( { [


Read ']':

    TOP = '['

    Match ✅

    POP

    Stack:
    ( {


Read '}':

    TOP = '{'

    Match ✅

    POP

    Stack:
    (


Read ')':

    TOP = '('

    Match ✅

    POP

    Stack:

    EMPTY


Final:

    st.empty() = true

    → VALID ✅


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## ❌ Dry Run — Invalid Case
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Input:

    "([)]"


Read '(':

    Stack:
    (


Read '[':

    Stack:
    ( [


Read ')':

    Closing bracket = ')'

    Expected TOP = '('

    Actual TOP = '['

    ❌ Mismatch

Therefore:

    return false


🔥 Notice:

The number of opening and closing brackets is balanced.

But the ORDER is wrong.

That's why simple counting is NOT enough.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## 🚨 Important Edge Cases
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

### 1. Closing bracket comes first

Input:

    "]"

Stack is empty.

We cannot access:

    st.top()

Therefore:

    !st.empty()

must be checked first.


### 2. Extra opening brackets

Input:

    "((("

Final stack is not empty.

Therefore:

    false


### 3. Wrong nesting

Input:

    "([)]"

Counts are balanced ❌

But nesting is incorrect ❌

Stack catches this immediately.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## 💡 Why Counting Doesn't Work
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Suppose:

    "([)]"

Counts:

    ( → 1
    ) → 1
    [ → 1
    ] → 1

Everything appears balanced.

But actual structure is:

    (
      [
      )

The `)` tries to close `(`,
but `[` is still on top.

🔥 So the problem is not:

    "How many brackets?"

The real question is:

    "Which bracket should close FIRST?"

That is a STACK problem.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## 🧠 The Real Pattern
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

This problem teaches a much bigger concept:

    NESTED STRUCTURES
          ↓
    Most recent element finishes first
          ↓
         LIFO
          ↓
        STACK


This same thinking appears in:

    • Function calls
    • Undo operations
    • Expression evaluation
    • Backtracking
    • Nested structures
    • Monotonic stack problems


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## ⚠️ Common Mistakes
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

❌ Only counting brackets.

    "([)]" proves why this fails.


❌ Calling st.top() when stack is empty.

    Always check:

        !st.empty()


❌ Popping without checking the matching pair.

    ')' → must match '('
    '}' → must match '{'
    ']' → must match '['


❌ Forgetting:

    return st.empty();


❌ Using a Queue.

    Queue → FIFO ❌
    Stack → LIFO ✅


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## 🚀 Cleaner Mental Template
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

When you see an opening bracket:

    PUSH


When you see a closing bracket:

    EMPTY?
      ↓
    YES → FALSE

    TOP MATCH?
      ↓
    NO → FALSE

    YES → POP


After loop:

    EMPTY → TRUE
    NOT EMPTY → FALSE


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## ⏱️ Complexity
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Let:

    n = length of string

Time:

    O(n)

Every character is processed once.

Space:

    O(n)

Worst case:

    "((((((("

All characters can be stored in the stack.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## 🎯 Interview Trigger
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Whenever the question contains:

    "Matching brackets"
    "Balanced parentheses"
    "Nested brackets"
    "Correct order"
    "Most recently opened"

Immediately think:

                🔥 STACK 🔥


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## ⚡ 10-Second Revision

    Opening bracket → PUSH

    Closing bracket → TOP CHECK

    Match → POP

    Mismatch → FALSE

    Empty stack during closing → FALSE

    End mein stack empty → TRUE


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## 🏆 Master Takeaway

    "Jo bracket LAST mein open hua hai,
     wahi FIRST mein close hona chahiye."

                    ↓

                  LIFO

                    ↓

                 STACK

                    ↓

             PUSH → MATCH → POP


🔥 Valid Parentheses ka entire pattern isi thought
process ke around revolve karta hai.
