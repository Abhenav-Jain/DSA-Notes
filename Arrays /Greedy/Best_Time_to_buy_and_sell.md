# Best Time to Buy and Sell Stock — LeetCode 121

**Pattern:** Greedy + Single Pass  
**Difficulty:** Easy

## 1. Problem
Ek array `prices` diya hai, jahan `prices[i]` stock ka price hai on day `i`.

Ek din stock buy aur uske baad wale din sell karna hai, taaki maximum profit mile.

**Example:**
```cpp
Input:  [7, 1, 5, 3, 6, 4]
Output: 5
```
Buy at `1`, sell at `6` → Profit = `6 - 1 = 5`.

## 2. Approach — Track Minimum Buying Price

Do variables maintain karo:

- `best_buy_price`: Ab tak ka minimum stock price.
- `max_profit`: Ab tak ka maximum profit.

Har price par:
1. Agar current price minimum hai, toh `best_buy_price` update karo.
2. Current price par sell karne ka profit calculate karo.
3. `max_profit` ko update karo.

**Formula:**

`profit = current_price - best_buy_price`

## 3. C++ Code

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int max_profit = 0;
        int best_buy_price = prices[0];

        for (int i = 1; i < prices.size(); i++) {
            if (best_buy_price > prices[i]) {
                best_buy_price = prices[i];
            }

            max_profit = max(
                max_profit,
                prices[i] - best_buy_price
            );
        }

        return max_profit;
    }
};
```

## 4. Dry Run

`prices = [7, 1, 5, 3, 6, 4]`

| Price | Minimum buy price | Current profit | Maximum profit |
|---:|---:|---:|---:|
| 7 | 7 | — | 0 |
| 1 | 1 | 0 | 0 |
| 5 | 1 | 4 | 4 |
| 3 | 1 | 2 | 4 |
| 6 | 1 | 5 | 5 |
| 4 | 1 | 3 | 5 |

**Final Answer:** `5`

## 5. Important Edge Cases

- `[7, 6, 4, 3, 1]` → `0` (prices continuously decrease)
- `[1, 2]` → `1`
- `[5, 5, 5]` → `0`

Profit negative hone par answer `0` hi rahega, kyunki transaction na karna bhi valid hai.

## 6. Complexity

- **Time:** `O(n)` — single traversal.
- **Auxiliary Space:** `O(1)` — only two variables.

## 7. Interview Takeaways

- Brute force mein har buy-sell pair check karne par `O(n²)` time lagta hai.
- Greedy approach mein minimum buying price track karte hain.
- Har day ka maximum possible profit calculate karte hain.
- Buying day selling day se pehle hona chahiye.
- Agar maximum profit nahi milta, toh `0` return karte hain.

**Revision mantra:** Minimum price track karo → current profit calculate karo → maximum profit update karo.
