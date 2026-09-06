# 🔥 Min Stack — LeetCode 155

> 🟢 MUST KEEP / IMPORTANT STACK PATTERN
> 📁 Stack → Min Stack / Auxiliary Stack
> 🎯 Pattern: Stack + Auxiliary Stack
> ⚡ Goal: getMin() in O(1)


## 🎯 Problem

Design a stack that supports:

    push(x)
    pop()
    top()
    getMin()

The special requirement:

    getMin() → minimum element in O(1)

The challenge is NOT implementing a normal stack.

The challenge is:

    "How can I know the minimum instantly?"


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## 🧠 Core Insight
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

A normal stack only knows:

    top element

But we also need:

    minimum element

So maintain TWO stacks:

    1. st
       → stores all elements normally

    2. min
       → stores the minimum values

Think of `min` as a:

    "minimum history"


Example:

    push(5)

    st  = [5]
    min = [5]


    push(3)

    st  = [5, 3]
    min = [5, 3]


    push(7)

    st  = [5, 3, 7]
    min = [5, 3]

7 is NOT smaller than current minimum 3,
so min stack doesn't need 7.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔥 THE KEY RULE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

When pushing `value`:

    If min stack is empty
        → push value

    Otherwise:

        if value <= min.top()
            → push value into min

        else
            → don't push into min


Why `<=` instead of `<`?

🔥 DUPLICATE MINIMUMS.

Example:

    push(5)
    push(2)
    push(2)

Minimum is:

    2

twice.

So:

    min = [5, 2, 2]

If we used only `<`:

    min = [5, 2]

Then after popping one `2`,
we would incorrectly lose the information that another `2`
is still present.

Therefore:

    value <= min.top()

is important.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📦 How the Two Stacks Work
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Suppose operations are:

    push(5)
    push(2)
    push(8)
    push(1)

After push(5):

    st  = [5]
    min = [5]


After push(2):

    st  = [5, 2]
    min = [5, 2]


After push(8):

    st  = [5, 2, 8]
    min = [5, 2]


After push(1):

    st  = [5, 2, 8, 1]
    min = [5, 2, 1]


Now:

    getMin()

simply returns:

    min.top()

Answer:

    1

🔥 No traversal required.

Therefore:

    getMin() = O(1)


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## ⚙️ Push Logic
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

For every value:

    push(value)

First push it into:

    st

Then ask:

    Is value <= current minimum?

If YES:

    min.push(value)

If NO:

    min remains unchanged.


Visual:

    value
      │
      ├──────────────→ st
      │
      └── smaller/equal?
                │
               YES
                ↓
               min


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## ⚙️ Pop Logic
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

This is the most important part after push.

Suppose:

    st  = [5, 2, 8, 1]
    min = [5, 2, 1]

Now:

    pop()

Top of `st`:

    1

Top of `min`:

    1

Since:

    st.top() == min.top()

this element is currently responsible for the minimum.

Therefore:

    min.pop()

Then:

    st.pop()


Result:

    st  = [5, 2, 8]
    min = [5, 2]

Minimum is now:

    2


🔥 Rule:

    If popped element == current minimum,
    remove it from min stack too.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## 💻 Clean C++ Solution
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

class MinStack {
public:

    stack<int> st;
    stack<int> minSt;

    MinStack() {
    }

    void push(int value) {

        st.push(value);

        if(minSt.empty() || value <= minSt.top()) {
            minSt.push(value);
        }
    }

    void pop() {

        if(st.empty()) {
            return;
        }

        if(st.top() == minSt.top()) {
            minSt.pop();
        }

        st.pop();
    }

    int top() {

        if(st.empty()) {
            return 0;
        }

        return st.top();
    }

    int getMin() {

        return minSt.top();
    }
};


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## 🧪 Dry Run
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Operations:

    push(5)
    push(3)
    push(7)
    push(2)
    pop()
    getMin()


──────────────────────────────────────────────

push(5)

    st  = [5]
    min = [5]


push(3)

    3 <= 5 ✅

    st  = [5, 3]
    min = [5, 3]


push(7)

    7 <= 3 ❌

    st  = [5, 3, 7]
    min = [5, 3]


push(2)

    2 <= 3 ✅

    st  = [5, 3, 7, 2]
    min = [5, 3, 2]


