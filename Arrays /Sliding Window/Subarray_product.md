━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧩 LEETCODE 713 — SUBARRAY PRODUCT LESS THAN K
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📌 PATTERN
Sliding Window (Two Pointer) + Shrinking Window on a Monotonic Product

Goal:
Diye gaye array nums (saare elements >= 1) aur ek integer k
ke liye, un CONTIGUOUS subarrays ki count nikalni hai jinka
product k se strictly chhota ho.

Example:

    nums = [10,5,2,6], k = 100

    Output: 8
    (valid subarrays: [10], [5], [2], [6], [10,5], [5,2],
     [2,6], [5,2,6])

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 CORE IDEA
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Ye Two Sum / HashSet wale patterns se alag hai — yaha
"brute force check every subarray" O(n^2) hoga, lekin ek
special property hai jo O(n) sliding window allow karti hai:

    Saare nums[i] >= 1 hain (constraint).

Iska matlab:
  - Window ko RIGHT se badhao (naya element multiply karo)
    → product hamesha BADHTA hai (ya same rehta hai agar 1 ho).
  - Window ko LEFT se chhota karo (element divide karke
    nikaalo) → product hamesha GHATTA hai (ya same rehta hai).

Ye MONOTONIC property hi hai jo two-pointer ko valid banati
hai. Agar array mein negative ya 0 allowed hote, ye approach
TOOT jaata (product ka direction unpredictable ho jaata).

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔑 MOST IMPORTANT PATTERN
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

For every right = 0 to n-1:

        prod *= nums[right]     (window mein naya element add)
              |
        prod >= k ?
              |
        YES ─────────────────→ NO
              |                  |
     SHRINK from left:      window already valid,
     while(prod>=k &&       aage badho
           left<=right):
         prod /= nums[left]
         left++
              |
        ab window [left..right] VALID hai
              |
        cnt += (right - left + 1)

⭐ Ye line sabse important hai: `cnt += (right - left + 1)`
Iska matlab — agar poora window [left..right] valid hai
(product < k), to isme jitne bhi subarrays HAIN jo `right`
pe KHATAM hote hain — [left..right], [left+1..right], ...,
[right..right] — SAB valid hain (kyunki chhota subarray ka
product bade subarray ke product se chhota ya barabar hota
hai, jab saare elements >= 1 hon). Total aise subarrays ki
ginti = (right - left + 1).

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔄 YOUR APPROACH (Final, Accepted — 560/560, O(n))
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

    class Solution {
    public:
        int numSubarrayProductLessThanK(vector<int>& nums, int k) {
            int left = 0;
            int right = 0;
            long long prod = 1;
            int cnt = 0;
            while(right < nums.size()){
                prod *= nums[right];
                if(prod < k){
                    cnt += (right - left + 1);
                }
                else{
                    while(prod >= k && left <= right){
                        prod /= nums[left];
                        left++;
                    }
                    cnt += (right - left + 1);
                }
                right++;
            }
            return cnt;
        }
    };

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🎯 SLIDING WINDOW INVARIANT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

At every point in the loop (after processing index `right`):

    [left, right] = sabse CHHOTA window jisse product < k
                    ho jaaye, `right` ko fix rakhte hue.

Iska matlab — humesha `left` ko sirf UTNA hi shrink karo
jitna zaroori hai (jab tak `prod >= k`), na ek step zyada,
na ek step kam.

Do cheezein guarantee karti hain ki ye safe hai:

    right < nums.size()      → right kabhi array ke bahar
                                nahi jaata
    left <= right (in shrink loop)
                              → left kabhi right se aage
                                nahi jaata (warna window
                                "negative size" ho jaata,
                                aur nums[left] out-of-bounds
                                ban jaata)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧪 EXAMPLE — DRY RUN
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

nums = [10,5,2,6], k = 100

right=0: prod = 1*10 = 10
         10 < 100 ✅ → cnt += (0-0+1) = 1        cnt=1

right=1: prod = 10*5 = 50
         50 < 100 ✅ → cnt += (1-0+1) = 2        cnt=3

right=2: prod = 50*2 = 100
         100 < 100 ❌ → SHRINK:
             prod=100 >= 100, left<=right(0<=2):
                 prod /= nums[0]=10 → prod=10, left=1
             prod=10 >= 100? No → shrink loop exit
         cnt += (2-1+1) = 2                       cnt=5

right=3: prod = 10*6 = 60
         60 < 100 ✅ → cnt += (3-1+1) = 3          cnt=8

