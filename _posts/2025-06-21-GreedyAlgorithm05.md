---
Title: GreedyAlgorithm05
Date: 2025-06-21
---
# 56. 合并区间 | 738.单调递增的数字
## 738.单调递增的数字
给定一个非负整数 N，找出小于或等于 N 的最大的整数，同时这个整数需要满足其各个位数上的数字是单调递增。\
（当且仅当每个相邻位数上的数字 x 和 y 满足 x <= y 时，我们称这个整数是单调递增的。）\
示例 1:\
输入: N = 10\
输出: 9
### 难点
最大的递增数列就是尽可能地保持原数列的递增性，在非递增的地方全部填上9。\
只需要知道，一旦出现了下降的位数，就知道从这个地方开始进入非递增顺序。\
由于我们需要知道最开始进入非递增的位置，需要考虑从左读入数列还是从右读入序列。\
从左读入序列的话，为了尽可能保持原数列的递增性同时保证最大，在非递增区域的开头会减一，这个减一会影响前面的数字的大小，就会需要重新读一次左边的数字。\
如果从右侧读入序列，就算减一影响了左边的数字，从右到左的顺序也能够很快地读取并修改左侧地数字\
自然是从右向左移动
### 代码
~~~C++
class Solution {
public:
    int monotoneIncreasingDigits(int N) {
        string strNum = to_string(N);
        // flag用来标记赋值9从哪里开始
        // 设置为这个默认值，为了防止第二个for循环在flag没有被赋值的情况下执行
        int flag = strNum.size();
        for (int i = strNum.size() - 1; i > 0; i--) {
            if (strNum[i - 1] > strNum[i] ) {
                flag = i;
                strNum[i - 1]--;
            }
        }
        for (int i = flag; i < strNum.size(); i++) {
            strNum[i] = '9';
        }
        return stoi(strNum);
    }
};
~~~
### 总结
这道题我最开始有一个错误想法：既然是找最左侧的递减区域的起始部分，那么直接从左到右读入不就好了吗？实际上，由于减一的存在，从左到右读无法处理减一对左侧数字的影响
## 56. 合并区间
给出一个区间的集合，请合并所有重叠的区间。\
示例 1:\
输入: intervals = [[1,3],[2,6],[8,10],[15,18]]\
输出: [[1,6],[8,10],[15,18]]\
解释: 区间 [1,3] 和 [2,6] 重叠, 将它们合并为 [1,6].
### 难点
贪心区间题。经典的左边界排序起手，比较（**比较右边界**）与当前区间的左边界的大小。若大于等于左边界，说明当前区间重叠，可以合并，令当前区间的右区间为**比较右边界**，执行下一个区间的比较。
### 代码
~~~C++
class Solution {
public:
    vector<vector<int>> merge(vector<vector<int>>& intervals) {
        vector<vector<int>> result;
        if (intervals.size() == 0) return result; // 区间集合为空直接返回
        // 排序的参数使用了lambda表达式
        sort(intervals.begin(), intervals.end(), [](const vector<int>& a, const vector<int>& b){return a[0] < b[0];});

        // 第一个区间就可以放进结果集里，后面如果重叠，在result上直接合并
        result.push_back(intervals[0]); 

        for (int i = 1; i < intervals.size(); i++) {
            if (result.back()[1] >= intervals[i][0]) { // 发现重叠区间
                // 合并区间，只更新右边界就好，因为result.back()的左边界一定是最小值，因为我们按照左边界排序的
                result.back()[1] = max(result.back()[1], intervals[i][1]); 
            } else {
                result.push_back(intervals[i]); // 区间不重叠 
            }
        }
        return result;
    }
};
~~~
### 总结
贪心区间题的特征基本就是给定一批区间，通过对区间的左边界进行排序，对区间进行合并，或者找到区间重叠的区域。
