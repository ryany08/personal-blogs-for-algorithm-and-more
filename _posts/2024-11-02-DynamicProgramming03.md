---
Title: DynamicProgramming03
Date: 2024-11-02
---
# 算法训练营第三十四天 | 01背包理论基础 | 416. 分割等和子集
##  01背包理论基础
对于所有的货物，只有选与不选两个选项，这就是所谓的0-1。在这样的条件下，求一定的背包容积下能装入的最大价值
### 链接
[link](https://kamacoder.com/problempage.php?pid=1046)
### 关键词
动态规划
### 难点
理解dp数组的意义。一维的dp数组代表状态的dp[i]表明在内存为i的状态下可以放入的最大价值。经过对每个物品进行遍历，尝试将物品放入背包中，就能够求出最后内存为目标背包内存时能放下的货物最大价值。\
每遍历到一个新物品i，从最大的下标开始向小部分遍历。这是因为当前的dp状态是i-1个物品的最大量。从小到大遍历就是将较小的值掩盖，就会出现物品i被放置多次的情况。
### 代码
~~~C++
#include<iostream>
#include<vector>
#include<cmath>

using namespace std;

int main() {
    int M, N;
    cin>>M>>N;
    vector<int> space, value;
    for(int i = 0; i < M; i++) {
        int sp;
        cin>>sp;
        space.push_back(sp);
    }
    for(int i = 0; i < M; i++) {
        int val;
        cin>>val;
        value.push_back(val);
    }
    vector<int> dp(N + 1, 0);
    for(int i = 0; i < M; i++) {
        for(int j = N; j >= space[i]; j--) {
            dp[j] = max(dp[j], dp[j - space[i]] + value[i]);
        }
    }
    cout<<dp[N];
    return 0;
}
~~~
## 416. 分割等和子集
将一个集合等分，使之和为一半
### 链接
[link](https://leetcode.cn/problems/partition-equal-subset-sum/description/)
### 关键词
动态规划，回溯
### 难点
本质是求一个集合的分割问题，因此可以使用回溯法。\
回溯结束条件是当前的集合的和是总和的一半，所有不符合和正好为一半的情况的结果都会被设为分割不存在\
如果采用动态规划，则首先确定dp数组和dp状态，dp[i]就是指当前子集和为i的情况下，子集的是否存在，为0不存在，为1存在。\
dp的初始状态dp[0]为1，这是因为子集和为0只有默认的一种情况。\
dp转移方程为：dp[j] = dp[j - num] if dp[j] == 0, num为当前选择的集合中的元素
### 代码
~~~c++
class Solution {
public:
    bool canPartition(vector<int>& nums) {
        int sum = 0;
        for(auto num:nums) {
            sum += num;
        }
        if (sum % 2 == 1) {
            return false;
        }
        int half = sum / 2; 
        vector<int> dp(half + 1, 0);
        dp[0] = 1;
        for(auto num: nums) {
            if (dp[half] == 1) {
                return true;
            }
            for(int j = half; j >= num; j--) {
                if (dp[j] == 1) {
                    continue;
                }
                else {
                    dp[j] = dp[j - num];
                }
            }
        }
        return dp[half] == 1 ? true : false;
    }
};
~~~
