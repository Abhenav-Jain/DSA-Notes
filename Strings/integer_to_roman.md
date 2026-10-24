# LeetCode 12 — Integer to Roman

**Pattern:** Greedy + Arrays + String Manipulation  
**Difficulty:** Medium  
**Core Concept:** Largest Valid Denomination First

---

## 1. Problem Understanding

Given an integer `num`, convert it into its Roman numeral representation.

Roman numerals use the following symbols:

| Symbol | Value |
|---|---:|
| I | 1 |
| V | 5 |
| X | 10 |
| L | 50 |
| C | 100 |
| D | 500 |
| M | 1000 |

### Important Subtractive Combinations

Roman numerals use six special combinations:

| Combination | Value | Meaning |
|---|---:|---|
| IV | 4 | 5 - 1 |
| IX | 9 | 10 - 1 |
| XL | 40 | 50 - 10 |
| XC | 90 | 100 - 10 |
| CD | 400 | 500 - 100 |
| CM | 900 | 1000 - 100 |

**Important:** These combinations must be treated as individual denominations.

For example:

- 4 → IV, not IIII
- 9 → IX, not VIIII
- 40 → XL, not XXXX
- 90 → XC, not LXXXX

---

## 2. Greedy Intuition

The greedy strategy is:

**Always select the largest Roman numeral value that is less than or equal to the remaining number.**

Why does this work?

Roman numeral denominations are arranged in descending order. By selecting the largest valid denomination, we construct the canonical Roman representation without needing to reconsider earlier choices.

Example:

`num = 58`

- 50 → L
- 5 → V
- 1 → I
- 1 → I
- 1 → I

Answer: `LVIII`

We repeatedly subtract the selected denomination until the remaining number becomes zero.

---

## 3. Approach 1 — My Original Approach (Greedy + Helper Function)

### Intuition

I divided the problem into two parts:

1. Find the largest valid denomination using a helper function.
2. Calculate how many times that denomination can be used.

The helper function `valid_divisor()` returns the index of the first denomination that is less than or equal to `num`.

### Code

```cpp
class Solution {
public:
    int valid_divisor(vector<int>& nums, int n) {
        for(int i = 0; i < nums.size(); i++) {
            if(nums[i] <= n) {
                return i;
            }
        }
        return -1;
    }

    string intToRoman(int num) {
        vector<int> values = {
            1000, 900, 500, 400, 100, 90, 50,
            40, 10, 9, 5, 4, 1
        };

        vector<string> symbols = {
            "M", "CM", "D", "CD", "C", "XC", "L",
            "XL", "X", "IX", "V", "IV", "I"
        };

        string result = "";

        while(num > 0) {
            int mark = valid_divisor(values, num);

            int divisor = values[mark];
            int times = num / divisor;
            int rem = num % divisor;

            while(times > 0) {
                result.append(symbols[mark]);
                times--;
            }

            num = rem;
        }

        return result;
    }
};
```

### Step-by-Step Explanation

**Step 1: Find the largest valid denomination**

```cpp
int mark = valid_divisor(values, num);
```

The helper function searches the descending array and returns the first valid index.

**Step 2: Get the denomination**

```cpp
int divisor = values[mark];
```

**Step 3: Calculate its frequency**

```cpp
int times = num / divisor;
```

This tells us how many times the selected Roman symbol can be used.

**Step 4: Calculate the remainder**

```cpp
int rem = num % divisor;
```

This gives the remaining number after using the current denomination as many times as possible.

**Step 5: Append the Roman symbol**

```cpp
while(times > 0) {
    result.append(symbols[mark]);
    times--;
}
```

**Step 6: Process the remaining number**

```cpp
num = rem;
```

Repeat until `num` becomes zero.

### Dry Run — 1994

| Iteration | Remaining num | Divisor | Symbol | Times | Remainder |
|---|---:|---:|---|---:|---:|
| 1 | 1994 | 1000 | M | 1 | 994 |
| 2 | 994 | 900 | CM | 1 | 94 |
| 3 | 94 | 90 | XC | 1 | 4 |
| 4 | 4 | 4 | IV | 1 | 0 |

Final result:

`M + CM + XC + IV = MCMXCIV`

### Complexity Analysis

Let `L` be the length of the resulting Roman numeral.

- Time Complexity: O(L), because there are only 13 fixed denominations and the total number of appended symbols is L.
- Auxiliary Space: O(1), excluding the output string. The two denomination arrays have fixed size.

### Observation

My approach explicitly separates:

- Searching for the valid denomination.
- Calculating its frequency.
- Appending the symbol.
- Updating the remaining number.

This makes the greedy decision easy to understand.

---

## 4. Approach 2 — Improved One-Pass Greedy

### Intuition

We do not actually need a helper function.

Instead of repeatedly searching for the largest valid denomination, we can directly traverse the denomination array from largest to smallest.

For every denomination:

- While `num >= values[i]`, append its corresponding symbol.
- Subtract that denomination from `num`.
- Move to the next denomination when it can no longer be used.

### Code

