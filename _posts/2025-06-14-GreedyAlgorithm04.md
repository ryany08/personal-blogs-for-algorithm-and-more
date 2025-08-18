---
Title: GreedyAlgorithm04
Date: 2025-06-14
---
# 452. 用最少数量的箭引爆气球 | 435. 无重叠区间 |  763.划分字母区间
## 435 用最少数量的箭引爆气球
给定一个区间的集合，找到需要移除区间的最小数量，使剩余区间互不重叠。\
注意: 可以认为区间的终点总是大于它的起点。 区间 [1,2] 和 [2,3] 的边界相互“接触”，但没有相互重叠。\
示例 1:\
输入: [ [1,2], [2,3], [3,4], [1,3] ]\
输出: 1\
解释: 移除 [1,3] 后，剩下的区间没有重叠。\
### 难点
问题要求给出移除的数量，并没有提到与顺序有关的量，因此可以先通过排序改变顺序，使得元素遵循由小到大的顺序，再通过遍历。\
但是每个元素都有两个子元素，优先根据第一个子元素还是第二个子元素进行排序呢？\
如果由第一个子元素排序，会出现一个元素所占空间较大，与多个较小的元素重叠，导致需要移除多个重叠的元素，反而使得需要移除的区间数增多；\
而且是否移除区间也是通过比较前一个区间的末尾与下一个区间的头部达成，因此需要优先以第二个子元素，也就是区间末尾作为比较的标杆。
### 代码
~~~c++
class Solution {
public:
    // 按照区间右边界排序
    static bool cmp (const vector<int>& a, const vector<int>& b) {
        return a[1] < b[1];
    }
    int eraseOverlapIntervals(vector<vector<int>>& intervals) {
        if (intervals.size() == 0) return 0;
        sort(intervals.begin(), intervals.end(), cmp);
        int count = 1; // 记录非交叉区间的个数
        int end = intervals[0][1]; // 记录区间分割点
        for (int i = 1; i < intervals.size(); i++) {
            if (end <= intervals[i][0]) {
                end = intervals[i][1];
                count++;
            }
        }
        return intervals.size() - count;
    }
};
~~~
### 总结
注意，如果优先第一个子元素排序，此代码就无法找出最优解。
## 452. 用最少数量的箭引爆气球
在二维空间中有许多球形的气球。对于每个气球，提供的输入是水平方向上，气球直径的开始和结束坐标。由于它是水平的，所以纵坐标并不重要，因此只要知道开始和结束的横坐标就足够了。开始坐标总是小于结束坐标。\
一支弓箭可以沿着 x 轴从不同点完全垂直地射出。在坐标 x 处射出一支箭，若有一个气球的直径的开始和结束坐标为 xstart，xend， 且满足  xstart ≤ x ≤ xend，则该气球会被引爆。可以射出的弓箭的数量没有限制。\
弓箭一旦被射出之后，可以无限地前进。我们想找到使得所有气球全部被引爆，所需的弓箭的最小数量。\
给你一个数组 points ，其中 points [i] = [xstart,xend] ，返回引爆所有气球所必须射出的最小弓箭数。\
示例 1：\
输入：points = [[10,16],[2,8],[1,6],[7,12]]\
输出：2\
解释：对于该样例，x = 6 可以射爆 [2,8],[1,6] 两个气球，以及 x = 11 射爆另外两个气球\
### 难点
确定比较第一个元素还是第二个元素：由于看需要多少只箭矢就是要看重叠区域的具体分布。而每个重叠区域都以一个区域的第二个子元素和后面区域的第一个子元素判断的。因此还是优先第二个子元素
### 代码
~~~C++
class Solution {
public:
    // 按照区间右边界排序
    static bool cmp (const vector<int>& a, const vector<int>& b) {
        return a[1] < b[1];
    }
    int eraseOverlapIntervals(vector<vector<int>>& intervals) {
        if (intervals.size() == 0) return 0;
        sort(intervals.begin(), intervals.end(), cmp);
        int count = 1; // 记录非交叉区间的个数
        int end = intervals[0][1]; // 记录区间分割点
        for (int i = 1; i < intervals.size(); i++) {
            if (end <= intervals[i][0]) {
                end = intervals[i][1];
                count++;
            }
        }
        return count;
    }
};
~~~
### 总结
如何确定根据拿个子元素进行排序是做此类题目的关键，这两题的判断顺序都是第二个子元素与后面所有元素的第一个子元素的比较，从而找出重叠部分，因此以第二个子元素为准。
