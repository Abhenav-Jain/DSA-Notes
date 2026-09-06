╔══════════════════════════════════════════════════════════════════╗
║              ✦ STRING COMPRESSION — LeetCode 443 ✦              ║
║         In-Place Modification • Two Pointers • Run Length       ║
║                         Encoding (RLE)                           ║
╚══════════════════════════════════════════════════════════════════╝

🟢 NOTES WORTHY / MUST KEEP
🔁 REVISION QUESTION — Practice 2 more times
📁 PATH → Strings → Two Pointers → In-place / Compression


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🎯 PROBLEM
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Given a character array, compress consecutive repeating characters.

Example:

    ['a','a','b','b','c','c','c']

becomes:

    ['a','2','b','2','c','3']

Return the length of the compressed array.

Important:

🔥 Compression MUST be done IN-PLACE.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 THE CORE IDEA
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Don't think:

    "How do I modify every character?"

Think:

    "How many times does each consecutive character occur?"
             ↓
       Find its frequency
             ↓
       Write compressed result

For every GROUP:

    character + frequency (if frequency > 1)

Example:

    aaabbcccc

Groups:

    aaa   → a3
    bb    → b2
    c     → c
    cccc  → c4

Result:

    a3b2c4c ❌

Wait!

Actually the groups are:

    aaa → a3
    bb  → b2
    ccccc? ...

🔥 The important rule is:

ONLY CONSECUTIVE SAME CHARACTERS form a group.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
💡 MASTER VISUAL
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Input:

    a a a b b c c c c
    └─────┘ └──┘ └──────┘
      a3      b2    c4

Compression:

    a 3 b 2 c 4

Output:

    ['a','3','b','2','c','4']


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🚀 THE TWO-POINTER IDEA
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

We use TWO logical pointers:

    i   → READ pointer
          Finds the next group.

    idx → WRITE pointer
          Writes compressed characters.

Visual:

    READ
      ↓
    a a a b b c c
          ↑
        WRITE

🔥 Most important concept:

    i    = where am I reading?
    idx  = where am I writing?


They don't have to move together.

This is what makes the solution IN-PLACE.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚙️ ALGORITHM
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Start:

    idx = 0
    i   = 0

While i < n:

    1️⃣ Store current character:

        ch = chars[i]

    2️⃣ Count its consecutive occurrences.

        while(i < n && chars[i] == ch)

            count++
            i++

    3️⃣ Write the character:

        chars[idx++] = ch

    4️⃣ If count > 1:

        Convert count → string

        Example:

            12 → "12"

        Write every digit separately.

    5️⃣ Continue from the next group.

Finally:

    chars.resize(idx)

    return idx


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔥 WHY `i` IS NOT INCREMENTED IN THE FOR LOOP
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Notice:

    for(int i = 0; i < n;)

There is NO:

    i++

inside the for loop.

Why?

Because the inner while loop itself moves `i` through the
ENTIRE GROUP.

Example:

    a a a b b

Start:

    i = 0

while:

    i = 0 → a
    i = 1 → a
    i = 2 → a
    i = 3 → b ❌

Now i is already pointing to the NEXT GROUP.

🔥 Therefore:

    while() controls READ pointer.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
💻 CLEAN C++ SOLUTION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

class Solution {
public:
    int compress(vector<char>& chars) {

        int idx = 0;
        int n = chars.size();

        for(int i = 0; i < n;) {

            char ch = chars[i];
            int count = 0;

            // Find size of current group
            while(i < n && chars[i] == ch) {
                count++;
                i++;
            }

            // Write the character
            chars[idx++] = ch;

            // Write frequency only if > 1
            if(count > 1) {

                string num = to_string(count);

                for(char digit : num) {
                    chars[idx++] = digit;
                }
            }
        }

        // Remove unused portion
        chars.resize(idx);

        return idx;
    }
};


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧩 THE MOST IMPORTANT PART
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

    string num = to_string(count);

Suppose:

    count = 12

We cannot write:

    chars[idx++] = 12;    ❌

Because chars stores characters.

We need:

    "12"

which consists of:

    '1' '2'

So:

    string num = to_string(count);

    for(char digit : num)
        chars[idx++] = digit;


🔥 This is especially important for MULTI-DIGIT frequencies.

Example:

    count = 13

Output:

    x13

not:

    x1
    x3 separately by some special logic.

`to_string()` handles it automatically.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧪 DRY RUN — STEP BY STEP
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Input:

    a a a b b c

Initial:

    idx = 0
    i   = 0

──────────────────────────────────────────────────────────────────

GROUP 1:

    ch = 'a'

while loop:

    a → count = 1
    a → count = 2
    a → count = 3

Now:

    i = 3
    count = 3

Write:

    chars[idx++] = 'a'

    idx = 1

Convert:

    to_string(3) = "3"

Write:

    chars[1] = '3'

Now:

    idx = 2


──────────────────────────────────────────────────────────────────

GROUP 2:

    ch = 'b'

Count:

    b → 1
    b → 2

So:

    count = 2

