# LeetCode 901 — Online Stock Span

**Pattern:** Monotonic Stack + Previous Greater Element (PGE)  
**Difficulty:** Medium  
**Topic:** Stack, Design, Array  
**Approach:** Decreasing Monotonic Stack

---

## 1. Problem Statement

Design a class `StockSpanner` that collects daily stock prices and returns the **span of the stock's price for the current day**.

The stock span of today's price is the maximum number of consecutive days ending today for which the stock price was less than or equal to today's price.

Today's day is always included.

### Example

**Input:**

```cpp
StockSpanner* obj = new StockSpanner();

obj->next(100);
obj->next(80);
obj->next(60);
obj->next(70);
obj->next(60);
obj->next(75);
obj->next(85);
```

**Output:**

```cpp
[1, 1, 1, 2, 1, 4, 6]
```

### Understanding the output

| Day | Price | Consecutive previous days with price <= current | Span |
|---|---:|---|---:|
| 0 | 100 | 100 | 1 |
| 1 | 80 | 80 | 1 |
| 2 | 60 | 60 | 1 |
| 3 | 70 | 60, 70 | 2 |
| 4 | 60 | 60 | 1 |
| 5 | 75 | 60, 70, 60, 75 | 4 |
| 6 | 85 | 80, 60, 70, 60, 75, 85 | 6 |

**Important:** We count consecutive days, not all previous days having a smaller price.

---

## 2. Core Observation — Previous Greater Element (PGE)

The key to solving this problem is finding the **Previous Greater Element**.

For every current price, we need to find the nearest previous price that is strictly greater than the current price.

Why?

Because the first previous greater price acts as a boundary. We cannot extend the stock span beyond it.

### Formula

Let:

- `i` = current day/index
- `PGE[i]` = index of the nearest previous strictly greater element

Then:

If PGE exists:

$$Span[i] = i - PGE[i]$$

If PGE does not exist:

$$Span[i] = i + 1$$

Why `i + 1`?

Because if there is no previous greater element, every previous day from day 0 through the current day belongs to the span.

---

## 3. Why Monotonic Stack?

A brute-force solution would move backwards from every current day until it encounters a price greater than today's price.

This can take:

$$O(n^2)$$

We can optimize it using a monotonic stack.

### Stack property

We maintain a **monotonically decreasing stack of prices**, represented by their indices.

The prices corresponding to the indices in the stack remain in strictly decreasing order from bottom to top.

For every new price:

1. Remove all previous prices that are smaller than or equal to the current price.
2. The remaining stack top is the Previous Greater Element.
3. Calculate the span.
4. Push the current index.

### Why pop smaller or equal elements?

Consider:

```text
Prices = [100, 80, 60, 70]
```

When 70 arrives, 60 gets removed because 70 is greater than 60.

The 60 can never be the nearest previous greater element for any future price as long as 70 is also available between that future price and 60.

Therefore, removing it is safe.

**Condition:**

```cpp
while(!st.empty() && prices[idx] >= prices[st.top()]) {
    st.pop();
}
```

Notice `>=` is important. Equal prices must also be removed because the problem allows previous prices equal to today's price to be included in the span.

---

## 4. Optimized C++ Solution — Monotonic Stack

```cpp
class StockSpanner {
public:
    vector<int> prices;
    stack<int> st;

    StockSpanner() {

    }

    int next(int price) {

        // 1. Store the current price
        prices.push_back(price);

        // 2. Get current index
        int idx = prices.size() - 1;

        // 3. Remove smaller or equal previous prices
        while(!st.empty() && prices[idx] >= prices[st.top()]) {
            st.pop();
        }

        // 4. Calculate stock span
        int span;

        if(st.empty()) {
            span = idx + 1;
        }
        else {
            span = idx - st.top();
        }

        // 5. Push current index
        st.push(idx);

        return span;
    }
};
```

---

## 5. Line-by-Line Explanation

### Step 1 — Store current price

```cpp
prices.push_back(price);
```

Every call to `next(price)` provides a new day's stock price.

We must retain previous prices because future calls may need them.

### Step 2 — Calculate current index

```cpp
int idx = prices.size() - 1;
```

Since indexing starts from zero, the last inserted price is at `size() - 1`.

### Step 3 — Find Previous Greater Element

```cpp
while(!st.empty() && prices[idx] >= prices[st.top()]) {
    st.pop();
}
```

Remove all previous prices that are smaller than or equal to the current price.

After this loop:

- If stack is empty, no previous greater element exists.
- Otherwise, `st.top()` contains the index of the nearest previous greater element.

### Step 4 — Calculate span

```cpp
if(st.empty()) {
    span = idx + 1;
}
else {
    span = idx - st.top();
}
```

If no previous greater price exists, the span covers all days so far.

Otherwise, the span is the distance between the current index and the PGE index.

### Step 5 — Push current index

```cpp
st.push(idx);
```

The current day can act as a previous greater element for future days.

### Step 6 — Return answer

```cpp
return span;
```

---

## 6. Complete Dry Run

Input:

```text
[100, 80, 60, 70, 60, 75, 85]
```

Stack stores indices, not prices.

