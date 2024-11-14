---
Title: DynamicProgamming13
Date: 2024-11-14
---
# 算法训练营第四十六天 | 647. 回文子串 | 516.最长回文子序列
## 647. 回文子串
求给定字符串下所有回文子串的个数，注意**回文子串必须是连续的**
### 链接
[link](https://leetcode.cn/problems/palindromic-substrings/)
### 关键词
回文，回文子串，动态规划
### 难点
本题如果使用暴力解法，则时间复杂度为n^3，究其原因是每个字符加入后，都需要花费n的时间复杂度判断是否是回文。\
我们发现，回文是通过两边新加入的元素判断的，如果新加入的元素相等而这两个元素夹着的序列也为回文，则可以说明新加入的字符和旧的字符组成的字符串为回文。\
此时，可以发现，**旧的状态就是夹着的序列，新的状态就是新加入的元素组成的序列。可以使用动态规划**。\
由于新加入的两个元素一首一尾，因此需要两个维度保存，dp数组为二维。\
dp[i][j]代表当前i，j范围（左闭右闭）的字符串是否为回文串。**不能定义为范围内含多少个回文串**，否则推不出递推情况\
每找到一个回文串，就将result加一，表示找到了一个。最终的result就是答案。
### 代码
~~~c++
class Solution {
public:
    int countSubstrings(string s) {
        vector<vector<bool>> dp(s.size(), vector<bool>(s.size(), false));
        int result = 0;
        for (int i = s.size() - 1; i >= 0; i--) {  // 注意遍历顺序
            for (int j = i; j < s.size(); j++) {
                if (s[i] == s[j]) {
                    if (j - i <= 1) { // 情况一 和 情况二
                        result++;
                        dp[i][j] = true;
                    } else if (dp[i + 1][j - 1]) { // 情况三
                        result++;
                        dp[i][j] = true;
                    }
                }
            }
        }
        return result;
    }
};
~~~
 516.最长回文子序列
 求给定字符串中的最长回文子序列长度，**子序列不一定是连续的**
 ## 链接
 [link](https://leetcode.cn/problems/longest-palindromic-subsequence/)
 ## 关键词
 回文，回文子序列，动态规划
 ## 难点
 本题在判断回文子序列的情况下，求子序列的最长长度。\
 本体可以设dp[i][j]为i，j范围内的最长子序列长度，这是因为扩大的i，j两个字符与长度有关。\
 如果i == j，则说明新加入的字符串可以组成新的回文子序列，而**回文子序列不要求连续**，因此只需要将内部i+1到j-1的回文子序列加2即可。\
 若不相等，则说明i和j的加入不能增加子序列长度，那就分别加入比较大小。max（dp[i+1][j],dp[i][j-1])
 ## 代码
 ~~~C++
class Solution {
public:
    int longestPalindromeSubseq(string s) {
        vector<vector<int>> dp(s.size(), vector<int>(s.size(), 0));
        for (int i = 0; i < s.size(); i++) dp[i][i] = 1;
        for (int i = s.size() - 1; i >= 0; i--) {
            for (int j = i + 1; j < s.size(); j++) {
                if (s[i] == s[j]) {
                    dp[i][j] = dp[i + 1][j - 1] + 2;
                } else {
                    dp[i][j] = max(dp[i + 1][j], dp[i][j - 1]);
                }
            }
        }
        return dp[0][s.size() - 1];
    }
};
~~~
