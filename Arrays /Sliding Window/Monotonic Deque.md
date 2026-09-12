━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🪟 LEETCODE 239 — SLIDING WINDOW MAXIMUM
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📌 PATTERN
Sliding Window + Monotonic Deque

Goal:
Har size-k window ka maximum element find karna.

Example:

nums = [1,3,-1,-3,5,3,6,7]
k = 3

Windows:

[1, 3, -1]      → 3
[3, -1, -3]      → 3
[-1, -3, 5]      → 5
[-3, 5, 3]       → 5
[5, 3, 6]        → 6
[3, 6, 7]        → 7

Answer:

[3,3,5,5,6,7]


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 WHY NORMAL SLIDING WINDOW IS NOT ENOUGH
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Window maintain karna easy hai:

    left
      ↓
    [  window  ]
             ↑
            right

Problem:

    Har window me maximum find karna.

Agar har window ko separately scan kiya:

    O(k) per window

Total:

    O(n × k)

Worst case me O(n²).


Hume maximum ko efficiently maintain karna hai.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔥 KEY IDEA — MONOTONIC DEQUE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Deque ko aise maintain karo ki:

    Front → largest
    Back  → smallest

Example:

    [3, 1, -1]

Front par:
    3 = maximum

Isliye:

    dq.front()

hamesha current window ka maximum
candidate rahega.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🎯 DEQUE ME INDEX STORE KARO
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Deque me:

    values ❌
    indices ✅

store karte hain.

Why?

Hume check karna hai ki element
current window ke bahar to nahi chala gaya.

Agar:

    left = 5
    dq.front() = 3

then index 3 expired hai.

So:

    if(left > dq.front())
        dq.pop_front();


Index ke bina ye check nahi kar sakte.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔥 MONOTONIC PROPERTY
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Deque me values decreasing order me rahengi.

Example:

    values:

    7
    5
    3
    1

So:

    nums[dq.front()] >=
    nums[dq[1]] >=
    nums[dq[2]] ...


Why?

Jab new element nums[right] aaye:

    while(!dq.empty() &&
          nums[dq.back()] <= nums[right])

        dq.pop_back();


Agar back wala current se
smaller/equal hai:

    → wo future maximum nahi ban sakta.

Isliye remove.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
💡 THE MOST IMPORTANT OBSERVATION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Suppose deque:

    [5, 3, 2]

New element:

    6

Then:

    2 <= 6 → remove
    3 <= 6 → remove
    5 <= 6 → remove

Deque:

    [6]

Why remove all?

Because 6 is newer AND bigger.

Jab tak 6 window me hai:

    5, 3, 2

me se koi bhi maximum nahi ban sakta.

This is the core reason monotonic deque works.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔍 YOUR CODE KA FLOW
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

while(right < n){

    // 1. Remove useless smaller elements
    while(!dq.empty() &&
          nums[dq.back()] <= nums[right]){
        dq.pop_back();
    }

    // 2. Add current index
    dq.push_back(right);

    // 3. Remove expired index
    if(left > dq.front()){
        dq.pop_front();
    }

    // 4. Window size == k
    if(right - left + 1 == k){

        ans.push_back(nums[dq.front()]);

        left++;
    }

    right++;
}


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧩 STEP 1 — REMOVE USELESS ELEMENTS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

    while(!dq.empty() &&
          nums[dq.back()] <= nums[right])

        dq.pop_back();


This maintains:

    decreasing values


Important:

    <=

not just <

Because equal values me newer index better hota hai.

Example:

    [3, 3]

Second 3 arrives.

Old 3 ko remove kar sakte hain because
new 3 will stay in the window longer.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧩 STEP 2 — PUSH CURRENT INDEX
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

    dq.push_back(right);


Now current element becomes
a possible future maximum.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧩 STEP 3 — REMOVE EXPIRED ELEMENT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Window ka left boundary:

    left

Agar:

    dq.front() < left

then deque front current window ke bahar hai.

So remove:

    dq.pop_front();


Tere code me:

    if(left > dq.front())

same condition ko represent karta hai.


More defensive/general form:

    while(!dq.empty() && dq.front() < left)
        dq.pop_front();


But because tera left boundary har time
sirf 1 step move karta hai, `if` bhi
is implementation me sufficient hai.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧩 STEP 4 — WINDOW COMPLETE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Window size:

    right - left + 1


Agar:

    right - left + 1 == k

then window complete hai.

Maximum:

    nums[dq.front()]


answer me push karo.


Then:

    left++


so next window ke liye slide karo.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧪 DRY RUN
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

nums = [1,3,-1]
k = 3


right = 0

value = 1

Deque:

    [0]

Window size:

    1


right = 1

value = 3

1 <= 3

Remove index 0.

Deque:

    []

Push 1:

    [1]

Window size:

    2


right = 2

value = -1

3 <= -1 ❌

Push:

    [1,2]

Window:

    [1,3,-1]

