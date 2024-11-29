---
Title: DynamicPrgramming09
Date: 2024-11-09
---
# 算法训练营第四十二天 |  300.最长递增子序列 |  674. 最长连续递增序列 |  718. 最长重复子数组 
## 300.最长递增子序列
给你一个整数数组 nums ，找到其中最长严格递增子序列的长度。\
子序列是由数组派生而来的序列，删除（或不删除）数组中的元素而不改变其余元素的顺序。例如，[3,6,2,7] 是数组 [0,3,1,6,2,2,7] 的子序列。
### 难点
设置dp数组表示以当前下标结尾的子串中最长的严格递增子序列的长度。每个dp[i]都有0 -- i-1的字符决定。
### 代码
~~~C++
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        if (nums.size() <= 1) return nums.size();
        vector<int> dp(nums.size(), 1);
        int result = 0;
        for (int i = 1; i < nums.size(); i++) {
            for (int j = 0; j < i; j++) {
                if (nums[i] > nums[j]) dp[i] = max(dp[i], dp[j] + 1);
            }
            if (dp[i] > result) result = dp[i]; // 取长的子序列
        }
        return result;
    }
};
~~~
## 674. 最长连续递增序列
给定一个未经排序的整数数组，找到最长且 连续递增的子序列，并返回该序列的长度。\
连续递增的子序列 可以由两个下标 l 和 r（l < r）确定，如果对于每个 l <= i < r，都有 nums[i] < nums[i + 1] ，那么子序列 [nums[l], nums[l + 1], ..., nums[r - 1], nums[r]] 就是连续递增子序列
### 难点
本题相对于300，要求必须是连续的序列。要求dp[i]表示以i下表结尾的序列的连续递增最长子序列长度。要求连续，那么就只需要比较两个相邻的元素的大小就好了，如果一旦发现不是递增的情况，就将dp[i]设置为1。因为以i结尾的子串只有当前元素连续递增。
### 代码
~~~C++
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        if (nums.size() <= 1) return nums.size();
        vector<int> dp(nums.size(), 1);
        int result = 0;
        for (int i = 1; i < nums.size(); i++) {
            for (int j = 0; j < i; j++) {
                if (nums[i] > nums[j]) dp[i] = max(dp[i], dp[j] + 1);
            }
            if (dp[i] > result) result = dp[i]; // 取长的子序列
        }
        return result;
    }
};
~~~
##  718. 最长重复子数组
给两个整数数组 A 和 B ，返回两个数组中公共的、长度最长的子数组的长度。
### 难点
本题使用二维dp数组解决。二维dp数组dp[i][j]表示到A的下标i - 1和到B的下标j - 1的情况下的状态变化。注意，本题二维数组初始化大小为AL + 1 x BL + 1（分别表示两个数组的长度）\
目的是方便初始化，对于0行和0列，只需要全部设置为0就可以。
### 代码
~~~C++
class Solution {
public:
    int findLength(vector<int>& nums1, vector<int>& nums2) {
        vector<vector<int>> dp (nums1.size() + 1, vector<int>(nums2.size() + 1, 0));
        int result = 0;
        for (int i = 1; i <= nums1.size(); i++) {
            for (int j = 1; j <= nums2.size(); j++) {
                if (nums1[i - 1] == nums2[j - 1]) {
                    dp[i][j] = dp[i - 1][j - 1] + 1;
                }
                if (dp[i][j] > result) result = dp[i][j];
            }
        }
        return result;
    }
};
~~~
