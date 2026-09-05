VALID PALINDROME — LeetCode 125

KEY IDEA:
String ko dono ends se check karo using two pointers.
- Non-alphanumeric characters ignore karo.
- Comparison case-insensitive hona chahiye.
- Agar kisi valid character par mismatch mila → false.
- Puri string successfully traverse ho gayi → true.

APPROACH:
1. left = 0, right = n-1
2. Agar s[left] alphanumeric nahi hai → left++
3. Agar s[right] alphanumeric nahi hai → right--
4. Dono valid characters hain:
      agar lowercase(s[left]) != lowercase(s[right])
          return false
5. left++, right--
6. End me return true.

TIME: O(n)
SPACE: O(1)

CODE:
class Solution {
public:
    bool isPalindrome(string s) {
        int left = 0;
        int right = s.length() - 1;

        while(left <= right) {
            if(!isalnum(s[left])) {
                left++;
                continue;
            }

            if(!isalnum(s[right])) {
                right--;
                continue;
            }

            if(tolower(s[left]) != tolower(s[right])) {
                return false;
            }

            left++;
            right--;
        }

        return true;
    }
};

PATTERN TO REMEMBER:
"Ignore invalid characters + compare from both ends"
→ Two Pointer pattern.
