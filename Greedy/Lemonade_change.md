# Lemonade Change — LeetCode 860

**Pattern:** Greedy + Array  
**Difficulty:** Easy  
**Core Concept:** Greedy Resource Management  
**Key Idea:** When giving change, prefer the combination that preserves more flexible denominations for future customers.

---

## 1. Problem Understanding

A lemonade stand sells each lemonade for **$5**.

Customers arrive one by one and pay using one of these denominations:

- $5
- $10
- $20

Initially, we have no money.

For every customer:
- Receive their bill.
- Give the correct change of $5.
- If we cannot provide exact change, return `false`.

If we successfully provide change to every customer, return `true`.

**Important Conditions:**
1. Lemonade price is fixed at $5.
2. Customers arrive in the given order.
3. We cannot change the order of customers.
4. Each customer must receive the exact change.
5. Initially, we have zero bills.

## 2. Greedy Intuition

We maintain two counters:

```cpp
int cnt1 = 0; // Number of $5 bills
int cnt2 = 0; // Number of $10 bills
```

We do not need to maintain the number of $20 bills because they cannot be used to provide change to future customers.

### Case 1: Customer pays $5

No change is required.

```cpp
cnt1++;
```

### Case 2: Customer pays $10

We must return $5.

Therefore, we need at least one $5 bill.

```cpp
if(cnt1 > 0){
    cnt1--;
    cnt2++;
}
else{
    return false;
}
```

### Case 3: Customer pays $20

We must return $15.

There are two possible combinations:

**Option A: $10 + $5**

```cpp
cnt2--;
cnt1--;
```

**Option B: $5 + $5 + $5**

```cpp
cnt1 -= 3;
```

Which option should we prefer?

**Prefer $10 + $5 whenever possible.**

Why?

Because $5 bills are more flexible:
- A $5 bill can provide change for a $10 customer.
- Three $5 bills can provide change for a $20 customer.
- A $10 bill can only help with a $20 customer.

Therefore, preserving $5 bills gives us more flexibility for future transactions.

This is the central greedy decision of this problem.

---

## 3. Greedy Strategy

For every customer:

| Bill received | Required change | Greedy decision |
|---|---|---|
| $5 | $0 | Store the $5 bill |
| $10 | $5 | Give one $5 bill |
| $20 | $15 | Prefer $10 + $5, otherwise give three $5 bills |

If the required change cannot be provided, immediately return `false`.

## 4. Optimal C++ Solution

```cpp
class Solution {
public:
    bool lemonadeChange(vector<int>& bills) {

        int cnt1 = 0; // $5 bills
        int cnt2 = 0; // $10 bills

        int n = bills.size();

        for(int i = 0; i < n; i++){

            // Customer pays $5
            if(bills[i] == 5){
                cnt1++;
            }

            // Customer pays $10
            else if(bills[i] == 10){

                if(cnt1 > 0){
                    cnt1--;
                    cnt2++;
                }
                else{
                    return false;
                }
            }

            // Customer pays $20
            else{

                // Prefer $10 + $5
                if(cnt2 > 0 && cnt1 > 0){
                    cnt2--;
                    cnt1--;
                }

                // Otherwise use three $5 bills
                else if(cnt1 >= 3){
                    cnt1 -= 3;
                }

                else{
                    return false;
                }
            }
        }

        return true;
    }
};
```

---

## 5. Dry Run

Consider:

```cpp
bills = [5, 5, 5, 10, 20]
```

Initially:

```cpp
cnt1 = 0
cnt2 = 0
```

| Customer | Bill | Action | $5 count | $10 count |
|---|---:|---|---:|---:|
| 1 | 5 | Store $5 | 1 | 0 |
| 2 | 5 | Store $5 | 2 | 0 |
| 3 | 5 | Store $5 | 3 | 0 |
| 4 | 10 | Give $5, store $10 | 2 | 1 |
| 5 | 20 | Give $10 + $5 | 1 | 0 |

All customers receive correct change.

**Output: `true`**

Notice that for the final customer, we used $10 + $5 instead of three $5 bills.

This preserves one extra $5 bill.

---

## 6. Another Important Dry Run — Greedy Failure

Consider:

```cpp
bills = [5, 10, 10]
```

Initially:

```cpp
cnt1 = 0
cnt2 = 0
```

| Customer | Bill | Action | $5 count | $10 count |
|---|---:|---|---:|---:|
| 1 | 5 | Store $5 | 1 | 0 |
| 2 | 10 | Give $5 | 0 | 1 |
| 3 | 10 | Cannot give $5 change | 0 | 1 |

