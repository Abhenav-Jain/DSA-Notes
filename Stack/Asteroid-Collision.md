# Asteroid Collision

**LeetCode:** 735 — Asteroid Collision
**Concept:** Stack + Simulation
**Difficulty:** Medium

---

# 1. Problem

We are given an array of asteroids.

* Positive value → asteroid is moving **right**
* Negative value → asteroid is moving **left**
* Absolute value → size of asteroid

When two asteroids collide:

* Smaller asteroid explodes.
* If both have the same size, both explode.
* Larger asteroid survives.

We need to return the state of the asteroids after all possible collisions.

### Example

```text
Input:
[5, 10, -5]

Output:
[5, 10]
```

Why?

```text
10 →      ← -5

10 is larger than 5
so -5 explodes.
```

---

# 2. Core Concept

## Stack + Simulation

The key observation is:

> **Only the most recent surviving asteroid can collide with the current asteroid.**

Therefore, a stack is perfect for maintaining the asteroids that are currently alive.

We process asteroids from left to right.

```text
Current asteroid
      ↓
Can it collide with stack.top()?
      ↓
If yes → resolve collision
      ↓
If no → push it
```

---

# 3. When Can a Collision Actually Happen?

This is the most important observation.

Suppose:

```text
A →        ← B
```

A collision happens.

In terms of signs:

```text
A > 0
B < 0
```

Therefore, collision is possible only when:

```cpp
st.top() > 0 && el < 0
```

This is exactly why our loop condition is:

```cpp
while(!st.empty() && st.top() > 0 && el < 0)
```

---

# 4. When Is There NO Collision?

### Case 1 — Both moving right

```text
→ →
```

They move in the same direction.

No collision.

---

### Case 2 — Both moving left

```text
← ←
```

No collision.

---

### Case 3 — Left-moving asteroid comes first

```text
← →
```

They move away from each other.

No collision.

---

### Only collision case

```text
→ ←
```

Therefore:

```text
stack top > 0
current asteroid < 0
```

---

# 5. Why Stack?

Suppose:

```text
asteroids = [10, 2, -5]
```

Process `10`:

```text
stack = [10]
```

Process `2`:

```text
stack = [10, 2]
```

Now `-5` comes.

The only asteroid that `-5` can immediately collide with is:

```text
2
```

which is the stack top.

After destroying `2`, it may then collide with:

```text
10
```

which becomes the new stack top.

So the stack naturally handles the chain reaction.

```text
-5
 ↓
collides with 2
 ↓
2 destroyed
 ↓
collides with 10
 ↓
10 survives
```

---

# 6. Collision Cases

Suppose:

```text
st.top() = 10
el = -5
```

We compare:

```text
abs(el) = 5
st.top() = 10
```

---

## Case 1 — Current asteroid is larger

```cpp
if(abs(el) > st.top())
```

Example:

```text
10 →    ← 15
```

`15` survives.

So:

```cpp
st.pop();
```

The current asteroid is still alive, so the `while` loop continues.

This is important because the current asteroid may collide with another asteroid behind the destroyed one.

---

## Case 2 — Both are equal

```cpp
else if(abs(el) == st.top())
```

Example:

```text
10 →    ← 10
```

Both explode.

Therefore:

```cpp
st.pop();
el = 0;
break;
```

Why `el = 0`?

Because the current asteroid has also been destroyed.

So we should not push it into the stack.

---

## Case 3 — Stack asteroid is larger

```cpp
else
```

Example:

```text
10 →    ← 5
```

`10` survives.

Current asteroid `-5` is destroyed.

Therefore:

```cpp
el = 0;
break;
```

The stack top remains unchanged.

---

# 7. Why Do We Need `el = 0`?

This is a clever part of the implementation.

After collision, we need to know:

> Is the current asteroid still alive?

We use:

```cpp
el != 0
```

to represent that.

### If current asteroid survives

```text
el = -15
```

Then:

```cpp
if(el != 0)
    st.push(el);
```

### If current asteroid gets destroyed

```text
el = 0
```

Then:

```cpp
if(el != 0)
    st.push(el);
```

does nothing.

So `el = 0` acts like:

> **Current asteroid has been destroyed.**

---

# 8. Complete Algorithm

For every asteroid:

```text
1. Put current asteroid in el.

2. While:
      stack is not empty
      AND stack top is moving right
      AND current asteroid is moving left

3. Resolve collision.

4. If stack asteroid is smaller:
      pop it
      current asteroid survives
      continue checking

5. If equal:
      pop stack
      destroy current asteroid
      stop

6. If stack asteroid is larger:
      destroy current asteroid
      stop

7. If current asteroid survives:
      push it into stack.
```

---

# 9. Dry Run

Consider:

```text
asteroids = [10, 2, -5]
```

### Step 1 — `10`

Stack empty.

```text
stack = [10]
```

---

### Step 2 — `2`

Current:

```text
2 > 0
```

Both are moving right, so no collision.

```text
stack = [10, 2]
```

---

### Step 3 — `-5`

Current:

```text
-5 < 0
```

Stack top:

```text
2 > 0
```

Collision possible.

Compare:

```text
5 > 2
```

Therefore `2` explodes.

```cpp
st.pop();
```

Now:

```text
stack = [10]
el = -5
```

The `while` loop continues.

---

### Second collision

Now:

```text
10 →    ← 5
```

Compare:

```text
5 < 10
```

So current asteroid `-5` explodes.

```cpp
el = 0;
break;
```

Final stack:

```text
[10]
```

Answer:

```text
[10]
```

---

# 10. Another Important Dry Run

Consider:

```text
[5, 10, -5]
```

### `5`

```text
stack = [5]
```

### `10`

No collision:

```text
stack = [5, 10]
```

### `-5`

Collision:

