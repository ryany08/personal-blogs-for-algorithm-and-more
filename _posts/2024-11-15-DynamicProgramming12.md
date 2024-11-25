---
Title: DyanmicProgramming12
Date: 2024-11-15
---
# 算法训练营第四十五天 | 115 不同子序列 ｜ 583 两个字符串的删除操作 ｜ 72 编辑距离
## 115 不同子序列
给定两个字符串s和t，计算s中出现t的个数，其中t出现在s中的元素可以不连续
### 关键词
动态规划，子序列
### 难点
这道题的难点就是如何处理不连续的子序列。连续的子序列可以通过构造二维dp数组[i][j]来表示，其中i是和j是两个序列的长度。一个二维数组可以表示两个序列的所有情况。/
为什么可以使用动态规划，因为动态规划是找出**重叠子问题**中的解的算法，重叠就代表状态会通过状态转化而来。而母序列中任意位置的子序列个数都会影响到下个位置的子序列个数，因此可以使用动态规划。\
因此，定义dp[i][j] = s下标为i-1和t下标为j-1的情况下，t的个数。
状态转移方程为：当s[i - 1] = t[j - 1]时dp[i][j] = dp[i - 1][j - 1] + dp[i - 1][j]，（之所以需要加上[i - 1][j]是因为s中可能出现相同字符按照紧密排列的情况，这种情况下需要考虑不使用当前字符匹配的情况）；
当不相等时：dp[i][j] = dp[i - 1][j]
### 代码
~~~C++
class Solution {
public:
    int numDistinct(string s, string t) {
        vector<vector<uint64_t>> dp(s.size() + 1, vector<uint64_t>(t.size() + 1));
        for (int i = 0; i < s.size(); i++) dp[i][0] = 1;
        for (int j = 1; j < t.size(); j++) dp[0][j] = 0;
        for (int i = 1; i <= s.size(); i++) {
            for (int j = 1; j <= t.size(); j++) {
                if (s[i - 1] == t[j - 1]) {
                    dp[i][j] = dp[i - 1][j - 1] + dp[i - 1][j];
                } else {
                    dp[i][j] = dp[i - 1][j];
                }
            }
        }
        return dp[s.size()][t.size()];
    }
};
~~~
## 583 两个字符串的删除操作
给定两个单词 word1 和 word2，找到使得 word1 和 word2 相同所需的最小步数，每步可以删除任意一个字符串中的一个字符
### 关键词
动态规划，子序列差值
### 难点
比较两个字符串，两个字符串之间的部分字符串对比就属于一个状态，状态之间相互影响（因为最终结果是由两个字符串全长度比较下的最小变动次数）
定义dp数组dp[i+1][j+1],i为word1长度，j为word2长度。dp[i][j]：以i-1为结尾的字符串word1，和以j-1位结尾的字符串word2，想要达到相等，所需要删除元素的最少次数。
状态转移方程就是：当word1[i - 1] 与 word2[j - 1]相同的时候，dp[i][j] = dp[i - 1][j - 1]；
当不相同的时候有三种情况：
情况一：删word1[i - 1]，最少操作次数为dp[i - 1][j] + 1
情况二：删word2[j - 1]，最少操作次数为dp[i][j - 1] + 1
情况三：同时删word1[i - 1]和word2[j - 1]，操作的最少次数为dp[i - 1][j - 1] + 2。
求这三种情况的最小值。
自然初始化dp数组为：对i=0和j=0的情况下，分别记dp[0][j] = j和dp[i][0] = i。
### 代码
~~~C++
class Solution {
public:
    int minDistance(string word1, string word2) {
        vector<vector<int>> dp(word1.size() + 1, vector<int>(word2.size() + 1));
        for (int i = 0; i <= word1.size(); i++) dp[i][0] = i;
        for (int j = 0; j <= word2.size(); j++) dp[0][j] = j;
        for (int i = 1; i <= word1.size(); i++) {
            for (int j = 1; j <= word2.size(); j++) {
                if (word1[i - 1] == word2[j - 1]) {
                    dp[i][j] = dp[i - 1][j - 1];
                } else {
                    dp[i][j] = min(dp[i - 1][j] + 1, dp[i][j - 1] + 1);
                }
            }
        }
        return dp[word1.size()][word2.size()];
    }
};
~~~
## 72 编辑距离
给你两个单词 word1 和 word2，请你计算出将 word1 转换成 word2 所使用的最少操作数，这些操作包括增加，删除，替换
### 关键词
动态规划，子序列差值
### 难点
本题与**583 两个字符串的删除操作**类似。不同的是除了删除操作外，还新增加了添加和替换操作，并且此时只能对其中一个元素进行操作。\
dp数组继续定义为以i-1为结尾的字符串word1，和以j-1位结尾的字符串word2，想要达到相等，所需要操作元素的最少次数。\
状态转移方程为：当word1[i - 1] 与 word2[j - 1]相同的时候，dp[i][j] = dp[i - 1][j - 1]；\
当不相同的时候有三种情况：增加，删除，替换\
增加的时候：word1增加，此时原来的word1的i加1，记为i1，word2的j下表不变。原来的i对应于j-1，因此可以转化为dp[i][j] = dp[i][j - 1] + 1\
减少的时候：word1的i-1，对应word2的j，word2不变：dp[i][j] = dp[i - 1][j] + 1\
替换的时候：由于此时i下标所指代的值被替换成与j指代的值一样，i无法再和j进行比较，转而只能使用没有变化的i-1和j-1进行比较：dp[i][j] = dp[i - 1][j - 1] + 1\
初始化dp数组时，与前一题方式一致，当任意一个i或j为0时，最少次数就是另一个index的长度。
### 代码
~~~C++
class Solution {
public:
    int minDistance(string word1, string word2) {
        vector<vector<int>> dp(word1.size() + 1, vector<int>(word2.size() + 1, 0));
        for (int i = 0; i <= word1.size(); i++) dp[i][0] = i;
        for (int j = 0; j <= word2.size(); j++) dp[0][j] = j;
        for (int i = 1; i <= word1.size(); i++) {
            for (int j = 1; j <= word2.size(); j++) {
                if (word1[i - 1] == word2[j - 1]) {
                    dp[i][j] = dp[i - 1][j - 1];
                }
                else {
                    dp[i][j] = min({dp[i - 1][j - 1], dp[i - 1][j], dp[i][j - 1]}) + 1;
                }
            }
        }
        return dp[word1.size()][word2.size()];
    }
};
~~~
