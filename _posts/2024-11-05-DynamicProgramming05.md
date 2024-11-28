# 代码随想录 | 完全背包 |  518. 零钱兑换 II  | 377. 组合总和 Ⅳ | 70. 爬楼梯 （进阶）
## 完全背包
完全背包问题就是0-1背包的升级版，不同的是，完全背包中的物件不止为1件，可以为很多件。注意由于物品种类无限，有**可能会超出int整数范围**
## 难点
dp数组还是和0-1背包一致，只是需要从小容量到大容量去遍历，从大容量中遍历的话，无法将一种物品重复放入，而从小到大，就能够用到大容量背包去重复装一个物品。
## 代码理解
~~~c++
#include <iostream>
#include <vector>
using namespace std;

// 先遍历背包，再遍历物品
void test_CompletePack(vector<int> weight, vector<int> value, int bagWeight) {

    vector<int> dp(bagWeight + 1, 0);

    for(int j = 0; j <= bagWeight; j++) { // 遍历背包容量
        for(int i = 0; i < weight.size(); i++) { // 遍历物品
            if (j - weight[i] >= 0) dp[j] = max(dp[j], dp[j - weight[i]] + value[i]);
        }
    }
    cout << dp[bagWeight] << endl;
}
int main() {
    int N, V;
    cin >> N >> V;
    vector<int> weight;
    vector<int> value;
    for (int i = 0; i < N; i++) {
        int w;
        int v;
        cin >> w >> v;
        weight.push_back(w);
        value.push_back(v);
    }
    test_CompletePack(weight, value, V);
    return 0;
}
~~~
## 518. 零钱兑换 II
给定不同面额的硬币和一个总金额。写出函数来计算可以凑成总金额的硬币组合数。假设每一种面额的硬币有**无限个**
### 难点
经典无穷背包问题，无穷个硬币对应无穷个数量
### 代码
~~~c++
class Solution {
public:
    int change(int amount, vector<int>& coins) {
        vector<int> dp(amount + 1, 0);
        dp[0] = 1; // 只有一种方式达到0
        for (int i = 0; i < coins.size(); i++) { // 遍历物品
            for (int j = coins[i]; j <= amount; j++) { // 遍历背包
                if (dp[j] < INT_MAX - dp[j - coins[i]]) { //防止相加数据超int
                    dp[j] += dp[j - coins[i]];
                }
            }
        }
        return dp[amount]; // 返回组合数
    }
};
~~~
## 377. 组合总和 Ⅳ
给定一个由正整数组成且不存在重复数字的数组，找出和为给定目标正整数的组合的个数
### 难点
看似是求组合，其实是求排序，因为求出的组合中，元素不同顺序就是新的组合。而求排序就需要优先遍历容量，再遍历物品种类
### 代码
~~~c++
class Solution {
public:
    int combinationSum4(vector<int>& nums, int target) {
        vector<int> dp(target + 1, 0);
        dp[0] = 1;
        for (int i = 0; i <= target; i++) { // 遍历背包
            for (int j = 0; j < nums.size(); j++) { // 遍历物品
                if (i - nums[j] >= 0 && dp[i] < INT_MAX - dp[i - nums[j]]) {
                    dp[i] += dp[i - nums[j]];
                }
            }
        }
        return dp[target];
    }
};
~~~
## 70. 爬楼梯 （进阶）
假设你正在爬楼梯。需要 n 阶你才能到达楼顶。
每次你可以爬至多m (1 <= m < n)个台阶。你有多少种不同的方法可以爬到楼顶呢？
### 难点
将爬楼梯问题转化为背包问题，到达哪一层，就表示哪一个背包容量中，每次前进1-m步，就表示向里面放入物品。因此本题为遍历背包容量先于遍历物品种类。
### 代码
~~~C++
#include <iostream>
#include <vector>
using namespace std;
int main() {
    int n, m;
    while (cin >> n >> m) {
        vector<int> dp(n + 1, 0);
        dp[0] = 1;
        for (int i = 1; i <= n; i++) { // 遍历背包
            for (int j = 1; j <= m; j++) { // 遍历物品
                if (i - j >= 0) dp[i] += dp[i - j];
            }
        }
        cout << dp[n] << endl;
    }
}
~~~