```text
10 →    ← 5
```

Since:

```text
10 > 5
```

`-5` is destroyed.

```text
stack = [5,10]
```

Final answer:

```text
[5,10]
```

---

# 11. Equal Collision

Consider:

```text
[8, -8]
```

Stack:

```text
[8]
```

Current:

```text
-8
```

Collision:

```text
8 →    ← 8
```

Equal sizes.

Both explode:

```cpp
st.pop();
el = 0;
break;
```

Final:

```text
[]
```

---

# 12. Recursion Is NOT Needed Here

Unlike our previous questions:

```text
Letter Case Permutation
Letter Combinations
Subsets
```

this is not a recursion/backtracking problem.

Why?

We aren't exploring multiple possibilities.

At every collision, the result is deterministic.

Therefore:

```text
Stack + Simulation
```

is the natural approach.

---

# 13. Code

```cpp
class Solution {
public:
    vector<int> asteroidCollision(vector<int>& asteroids) {

        stack<int> st;

        for(int el : asteroids) {

            while(!st.empty() &&
                  st.top() > 0 &&
                  el < 0) {

                if(abs(el) > st.top()) {
                    // Stack asteroid is destroyed
                    st.pop();
                }
                else if(abs(el) == st.top()) {
                    // Both are destroyed
                    st.pop();
                    el = 0;
                    break;
                }
                else {
                    // Current asteroid is destroyed
                    el = 0;
                    break;
                }
            }

            // Current asteroid survived
            if(el != 0) {
                st.push(el);
            }
        }

        vector<int> ans(st.size());

        for(int i = ans.size() - 1; i >= 0; i--) {
            ans[i] = st.top();
            st.pop();
        }

        return ans;
    }
};
```

---

# 14. Why `while`, Not `if`?

This is one of the most important parts of the problem.

Suppose:

```text
[10, 2, -20]
```

`-20` first collides with `2`.

```text
2 →    ← 20
```

`2` dies.

But `-20` is still alive.

Now it must collide with `10`.

```text
10 →    ← 20
```

`10` also dies.

Therefore one asteroid can cause **multiple consecutive collisions**.

That's why we need:

```cpp
while(...)
```

instead of:

```cpp
if(...)
```

---

# 15. Why Is the Time Complexity O(n)?

At first glance, the nested `while` may look like:

```text
O(n²)
```

But it is actually:

```text
O(n)
```

amortized.

Why?

Every asteroid can be:

* pushed into the stack at most once
* popped from the stack at most once

So even though one asteroid may cause multiple collisions, the total number of pops across the entire algorithm is at most `n`.

Therefore:

```text
Time = O(n)
```

---

# 16. Complexity

### Time Complexity

```text
O(n)
```

Each asteroid is pushed at most once and popped at most once.

### Auxiliary Space

```text
O(n)
```

In the worst case, no asteroid collides and all `n` asteroids remain in the stack.

### Output Space

```text
O(n)
```

for the resulting vector.

---

# 17. Pattern Recognition

This question teaches an important **Stack Simulation** pattern.

When you see:

> Elements arrive one by one, and the current element may interact with the most recent surviving element.

Think:

```text
Stack
```

Especially when:

* Previous elements can be destroyed/removed.
* The latest surviving element matters.
* Removal can trigger another interaction.
* We need to simulate a chain reaction.

The general structure becomes:

```cpp
for(each element) {

    while(stack is not empty && collision_possible) {

        resolve collision;

    }

    if(current survives)
        push(current);
}
```

---

# 18. Connection With Other Stack Problems

This question is different from simple stack problems because the stack represents:

> **Currently surviving elements.**

This idea is useful in problems involving:

```text
Collision
Removal
Elimination
Adjacent interactions
Chain reactions
```

---

# 19. Mistakes / Important Learning

### 🔑 Mistake 1 — Checking collision for every pair

We don't need to compare the current asteroid with every previous asteroid.

Only:

```cpp
st.top()
```

can interact with the current asteroid.

If it gets destroyed:

```cpp
st.pop();
```

then the next surviving asteroid becomes the new candidate.

---

### 🔑 Mistake 2 — Using `if` instead of `while`

One asteroid can destroy multiple previous asteroids.

Therefore:

```cpp
while(...)
```

is necessary.

---

### 🔑 Mistake 3 — Forgetting that the current asteroid may survive

After:

```cpp
st.pop();
```

the current asteroid may still be alive.

So we continue the loop.

Only when it is destroyed do we set:

```cpp
el = 0;
```

---

# 20. Key Takeaways

### 1.

Collision happens only when:

```text
stack top > 0
current < 0
```

### 2.

Use a stack to maintain:

```text
currently surviving asteroids
```

### 3.

Use `while` because one asteroid can cause multiple collisions.

### 4.

Use `el = 0` to indicate:

```text
current asteroid destroyed
```

### 5.

The nested `while` does **not** make the algorithm O(n²).

Each asteroid is pushed/popped at most once.

Therefore:

```text
O(n)
```

amortized time.

---

# ⭐ One-Line Revision

> **Maintain surviving asteroids in a stack; whenever the stack top moves right and the current asteroid moves left, repeatedly resolve the collision until either the current asteroid dies or no collision is possible.**

---

# 🔗 Similar Stack Patterns

Good questions to connect with this pattern:

1. **Remove All Adjacent Duplicates In String** — LeetCode 1047
2. **Remove K Digits** — LeetCode 402
3. **Daily Temperatures** — LeetCode 739
4. **Next Greater Element I** — LeetCode 496
5. **Online Stock Span** — LeetCode 901
6. **Valid Parentheses** — LeetCode 20

The exact logic differs, but the common idea is:

> **Use the stack to maintain the useful/relevant previous elements instead of repeatedly scanning the entire history.**
