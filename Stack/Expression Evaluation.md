━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 LEETCODE 150 — EVALUATE REVERSE POLISH NOTATION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📌 PATTERN
Stack → Expression Evaluation → LIFO

RPN me operator ke operands hamesha stack ke top par milte hain.

Normal/Infix:
    2 + 3

RPN:
    2 3 +

Example:
    ["2", "1", "+", "3", "*"]

    2 1 + 3 *
    → (2 + 1) * 3
    → 9


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
💡 CORE IDEA
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Har token ko left → right process karo:

1. Agar token NUMBER hai
   → stack me push karo.

2. Agar token OPERATOR hai (+, -, *, /)
   → stack se 2 operands nikalo.
   → operation perform karo.
   → result ko wapas stack me push karo.

3. End me stack ka top = final answer.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️ MOST IMPORTANT: OPERAND ORDER
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Stack se pehla nikla element = RIGHT operand
Stack se doosra nikla element = LEFT operand

Suppose:

    5 3 -

Stack:
    [5, 3]

pop() → a = 3
pop() → b = 5

Correct:
    b - a
    5 - 3 = 2

❌ a - b  → wrong

Isi liye code me:

    int a = st.top(); st.pop();
    int b = st.top(); st.pop();

    result = b - a;


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔍 DRY RUN
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

tokens = ["4", "13", "5", "/", "+"]

Step 1:
    "4" → push

    Stack: [4]

Step 2:
    "13" → push

    Stack: [4, 13]

Step 3:
    "5" → push

    Stack: [4, 13, 5]

Step 4:
    "/" → pop 5, pop 13

    13 / 5 = 2

    Stack: [4, 2]

⚠️ Integer division is used.

Step 5:
    "+" → pop 2, pop 4

    4 + 2 = 6

    Stack: [6]

Answer = 6


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧩 WHY STACK?
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

RPN naturally follows LIFO.

Jab operator milta hai, uske required operands
exactly stack ke top par available hote hain.

Example:

    2 3 4 * +

First:
    4 * 3 = 12

Then:
    2 + 12 = 14

Stack automatically correct order maintain karta hai.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
💻 C++ IMPLEMENTATION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

class Solution {
public:

    int operate(int a, int b, string& token) {

        if(token == "+") {
            return b + a;
        }
        else if(token == "-") {
            return b - a;
        }
        else if(token == "*") {
            return b * a;
        }
        else {
            return b / a;
        }
    }

    int evalRPN(vector<string>& tokens) {

        stack<int> st;

        for(string& token : tokens) {

            // Operator
            if(token == "+" || token == "-" ||
               token == "*" || token == "/") {

                int a = st.top();
                st.pop();

                int b = st.top();
                st.pop();

                int result = operate(a, b, token);

                st.push(result);
            }

            // Number
            else {
                st.push(stoi(token));
            }
        }

        return st.top();
    }
};


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🎯 CODE KA MAIN LOGIC
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Number:
    st.push(stoi(token));

Operator:
    a = st.top(); st.pop();   // RIGHT
    b = st.top(); st.pop();   // LEFT

    result = operate(a, b, token);

    st.push(result);


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️ IMPORTANT INTERVIEW POINTS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. Subtraction aur division me operand order matter karta hai.

       b - a
       b / a

   NOT:

       a - b
       a / b


2. stoi(token)

   String number ko integer me convert karta hai.

       "123" → 123
       "-7"  → -7


3. Negative numbers aur operators ko distinguish karna:

   Sirf exact operators check karo:

       "+"
       "-"
       "*"
       "/"

   Isliye "-11" ko operator nahi, number treat kiya jayega.


4. Division:

   C++ integer division use hoti hai.

   Example:
       13 / 5 = 2

   LeetCode constraints ke according valid division assume ki ja sakti hai.


5. Har operator ke baad intermediate result
   stack me push karna zaroori hai.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏱️ COMPLEXITY
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Time:
    O(n)

Har token exactly once process hota hai.

Space:
    O(n)

Worst case me stack me O(n) operands ho sakte hain.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 PATTERN TO REMEMBER
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

"Number → PUSH

 Operator → POP 2 → OPERATE → PUSH RESULT"

Aur:

"First POP = Right
 Second POP = Left"


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔥 INTERVIEW TAKEAWAY
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Jab bhi question me:

• expression evaluation
• operands + operators
• nested calculation
• LIFO dependency
• previous values ko reverse order me use karna

dikhe → STACK consider karo.

RPN ka sabse important concept:

    Stack + Operand Order

Bas ye pattern clear hona chahiye.
