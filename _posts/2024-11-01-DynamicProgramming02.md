---
Title: DynamicProgramming02
Date: 2024-11-01
---
# 算法训练营 第三十三天 | 62.不同路径 | 63. 不同路径 II | 343. 整数拆分 | 96.不同的二叉搜索树
## 62.不同路径 
只能向右或向下走，求到达某点的最多路径种类
### 链接
[link](https://leetcode.cn/problems/unique-paths/description/)
### 关键词
动态规划
### 难点
经典的通过路径进行状态转移的动态规划问题，每个点的路径总和就是它左边的点加上它上面的点的路径总和（除了第一行或第一列的点），**第一层和第一列的点路径数只能为1**\
可以写出状态转义方程：dp[i][j] = dp[i-1][j] + dp[i][j-1]
### 代码
~~~C++
class Solution {
public:
    int uniquePaths(int m, int n) {
        vector<vector<int>> dp(m, vector<int>(n, 0));
        dp[0][0] = 1;
        for(int i = 0; i < m; i++) {
            for(int j = 0; j < n; j++) {
                if (i > 0 && j > 0) {
                    dp[i][j] += dp[i - 1][j];
                    dp[i][j] += dp[i][j - 1];
                    
                }
                else if(j == 0 && i > 0) {
                    dp[i][j] += dp[i - 1][j];
                }
                else if(i == 0 && j > 0) {
                    dp[i][j] += dp[i][j - 1];
                }
            }
        }
        return dp[m - 1][n - 1];
    }
};
~~~
## 63. 不同路径 II
对于基本的不同路径问题，增加了路障的特殊条件，无法访问路障点也无法从路障点进行状态转移
### 链接
[link](https://leetcode.cn/problems/unique-paths-ii/)
### 关键词
动态规划
### 难点
由于有路障存在，一旦出现路障，就跳过该点，将该点的路径数设为0，自然而然就将来自路障的转移隔绝了
### 代码
~~~C++
class Solution {
public:
    int uniquePathsWithObstacles(vector<vector<int>>& obstacleGrid) {
        int m = obstacleGrid.size();
        int n = obstacleGrid[0].size();
        vector<vector<int>> dp(m, vector<int>(n, 0));
        if (obstacleGrid[0][0] != 1) {
            dp[0][0] = 1;
        }
        for(int i = 0; i < m; i++) {
            for(int j = 0; j < n; j++) {
                if (obstacleGrid[i][j] == 1) continue;
                if (i > 0 && j > 0) {
                    if (obstacleGrid[i - 1][j] != 1 && obstacleGrid[i][j - 1] != 1) {
                        dp[i][j] += dp[i - 1][j];
                        dp[i][j] += dp[i][j - 1];
                        continue;
                    }
                    if (obstacleGrid[i - 1][j] != 1) {
                        dp[i][j] += dp[i - 1][j];
                        continue;
                    }
                    if (obstacleGrid[i][j - 1] != 1) {
                        dp[i][j] += dp[i][j - 1];
                        continue;
                    }
                }
                else if(j == 0 && i > 0) {
                    if (obstacleGrid[i - 1][j] != 1) {
                        dp[i][j] += dp[i - 1][j];
                    }
                }
                else if(i == 0 && j > 0) {
                    if (obstacleGrid[i][j - 1] != 1) {
                        dp[i][j] += dp[i][j - 1];
                    }
                }
            }
        }
        return dp[m - 1][n - 1];
    }
};
~~~
## 343. 整数拆分
将一个数拆分成多个和为该数的数，求最大的乘积
### 链接
[link](https://leetcode.cn/problems/integer-break/description/)
### 关键词
动态规划
### 难点
本题难点在于想到状态的含义。这个状态就是能拆分成的数的最大乘积的表示。一旦面对新的数，就能通过比他小的数的dp状态来得出是否需要继续分，或者只是保留两个数。\
本题最低要求分成两个数，即i和j - i，求i * j的乘积，再细分就是dp[i]和dp[j]，就将i和j分成了两个以上的数。\
因此状态转移函数时：dp[i] = max({dp[i], (j - i) * i, dp[j - i] * i});\
这里比较原生dp元素和区分成两份i * j - i和区分成三分以上的dp[j - i] * i\
不区分dp[i]的原因是，dp[i]作为偏小的一方，区分下对结果的影响不大
### 代码
~~~c++
class Solution {
public:
    int integerBreak(int n) {
        vector<int> dp(n + 1, 0);
        dp[1] = 1;
        for(int i = 2; i <= n; i++) {
            for(int j = 1; j <= i / 2; j++)  {
                dp[i] = max(dp[i], max((i - j) * j, dp[i - j] * j));
            }
        }
        return dp[n];
    }
};
~~~
## 96.不同的二叉搜索树（二刷未做出）
二叉树与动态规划的结合，寻找给定节点数的最多二叉搜索数类数
### 链接
[link](https://leetcode.cn/problems/unique-binary-search-trees/)
### 关键词
动态规划
### 难点
二刷未做出的一题\
这道题的关键是找出添加一个节点后，新二叉树如何从之前的二叉树产生的状态。\
给定一个数n，求由1-n，n个节点组成的二叉搜索树的个数。\
以1-n，n个节点作为父节点，剩下的n-1节点就负责组成左右两子树。这n-1个节点通过父节点的序号分成两个部分。\
由于二叉搜索树遵循中序遍历为非递减序列的要求，即使作为子树时，这个要求也成立，这就可以化为dp数组中对应元素总个数的最大二叉搜索数个数。
因此转移方程为：dp[i] = {dp[k - 0] * dp[i - k] } k = 1,2,...,i
### 代码
~~~c++
class Solution {
public:
    int numTrees(int n) {
        vector<int> dp(n + 1);
        dp[0] = 1;
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= i; j++) {
                dp[i] += dp[j - 1] * dp[i - j];
            }
        }
        return dp[n];
    }
};
~~~
