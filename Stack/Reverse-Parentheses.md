# Reverse Parentheses — LeetCode 1190

## 🔴 Pattern
**Stack + String Manipulation**

## 💡 Core Idea

Parentheses ke andar ke characters ko reverse karna hai.

Important observation:

> Jab bhi `(` mile, hume bas ye remember karna hai ki us point par `ans` ki length kitni thi.

Ye index batayega ki is bracket ke andar ka substring **kahan se start hua tha**.

### Example

```text
(u(love)i)

At first '(':
ans = ""
stack = [0]

After 'u':
ans = "u"

At second '(':
ans = "u"
stack = [0, 1]

After "love":
ans = "ulove"

At first ')':
start = 1

Reverse:
ans = "uevol"

pop 1

At second ')':
start = 0

Reverse:
ans = "loveu"
