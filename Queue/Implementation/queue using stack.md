━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🚶 LEETCODE 232 — IMPLEMENT QUEUE USING STACKS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📌 GOAL

Queue follows:

    FIFO
    First In → First Out

Stack follows:

    LIFO
    Last In → First Out

Challenge:

    Queue ko sirf stacks use karke implement karna.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 APPROACH 1 — TWO STACKS + LAZY TRANSFER ⭐
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Use:

    st1 → input stack
    st2 → output stack


PUSH:
    Always st1 me push.

POP / PEEK:

    If st2 is NOT empty:
        directly use st2

    Else:
        st1 ke saare elements st2 me transfer karo.

Because:

    st1 = LIFO order

Transfer ke baad:

    st2 = FIFO order


Example:

Queue:

    1 → 2 → 3

st1:

    [1, 2, 3]
            ↑ top


Transfer:

    st2:

    [3, 2, 1]
         ↑
        top = 1


Now:

    pop() → 1
    pop() → 2
    pop() → 3

Exactly FIFO.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔥 KEY IDEA — LAZY TRANSFER
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Sabse important concept:

    st1 → input
    st2 → output

Transfer HAR push par nahi karna.

Sirf tab:

    st2.empty()

hone par transfer karo.


Why?

Agar st2 me elements already available hain,
to unka order already correct hai.

So:

    st2 non-empty
        ↓
    directly pop/peek

    st2 empty
        ↓
    transfer st1 → st2


This avoids unnecessary repeated work.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔍 APPROACH 1 DRY RUN
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

push(1)

    st1 = [1]
    st2 = []


push(2)

    st1 = [1,2]
    st2 = []


push(3)

    st1 = [1,2,3]
    st2 = []


pop()

st2 empty → transfer:

    st1 → st2

    st1 = []
    st2 = [3,2,1]

Now:

    pop st2 → 1

    st2 = [3,2]


pop()

st2 non-empty:

    pop directly → 2

    st2 = [3]


push(4)

    st1 = [4]
    st2 = [3]


pop()

st2 non-empty:

    → 3

Notice:

    4 ko abhi transfer nahi kiya.

Why?

Because 3 still has to come out first.


Next pop():

st2 empty

Transfer:

    st1 → st2

    st2 = [4]

Then:

    → 4


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⭐ APPROACH 1 — WHY IT IS EFFICIENT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Ek element:

    st1 me push
        ↓
    at most ONE transfer
        ↓
    st2 me pop

An element baar-baar transfer nahi hota.

So although ek individual operation kabhi-kabhi
O(n) ho sakta hai, sequence of operations ka
AMORTIZED cost O(1) per operation hai.


Important:

    push     → O(1)
    pop      → O(1) amortized
    peek     → O(1) amortized
    empty    → O(1)


Overall sequence:

    O(n) for n operations


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 APPROACH 2 — MAKE PUSH EXPENSIVE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Approach 2 me:

    st1 = queue-maintained stack
    st2 = temporary stack


push(x):

    1. st1 ke saare elements st2 me move karo.
    2. x ko st1 me push karo.
    3. st2 ke elements wapas st1 me move karo.


Example:

Current queue:

    [1,2,3]

st1:

    [1,2,3]


push(4):

Step 1:

    st1 → st2

    st1 = []
    st2 = [3,2,1]


Step 2:

    push 4

    st1 = [4]


Step 3:

    st2 → st1

    st1 = [4,3,2,1]


Now top:

    1

So pop() directly FIFO order dega.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔍 APPROACH 2 DRY RUN
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

push(1):

    st1 = [1]


push(2):

    move 1:

    st2 = [1]

    push 2:

    st1 = [2]

    move back:

    st1 = [2,1]

Top = 1


push(3):

    move all:

    st2 = [1,2]

    push 3:

    st1 = [3]

    move back:

    st1 = [3,2,1]

Top = 1


So queue order is ALWAYS maintained
inside st1.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚖️ APPROACH 1 VS APPROACH 2
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

                APPROACH 1       APPROACH 2

Push            O(1) ⭐           O(n) ❌

Pop             O(1) amortized   O(1)

Peek            O(1) amortized   O(1)

Empty           O(1)             O(1)

Overall          O(n) amortized   O(n²) worst-case

