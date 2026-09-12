━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
💧 LEETCODE 42 — TRAPPING RAIN WATER
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🟢 STATUS: MUST KEEP ⭐⭐⭐⭐⭐

📂 STORE:
Arrays → Prefix/Suffix Maximum
         OR
Arrays → Two Pointers

Also remember:
This problem can be solved using a Monotonic Stack,
but the two approaches below are different from
the classic NGE problem.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 CORE IDEA
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Har index i par kitna water store hoga?

    water[i] =
        min(leftMax[i], rightMax[i])
        - height[i]

Where:

    leftMax[i]
        = maximum height strictly LEFT of i

    rightMax[i]
        = maximum height strictly RIGHT of i


Why minimum?

Suppose:

    leftMax  = 7
    rightMax = 4

Water 7 height tak nahi ja sakta because
right boundary sirf 4 tak hai.

Therefore:

    water level = min(7,4)
                = 4


Final:

    water[i] =
        min(leftMax[i], rightMax[i])
        - height[i]


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️ VERY IMPORTANT — NGE/PGE CONFUSION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Trapping Rain Water ko solve karne ke liye
hume actual:

    NGE = Next Greater Element
    PGE = Previous Greater Element

nahi chahiye.

Hume chahiye:

    Right Maximum
    Left Maximum


Difference:

    NGE
    ↓
    first greater element on the right


    Right Maximum
    ↓
    maximum element among ALL elements
    on the right


Example:

    [5, 1, 2, 3, 4]

For element 1:

    NGE = 2

    Right Maximum = 4


Water calculation ke liye:

    4 chahiye.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔥 APPROACH 1 — MONOTONIC STACK BASED
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Tere first solution me function names:

    NGE()
    PGE()

hain.

But actual me ye functions:

    Right Maximum
    Left Maximum

calculate kar rahe hain.

So:

    Function name  → NGE/PGE ❌ misleading
    Actual logic   → RightMax/LeftMax ✅


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
➡️ RIGHT MAXIMUM USING STACK
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Right → Left traverse karte hain.

Stack me useful maximum candidates maintain
kiye ja rahe hain.

Core:

    while(!st.empty() && nums[i] > nums[st.top()])
        st.pop();


Iska purpose:

    current element se chhote elements
    future ke liye useless hain.

Then:

    if(!st.empty())
        ans[i] = nums[st.top()];

and current index ko stack me push karte hain.


Conceptually:

    current ke RIGHT me jo maximum available hai
    usko maintain karna hai.


Example:

    [5,1,2,3,4]

For 1:

    right maximum = 4

Even though:

    NGE(1) = 2

stack approach ultimately 4 ko preserve
karti hai as the relevant boundary.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⬅️ LEFT MAXIMUM USING STACK
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Same idea opposite direction.

Left → Right traverse.

Core:

    while(!st.empty() && nums[i] > nums[st.top()])
        st.pop();


Then:

    if(!st.empty())
        ans[i] = nums[st.top()];

Current index push.


This gives the relevant maximum on the left
for water calculation.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
💻 APPROACH 1 — CLEANED VERSION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

class Solution {
public:

    vector<int> rightMax(vector<int>& nums) {

        int n = nums.size();

        vector<int> ans(n, -1);

        stack<int> st;

        for(int i = n - 1; i >= 0; i--) {

            while(!st.empty() &&
                  nums[i] > nums[st.top()]) {

                st.pop();
            }

            if(!st.empty()) {
                ans[i] = nums[st.top()];
            }

            st.push(i);
        }

        return ans;
    }


    vector<int> leftMax(vector<int>& nums) {

        int n = nums.size();

        vector<int> ans(n, -1);

        stack<int> st;

        for(int i = 0; i < n; i++) {

            while(!st.empty() &&
                  nums[i] > nums[st.top()]) {

                st.pop();
            }

            if(!st.empty()) {
                ans[i] = nums[st.top()];
            }

            st.push(i);
        }

        return ans;
    }


    int trap(vector<int>& height) {

        int n = height.size();

        vector<int> rMax = rightMax(height);
        vector<int> lMax = leftMax(height);

        int water = 0;

        for(int i = 0; i < n; i++) {

            int area =
                min(lMax[i], rMax[i])
                - height[i];

            if(area > 0) {
                water += area;
            }
        }

        return water;
    }
};


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⭐ IMPORTANT NOTE ABOUT APPROACH 1
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Tera original code correct hai.

But ye:

    if(st.empty()){
        st.push(i);
    }
    else{
        if(nums[i] > nums[st.top()]){
            st.push(i);
        }
    }

actually simplify ho sakta hai:

    st.push(i);

Because while loop ke baad:

    either stack empty hai

OR

    nums[i] <= nums[st.top()]

So second:

    if(nums[i] > nums[st.top()])

normally true nahi ho sakta.

Therefore code ko cleaner banaya ja sakta hai.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔥 APPROACH 2 — PREFIX/SUFFIX MAXIMUM
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Ye much simpler approach hai.

No stack.

Sirf running maximum maintain karo.


LEFT MAX:

    Left → Right

    ans[i] = maximum seen BEFORE i

    maxi = max(maxi, nums[i])


RIGHT MAX:

    Right → Left

    ans[i] = maximum seen AFTER i

    maxi = max(maxi, nums[i])


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
💻 APPROACH 2 — YOUR SOLUTION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

class Solution {
public:

    // Maximum height on the RIGHT
    vector<int> rightMax(vector<int>& nums) {

        int n = nums.size();

        vector<int> ans(n, -1);

        int maxi = -1;

        for(int i = n - 1; i >= 0; i--) {

            ans[i] = maxi;

            maxi = max(maxi, nums[i]);
        }

        return ans;
    }