Final cnt = 8  ✅ (matches expected)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚔️ SLIDING WINDOW (SHRINK) vs TWO-POINTER (SUM/HASHMAP)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Contiguous Array (LC 525) jaise questions mein hum ek
HASHMAP use karte hain kyunki "sum" NEGATIVE bhi ho sakta
hai — usme monotonic property nahi hoti, isliye simple
shrink-window kaam nahi karta, humein "kis index pe ye sum
pehle mila tha" yaad rakhna padta hai.

Is question mein "product" sirf BADHTA/GHATTA hai (kyunki
sab elements >= 1) — isliye koi hashmap nahi chahiye, seedha
do pointers (`left`, `right`) hi kaafi hain.

    Monotonic property hai?
        HAAN → Sliding Window (2 pointers, O(1) space)
        NAHI → Prefix Sum + HashMap (O(n) space)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏱️ COMPLEXITY
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Time:  O(n)   — `right` n baar move karta hai; `left` bhi
               poore run mein total milake max n baar hi
               move karta hai (kabhi peeche nahi jaata) →
               amortized O(1) per step → overall O(n)

Space: O(1)   — sirf `left`, `right`, `prod`, `cnt`
               (koi extra array/map nahi)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🚨 COMMON MISTAKES (jo maine khud kiye, is order mein)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

❌ Attempt 1 — `int product` use karna
    → Bade products (jaise 45258850 * 804) `int` ke range
      (~2.1 billion) se bahar chale jaate hain →
      SIGNED INTEGER OVERFLOW (undefined behavior).
      FIX: `long long product` use karo jab bhi multiple
      numbers multiply ho rahe hon.

❌ Attempt 1 — Shrink karte waqt `left++` PEHLE, divide
   BAAD mein karna
    → `left++; prod /= nums[left];` galat hai — ye NAYE
      `left` se divide karta hai, jabki window se
      PURANA `left` bahar nikal raha hai. Ye ek non-
      contiguous, meaningless product bana deta hai.
      FIX: pehle `prod /= nums[left]`, PHIR `left++`.

❌ Attempt 1 — `left` ke liye koi bound-check nahi
    → Agar poora window (single element sahit) bhi k se
      bada ho, code `left` ko `right` se aage bhi bada
      sakta hai → `nums[left]` out-of-bounds read/crash.
      FIX: shrink loop mein hamesha `left <= right` bhi
      check karo, sirf `prod >= k` nahi.

❌ Attempt 1 — Ad-hoc "duplicate avoid" checks
   (`if(product != nums[right])`)
    → Jab counting logic mein "kahin ye dobara count to
      nahi ho raha" jaisa manual patch lagana pade, ye
      signal hai ki underlying approach hi galat hai.
      FIX: `cnt += (right - left + 1)` formula khud hi
      guarantee karta hai no double-counting — kisi patch
      ki zaroorat nahi padti.

❌ Attempt 2 — Outer loop `while(left <= right)` use karna
    → `right` har iteration mein unconditionally badhta
      hai, lekin `left` nahi — isliye `left <= right`
      HAMESHA true reh sakta hai, aur `right` array ke
      size se bhi bahar nikal jaata hai → crash.
      FIX: outer loop hamesha `right < nums.size()` pe
      based hona chahiye, `left` se uska seedha relation
      mat banao.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 ONE-LINE INTERVIEW EXPLANATION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

"Saare elements positive hain, isliye product window ke
size ke saath monotonically badhta/ghatta hai — ek sliding
window maintain karo jo shrink hoke hamesha product < k
rakhe, aur har valid window `right` pe (right - left + 1)
naye valid subarrays contribute karta hai."

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔥 PATTERN TO REMEMBER
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

SUBARRAY PRODUCT LESS THAN K

for right = 0 to n-1
  ↓
prod *= nums[right]
  ↓
prod >= k?
  ↓ yes                         ↓ no
SHRINK from left               window already valid
(while prod>=k && left<=right)
  ↓
window [left..right] valid
  ↓
cnt += (right - left + 1)
  ↓
loop continues, right++

⭐ Key Rule:
"Positive elements → product monotonic → 2-pointer shrink
window works. Har valid window pe (right-left+1) NAYE
subarrays add karo, kisi extra duplicate-check ki zaroorat
nahi."

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔗 SIMILAR QUESTIONS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. Longest Substring Without Repeating Characters — LC 3
2. Minimum Size Subarray Sum — LC 209
3. Fruit Into Baskets — LC 904
4. Max Consecutive Ones III — LC 1004
5. Longest Repeating Character Replacement — LC 424
6. Contiguous Array — LC 525 (monotonic NAHI hai → prefix
   sum + hashmap approach chahiye, is question se compare
   karne layak)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
