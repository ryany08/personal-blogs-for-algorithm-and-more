---
Title: DynamicProgramming08
Date: 2024-11-08
---
# 算法训练营第四一十天 |  188.买卖股票的最佳时机IV  | 309.最佳买卖股票时机含冷冻期 | 714.买卖股票的最佳时机含手续费 
##  188.买卖股票的最佳时机IV
给定一个整数数组 prices ，它的第 i 个元素 prices[i] 是一支给定的股票在第 i 天的价格\
设计一个算法来计算你所能获取的最大利润。你最多可以完成 k 笔交易。
### 难点
由买卖股票的最佳时机3（2次交易）升级到了k次交易，也就是交易次数可变。但是dp数组的设定的状态改为了2*k + 1 次，每次交易都有买入卖出。最后加上一个最开始的状态
### 代码
~~~C++
class Solution {
public:
    int maxProfit(int k, vector<int>& prices) {

        if (prices.size() == 0) return 0;
        vector<vector<int>> dp(prices.size(), vector<int>(2 * k + 1, 0));
        for (int j = 1; j < 2 * k; j += 2) {
            dp[0][j] = -prices[0];
        }
        for (int i = 1;i < prices.size(); i++) {
            for (int j = 0; j < 2 * k - 1; j += 2) {
                dp[i][j + 1] = max(dp[i - 1][j + 1], dp[i - 1][j] - prices[i]);
                dp[i][j + 2] = max(dp[i - 1][j + 2], dp[i - 1][j + 1] + prices[i]);
            }
        }
        return dp[prices.size() - 1][2 * k];
    }
};
~~~
## 309.最佳买卖股票时机含冷冻期
给定一个整数数组，其中第 i 个元素代表了第 i 天的股票价格 。
设计一个算法计算出最大利润。在满足以下约束条件下，你可以尽可能地完成更多的交易（多次买卖一支股票）:\
你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。\
卖出股票后，你无法在第二天买入股票 (即冷冻期为 1 天)
### 难点
新增加了一个买卖股票冷静期的操作，也就是说买入股票的操作只能由大前天的卖出（非持有）和昨天的买入（持有）决定，无法由昨天的卖出（非持有）决定。\
因此状态的转化上更为复杂
### 代码
~~~C++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        if (n == 0) return 0;
        vector<vector<int>> dp(n, vector<int>(4, 0));
        dp[0][0] -= prices[0]; // 持股票
        for (int i = 1; i < n; i++) {
            dp[i][0] = max(dp[i - 1][0], max(dp[i - 1][3] - prices[i], dp[i - 1][1] - prices[i]));
            dp[i][1] = max(dp[i - 1][1], dp[i - 1][3]);
            dp[i][2] = dp[i - 1][0] + prices[i];
            dp[i][3] = dp[i - 1][2];
        }
        return max(dp[n - 1][3], max(dp[n - 1][1], dp[n - 1][2]));
    }
};
~~~
##  714.买卖股票的最佳时机含手续费 
给定一个整数数组 prices，其中第 i 个元素代表了第 i 天的股票价格 ；非负整数 fee 代表了交易股票的手续费用。\

你可以无限次地完成交易，但是你每笔交易都需要付手续费。如果你已经购买了一个股票，在卖出它之前你就不能再继续购买股票了。\

返回获得利润的最大值。
### 难点
在基础的买卖股票的状态下添加了一个交易的手续费，就是在**卖出时的收入上额外减去一个手续费**，或是**买入时多减去一个手续费**
### 代码
~~~C++
class Solution {
public:
    int maxProfit(vector<int>& prices, int fee) {
        int n = prices.size();
        vector<vector<int>> dp(n, vector<int>(2, 0));
        dp[0][0] -= prices[0]; // 持股票
        for (int i = 1; i < n; i++) {
            dp[i][0] = max(dp[i - 1][0], dp[i - 1][1] - prices[i]);
            dp[i][1] = max(dp[i - 1][1], dp[i - 1][0] + prices[i] - fee);
        }
        return max(dp[n - 1][0], dp[n - 1][1]);
    }
};
~~~