    // Maximum height on the LEFT
    vector<int> leftMax(vector<int>& nums) {

        int n = nums.size();

        vector<int> ans(n, -1);

        int maxi = -1;

        for(int i = 0; i < n; i++) {

            ans[i] = maxi;

            maxi = max(maxi, nums[i]);
        }

        return ans;
    }


    int trap(vector<int>& height) {

        int n = height.size();

        vector<int> rMax = rightMax(height);
        vector<int> lMax = leftMax(height);

        int water = 0;

        for(int i = 0; i < n; i++) {

            int area =
                min(lMax[i], rMax[i])
                - height[i];

            if(area > 0) {
                water += area;
            }
        }

        return water;
    }
};


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧪 DRY RUN
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

height:

    [4, 2, 0, 3, 2, 5]


LEFT MAX:

    i = 0 → -1
    i = 1 → 4
    i = 2 → 4
    i = 3 → 4
    i = 4 → 4
    i = 5 → 4

    lMax:

    [-1, 4, 4, 4, 4, 4]


RIGHT MAX:

    i = 5 → -1
    i = 4 → 5
    i = 3 → 5
    i = 2 → 5
    i = 1 → 5
    i = 0 → 5

    rMax:

    [5, 5, 5, 5, 5, -1]


Now:

    water[i] =
        min(lMax[i],rMax[i])
        - height[i]


i = 1:

    min(4,5) - 2
    = 2


i = 2:

    min(4,5) - 0
    = 4


i = 3:

    min(4,5) - 3
    = 1


i = 4:

    min(4,5) - 2
    = 2


Total:

    2 + 4 + 1 + 2
    = 9


Answer:

    9


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚖️ APPROACH 1 VS APPROACH 2
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

APPROACH 1
Monotonic Stack

    RightMax → Stack
    LeftMax  → Stack

    Time  → O(n)
    Space → O(n)


APPROACH 2
Prefix/Suffix Maximum

    RightMax → Running maximum
    LeftMax  → Running maximum

    Time  → O(n)
    Space → O(n)


Both:

    ✅ Correct
    ✅ O(n) time
    ✅ Produce required boundaries


But:

    Approach 2
        ↓
    simpler
        ↓
    easier to explain
        ↓
    easier to remember


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 WHY APPROACH 2 IS BETTER HERE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Problem ko actually sirf ye chahiye:

    "Maximum on left"
    "Maximum on right"

Iske liye stack ki need hi nahi hai.

Running maximum enough hai:

    leftMax:
        maxi maintain karo


    rightMax:
        maxi maintain karo


So:

    Stack solution = valid but unnecessary complexity

    Prefix/Suffix = natural solution


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🚀 APPROACH 3 — TWO POINTERS ⭐⭐⭐⭐⭐
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Most space-efficient solution.

Instead of storing:

    leftMax[]
    rightMax[]

maintain only:

    leftMax
    rightMax

and:

    left
    right


Core idea:

If:

    height[left] <= height[right]

then left side process karo.

Otherwise:

    right side process karo.


Because smaller boundary side ka water level
us side ke maximum se determine ho sakta hai.


Pseudo:

    left = 0
    right = n - 1

    leftMax = 0
    rightMax = 0

    while(left <= right):

        if(height[left] <= height[right]):

            if(height[left] >= leftMax)
                leftMax = height[left]

            else
                water += leftMax - height[left]

            left++

        else:

            if(height[right] >= rightMax)
                rightMax = height[right]

            else
                water += rightMax - height[right]

            right--


Complexity:

    Time  → O(n)
    Space → O(1)


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 THREE MAIN APPROACHES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1️⃣ Brute Force

For every index:

    find left maximum
    find right maximum

    Time  → O(n²)
    Space → O(1)


2️⃣ Prefix/Suffix Maximum ⭐

Precompute:

    leftMax[]
    rightMax[]

    Time  → O(n)
    Space → O(n)


3️⃣ Two Pointers ⭐⭐⭐⭐⭐

Maintain:

    leftMax
    rightMax
    left
    right

    Time  → O(n)
    Space → O(1)


Extra:

4️⃣ Monotonic Stack

Possible, but more complicated than
prefix/suffix or two-pointer approach for
this particular problem.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️ COMMON CONFUSION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

NGE/PGE:

    nearest greater element


LeftMax/RightMax:

    maximum element on the entire side


They are NOT the same concept.


Example:

    [5, 1, 2, 3, 4]

For 1:

    NGE = 2
    RightMax = 4


Trapping Rain Water:

    needs 4


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🎯 INTERVIEW TRIGGER
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Question:

    "How much water can be trapped
     between bars?"

Immediately think:

    LEFT MAX
        +
    RIGHT MAX

Then choose:

    Prefix/Suffix
        OR
    Two Pointers


Formula:

    water =
        min(leftMax,rightMax)
        - height


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔥 ONE-LINE MEMORY TRICK
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

"Har bar ke liye dono sides ki maximum wall
nikalo; water level weaker wall decide karegi."


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🏆 FINAL TAKEAWAY
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

TRAPPING RAIN WATER

    ┌─────────────────────────────┐
    │      Left Maximum           │
    │            ↓                │
    │       ┌────────┐            │
    │       │ water  │ ← min      │
    │       └────────┘            │
    │            ↑                │
    │      Right Maximum          │
    └─────────────────────────────┘

Formula:

    water[i]
    =
    min(leftMax[i], rightMax[i])
    - height[i]


MOST IMPORTANT:

    NGE/PGE
        ≠
    LeftMax/RightMax


Tera Approach 1:
    ✅ Correct
    → stack-based maximum boundary approach


Tera Approach 2:
    ✅ Correct
    → prefix/suffix maximum approach


Best for understanding:
    Approach 2


Best for space:
    Two Pointer → O(1)


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
