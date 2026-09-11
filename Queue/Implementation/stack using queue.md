━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📚 LEETCODE 225 — IMPLEMENT STACK USING QUEUES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📌 GOAL

Stack follows:

    LIFO
    Last In → First Out

Queue follows:

    FIFO
    First In → First Out

Challenge:

    Queue use karke Stack implement karna.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 APPROACH — SINGLE QUEUE ⭐
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Hum sirf ek queue:

    q

use karenge.

Main idea:

    New element ko queue ke BACK me push karo.

But Stack me new element ko immediately
FRONT par hona chahiye because:

    stack.pop()
    stack.top()

dono newest element ko access karte hain.


So new element push karne ke baad:

    queue ke old elements ko
    front se remove karke back me daal do.

This rotates the queue.

Result:

    NEWEST ELEMENT → FRONT


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔥 EXAMPLE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Initially:

    q = []


push(1):

    q.push(1)

    [1]


push(2):

First:

    q.push(2)

    [1, 2]

Now 2 ko front par lana hai.

Old elements = 1

Rotate:

    pop 1
    push 1

Result:

    [2, 1]


push(3):

First:

    [2, 1, 3]

Old elements:

    2, 1

Rotate both:

    pop 2 → push 2
    pop 1 → push 1

Result:

    [3, 2, 1]


Now queue ka FRONT:

    3

which is exactly Stack ka TOP.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🎯 THE KEY TRICK
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

After every push:

    New element ko FRONT par le aao.

Then queue behaves like a stack:

    Queue front = Stack top


So:

    push(x)
        ↓
    q.push(x)
        ↓
    rotate old elements
        ↓
    x becomes front


Then:

    pop()  → q.front()
    top()  → q.front()


Both become O(1).


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
💻 YOUR CODE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

class MyStack {

    queue<int> q;

public:

    MyStack() {}

    void push(int x) {

        q.push(x);

        int s = q.size();

        for(int i = 0; i < s - 1; i++) {

            q.push(q.front());
            q.pop();
        }
    }

    int pop() {

        int val = q.front();
        q.pop();

        return val;
    }

    int top() {

        return q.front();
    }

    bool empty() {

        return q.empty();
    }
};


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔍 WHY `s - 1`?
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

This is the most important line:

    int s = q.size();

    for(int i = 0; i < s - 1; i++)

Suppose:

    q = [1, 2]

push(3):

    q = [1, 2, 3]

Hume:

    [3, 1, 2]

chahiye.

So sirf OLD elements ko rotate karna hai:

    1
    2

Total old elements:

    s - 1


That's why:

    i < s - 1


⚠️ Agar `s` times rotate karoge,
new element bhi rotate ho jayega and
original arrangement wapas aa jayega.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧪 COMPLETE DRY RUN
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Operations:

    push(10)
    push(20)
    push(30)
    top()
    pop()
    top()


──────────────────────────────────────

push(10)

q.push(10)

    [10]

s = 1

    s - 1 = 0

No rotation.

Queue:

    [10]


──────────────────────────────────────

push(20)

After push:

    [10,20]

s = 2

Rotate 1 old element:

    [20,10]

Now:

    front = 20

So stack representation:

    TOP → 20
           10


──────────────────────────────────────

push(30)

After push:

    [20,10,30]

s = 3

Rotate 2 old elements:

    [10,30,20]
    [30,20,10]

Final:

    [30,20,10]

So:

    TOP = 30


──────────────────────────────────────

top()

    q.front()

    → 30


──────────────────────────────────────

pop()

    q.front() = 30

Remove:

    [20,10]

Returns:

    30


──────────────────────────────────────

top()

    q.front()

    → 20


Exactly Stack behavior.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
💡 WHY DOES THIS WORK?
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Normal Queue:

    FRONT → oldest
             ...
             newest ← BACK


But Stack needs:

    TOP → newest
           ...
           oldest


After every push we rotate the queue:

    newest → FRONT

Therefore:

    Queue FRONT
        =
    Stack TOP


This makes:

    q.front()

equivalent to:

    stack.top()


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚖️ TWO POSSIBLE APPROACHES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Approach 1 — Single Queue ⭐

    push → O(n)
    pop  → O(1)
    top  → O(1)


Approach 2 — Two Queues

    push/pop trade-offs possible

But single queue is cleaner and
easier to explain for this problem.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏱️ COMPLEXITY
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

For your approach:

    push:
        O(n)

Because existing elements ko rotate karna padta hai.

    pop:
        O(1)

    top:
        O(1)

    empty:
        O(1)


Space:

    O(n)

Queue me maximum n elements store honge.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 IMPORTANT COMPARISON WITH Q232
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

232 — Queue using Stacks

    Queue = FIFO
    Stack  = LIFO

Strategy:

    Push cheap
    Pop/Peek lazy

    push → O(1)
    pop  → O(1) amortized


225 — Stack using Queue

    Stack = LIFO
    Queue = FIFO

Strategy:

    Push expensive
    Pop/Top cheap

    push → O(n)
    pop  → O(1)
    top  → O(1)


Remember:

    Queue using Stacks
        → reverse when needed

    Stack using Queue
        → rotate after push


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️ COMMON MISTAKES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

❌ Simply q.push(x) kar dena.

Then:

    q = [1,2,3]

but:

    q.front() = 1

Stack ko 3 return karna chahiye.

So order must be rearranged.


❌ `s` ke jagah `s-1` se rotate na karna.

New element ko rotate nahi karna.

Only OLD elements rotate honge.


❌ pop/top ke liye `q.back()` use karna.

Our invariant is:

    newest element = FRONT

So:

    q.front()


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⭐ MOST IMPORTANT INVARIANT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Har `push()` ke baad:

    q.front()
        ↓
    latest pushed element
        ↓
    Stack TOP


This invariant poore solution ko
drive karta hai.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🎯 INTERVIEW EXPLANATION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Interviewer:

"How do you implement a stack using one queue?"

Answer:

    "Since a queue gives FIFO but a stack requires LIFO,
     I maintain the newest element at the front of the queue.

     Whenever I push a new element, I first insert it
     at the back and then rotate all previously existing
     elements from the front to the back.

     This makes the newly inserted element the front
     element, so pop and top can directly use q.front()."


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔥 ONE-LINE REVISION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

"Stack using one queue:
push new element → rotate all old elements →
new element comes to front → front behaves as stack top."


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🏆 STACK ↔ QUEUE IMPLEMENTATION FAMILY
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

232 — Queue using Stacks
    ↓
Two stacks
    ↓
Lazy transfer
    ↓
FIFO

225 — Stack using Queue
    ↓
One queue
    ↓
Rotate after push
    ↓
LIFO


Core lesson:

    Data structure ka behavior change karne ke liye
    elements ka ORDER manipulate karo.

    FIFO → LIFO
    LIFO → FIFO
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