The third customer cannot receive change.

**Output: `false`**

We cannot use a $10 bill to give $5 change.

---

## 7. Why Is This Greedy Strategy Correct?

The important part is understanding why we prefer `$10 + $5` over `$5 + $5 + $5`.

Suppose we receive a $20 bill.

We have two valid ways to return $15:

- One $10 bill and one $5 bill.
- Three $5 bills.

Both satisfy the current customer.

However, the second option consumes two additional $5 bills.

A $5 bill is more flexible because it can be used for both:
- A future $10 customer.
- A future $20 customer.

A $10 bill is useful for the $20 change combination, but cannot independently provide change for a $10 customer.

Therefore, whenever both combinations are available, using `$10 + $5` preserves more $5 bills without making the current transaction invalid.

This is a resource-preservation greedy decision.

**Important:** Greedy does not mean choosing any valid option. It means choosing a valid option that preserves the best possibilities for future decisions.

---

## 8. Complexity Analysis

Let `n` be the number of customers.

### Time Complexity

We traverse the bills array exactly once.

Each customer requires only constant-time operations.

\[
T(n) = O(n)
\]

### Auxiliary Space Complexity

We maintain only two counters:

```cpp
cnt1
cnt2
```

No additional data structure is required.

\[
S(n) = O(1)
\]

**This is optimal:** We must inspect each customer's bill, so the time complexity cannot be better than O(n).

---

## 9. Important Edge Cases

| Input | Output | Explanation |
|---|---|---|
| `[5]` | `true` | No change required |
| `[10]` | `false` | No $5 bill available |
| `[20]` | `false` | No change available |
| `[5, 5, 10]` | `true` | One $5 remains |
| `[5, 10, 10]` | `false` | Cannot serve the third customer |
| `[5, 5, 5, 20]` | `true` | Three $5 bills provide $15 |
| `[5, 5, 10, 20]` | `false` | Only one $5 and one $10 are available for the $20 transaction |

---

## 10. Common Mistakes

### Mistake 1: Using separate `if` statements instead of `if-else if`

Incorrect structure:

```cpp
if(bills[i] == 5){
    cnt1++;
}

if(bills[i] == 10){
    // ...
}
else{
    // $20 handling
}
```

The `else` belongs to the second `if`, so it also executes when the bill is $5.

Use:

```cpp
if(bills[i] == 5){
    // ...
}
else if(bills[i] == 10){
    // ...
}
else{
    // ...
}
```

### Mistake 2: Preferring three $5 bills over $10 + $5

This consumes more flexible $5 bills unnecessarily.

### Mistake 3: Forgetting to update both counters

When giving `$10 + $5`, both denominations must decrease by one.

### Mistake 4: Tracking $20 bills unnecessarily

A $20 bill cannot help us provide change to any future customer. Therefore, we do not need to track it.

---

## 11. Pattern Recognition — When to Think of Greedy Resource Management

Look for problems where:

1. Resources arrive over time.
2. Each operation consumes some resources.
3. You have multiple valid ways to perform an operation.
4. The choice made now affects future operations.
5. You want to preserve the most useful resources for future requirements.

Possible applications:
- Cash change management.
- Task scheduling.
- Resource allocation.
- Capacity management.
- Greedy selection under limited resources.

---

## 12. Interview Follow-up Questions

**Q1. Why do we prefer $10 + $5 for a $20 bill?**

Because it preserves more $5 bills, which are more flexible for future transactions.

**Q2. Why don't we track $20 bills?**

They cannot be used to provide change for any customer in this problem.

**Q3. Can we sort the bills array?**

No. Customers arrive in a fixed order, and changing the order can change whether we can provide change.

**Q4. What is the time complexity?**

O(n), because every customer is processed exactly once.

**Q5. What is the auxiliary space complexity?**

O(1), because we maintain only two counters.

**Q6. Is the greedy decision important even if both combinations work?**

Yes. Both may work for the current customer, but preserving flexible resources improves our ability to serve future customers.

---

## 13. Quick Revision Cheat Sheet

| Concept | Remember |
|---|---|
| Pattern | Greedy + Resource Management |
| Price | $5 |
| Track | Number of $5 and $10 bills |
| $5 payment | Store it |
| $10 payment | Give one $5 |
| $20 payment | Prefer $10 + $5 |
| Alternative | Three $5 bills |
| Customer order | Must remain unchanged |
| Time | O(n) |
| Space | O(1) |

### One-Line Takeaway

**When multiple valid choices exist, prefer the one that preserves the most flexible resources for future operations.**