Main idea       Lazy transfer    Maintain order
                when needed       after every push


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🚨 APPROACH 2 KI MAIN PROBLEM
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Suppose:

    push(1)
    push(2)
    push(3)
    ...
    push(n)

Har push par:

    st1 → st2
    push
    st2 → st1

So har new element ke liye existing elements
ko repeatedly move karna padta hai.

Cost:

    push(1) → O(1)
    push(2) → O(1)
    push(3) → O(2)
    push(4) → O(3)
    ...
    push(n) → O(n)

Total:

    1 + 2 + 3 + ... + n

    = O(n²)


Isliye Approach 2 logically correct hone ke
baad bhi inefficient hai.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
💻 APPROACH 1 — RECOMMENDED IMPLEMENTATION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

class MyQueue {
public:

    stack<int> st1;
    stack<int> st2;

    MyQueue() {}

    void push(int x) {
        st1.push(x);
    }

    int pop() {

        if(st2.empty()) {

            while(!st1.empty()) {
                st2.push(st1.top());
                st1.pop();
            }
        }

        int top = st2.top();
        st2.pop();

        return top;
    }

    int peek() {

        if(st2.empty()) {

            while(!st1.empty()) {
                st2.push(st1.top());
                st1.pop();
            }
        }

        return st2.top();
    }

    bool empty() {
        return st1.empty() && st2.empty();
    }
};


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
💡 WHY SAME TRANSFER LOGIC IN POP & PEEK?
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Both operations need the FRONT element.

So:

    if(st2.empty())
        transfer st1 → st2


After transfer:

    st2.top()

= queue front.


Difference:

    pop()
        → top ko remove bhi karta hai

    peek()
        → sirf top return karta hai


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 IMPORTANT CONCEPT — AMORTIZED ANALYSIS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Ye question amortized analysis ka classic example hai.

Kabhi:

    pop() = O(n)

lag sakta hai because transfer karna pada.

But transfer ke baad har element
sirf ek baar transfer hua.

Suppose n elements hain.

Total transfer:

    n elements × one transfer

    = O(n)


Then n operations ke liye total cost:

    O(n)

Therefore:

    O(n) / n
      ↓
    O(1) amortized per operation


IMPORTANT:

    "Every operation is NOT O(1)."

Instead:

    "Average over a sequence of operations
     is O(1) amortized."


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️ COMMON MISTAKE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

❌ Har push ke baad st1 → st2 transfer karna.

That becomes Approach 2.

Better:

    push → st1

    pop/peek:
        if st2 empty:
            transfer
        else:
            use st2 directly


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔄 MENTAL MODEL
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Think of:

    st1 = INBOX 📥
    st2 = OUTBOX 📤


New elements:

    → INBOX


When OUTBOX empty:

    INBOX → OUTBOX


Then OUTBOX se:

    oldest element first


This simple mental model makes the
two-stack queue extremely easy to remember.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🎯 INTERVIEW EXPLANATION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

If interviewer asks:

"How will you implement a queue using stacks?"

Say:

    "I will use two stacks.

     The first stack stores newly pushed elements.
     The second stack is used for popping and peeking.

     Whenever the second stack is empty, I transfer
     all elements from the first stack to the second.
     This reverses their order and gives FIFO behavior.

     Each element is transferred at most once, so
     pop and peek are O(1) amortized."


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⭐ APPROACH TO REMEMBER
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Approach 1 ⭐⭐⭐⭐⭐

    PUSH CHEAP
        ↓
    POP/PEEK LAZY
        ↓
    TWO STACKS
        ↓
    AMORTIZED O(1)


Approach 2 ⭐⭐

    PUSH EXPENSIVE
        ↓
    ALWAYS MAINTAIN FIFO ORDER
        ↓
    O(n) PUSH
        ↓
    O(n²) over many pushes


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔥 ONE-LINE REVISION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

"Two-stack queue: push into input stack;
when output stack is empty, transfer everything
to reverse the order; then pop/peek from output."


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🏆 PATTERN CONNECTION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Stack → Queue

    Stack:
        LIFO

    Queue:
        FIFO

    Two stacks:
        Reverse order twice
            ↓
        FIFO behavior


This is an important Stack + Queue
implementation problem.

Keep BOTH approaches in your conceptual
understanding, but for interviews:

    ⭐ APPROACH 1 = preferred answer
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
