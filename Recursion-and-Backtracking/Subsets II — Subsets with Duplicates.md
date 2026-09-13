# Subsets II — Subsets with Duplicates
LeetCode 90

📌 Pattern:
Backtracking + Include/Exclude + Duplicate Skipping

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

## 🧠 Core Idea

Normal Subsets mein har element ke 2 choices hote hain:

1. Take current element
2. Don't take current element

Duplicates hone par problem ye hai ki same subset multiple times generate ho sakta hai.

Example:
nums = [1,2,2]

Without duplicate handling:
[1,2] multiple times generate ho sakta hai.

### Solution:
1. Array ko sort karo.
2. Take branch mein duplicate ko normally process karo.
3. Don't-take branch mein consecutive duplicates ko skip karo.

Sorting important hai because duplicates adjacent aa jaate hain.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

## 🔑 Most Important Pattern

For every nums[idx]:

        TAKE
          |
      nums[idx]
          |
      idx + 1

        DON'T TAKE
          |
   skip all duplicates
          |
     next unique index


⚠️ Duplicate skipping ONLY in the DON'T-TAKE branch.

Why?

Suppose nums = [2,2]

We need:
[]
[2]
[2,2]

Agar TAKE branch mein duplicate skip kar diya,
to [2,2] miss ho jayega.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

## 🔄 Your Approach

void solve(vector<int>& nums,
           vector<vector<int>>& ans,
           int idx,
           vector<int>& temp) {

    if(idx == nums.size()) {
        ans.push_back(temp);
        return;
    }

    // TAKE
    temp.push_back(nums[idx]);
    solve(nums, ans, idx + 1, temp);
    temp.pop_back();

    // DON'T TAKE + SKIP DUPLICATES
    int i = idx + 1;

    while(i < nums.size() &&
          nums[i] == nums[i - 1]) {
        i++;
    }

    solve(nums, ans, i, temp);
}

vector<vector<int>> subsetsWithDup(vector<int>& nums) {

    sort(nums.begin(), nums.end());

    vector<vector<int>> ans;
    vector<int> temp;

    solve(nums, ans, 0, temp);

    return ans;
}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

## 🧩 Duplicate Skipping — Important

Suppose:

nums = [1,2,2,2,3]

At the first 2:

        TAKE 2
           ↓
       idx + 1
           ↓
      process next 2
           ↓
      can take another 2
           ↓
      can take another 2

This allows:

[2]
[2,2]
[2,2,2]

But when we DON'T TAKE the first 2:

        DON'T TAKE 2
              ↓
       skip all 2's
              ↓
             3

So we don't create duplicate versions of:

[... , 2]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

## 💡 Why Sorting?

Before sorting:

[2,1,2]

Duplicates are not adjacent.

After sorting:

[1,2,2]

Now we can easily do:

while(nums[i] == nums[i-1])
    i++;

So:

SORT → GROUP DUPLICATES → SKIP DUPLICATE BRANCHES

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

## 🎯 Backtracking Invariant

At every recursive call:

temp = current subset being constructed.

TAKE:
    add nums[idx]
    recurse
    remove nums[idx]

DON'T TAKE:
    don't add nums[idx]
    skip its duplicate occurrences
    recurse

The pop_back() is necessary to restore temp
before exploring the DON'T-TAKE branch.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

## 🧪 Example

nums = [1,2,2]

At 1:

        []
       /  \
      /    \
    [1]     []

From [1]:

    TAKE 2
      ↓
    [1,2]

Then next 2 can be taken:

    [1,2,2]

If we DON'T TAKE the first 2,
we skip the second 2 as well.

This prevents generating [1,2] again.

Final result:

[]
[1]
[1,2]
[1,2,2]
[2]
[2,2]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

## ⚔️ Normal Subsets vs Subsets II

Normal Subsets:
    Every element → Take / Don't Take
    No duplicate handling required.

Subsets II:
    Sort first
    Take normally
    Don't Take → skip duplicates

Key difference:

Normal:
    solve(idx + 1)

Subsets II DON'T-TAKE:
    skip duplicate elements
    solve(next_unique_index)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

## ⏱️ Complexity

Let n = number of elements.

Sorting:
    O(n log n)

Backtracking:
    Output-sensitive because we have to generate
    every unique subset.

There can be up to 2^n subsets.

Time:
    O(n log n + n * 2^n) worst case

Space:
    O(n) recursion depth
    + O(n) current subset

Output space:
    O(number of unique subsets × n)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

## 🚨 Common Mistakes

❌ Forgetting to sort
    → duplicates won't be adjacent.

❌ Skipping duplicates in TAKE branch
    → valid subsets like [2,2] may disappear.

❌ Skipping only one duplicate
    → duplicate subsets can still be generated.

❌ Forgetting pop_back()
    → elements from previous branch remain in temp.

❌ Using the common for-loop duplicate pattern blindly.

Important:
There are multiple valid ways to solve Subsets II.
This solution uses an INCLUDE/EXCLUDE recursion where
duplicate skipping happens in the EXCLUDE branch.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

## 🧠 One-Line Interview Explanation

"Sort the array so duplicates become adjacent, then use
include/exclude backtracking. When excluding an element,
skip all of its consecutive duplicates so the same subset
is not generated again, while the include branch still
allows repeated values to form subsets like [2,2]."

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

## 🔥 Pattern to Remember

SUBSETS II

SORT
  ↓
TAKE current element
  ↓
recurse(idx + 1)
  ↓
UNDO
  ↓
DON'T TAKE
  ↓
SKIP DUPLICATES
  ↓
recurse(next unique index)

⭐ Key Rule:
"Duplicate skipping belongs to the DON'T-TAKE branch,
not the TAKE branch."

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