pop()

Top of st:

    2

Top of min:

    2

They are equal.

So:

    min.pop()
    st.pop()

Now:

    st  = [5, 3, 7]
    min = [5, 3]


getMin()

    min.top()

    = 3

Answer:

    3


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## 🔥 Why Does `min` Store Only Some Elements?
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Suppose:

    st = [5, 3, 7, 8]

Current minimum:

    3

Do we need to store:

    7
    8

inside min stack?

NO.

Because neither can become the minimum while `3` is still
inside the stack.

So min stack stores only the values that create a
NEW MINIMUM.

Example:

    5 → minimum = 5
    3 → minimum = 3
    7 → minimum = 3
    2 → minimum = 2

Therefore:

    min = [5, 3, 2]


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## ⚠️ The Duplicate Minimum Case
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

This is the classic trap.

Operations:

    push(2)
    push(2)
    push(3)

If we use:

    value < min.top()

then:

    min = [2]

But there are TWO `2`s.

After:

    pop()

the top `2` is removed.

If min stack had only one `2`,
we would incorrectly think:

    minimum = 3

❌ Wrong.

Therefore we use:

    value <= min.top()

Now:

    min = [2, 2]

After one pop:

    min = [2]

Correct.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## 💡 Why `getMin()` Is O(1)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Without min stack:

    getMin()
        ↓
    Traverse entire stack
        ↓
    Find minimum

Time:

    O(n)


With auxiliary min stack:

    getMin()
        ↓
    minSt.top()

Time:

    O(1)


🔥 This is the entire reason for maintaining the second stack.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## 🚨 Important Bug in Your Original Code
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Your original `pop()` was:

    if(st.top() == min.top()){
        min.pop();
    }

    if(!st.empty()){
        st.pop();
    }


Problem:

You are calling:

    st.top()

BEFORE checking:

    st.empty()


Correct order:

    if(st.empty())
        return;

    if(st.top() == min.top())
        min.pop();

    st.pop();


🔥 General Stack Rule:

    Never call top(), pop(), or similar operations
    on an empty stack.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## 🧠 Why `st.top() == minSt.top()` Works
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Suppose:

    st  = [5, 2, 7, 1]
    min = [5, 2, 1]

The minimum stack represents the minimum values
encountered at different stages.

If the element being removed is:

    current minimum

then that minimum disappears.

So we remove it from:

    minSt


If the popped element is NOT the minimum:

    minSt remains unchanged.


This keeps both stacks synchronized.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## ⏱️ Complexity
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Operation        Time

    push()        O(1)
    pop()         O(1)
    top()         O(1)
    getMin()      O(1)

Space:

    O(n)

Because the auxiliary min stack may also contain
up to n elements.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## 🎯 Interview Trigger
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Whenever you see:

    "Stack + minimum"
    "Get minimum in O(1)"
    "Track minimum while pushing/popping"

Immediately think:

                🔥 AUXILIARY STACK


Normal stack:

    Stores elements


Min stack:

    Stores minimum history


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## ⚡ 10-SECOND REVISION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

    Main Stack
        ↓
    Stores everything

    Min Stack
        ↓
    Stores current minimum history

    push:
        value <= min.top()
        → push into min

    pop:
        popped value == min.top()
        → pop min too

    getMin:
        → min.top()


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## 🏆 Master Pattern

    TWO STACKS

    st:
        [all elements]

    minSt:
        [minimum history]


    PUSH
        ↓
    Main stack always
        ↓
    Min stack only if new/equal minimum


    POP
        ↓
    If popped == current minimum
        ↓
    Remove from both


    GET MIN
        ↓
    minSt.top()
        ↓
       O(1)


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## 🔥 Golden Takeaway

"Jab kisi stack operation ke saath minimum/maximum ko
O(1) mein maintain karna ho, ek auxiliary stack use karo
jo minimum/maximum ki history maintain kare."

                    ↓

              Normal Stack
                    +
             Auxiliary Stack
                    ↓
               O(1) getMin()


💎 The real trick is NOT finding the minimum.

The real trick is:

    "Minimum ko pehle se maintain karke rakho."

That's what turns:

    O(n) getMin()

into:

    O(1) getMin(). 🚀
