━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🟩 LEETCODE 85 — MAXIMAL RECTANGLE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📌 PATTERN
Matrix → Histogram → Largest Rectangle in Histogram
       → PSE + NSE
       → Monotonic Stack


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 CORE IDEA
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Maximal Rectangle ko directly matrix par solve
karne ke bajay:

    Each row ko ek HISTOGRAM ki tarah treat karo.

Har row ke liye:

    height[j] = consecutive '1's
                ending at current row

Example:

Matrix:

    1 0 1
    1 1 1
    1 1 1

Row 1:
    [1,0,1]

Row 2:
    [2,1,2]

Row 3:
    [3,2,3]


Ab har row ke heights ko:

    Largest Rectangle in Histogram

ki tarah solve karo.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔥 STEP 1 — HEIGHT ARRAY BUILD KARNA
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Agar:

    matrix[i][j] == '1'

then:

    height[i][j] =
        height[i-1][j] + 1

Agar current cell '0' hai:

    height[i][j] = 0


For first row:

    height[0][j] = 1
    if matrix[0][j] == '1'


Example:

Matrix:

    1 1 0 1
    1 1 1 1
    0 1 1 1

Heights:

Row 0:
    [1,1,0,1]

Row 1:
    [2,2,1,2]

Row 2:
    [0,3,2,3]


Har row = ek histogram.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧩 STEP 2 — LARGEST RECTANGLE IN HISTOGRAM
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

For every bar i:

    Find Previous Smaller Element
    Find Next Smaller Element

Then:

    PSE[i] = first smaller index on LEFT
    NSE[i] = first smaller index on RIGHT


Visualization:

        PSE       i        NSE
         ↓        ↓         ↓
    smaller |  height[i]  | smaller

Rectangle i ke around tab tak extend kar sakta hai
jab tak smaller height na mil jaye.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📐 WIDTH FORMULA
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

    width = NSE[i] - PSE[i] - 1


Why -1?

PSE aur NSE khud rectangle ka part nahi hain.

Example:

    PSE = 1
    NSE = 5

Valid indices:

    2, 3, 4

So:

    width = 5 - 1 - 1
          = 3


Then:

    area = width × height[i]


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 BOUNDARY INITIALIZATION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

This is VERY important:

    PSE → -1
    NSE → n


Why?

Agar left me smaller element nahi hai:

    PSE = -1

Agar right me smaller element nahi hai:

    NSE = n


So:

    vector<int> PSE(n, -1);
    vector<int> NSE(n, n);


Ye exactly wahi subtle point hai jo
Largest Rectangle me bahut important hai.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⬅️ PSE — PREVIOUS SMALLER ELEMENT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

PSE ke liye:

    LEFT → RIGHT

Traverse karo.

Stack me indices store karo.

While:

    nums[st.top()] >= nums[i]

pop.

After popping:

    stack.top()

= nearest previous smaller element.


Template:

    for(int i = 0; i < n; i++){

        while(!st.empty() &&
              nums[st.top()] >= nums[i]){
            st.pop();
        }

        if(!st.empty()){
            ans[i] = st.top();
        }

        st.push(i);
    }


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
➡️ NSE — NEXT SMALLER ELEMENT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

NSE ke liye:

    RIGHT → LEFT

Traverse karo.

Again:

    nums[st.top()] >= nums[i]

wale elements pop karo.

After popping:

    stack.top()

= nearest next smaller element.


Template:

    for(int i = n-1; i >= 0; i--){

        while(!st.empty() &&
              nums[st.top()] >= nums[i]){
            st.pop();
        }

        if(!st.empty()){
            ans[i] = st.top();
        }

        st.push(i);
    }


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️ WHY >= ?
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Suppose:

    [2, 2, 2]

Equal height bhi current bar ke liye
boundary nahi honi chahiye.

Agar:

    stack.top() == current

hai, to equal bar ke through rectangle
continue kar sakta hai.

Isliye equal elements ko bhi pop karo:

    >=


This allows all equal-height bars to be
combined into one wider rectangle.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
💻 YOUR COMPLETE APPROACH
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

class Solution {
public:

    vector<int> NSE(vector<int>& nums){

        int n = nums.size();
        vector<int> ans(n, n);
        stack<int> st;

        for(int i = n - 1; i >= 0; i--){

            while(!st.empty() &&
                  nums[st.top()] >= nums[i]){
                st.pop();
            }

            if(!st.empty()){
                ans[i] = st.top();
            }

            st.push(i);
        }

        return ans;
    }

    vector<int> PSE(vector<int>& nums){

        int n = nums.size();
        vector<int> ans(n, -1);
        stack<int> st;

        for(int i = 0; i < n; i++){

            while(!st.empty() &&
                  nums[st.top()] >= nums[i]){
                st.pop();
            }

            if(!st.empty()){
                ans[i] = st.top();
            }

            st.push(i);
        }

        return ans;
    }

    int largest_rectangle_in_histogram(vector<int>& mat){

        int n = mat.size();

        vector<int> pse = PSE(mat);
        vector<int> nse = NSE(mat);

        int max_area = 0;

        for(int i = 0; i < n; i++){

            int width = nse[i] - pse[i] - 1;

            int area = width * mat[i];

            max_area = max(max_area, area);
        }

        return max_area;
    }

