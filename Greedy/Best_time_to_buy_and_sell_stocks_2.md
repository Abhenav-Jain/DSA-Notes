# Best Time to Buy and Sell Stock II — LeetCode 122

**Pattern:** Greedy + Array  
**Difficulty:** Medium  
**Core Concepts:** Local Minima, Local Maxima, Greedy Profit Accumulation

---

## 1. Problem Understanding

We are given an array `prices`, where:

- `prices[i]` represents the stock price on day `i`.
- We can buy and sell stocks multiple times.
- We must sell the stock before buying again.
- We can hold at most one stock at a time.

**Goal:** Maximize the total profit.

### Important Conditions

1. Multiple transactions are allowed.
2. We cannot hold multiple stocks simultaneously.
3. We must sell before making another purchase.
4. We can buy and sell on the same day, but this does not create additional profit.

---

## 2. Greedy Intuition

The key observation is:

**Buy at a local minimum and sell at the end of the increasing sequence (local maximum).**

Consider:

```cpp
prices = [1, 3, 6, 2, 8]
```

Visualize the price movement:

```text
1 → 3 → 6
        ↓
        2 → 8
```

We have two increasing sequences:

- First: `1 → 3 → 6`
- Second: `2 → 8`

Our transactions:

| Transaction | Buy | Sell | Profit |
|---|---:|---:|---:|
| 1 | 1 | 6 | 5 |
| 2 | 2 | 8 | 6 |

Total profit:

\[
5+6=11
\]

**Output: 11**

### Why not sell at every intermediate increase?

Suppose:

```cpp
prices = [1, 3, 6]
```

Option A:
- Buy at 1, sell at 3: Profit = 2
- Buy at 3, sell at 6: Profit = 3
- Total = 5

Option B:
- Buy at 1, sell at 6: Profit = 5

Both give the same profit.

Therefore, holding through a continuously increasing sequence is equally profitable.

---

## 3. Approach 1 — Local Minima and Local Maxima

This is the approach derived independently.

### Step 1: Initialize variables

```cpp
int buying_price = prices[0];
int max_profit = 0;
```

- `buying_price` stores the current buying price.
- `max_profit` stores accumulated profit.

### Step 2: Find a better buying price

```cpp
if(prices[i] < buying_price){
    buying_price = prices[i];
}
```

If the current price is lower, update the buying price.

### Step 3: Identify an increasing sequence

```cpp
else if(prices[i] > buying_price){
    while(i+1 < n && prices[i+1] > prices[i]){
        i++;
    }
```

Continue moving forward while prices keep increasing.

The loop stops when:
- The next price is not greater than the current price.
- Or we reach the last day.

At this point, `prices[i]` is the end of the increasing sequence.

### Step 4: Calculate profit

```cpp
max_profit += (prices[i] - buying_price);
```

Sell at the local maximum and add the profit.

### Step 5: Prepare for the next transaction

```cpp
if(i+1 < n){
    buying_price = prices[i+1];
}
```

The next day becomes the next candidate buying price.

---

## 4. C++ Implementation — Independently Derived Solution

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {

        int buying_price = prices[0];
        int n = prices.size();
        int max_profit = 0;

        for(int i = 0; i < n; i++){

            if(prices[i] < buying_price){
                buying_price = prices[i];
            }

            else if(prices[i] > buying_price){

                // Reach the end of the increasing sequence
                while(i + 1 < n && prices[i + 1] > prices[i]){
                    i++;
                }

                // Sell at the local maximum
                max_profit += (prices[i] - buying_price);

                // Prepare for the next transaction
                if(i + 1 < n){
                    buying_price = prices[i + 1];
                }
            }
        }

        return max_profit;
    }
};
```

### Important Observation

The `while` loop does not make the solution O(n²).

The index `i` only moves forward. Every array element is processed at most a constant number of times.

---

## 5. Dry Run

Consider:

```cpp
prices = [7, 1, 5, 3, 6, 4]
```

Initially:

```cpp
buying_price = 7
max_profit = 0
```

| Day | Price | Decision | Buying Price | Total Profit |
|---|---:|---|---:|---:|
| 0 | 7 | Initial value | 7 | 0 |
| 1 | 1 | Update buying price | 1 | 0 |
| 2 | 5 | Sell at local peak | 1 | 4 |
| 3 | 3 | New buying price | 3 | 4 |
| 4 | 6 | Sell at local peak | 3 | 7 |
| 5 | 4 | No profitable transaction | 3 | 7 |

**Output: 7**

---

## 6. Approach 2 — Sum of Positive Differences

There is another elegant greedy approach.

Instead of explicitly finding local minima and maxima, we collect every positive increase between consecutive days.

### Mathematical Observation

For an increasing sequence:

\[
a_1 < a_2 < a_3 < a_4
\]

Profit from buying at the beginning and selling at the end:

\[
a_4-a_1
\]

This is equivalent to:

\[
(a_2-a_1)+(a_3-a_2)+(a_4-a_3)
\]

All intermediate terms cancel.

Therefore:

**Total profit = Sum of all positive consecutive price differences.**

### C++ Code

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {

        int profit = 0;

        for(int i = 1; i < prices.size(); i++){

            if(prices[i] > prices[i-1]){
                profit += prices[i] - prices[i-1];
            }
        }

        return profit;
    }
};
```