| Day | Price | Popped indices | PGE index | Span | Stack after processing |
|---:|---:|---|---:|---:|---|
| 0 | 100 | None | None | 1 | [0] |
| 1 | 80 | None | 0 | 1 | [0, 1] |
| 2 | 60 | None | 1 | 1 | [0, 1, 2] |
| 3 | 70 | 2 | 1 | 2 | [0, 1, 3] |
| 4 | 60 | None | 3 | 1 | [0, 1, 3, 4] |
| 5 | 75 | 4, 3 | 1 | 4 | [0, 1, 5] |
| 6 | 85 | 5, 1 | 0 | 6 | [0, 6] |

Final output:

```text
[1, 1, 1, 2, 1, 4, 6]
```

---

## 7. Complexity Analysis

Let `n` be the total number of calls to `next()`.

### Time Complexity

Each price is pushed into the stack exactly once.

Each price can also be popped from the stack at most once.

Therefore, across all `n` calls:

- Total pushes = n
- Total pops <= n

Total work:

$$O(n)$$

Amortized time per `next()` call:

$$O(1)$$

**Important interview distinction:** A single `next()` call can pop many elements and take O(n), but the amortized cost per call is O(1).

### Space Complexity

- `prices` vector: O(n)
- `stack`: O(n)

Total auxiliary storage:

$$O(n)$$

---

## 8. Why Do We Store Indices Instead of Only Prices?

We need the distance between the current day and the previous greater day.

Formula:

```cpp
span = idx - st.top();
```

If we store only prices, we lose the day/index information required to calculate the span.

Therefore, we store indices and access their corresponding prices using:

```cpp
prices[st.top()]
```

---

## 9. Important Edge Cases

### Case 1 — Strictly Increasing Prices

```text
Input:  [10, 20, 30, 40]
Output: [1, 2, 3, 4]
```

Every new price removes all previous stack elements.

No previous greater element exists.

### Case 2 — Strictly Decreasing Prices

```text
Input:  [40, 30, 20, 10]
Output: [1, 1, 1, 1]
```

No elements are popped because the previous price is always greater.

### Case 3 — Equal Prices

```text
Input:  [10, 10, 10]
Output: [1, 2, 3]
```

Equal prices are included in the span.

This is why we use:

```cpp
prices[idx] >= prices[st.top()]
```

### Case 4 — Single Price

```text
Input: [50]
Output: [1]
```

The stack is initially empty, so span = index + 1 = 1.

---

## 10. Common Mistakes

| Mistake | Why it is wrong |
|---|---|
| Using `>` instead of `>=` | Equal prices won't be included correctly |
| Storing only prices | Cannot calculate index distance |
| Creating a fresh stack inside `next()` | Loses information from previous calls |
| Recalculating PGE for the entire array on every call | Unnecessary repeated work |
| Returning `idx - st.top() + 1` | Off-by-one error; the greater element itself is excluded |
| Forgetting to push current index | Future calls cannot use the current day as a boundary |

---

## 11. Connection With Other Monotonic Stack Problems

| Problem | Pattern | What we find |
|---|---|---|
| LC 496 — Next Greater Element I | Monotonic Stack | NGE |
| LC 739 — Daily Temperatures | Monotonic Stack | NGE index |
| LC 503 — Next Greater Element II | Circular Monotonic Stack | Circular NGE |
| LC 1475 — Final Prices | Monotonic Stack | NSE |
| LC 901 — Online Stock Span | Monotonic Stack | PGE |

### Pattern Recognition

Whenever a problem asks:

- Nearest greater element on the left → PGE
- Nearest smaller element on the left → PSE
- Nearest greater element on the right → NGE
- Nearest smaller element on the right → NSE
- Consecutive previous elements satisfying a condition → Look for a boundary element and consider a monotonic stack.

---

## 12. Interview Follow-Up Questions

**Q1. Why is the stack maintained across multiple `next()` calls?**

Because the class processes an online stream of stock prices. Each new call depends on prices received in previous calls.

**Q2. Why is the stack decreasing?**

We remove all prices smaller than or equal to the current price. The remaining prices are strictly decreasing from bottom to top.

**Q3. Can one call take O(n) time?**

Yes. A new price may pop many previous indices. But every index is pushed and popped at most once, so total processing is O(n).

**Q4. What if all prices are equal?**

Every new price pops the previous one, and the stack contains only the latest index. The returned spans are 1, 2, 3, and so on.

**Q5. Why does an empty stack mean span = index + 1?**

Because no previous day has a price strictly greater than the current price. Therefore, all days from the beginning are included.

---

## 13. Final Revision Cheat Sheet

```text
Problem: LC 901 — Online Stock Span

Pattern:
Monotonic Stack + PGE

Stack stores:
Indices

Stack order:
Strictly decreasing prices (bottom to top)

Pop condition:
prices[idx] >= prices[st.top()]

PGE:
st.top() after popping

Span:
If stack empty -> idx + 1
Otherwise -> idx - st.top()

Push:
Current index

Time:
O(n) total, O(1) amortized per call

Space:
O(n)
```

### Core takeaway

**Stock Span = Distance from Current Index to Previous Greater Element.**

The Previous Greater Element acts as the boundary that determines how far backwards the current stock price can extend its span.

Master this connection: PGE + Index Difference = Stock Span.
