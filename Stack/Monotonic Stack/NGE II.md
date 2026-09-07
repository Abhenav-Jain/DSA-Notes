━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔄 LEETCODE 503 — NEXT GREATER ELEMENT II
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📌 PATTERN
Circular Array + Monotonic Stack + NGE

Question:
Har element ke liye uske RIGHT side ka first
GREATER element find karo.

But array CIRCULAR hai.

Meaning:

    [1, 2, 1]

last element ke baad:
    first element

consider hoga.

So for:

    [1, 2, 1]

Answer:

    [2, -1, 2]


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 PROBLEM KA MAIN TWIST
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Normal NGE (LeetCode 496):

    [1, 3, 2, 4]

Element ke right me sirf actual right side
consider hoti hai.

Circular NGE me:

    [1, 3, 2, 4]

4 ke baad again:

    1 → 3 → 2 → 4 → ...

So 4 ka NGE still -1,
but 2 ka NGE 4 hai,
and elements near the end first part
of the array ko bhi consider kar sakte hain.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔥 KEY TRICK — ARRAY KO 2 TIMES IMAGINE KARO
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Instead of actually duplicating the array:

    [1, 2, 1]

Imagine:

    [1, 2, 1, 1, 2, 1]

Length:

    2 * n


But actual array access:

    nums[i % n]

Example:

    n = 3

    i = 0 → nums[0 % 3] = nums[0]
    i = 1 → nums[1 % 3] = nums[1]
    i = 2 → nums[2 % 3] = nums[2]

    i = 3 → nums[3 % 3] = nums[0]
    i = 4 → nums[4 % 3] = nums[1]
    i = 5 → nums[5 % 3] = nums[2]


So:

    nums[i % n]

circular traversal simulate karta hai.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🚀 WHY 2*n ITERATIONS?
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Hume har element ke liye:

    apne right side
    +
    array ka wrapped-around part

dono check karne hain.

Ek complete extra traversal enough hai.

So:

    i = 2*n - 1 → 0


First n iterations:
    stack ko future candidates se prepare karte hain.

Second n iterations:
    original elements ke actual answers calculate karte hain.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧩 YOUR CODE KA CORE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

    for(int i = 2*n-1; i >= 0; i--)

Circular array ko virtually 2 baar traverse karta hai.

Current value:

    nums[i % n]


Then:

    while(!st.empty() && st.top() <= nums[i%n]){
        st.pop();
    }

Smaller OR equal elements useless hain.

After popping:

    st.top()

= next greater candidate.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️ WHY <= ?
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Hume STRICTLY greater element chahiye.

Suppose:

    current = 3
    stack.top() = 3

3 > 3 false hai.

So equal value useful nahi hai.

Therefore:

    st.top() <= current

ko pop karna hai.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🎯 MOST IMPORTANT PART — if(i < n)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Ye line bahut important hai:

    if(i < n)

Kyun?

Hum 2*n iterations kar rahe hain,
but answer sirf original n elements ka chahiye.

Indices:

    2n-1 ... n
       ↓
    preparation phase

    n-1 ... 0
       ↓
    actual answer phase


So:

    if(i < n)

ka matlab:

    "Ab original array ke elements process
     ho rahe hain, answer store karo."


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔍 DRY RUN
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

nums = [1, 2, 1]

n = 3

Virtual array:

    [1, 2, 1, 1, 2, 1]

Traverse:

    i = 5 → 1
    i = 4 → 2
    i = 3 → 1
    i = 2 → 1
    i = 1 → 2
    i = 0 → 1


Important answer phase:

i = 2 → value 1

Stack ke top se greater value:
    2

ans[2] = 2


i = 1 → value 2

No greater element:
    ans[1] = -1


i = 0 → value 1

Next greater:
    2

ans[0] = 2


Final:

    [2, -1, 2]


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
💻 YOUR SOLUTION — CLEAN VERSION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

class Solution {
public:

