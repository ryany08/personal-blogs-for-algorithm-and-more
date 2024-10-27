---
Title: GreedyAlgorithm01
Date: 2024-10-25
---
# 算法训练营第二十六天 ｜ 455.分发饼干｜ 376. 摆动序列 ｜ 53. 最大子序和
开始贪心算法的学习，贪心算法是一个求局部最大值从而得到全局最大值的方法，其中的子问题不出现重复
## 455 发放饼干
为每个孩子分发且仅分发一块饼干，求最多能满足多少人
### 链接 
[link](https://leetcode.cn/problems/assign-cookies/description/)
### 关键词
贪心，排序
### 难点
贪心的目的是**通过局部最优解推出全局最优解，前提是子问题不能重复**。\
本题的是某块饼干分给某个孩子，本题的子问题不会重复是因为每块饼干只会被分给一个孩子，每个孩子也只会得到最多一块饼干。\
全局最优解就是这块饼干在满足孩子胃口的条件下会尽可能剩下最少的残渣，如果剩下的残渣很多，这些饼干可以重新分配，到达分配给更多人的目的。
### 代码
～～～C++
～～～
## 376 摆动序列
求一个序列中上下摆动的元素序列的长度最大值，这个序列**由非负整数组成**
### 链接
[link](https://leetcode.cn/problems/wiggle-subsequence/)
### 关键词
贪心
### 难点
由于没有想出使用贪心同时删除节点和计算最大值的方法，故采用题解的解法，并作分析\
由于本题是求节点删除后的数组总长，其实也**不需要主动删除节点，跳过该节点计算长度就好**\
最开始为了比较每个差值是否上下起伏打算采用和flag模拟记录增减。但是发现：**flag不好处理首尾为平坡的状态**；\
![image](https://github.com/user-attachments/assets/94721b54-5e3a-485d-9f16-3abef52444c2)\
还是使用差值直接判断是否上下起伏\
这就需要判断前差值和后差值，**一旦前差值和后差值的正负不同，就说明完成一次起伏，最终数组的长度需要加一**，长度起始值为1（**原数组长度为2及以上，默认结尾元素是一个从峰顶下降的元素，所以初始值为1**），后差值初始为0（**假设从第一第二个元素开始算前差值**）。\
由于**每经过一个节点就需要判断它的前差值和后差值，所以遍历节点的下标在倒数第二个元素时就会结束**\
如何处理非起伏情况（差值为0，单调递增或递减）：只有当差值符号相反的情况下才说明当前元素不删除，res + 1且更新后差值。\
把差值为0纳入考虑范围：如果前差值为0而后插值不为0，该节点也可保留，这实际上是将平坡（差值为0的一系列元素）的最后一个元素视为顶峰或谷底，这种情况只会出现在元素集合头部\
后插值为0的情况下不会将该节点纳入结果中。\
本题的局部最优就是遍历每个元素，查看其是否是摆动序列的一员，如果是则加入结果序列，保证所有所有加入结果序列中的元素都是摆动序列中的一员，则结果序列就是最大的序列
### 代码
~~~C++
class Solution {
private:
    int res = 1;
public:
    int wiggleMaxLength(vector<int>& nums) {
        res = 1;
        if (nums.size() == 1) return res;
        int preDiff = 0;
        int nextDiff = 0;
        for(int i = 0; i < nums.size() - 1; i++) {
            nextDiff = nums[i + 1] - nums[i];
            if ((nextDiff > 0 && preDiff <= 0) || (nextDiff < 0 && preDiff >= 0)) {
                res += 1;
                preDiff = nextDiff;
            }
        }
        return res;
    }
};
~~~
## 53. 最大子序和
求序列中连续部分的最大值
### 链接
[link](https://leetcode.cn/problems/maximum-subarray/)
### 关键词
贪心
### 难点
本题使用贪心算法的解法，局部最优就是如果当前子序和为负数，则立刻抛弃当前和，从下一个数开始计算和\
使用一个全局变量记录当前的子序和的大小，初始值为0，因为**其最开始什么值都不记录**。
### 代码
~~~c++
class Solution {
private:
    int res = 1;
public:
    int wiggleMaxLength(vector<int>& nums) {
        res = 1;
        if (nums.size() == 1) return res;
        int preDiff = 0;
        int nextDiff = 0;
        for(int i = 0; i < nums.size() - 1; i++) {
            nextDiff = nums[i + 1] - nums[i];
            if ((nextDiff > 0 && preDiff <= 0) || (nextDiff < 0 && preDiff >= 0)) {
                res += 1;
                preDiff = nextDiff;
            }
        }
        return res;
    }
};
~~~



