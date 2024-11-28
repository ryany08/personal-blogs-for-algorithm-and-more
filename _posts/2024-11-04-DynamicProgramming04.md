---
Title: DynamicProgramming04
Date: 2024-11-04
---
# 算法训练营第三十六天 | 1049.最后一块石头的重量II | 494.目标和 | 474.一和零
##  1049.最后一块石头的重量II（二刷没做出）
每两块石头相互碰撞，求碰撞后的质量的最小值
### 链接
[link](https://leetcode.cn/problems/last-stone-weight-ii/)
### 关键词
动态规划，拆分
### 难点
本体的难点关键是正确认清石头碰撞代表的含义。\
如果只看到表面的石头碰撞这一层，就很难想到解决方法。\
但是如果从较小的石头数为2开始推导，两颗石头相互碰撞结束；三颗石头相互碰撞，首先选两颗碰撞，剩下一颗石头与碰撞后质量大于等于0的石头相碰，三颗石头的问题就变化成了两颗石头的问题。同理到n颗石头，分成两组，最后转化为二颗石头的问题，\
为了使得两颗石头相撞最小，就要让他们之间的质量相差最小，也就是尽量平分总质量。这样n颗石头问题就转化成了分成两堆，使得两堆的质量相差最小，最靠近总质量的一半。类似于[416. 分割等和子集](https://leetcode.cn/problems/partition-equal-subset-sum/)\
因此可以得到**dp数组代表内存一定的条件下所能放置的最大重量**。
### 代码
~~~C++
class Solution {
public:
    int lastStoneWeightII(vector<int>& stones) {
        vector<int> dp(15001, 0);
        int sum = 0;
        for (int i = 0; i < stones.size(); i++) sum += stones[i];
        int target = sum / 2;
        for (int i = 0; i < stones.size(); i++) { // 遍历物品
            for (int j = target; j >= stones[i]; j--) { // 遍历背包
                dp[j] = max(dp[j], dp[j - stones[i]] + stones[i]);
            }
        }
        return sum - dp[target] - dp[target];
    }
};
~~~
## 494.目标和
给定一个数组，求出为这个数组中的数添加正负符号的情况下，满足和为定值的情况。
### 链接
[link](https://leetcode.cn/problems/target-sum/)
### 关键词
动态规划，回溯
### 难点
本题使用不同维度的dp数组可以有不同的解法：一维dp数组只能从后往前读，并只能通过前项得出后项的状态。而二维数组由于可以记录目标元素前面所有元素的情况，因此可以从两边遍历。\
因此，在一维dp数组的情况下，就**需要对数据进行处理，使其转化为仅仅通过单方面就能进行转移的状态**。\
所给的元素其实可以**分为加上正号和加上负号的两部分，这两部分的和相减就是目标值**\
dp数组**代表一定内存条件下填满内存的组合的个数，因此dp初始值为1**
### 代码
一维dp代码
~~~C++
class Solution {
public:
    int findTargetSumWays(vector<int>& nums, int target) {
        int sum = 0;
        for (int i = 0; i < nums.size(); i++) sum += nums[i];
        if (abs(target) > sum) return 0; // 此时没有方案
        if ((target + sum) % 2 == 1) return 0; // 此时没有方案
        int bagSize = (target + sum) / 2;
        vector<int> dp(bagSize + 1, 0);
        dp[0] = 1;
        for (int i = 0; i < nums.size(); i++) {
            for (int j = bagSize; j >= nums[i]; j--) {
                dp[j] += dp[j - nums[i]];
            }
        }
        return dp[bagSize];
    }
};
~~~
二维dp代码
~~~JavaScript
function findTargetSumWays(nums: number[], target: number): number {
    let sum = nums.reduce((a, b) => a + b);
    let size: number = nums.length;
    let dp: number[][] = new Array(size).fill(0).map(_ => new Array(2001).fill(0));

    dp[0][1000 + nums[0]] = 1;
    dp[0][1000 - nums[0]] += 1;

    for(let i = 1; i < size; i++) {
        for(let j = 1000 - sum; j <= 1000 + sum; j++) {
            dp[i][j] = (j - nums[i] >= 0 ? dp[i - 1][j - nums[i]] : 0) + (j + nums[i] < 2001 ? dp[i - 1][j + nums[i]] : 0);
        }
    }

    return dp[size - 1][1000 + target];
}
~~~
## 474.一和零
将滚动dp放大到二维，求组合成最多维m个0和n个1的最大情况
### 链接
[link](https://leetcode.cn/problems/ones-and-zeroes/description/)
### 关键词
动态规划，回溯
### 难点
本题将以往的一维滚动dp转化为了二维，其原因是本题需要满足最多m个0和n个1的条件。\
dp数组的含义是dp[i][j]下，最多i个0和j个1的条件下最大的子集元素的个数。dp初始值为dp[0][0] = 0（因为最开始没有任何元素被加入其中）
### 代码
~~~C++
class Solution {
public:
    void get_0_and_1(string &str, int& num_0, int& num_1) {
        for(auto c: str) {
            if (c == '0') {
                num_0++;
            }
            else {
                num_1++;
            }
        }
    }
    int findMaxForm(vector<string>& strs, int m, int n) {
        vector<vector<int>> dp(m + 1, vector<int> (n + 1, 0));
        for(auto str: strs) {
            int num_0 = 0, num_1 = 0;
            get_0_and_1(str, num_0, num_1);
            for(int i = m; i >= num_0; i--)  {
                for(int j = n; j >= num_1; j--) {
                    dp[i][j] = max(dp[i][j], dp[i - num_0][j - num_1] + 1);
                }
            }
        }
        return dp[m][n];
    }
};
~~~



