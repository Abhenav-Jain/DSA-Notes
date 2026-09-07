━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🌡️ LEETCODE 739 — DAILY TEMPERATURES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📌 PATTERN
Monotonic Stack → Next Greater Element (NGE)

Question:
Har day ke liye find karo ki future me kitne days baad
temperature strictly greater hogi.

Example:
temperatures = [73,74,75,71,69,72,76,73]

answer:
[1,1,4,2,1,1,0,0]

Meaning:
73 → 74 → 1 day
74 → 75 → 1 day
75 → 76 → 4 days
71 → 72 → 2 days
69 → 72 → 1 day
72 → 76 → 1 day
76 → no greater → 0
73 → no greater → 0


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 CORE IDEA
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Hume har element ka:

    "Next Greater Element"

chahiye.

Kyuki answer RIGHT side me hai,
array ko RIGHT → LEFT traverse karenge.

Stack me temperature nahi,
INDEX store karenge.

    st.push(i)

Index store karne ka fayda:

    temperature  → nums[st.top()]
    distance     → st.top() - i

Yahi is question ka main trick hai.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔥 MONOTONIC STACK LOGIC
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

For every i:

1. Stack empty nahi hai AND
   current temperature >= stack-top temperature

       while(!st.empty() &&
             temperatures[st.top()] <= temperatures[i])

       → st.pop()

   Kyuki stack-top wala temperature
   current day se greater answer nahi de sakta.

2. Agar stack empty nahi hai:

       st.top()

   = next greater temperature ka INDEX

3. Distance:

       st.top() - i

4. Current index ko stack me push karo.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️ WHY <= ?
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Question me strictly GREATER temperature chahiye.

Agar:

    current = 75
    stack top = 75

To 75, 75 se greater nahi hai.

Isliye:

    <=

use karke equal temperatures bhi remove karte hain.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔍 DRY RUN
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Example:

    [73, 74, 75, 71, 72]

Right → Left


i = 4 → 72

Stack empty

ans[4] = 0
push index 4

Stack:
    [4]


i = 3 → 71

72 > 71

ans[3] = 4 - 3 = 1

push 3

Stack:
    [4, 3]


i = 2 → 75

71 <= 75 → pop
72 <= 75 → pop

Stack empty

ans[2] = 0

push 2

Stack:
    [2]


i = 1 → 74

75 > 74

ans[1] = 2 - 1 = 1

push 1

Stack:
    [2, 1]


i = 0 → 73

74 > 73

ans[0] = 1 - 0 = 1

push 0


Final:

    [1, 1, 0, 1, 0]


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
💡 WHY INDEX STORE KARTE HAIN?
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Agar stack me sirf temperature store karte:

    [74, 75, 76]

To next greater temperature to mil jaata,
but answer me required hai:

    "kitne DAYS baad?"

Isliye index store karo.

Example:

    current index = 2
    next greater index = 5

Answer:

    5 - 2 = 3 days


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
💻 CLEAN IMPLEMENTATION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

class Solution {
public:

    vector<int> NGE(vector<int>& nums) {

        stack<int> st;
        int n = nums.size();

        vector<int> ans(n, 0);

        for(int i = n - 1; i >= 0; i--) {

            // Remove elements that cannot be
            // the next greater element
            while(!st.empty() &&
                  nums[st.top()] <= nums[i]) {

                st.pop();
            }

            // Stack top = next greater element index
            if(!st.empty()) {
                ans[i] = st.top() - i;
            }

            st.push(i);
        }

        return ans;
    }

    vector<int> dailyTemperatures(vector<int>& temperatures) {

        return NGE(temperatures);
    }
};


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🎯 THE MOST IMPORTANT TEMPLATE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

RIGHT → LEFT + MONOTONIC STACK

for(int i = n-1; i >= 0; i--) {

    while(!st.empty() && condition) {
        st.pop();
    }

    if(!st.empty()) {
        answer = st.top() - i;
    }

    st.push(i);
}


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧩 GENERAL NGE PATTERN
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Next Greater Element on RIGHT:

    Traverse → Right to Left

    Stack → useful candidates

    While top is not useful:
        pop()

    If stack not empty:
        top = answer

    Push current


For Daily Temperatures:

    "useful" means:

    temperature[stack.top()] > temperature[i]


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️ COMMON MISTAKES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

❌ Store values instead of indices
   → distance calculate nahi kar paoge.

❌ Use < instead of <=
   → equal temperature incorrectly consider ho sakti hai.

❌ Traverse left → right without knowing
   the alternate monotonic-stack approach.

❌ Forget to pop useless candidates.

❌ Distance ko:
       i - st.top()

   likh dena.

Correct:

       st.top() - i


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏱️ COMPLEXITY
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Time:
    O(n)

Although while loop hai, har index maximum
ek baar push aur ek baar pop hota hai.

Space:
    O(n)

Worst case stack me O(n) indices ho sakte hain.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 ONE-LINE MEMORY TRICK
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

"Future ka greater element chahiye
→ Right se traverse karo
→ useless elements pop karo
→ stack top answer candidate hai
→ index difference lo."


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔥 INTERVIEW TAKEAWAY
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Daily Temperatures =

    Next Greater Element
            +
    Monotonic Decreasing Stack
            +
    Index Difference

Ye question properly samajh liya to
NGE family ke bahut saare questions easy ho jaate hain:

    • Next Greater Element
    • Daily Temperatures
    • Next Greater Element II
    • Stock Span
    • Largest Rectangle in Histogram
    • Remove K Digits

Sab me underlying idea:
    "Useful candidates ko stack me maintain karo."
