---
Title: GreedyAlgorithm02
Date: 2024-10-26
---
# 代码随想录第二十七天 | 122.买卖股票的最佳时机 II | 55. 跳跃游戏 | 45.跳跃游戏 II | 1005.K次取反后最大化的数组和
## 122 买卖股票的最佳时机 II
根据每一天的价格买卖股票，求最大的值
### 链接
[link](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-ii/)
### 关键词
贪心，动态规划
### 难点
本题要求求卖出的股票价格最高的情况，其实就是求所给的元素集合中，元素差值的和的最大值。\
这是**因为同一天可以买或卖出股票，但是同一天购买股票的情况必须是在手头没有股票，已经卖出的前提下**。\
这就转化为求所给元素数组的所有正差值。但如果一天只能买或卖一种操作，就无法通过计算正差值求出
### 代码
~~~C++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int result = 0;
        for (int i = 1; i < prices.size(); i++) {
            result += max(prices[i] - prices[i - 1], 0);
        }
        return result;
    }
};
~~~
## 55. 跳跃游戏
