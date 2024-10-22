---
Title: BackTracking02
Date: 2024-10-22
---
# 算法训练营第二十三天 | 39. 组合总和 | 40.组合总和II | 131.分割回文串
今天的题目是是组合问题的变种，扩展了条件。例如不限制元素的重复等（**但由于是组合问题，仍不包括元素相同而顺序不同的情况**）
## 39. 组合总和
数组内待选元素不重复，可重复选择待选元素
### 链接
[link](https://leetcode.cn/problems/combination-sum/)
### 关键词
回溯，组合
### 难点
本题在昨日的组合问题的基础上扩充/放宽了条件。要求在不重复的待选元素中，可重复地选出符合和为定值的一组元素，不拘泥于选出的元素个数。\
虽然可以重复选取相同元素，但由于待选元素的集合中不会出现重复元素，因此不需要考虑重复选取元素而带来的组合重复的可能性。\
因此只需要将允许选择元素的下标改为不变就行，因为可以重复选取同意元素，选完当前元素后，下一个元素的选取范围并不一定需要+1。
### 代码
~~~c++
class Solution {
private:
    vector<vector<int>> res;
    vector<int> tem;

    void backtracking(vector<int>& candidates, int target, int presum, int idx){
        if(presum == target){
            res.push_back(tem);
            return;
        }
        if(presum > target){ // branch cutting
            return;
        }
        for(int i = idx; i < candidates.size(); i++){
            presum += candidates[i];
            tem.push_back(candidates[i]);
            backtracking(candidates, target, presum, i); // index stays still
            tem.pop_back();
            presum -= candidates[i];
        }
    }

public:
    vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
        backtracking(candidates, target, 0, 0);
        return res;
    }
};
~~~
## 40.组合总和II
### 链接
[link](https://leetcode.cn/problems/combination-sum-ii/description/)
### 关键词
回溯，组合
### 难点
本题是组合总和的升级版，本题的**待选数包括重复的元素**，因此要求不能对一个元素重复选择，但可以选择重复出现的元素。\
为了不重复选择同一个元素，下个元素的其实范围就会需要自动+1。\
为了不在同一层，也就是for循环时选出相同的数，需要使用一个used数组记录是否被使用过，每找到一个数，都需要对这个数标记已经使用过，然后继续找下一个数；如果遇见相同的数且有多个相同的数没有被使用过，说明这些数在没有在前一个数中被使用过，而是集中在当前层的for循环中了\
for循环如果在同一个位置放入重复的元素就会出现重复的组合，因此需要往下一个数去寻找。
### 代码
~~~c++
class Solution {
private:
vector<vector<int>> res;
vector<int> path;

void backtracking(vector<int>& candidates, int target, int start, int presum, vector<int>& used){
    if(presum == target){
        res.push_back(path);
        return;
    }
    if(presum > target){ // branch cutting
        return;
    }
    for(int i = start; i < candidates.size(); i++){
        if(i > 0 && candidates[i] == candidates[i-1] && used[i-1] == 0){
            continue;
        }
        presum += candidates[i];
        used[i] = 1;
        path.push_back(candidates[i]);
        backtracking(candidates, target, start + 1 , presum, used);
        path.pop_back();
        used[i] = 0;
        presum -= candidates[i];
    }
}

public:
    vector<vector<int>> combinationSum2(vector<int>& candidates, int target) {
        vector<int> used(candidates.size(), 0);
        sort(candidates.begin(), candidates.end());
        backtracking(candidates, target, 0, 0, used);
        return res;
    }
};
~~~
## 131.分割回文串
切割问题就是组合问题，因为切割问题不在乎顺序。
### 链接
[link](https://leetcode.cn/problems/palindrome-partitioning/description/)
### 关键词
回溯，切割，组合
### 难点
本题说是切割字符串，其实就是**把字符串的集合中的元素拿来组合**，使其满足每个子元素都是回文，且子元素相加为原来的字符串。\
难点在于选出的子字符串要如何添加入回溯中。\
之前的组合题目都是直接给出了切分好的元素集合，只需要从元素范围中选出一个就好。\
而本题需要自己切分元素，分成集合。\
也就是说本题**从可以开始切分的下标开始，需要自己切分元素直到超出允许切分的范围**，然后将切好的元素进行判断其是否属于回文，再将其如同其他组合题目一样放入暂时存放的数组中。\
找到这样一个切割元素的条件是：**下一个可选元素的范围已经超过字符串范围**。
### 代码
~~~c++
class Solution {
private:
    vector<vector<string>> res;
    vector<string> path;
    bool isPalindrome(string s){
        int front = 0;
        int rear = s.size() - 1;
        while(front < rear){
            if(s[front] != s[rear]){
                return false;
            }
            front += 1;
            rear -= 1;
        }
        return true;
    }
    void backtracking(string s, int start){
        if(start == s.size()){
            res.push_back(path);
            return;
        }
        if (start > s.size()) {
            return;
        }
        string temp = "";
        for(int i = start; i < s.size(); i++){
            temp += s[i]; // slice off an element
            if(isPalindrome(temp)){ // before inserting it into accumulated path
                path.push_back(temp);
                backtracking(s, i + 1);
                path.pop_back();
            }
        }
    }
public:
    vector<vector<string>> partition(string s) {
        backtracking(s, 0);
        return res;
    }
};
~~~