Write:

    b2

Now:

    idx = 4


──────────────────────────────────────────────────────────────────

GROUP 3:

    ch = 'c'

Count:

    c → 1

Since:

    count == 1

Only write:

    c

Final:

    a 3 b 2 c

Answer:

    5


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✨ CASE 1 — SINGLE CHARACTER
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Input:

    ['a','b','c']

Groups:

    a → count 1
    b → count 1
    c → count 1

Output:

    ['a','b','c']

🔥 Rule:

    count == 1
    → write only character

No "1" is added.

So:

    a1b1c1 ❌

Correct:

    abc ✅


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✨ CASE 2 — REPEATED CHARACTER
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Input:

    ['a','a','a']

Count:

    3

Output:

    ['a','3']

So:

    aaa → a3


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✨ CASE 3 — MULTI-DIGIT COUNT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Suppose:

    12 × 'a'

Then:

    count = 12

We write:

    'a'
    '1'
    '2'

Result:

    a12

🔥 This is why:

    to_string(count)

is useful.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔥 WHY `idx` IS NECESSARY
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Suppose:

    a a a b b c

Original positions:

    0 1 2 3 4 5

Compressed result:

    a 3 b 2 c

We are writing fewer characters than we are reading.

So:

    READ pointer  → i
    WRITE pointer → idx

Example:

    i   = 3
    idx = 2

Meaning:

    We have READ 3 original characters,
    but only WRITTEN 2 compressed characters.

🔥 This separation is the heart of the problem.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
♻️ WHY `chars.resize(idx)`?
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Compression can make the array smaller.

Example:

Before:

    ['a','a','a','b','b','c']

After writing:

    ['a','3','b','2','c', ...]

The old unused characters may still exist after `idx`.

So:

    chars.resize(idx);

removes everything after the compressed result.

🔥 `idx` = actual length of compressed array.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🚨 COMMON MISTAKES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

❌ Mistake 1:

    Adding '1' when count == 1.

Wrong:

    a → a1

Correct:

    a → a


❌ Mistake 2:

    Only handling single-digit counts.

For:

    12

Need:

    '1','2'

not a single character.


❌ Mistake 3:

    Using an extra vector/string for the final answer.

Problem specifically expects IN-PLACE modification.

Use:

    idx

to overwrite the original array.


❌ Mistake 4:

    Incrementing i twice.

If you already have:

    while(i < n && chars[i] == ch) {
        i++;
    }

then don't additionally increment i for that group.


❌ Mistake 5:

    Forgetting chars.resize(idx).

Compressed data may be correct,
but the array can still contain old garbage after idx.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏱️ COMPLEXITY
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Time:

    O(n)

Why?

Every character is effectively processed once.

Even though we have:

    for + while

it is NOT O(n²).

The `i` pointer only moves forward.

Space:

    O(n)

Worst case auxiliary space due to:

    string num = to_string(count)

For the problem's alphabet/count constraints this is tiny,
but strictly speaking the temporary digit string can take
O(log n) space for one count.

Core algorithm itself:

    O(1) extra working space
    + output stored inside chars


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 INTERVIEW PATTERN
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Whenever you see:

    "Compress consecutive elements"

Think:

        GROUP
          ↓
       COUNT
          ↓
       WRITE
          ↓
      NEXT GROUP

And for arrays/strings:

    READ pointer  +  WRITE pointer


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔥 MASTER TEMPLATE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

for(int i = 0; i < n;) {

    char ch = chars[i];
    int count = 0;

    // 1. Find group
    while(i < n && chars[i] == ch) {
        count++;
        i++;
    }

    // 2. Write character
    chars[idx++] = ch;

    // 3. Write frequency if needed
    if(count > 1) {

        string num = to_string(count);

        for(char digit : num) {
            chars[idx++] = digit;
        }
    }
}

chars.resize(idx);


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚡ 10-SECOND REVISION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

"Consecutive same characters ka group pakdo, uski frequency
count karo, `idx` se compressed form overwrite karo, aur
frequency > 1 hone par uske digits individually write karo."


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🏆 PATTERN CARD
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Problem:
    String Compression

Pattern:
    Two Pointers + Grouping + In-Place Modification

Hidden Concept:
    Run-Length Encoding (RLE)

READ pointer:
    i

WRITE pointer:
    idx

Group:
    Consecutive equal characters

Frequency:
    count

Frequency conversion:
    to_string(count)

Final cleanup:
    chars.resize(idx)

Time:
    O(n)

Space:
    O(log n) temporary digit string
    / O(1) core working space


╔══════════════════════════════════════════════════════════════════╗
║                     🔥 GOLDEN TAKEAWAY                           ║
║                                                                  ║
║   "READ the whole group → COUNT it → WRITE compressed form →   ║
║                      MOVE to the next group."                   ║
║                                                                  ║
║              READ ≠ WRITE                                        ║
║              i    ≠ idx                                          ║
║                                                                  ║
║        यही separation इस problem की असली key है. 🚀             ║
╚══════════════════════════════════════════════════════════════════╝