    int maximalRectangle(vector<vector<char>>& matrix){

        int n = matrix[0].size();
        int m = matrix.size();

        vector<vector<int>> temp(
            m,
            vector<int>(n, 0)
        );

        for(int i = 0; i < m; i++){

            for(int j = 0; j < n; j++){

                if(matrix[i][j] == '1'){

                    if(i > 0){
                        temp[i][j] =
                            temp[i-1][j] + 1;
                    }
                    else{
                        temp[i][j] = 1;
                    }
                }
                else{
                    temp[i][j] = 0;
                }
            }
        }

        int max_area = 0;

        for(int i = 0; i < m; i++){

            int area =
                largest_rectangle_in_histogram(temp[i]);

            max_area = max(max_area, area);
        }

        return max_area;
    }
};


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔍 COMPLETE FLOW
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

                MATRIX
                   ↓
          Build HEIGHT matrix
                   ↓
        Each row becomes HISTOGRAM
                   ↓
     ┌─────────────┴─────────────┐
     ↓                           ↓
    PSE                         NSE
     ↓                           ↓
Previous Smaller          Next Smaller
     └─────────────┬─────────────┘
                   ↓
        width = NSE - PSE - 1
                   ↓
        area = width × height
                   ↓
          Maximum of all rows
                   ↓
          MAXIMAL RECTANGLE


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧪 SMALL EXAMPLE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Matrix:

    1 0 1 0 0
    1 0 1 1 1
    1 1 1 1 1
    1 0 0 1 0


Height arrays:

    [1,0,1,0,0]

    [2,0,2,1,1]

    [3,1,3,2,2]

    [4,0,0,3,0]


For:

    [3,1,3,2,2]

Largest rectangle:

    height = 2
    width  = 2

    area = 2 × 2
         = 4

Other rows may produce larger areas.

Maximum across all histograms
= answer.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⭐ WHY THIS APPROACH IS POWERFUL
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Directly matrix me every possible rectangle
check karna expensive ho sakta hai.

Instead:

    2D problem
       ↓
    convert into multiple 1D problems
       ↓
    solve each using O(n) monotonic stack


This is a very important interview technique:

    "Convert a 2D problem into repeated
     1D subproblems."


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️ YOUR CODE — WHAT WAS WRONG BEFORE?
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Tera current PSE/NSE implementation:

    PSE → -1
    NSE → n

ab CORRECT hai.

Previously PSE ka default:

    0

hona problem tha.

Now:

    vector<int> ans(n, -1);

correct hai.

NSE:

    vector<int> ans(n, n);

also correct hai.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
💡 SMALL CLEANUP
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Ye:

    if(!st.empty() &&
       nums[st.top()] < nums[i])

technically redundant hai.

While loop ke baad agar stack non-empty hai,
to automatically:

    nums[st.top()] < nums[i]

true hoga.

So simply:

    if(!st.empty()){
        ans[i] = st.top();
    }

likhna cleaner hai.


Also:

    larget_rectangle_in_histogram

sirf spelling hai.

Better:

    largest_rectangle_in_histogram


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏱️ COMPLEXITY
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Let:

    m = number of rows
    n = number of columns

Height matrix build:

    O(m × n)

Har row ka histogram:

    PSE → O(n)
    NSE → O(n)
    Area → O(n)

So all rows:

    O(m × n)


Overall:

    Time  → O(m × n)
    Space → O(m × n)


Your implementation stores the complete
height matrix.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🚀 SPACE OPTIMIZATION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Complete temp matrix store karna necessary nahi hai.

Sirf ek:

    vector<int> heights(n, 0);

rakho.

Har row ke liye heights update karo:

    if(matrix[i][j] == '1')
        heights[j]++;
    else
        heights[j] = 0;


Then:

    largest_rectangle_in_histogram(heights)


This reduces extra space from:

    O(m × n)

to:

    O(n)

while time remains:

    O(m × n)


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🎯 INTERVIEW TRIGGER
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Agar question me:

    Binary Matrix
        +
    Largest all-1 rectangle

dikhe:

    Think:

    "Histogram bana sakta hoon?"

Then:

    Matrix
      ↓
    Row-wise heights
      ↓
    Largest Rectangle in Histogram
      ↓
    PSE + NSE
      ↓
    Monotonic Stack


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔥 ONE-LINE MEMORY TRICK
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

"Maximal Rectangle = Har row ko histogram banao
+ Largest Rectangle in Histogram solve karo."


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🏆 PATTERN CHAIN — VERY IMPORTANT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

496 → Next Greater Element
503 → Circular NGE
739 → Daily Temperatures
84  → Largest Rectangle in Histogram
85  → Maximal Rectangle

The progression:

    NGE
     ↓
    Monotonic Stack
     ↓
    NSE / PSE
     ↓
    Largest Rectangle
     ↓
    2D → Histogram
     ↓
    Maximal Rectangle


This is a VERY valuable Monotonic Stack family.
Definitely keep this in your GitHub notes.