### Dry Run

```cpp
prices = [1, 5, 3, 8, 4, 10]
```

| Consecutive Prices | Difference | Contribution |
|---|---:|---:|
| 1 → 5 | +4 | 4 |
| 5 → 3 | -2 | 0 |
| 3 → 8 | +5 | 5 |
| 8 → 4 | -4 | 0 |
| 4 → 10 | +6 | 6 |

\[
TotalProfit=4+5+6=15
\]

**Output: 15**

---

## 7. Why Is the Greedy Approach Correct?

We can understand the correctness through two observations.

### Observation 1: A transaction should not cross a decreasing price movement

Suppose we buy at price 1, prices rise to 6, fall to 2, and then rise to 8.

Selling at 6 and buying again at 2 gives:

\[
(6-1)+(8-2)=11
\]

Holding from 1 until 8 gives:

\[
8-1=7
\]

Selling before the decline allows us to avoid losing the value of that downward movement.

### Observation 2: Consecutive upward movements can be combined

For an increasing sequence:

\[
(a_2-a_1)+(a_3-a_2)=a_3-a_1
\]

Thus, selling at every intermediate increase or holding until the end of the increasing sequence produces the same total profit.

Therefore, we can safely:
- Buy at a local minimum.
- Sell at the next local maximum.
- Repeat for subsequent increasing sequences.

---

## 8. Complexity Analysis

Let `n` be the number of days.

| Approach | Time | Auxiliary Space |
|---|---|---|
| Local Minima + Maxima | O(n) | O(1) |
| Positive Differences | O(n) | O(1) |

Both approaches are optimal in asymptotic complexity.

We must inspect every price at least once, so O(n) is the best possible time complexity.

---

## 9. Important Edge Cases

| Input | Output | Reason |
|---|---:|---|
| `[1,2,3,4,5]` | 4 | Continuously increasing |
| `[7,6,4,3,1]` | 0 | Continuously decreasing |
| `[1,5,3,8,4,10]` | 15 | Multiple increasing sequences |
| `[5]` | 0 | Only one day |
| `[3,3,3,3]` | 0 | No price increase |
| `[1,2,1,2]` | 2 | Two separate upward movements |
| `[2,1,2,1,2]` | 2 | Multiple local minima and maxima |

---

## 10. Common Mistakes

1. **Assuming only one transaction is allowed:** This is Stock II, so multiple transactions are allowed.
2. **Selling during an increasing sequence unnecessarily:** It does not increase profit compared with holding until its end.
3. **Holding through a decreasing movement:** This can reduce total achievable profit.
4. **Buying again before selling:** Only one stock can be held at a time.
5. **Confusing Stock I and Stock II:** Stock I allows one transaction; Stock II allows unlimited transactions.
6. **Thinking nested loops automatically mean O(n²):** The local peak traversal advances the same index, so total time remains O(n).

---

## 11. Pattern Recognition

Think of this greedy approach when:

- Multiple transactions are allowed.
- You can perform only one transaction at a time.
- The objective is to maximize accumulated profit.
- Consecutive positive movements can be independently collected.
- You need to identify profitable increasing segments.

### Two Important Greedy Ideas

**Idea 1: Local Minima and Local Maxima**

Buy at the valley and sell at the end of the increasing sequence.

**Idea 2: Positive Difference Accumulation**

Collect every positive difference between consecutive elements.

Both represent the same optimal profit for this problem.

---

## 12. Interview Follow-up Questions

**Q1. Why can we sell at the end of an increasing sequence?**

Because the sum of consecutive positive differences equals the difference between the final and initial prices.

**Q2. Is it necessary to sell at every local peak?**

No. We sell at the end of each increasing sequence. Intermediate upward movements can be combined into one transaction.

**Q3. Why should we sell before a decreasing sequence?**

Because selling before a decline and buying again at the lower price can increase total profit.

**Q4. Can we use Dynamic Programming?**

Yes. A DP formulation can track the maximum profit when holding or not holding a stock. However, the unlimited-transactions version has a simpler greedy solution.

**Q5. What is the difference between Stock I and Stock II?**

- Stock I: At most one transaction.
- Stock II: Unlimited transactions, but only one stock can be held at a time.

---

## 13. Quick Revision Cheat Sheet

| Concept | Remember |
|---|---|
| Pattern | Greedy |
| Core idea | Buy at local minimum, sell at local maximum |
| Transaction | Multiple allowed |
| Holding restriction | At most one stock |
| Alternative approach | Sum positive differences |
| Time | O(n) |
| Auxiliary Space | O(1) |
| Important property | Consecutive increases can be combined |

### One-Line Takeaway

**In an unlimited-transactions stock problem, capture every profitable upward movement while avoiding downward movements.**