    vector<int> nextGreaterElements(vector<int>& nums) {

        stack<int> st;

        int n = nums.size();

        vector<int> ans(n, -1);

        for(int i = 2 * n - 1; i >= 0; i--) {

            int current = nums[i % n];

            while(!st.empty() && st.top() <= current) {
                st.pop();
            }

            if(i < n) {

                if(!st.empty()) {
                    ans[i] = st.top();
                }
            }

            st.push(current);
        }

        return ans;
    }
};


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 IMPORTANT: STACK ME VALUE KYU?
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Yahan:

    stack<int> st;

me values store kar rahe hain.

Because answer chahiye:

    "Next Greater VALUE"

Distance/index nahi chahiye.

Compare:

496 — Next Greater Element I
    → value stack

503 — Next Greater Element II
    → value stack

739 — Daily Temperatures
    → index stack

Because Daily Temperatures me:
    "kitne days baad?"

calculate karna tha.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔄 496 vs 503
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

496 — Next Greater Element I

    Normal array
        ↓
    Right → Left
        ↓
    Monotonic Stack
        ↓
    HashMap


503 — Next Greater Element II

    Circular array
        ↓
    Virtually 2*n traversal
        ↓
    nums[i % n]
        ↓
    Monotonic Stack


Main new concept:

    CIRCULAR ARRAY


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚡ CIRCULAR ARRAY KE 2 COMMON APPROACHES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Approach 1:
    Physically duplicate array

    [1,2,1]
       ↓
    [1,2,1,1,2,1]

But extra O(n) memory.


Approach 2 ⭐:
    Virtual duplication

    nums[i % n]

No extra duplicated array required.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📌 MONOTONIC STACK TEMPLATE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Circular Next Greater Element:

    for(int i = 2*n - 1; i >= 0; i--) {

        while(!st.empty() &&
              st.top() <= nums[i % n]) {

            st.pop();
        }

        if(i < n && !st.empty()) {
            ans[i] = st.top();
        }

        st.push(nums[i % n]);
    }


Memory trick:

    Circular
       ↓
    2*n traversal
       ↓
    i % n
       ↓
    Right → Left
       ↓
    Pop smaller/equal
       ↓
    Top = NGE
       ↓
    Answer only when i < n


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️ COMMON MISTAKES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

❌ Only n iterations karna
   → circular part miss ho jayega.

❌ Actual array duplicate karna unnecessarily
   → extra memory.

❌ nums[i] directly use karna
   → i >= n par out of bounds.

Correct:

    nums[i % n]


❌ Answer second traversal me calculate na karna.

❌ Equal values ko stack me useful candidate
   samajhna.

Correct:

    <= → pop


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏱️ COMPLEXITY
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Time:
    O(n)

Although loop 2*n times chal raha hai,
each element maximum push/pop limited times hota hai.

More precisely:
    O(2n) = O(n)


Space:
    O(n)

Stack:
    O(n)

Answer:
    O(n) output space


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔥 NGE FAMILY — AB PATTERN CONNECT KARO
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

496 — Next Greater Element I
    → Basic NGE
    → Monotonic Stack + HashMap

739 — Daily Temperatures
    → NGE
    → Index Difference

503 — Next Greater Element II
    → Circular NGE
    → 2*n traversal + modulo


So ab tak:

    496
     ↓
    Basic NGE
     ↓
    739
     ↓
    NGE + Distance
     ↓
    503
     ↓
    Circular NGE


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 ONE-LINE REVISION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

"Circular NGE = 2*n traversal +
 modulo indexing + monotonic stack."


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🎯 INTERVIEW TRIGGER
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Agar question bole:

    "Find next greater element on the right"

→ Monotonic Stack

Agar bole:

    "Array is circular"

→ 2*n traversal + i % n

Agar bole:

    "How many positions/days later?"

→ Store INDEX instead of value.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⭐ FINAL PATTERN

        NEXT GREATER ELEMENT
                 ↓
        MONOTONIC STACK
                 ↓
        ┌────────┴────────┐
        ↓                 ↓
   Normal Array      Circular Array
        ↓                 ↓
   Right → Left        2*n traversal
                         ↓
                       i % n