Maximum:

    nums[dq.front()]
    = nums[1]
    = 3

Answer:

    [3]

Then:

    left++


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔍 WHY dq.front() IS ALWAYS MAXIMUM
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Deque maintains:

    decreasing values

Example:

    dq:

    [3, 2, 1]

Therefore:

    front = 3

which is maximum.

Whenever larger value arrives:

    smaller values at back
    are removed.

Whenever front expires:

    pop_front()

So front always represents
the maximum of the current window.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
💻 CLEAN IMPLEMENTATION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

class Solution {
public:

    vector<int> maxSlidingWindow(
        vector<int>& nums,
        int k
    ) {

        vector<int> ans;

        deque<int> dq;

        int left = 0;

        for(int right = 0; right < nums.size(); right++) {

            // Remove useless smaller/equal elements
            while(!dq.empty() &&
                  nums[dq.back()] <= nums[right]) {

                dq.pop_back();
            }

            // Add current index
            dq.push_back(right);

            // Remove expired index
            while(!dq.empty() && dq.front() < left) {
                dq.pop_front();
            }

            // Window is ready
            if(right - left + 1 == k) {

                ans.push_back(nums[dq.front()]);

                left++;
            }
        }

        return ans;
    }
};


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️ WHY `dq.back()` POP?
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

New element current hai.

Deque ke BACK par smaller elements hain.

So:

    smaller elements
        ↓
    dq.back()

remove karo.

Therefore:

    pop_back()


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️ WHY `dq.front()` POP?
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Deque ka FRONT maximum candidate hai.

But agar wo window ke bahar chala gaya:

    index < left

then:

    pop_front()


So:

    BACK → remove useless candidates

    FRONT → remove expired candidates


This distinction is VERY important.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 TWO CONDITIONS — DON'T MIX THEM
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Condition 1:

    nums[dq.back()] <= nums[right]

Meaning:

    "Ye element future maximum nahi ban sakta."

Action:

    pop_back()


Condition 2:

    dq.front() < left

Meaning:

    "Ye element current window ke bahar hai."

Action:

    pop_front()


Remember:

    VALUE comparison
        → back

    INDEX/window comparison
        → front


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚖️ WHY DEQUE, NOT STACK?
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Stack:

    insertion/removal mainly one end.

Deque:

    front + back dono ends available.


Hume:

    back se useless elements remove

AND:

    front se expired elements remove

dono karne hain.

Therefore:

    deque


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏱️ COMPLEXITY
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Time:

    O(n)

Although while loops hain,
har index:

    → deque me maximum once push
    → deque se maximum once pop

So total operations linear hain.


Space:

    O(k)

Deque me maximum current window ke
relevant indices store hote hain.

Output ko auxiliary space me count
nahi karte.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🚫 BRUTE FORCE VS OPTIMAL
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Brute Force:

    For every window:
        scan k elements
        find maximum

    Time:
        O(nk)


Optimal:

    Sliding Window
          +
    Monotonic Deque

    Time:
        O(n)

    Extra Space:
        O(k)


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🎯 INTERVIEW TRIGGER
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Agar question bole:

    "Maximum/minimum of every
     fixed-size sliding window"

Immediately think:

    Sliding Window
          +
    Monotonic Deque


For maximum:

    Decreasing deque

For minimum:

    Increasing deque


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔥 MAXIMUM VS MINIMUM
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Sliding Window Maximum:

    while nums[dq.back()] <= nums[right]
        pop_back()

    → decreasing deque
    → front = maximum


Sliding Window Minimum:

    while nums[dq.back()] >= nums[right]
        pop_back()

    → increasing deque
    → front = minimum


Bas comparison reverse.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 ONE-LINE MEMORY TRICK
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

"Deque me indices rakho,
back se smaller/equal values hatao,
front se expired indices hatao,
aur front hamesha window ka maximum dega."


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🏆 PATTERN CONNECTION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Sliding Window family:

    Fixed Window
        ↓
    Maximum Average Subarray
        ↓
    O(n)


    Variable Window
        ↓
    Minimum Size Subarray Sum
        ↓
    O(n)


    Fixed Window Maximum
        ↓
    Sliding Window Maximum
        ↓
    Monotonic Deque
        ↓
    O(n)


Monotonic Stack family:

    NGE
      ↓
    Daily Temperatures
      ↓
    Circular NGE
      ↓
    PSE / NSE
      ↓
    Largest Rectangle
      ↓
    Maximal Rectangle


Very important distinction:

    MONOTONIC STACK
        → one-direction candidate maintenance

    MONOTONIC DEQUE
        → candidate maintenance + window expiry


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⭐ FINAL TAKEAWAY
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Sliding Window Maximum is the classic
"Monotonic Deque" problem.

Core invariant:

    dq values are decreasing

Therefore:

    dq.front() = maximum candidate

And every element is:

    pushed once
    popped at most once

→ O(n) time.


This is a MUST-KNOW interview pattern.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
