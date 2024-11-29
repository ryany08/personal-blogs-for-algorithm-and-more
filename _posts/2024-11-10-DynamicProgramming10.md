---
Title: DynamicProgramming10
Date: 2024-11-10
---
# 算法训练营 | 1143.最长公共子序列  |  1035.不相交的线 |  53. 最大子序和 | 392.判断子序列
##  1143.最长公共子序列
给定两个字符串 text1 和 text2，返回这两个字符串的最长公共子序列的长度。\
一个字符串的 子序列 是指这样一个新的字符串：它是由原字符串在不改变字符的相对顺序的情况下删除某些字符（也可以不删除任何字符）后组成的新字符串。\
例如，"ace" 是 "abcde" 的子序列，但 "aec" 不是 "abcde" 的子序列。两个字符串的「公共子序列」是这两个字符串所共同拥有的子序列。
### 代码
~~~C++
class Solution {
public:
    int longestCommonSubsequence(string text1, string text2) {
        vector<vector<int>> dp(text1.size() + 1, vector<int>(text2.size() + 1, 0));
        for (int i = 1; i <= text1.size(); i++) {
            for (int j = 1; j <= text2.size(); j++) {
                if (text1[i - 1] == text2[j - 1]) {
                    dp[i][j] = dp[i - 1][j - 1] + 1;
                } else {
                    dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);
                }
            }
        }
        return dp[text1.size()][text2.size()];
    }
};
~~~
## 1035.不相交的线
我们在两条独立的水平线上按给定的顺序写下 A 和 B 中的整数。\
现在，我们可以绘制一些连接两个数字 A[i] 和 B[j] 的直线，只要 A[i] == B[j]，且我们绘制的直线不与任何其他连线（非水平线）相交。\
以这种方法绘制线条，并返回我们可以绘制的最大连线数
### 难点
如何理解两条连线交叉是本题的重点。本题中两条连线不相交，就是A和B中连接的点的在子集数组中的相对位置不变。也就是这些点所形成的在自己数组内的相对顺序相同，也就是求A和B的最长公共子序列。
### 代码
~~~C++
class Solution {
public:
    int maxUncrossedLines(vector<int>& A, vector<int>& B) {
        vector<vector<int>> dp(A.size() + 1, vector<int>(B.size() + 1, 0));
        for (int i = 1; i <= A.size(); i++) {
            for (int j = 1; j <= B.size(); j++) {
                if (A[i - 1] == B[j - 1]) {
                    dp[i][j] = dp[i - 1][j - 1] + 1;
                } else {
                    dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);
                }
            }
        }
        return dp[A.size()][B.size()];
    }
};
~~~
## 53. 最大子序和
给定一个整数数组 nums ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。\
示例:\
输入: [-2,1,-3,4,-1,2,1,-5,4]\
输出: 6\
解释: 连续子数组 [4,-1,2,1] 的和最大，为 6。
### 代码
~~~C++
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        if (nums.size() == 0) return 0;
        vector<int> dp(nums.size());
        dp[0] = nums[0];
        int result = dp[0];
        for (int i = 1; i < nums.size(); i++) {
            dp[i] = max(dp[i - 1] + nums[i], nums[i]); // 状态转移公式
            if (dp[i] > result) result = dp[i]; // result 保存dp[i]的最大值
        }
        return result;
    }
};
~~~
## 392.判断子序列
给定字符串 s 和 t ，判断 s 是否为 t 的子序列。\
字符串的一个子序列是原始字符串删除一些（也可以不删除）字符而不改变剩余字符相对位置形成的新字符串。（例如，"ace"是"abcde"的一个子序列，而"aec"不是）。\
示例 1：\
输入：s = "abc", t = "ahbgdc"\
输出：true
### 难点
理解子序列的意义，本题其实和最长公共序列相似。区别在于当比较的元素不一致时，不能根据比较序列的前一个状态判断，只能根据被比较序列的前一个状态判断。否则，就会无法涵盖比较序列序列位于被比较序列的前部的情况。
### 代码
~~~C++
class Solution {
public:
    bool isSubsequence(string s, string t) {
        vector<vector<int>> dp(s.size() + 1, vector<int>(t.size() + 1, 0));
        for (int i = 1; i <= s.size(); i++) {
            for (int j = 1; j <= t.size(); j++) {
                if (s[i - 1] == t[j - 1]) dp[i][j] = dp[i - 1][j - 1] + 1;
                else dp[i][j] = dp[i][j - 1];
            }
        }
        if (dp[s.size()][t.size()] == s.size()) return true;
        return false;
    }
};
~~~