```cpp
class Solution {
public:
    string intToRoman(int num) {
        vector<int> values = {
            1000, 900, 500, 400, 100, 90, 50,
            40, 10, 9, 5, 4, 1
        };

        vector<string> symbols = {
            "M", "CM", "D", "CD", "C", "XC", "L",
            "XL", "X", "IX", "V", "IV", "I"
        };

        string result = "";

        for(int i = 0; i < values.size(); i++) {
            while(num >= values[i]) {
                result += symbols[i];
                num -= values[i];
            }
        }

        return result;
    }
};
```

### Step-by-Step Explanation

**Step 1: Traverse denominations in descending order**

```cpp
for(int i = 0; i < values.size(); i++)
```

We start with 1000 and gradually move towards 1.

**Step 2: Check whether the current denomination is usable**

```cpp
while(num >= values[i])
```

If the remaining number is greater than or equal to the current denomination, we can use it.

**Step 3: Append its corresponding symbol**

```cpp
result += symbols[i];
```

**Step 4: Subtract its value**

```cpp
num -= values[i];
```

Continue until the current denomination cannot be used anymore.

### Dry Run — 1994

| Denomination | Remaining number | Action |
|---:|---:|---|
| 1000 | 1994 → 994 | Append M |
| 900 | 994 → 94 | Append CM |
| 500 | 94 | Skip |
| 400 | 94 | Skip |
| 100 | 94 | Skip |
| 90 | 94 → 4 | Append XC |
| 50 to 5 | 4 | Skip |
| 4 | 4 → 0 | Append IV |

Output: `MCMXCIV`

### Complexity Analysis

- Time Complexity: O(L), because the number of denominations is fixed at 13 and every output symbol is appended once.
- Auxiliary Space: O(1), excluding the output string.

---

## 5. Comparison — Both Approaches

| Parameter | Original Approach | Improved Approach |
|---|---|---|
| Strategy | Greedy | Greedy |
| Helper function | Required | Not required |
| Searching | Repeated search | Single traversal |
| Frequency calculation | Division and modulo | Repeated subtraction |
| Code structure | More explicit | More concise |
| Time complexity | O(L) | O(L) |
| Auxiliary space | O(1) | O(1) |

**Important interview point:** Both approaches have the same asymptotic complexity because the number of denominations is fixed. The improved approach is cleaner, but it does not change the Big-O complexity.

---

## 6. Why Do We Need Subtractive Combinations?

Consider the number `49`.

If we only used the basic Roman symbols:

- 40 would become XXXX.
- 9 would become VIIII.

But standard Roman representation requires:

- 40 → XL
- 9 → IX

Therefore, the denominations array must include:

```cpp
{1000, 900, 500, 400, 100, 90, 50,
 40, 10, 9, 5, 4, 1}
```

The descending order is critical because the greedy algorithm always chooses the largest valid denomination.

---

## 7. Common Mistakes and Interview Traps

1. **Forgetting subtractive combinations:** Include 900, 400, 90, 40, 9 and 4.
2. **Incorrect ordering:** Keep the denomination array in descending order.
3. **Mismatched arrays:** `values[i]` and `symbols[i]` must represent the same Roman numeral.
4. **Using `>` instead of `>=`:** A denomination equal to the remaining number must also be selected.
5. **Incorrect remainder handling:** In the original approach, update `num` to `rem`.
6. **Passing vectors by value:** Prefer `const vector<int>&` when a helper only reads a vector.

---

## 8. Interview Questions Based on This Problem

**Q1. Which algorithmic paradigm is used?**

Greedy. We repeatedly select the largest valid denomination.

**Q2. Why do we sort or arrange values in descending order?**

So the first valid denomination is always the largest denomination that can be used.

**Q3. Why do we include 900 and 400 separately?**

Because Roman numerals use subtractive notation. These values map to CM and CD respectively.

**Q4. Can we solve this problem without division and modulo?**

Yes. The one-pass approach repeatedly subtracts the current denomination.

**Q5. Are both approaches equally efficient?**

Yes, asymptotically. Both run in O(L) because the denomination count is fixed.

**Q6. What is the greedy choice?**

Choose the largest denomination that does not exceed the remaining number.

---

## 9. Pattern Recognition — When to Think Greedy?

Look for these clues:

- You repeatedly need to make a locally optimal choice.
- Choices can be processed in a specific order.
- A larger valid denomination should be consumed before smaller ones.
- The problem asks for a canonical representation or construction.

This problem demonstrates a useful pattern:

**Descending choices + largest valid choice + update remaining value.**

---

## 10. Final Revision Cheat Sheet

| Concept | Remember |
|---|---|
| Problem | Integer to Roman |
| LeetCode | 12 |
| Pattern | Greedy |
| Main idea | Largest valid denomination first |
| Denominations | 13 fixed values |
| Special cases | IV, IX, XL, XC, CD, CM |
| Original approach | Helper + division + modulo |
| Improved approach | One-pass traversal + subtraction |
| Time | O(L) |
| Auxiliary Space | O(1) |

**Key takeaway:** Greedy does not always mean choosing the largest number blindly. It means choosing the largest *valid* option according to the problem's rules. Here, the descending denomination table makes that choice straightforward.
