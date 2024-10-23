---
Title: BackTracking03
Date: 2024-10-23
---
# 算法训练营第二十四天 | 93.复原IP地址 | 78.子集 | 90.子集II | 
## 93 复原IP地址
本题和[131 分割回文串](https://leetcode.cn/problems/palindrome-partitioning/)的思路是一致的，都是主动切割元素，判断其是否符合条件，将符合条件的放入数组中暂时存储，再切割下一个元素
### 链接
[link](https://leetcode.cn/problems/restore-ip-addresses/description/)
### 关键词
回溯，切割，组合
### 难点
本题的条件由回文变成了判断字符串是否符合IP地址。一个分割和元素组合如果满足IP地址的条件则**需要严格分割成4份**\
IP地址的判断条件主要有三：长度位于1到3之间；以0开头则只能长度为1；int型实数大小为0到255之前；\
其中前两个条件都很好满足吗，第三个条件需要将字符串先转化为int型数；转化方法可为使用stoi(string s)和以下的方法：\
~~~c++
for (int i = 0; i < s.size(); ++i) { // between 0 and 255
            num = 10 * num + (s[i] - '0');
}
~~~
**根据每一个字符的位置按照十进制转化获取总合，就是求一个数的个个位数的逆运算**。\
本题还有一点不同的地方在于：一个成功的ip地址返回string类型数据，而储存这个ip地址一般需要vector，将ip地址的所有部分存储起来。这就会涉及vector元素转字符串的操作。\
根据回溯公式可以轻易写出：回溯函数**接受带分割字符串和选取元素的开始下标**；一旦切割的元素数量为4开始下标超过字符串范围，说明找到了一个成功的分割，这两个条件缺一不可。只满足其中一个条件就会导致回溯。\
在遍历切割范围寻找元素的过程中，如果发现一个元素不符合ip地址要求，直接回溯剪枝，因为包含这个元素的所有字符串都不会符合。
### 代码
~~~C++
lass Solution {
private:
    vector<string> res;
    vector<string> path;
    bool isValid(string s){
        if(s.size() > 3 || s.size() < 1){
            return false;
        }
        if(s[0] == '0' && s.size() > 1){
            return false;
        }
        int num = 0;
        for (int i = 0; i < s.size(); ++i) { // between 0 and 255
            num = 10 * num + (s[i] - '0');
        }
        if(num > 255){
            return false;
        }
        return true;
    }
    void backtracking(string s, int start){
        if(path.size() == 4){
            if(start == s.size()){ // finished
                string val = "";
                for(auto c:path){
                    val += c;
                    val += '.';
                }
                val.pop_back(); // pop dot from dot
                res.push_back(val);
                return;
            }
            else{
                return;
            }
        }
        string temp = ""; // 相当于开始选取元素
        for(int i = start; i < s.size() && (i - start) < 3; i++){ // branch cutting
            temp += s[i];
            if(isValid(temp)){
                path.push_back(temp);
                backtracking(s, i + 1);
                path.pop_back();
            }
            else{
                return; //这里的return表示这部分的ip不能再往后加了，必须要回溯 branch cutting
            }
        }
    }

public:
    vector<string> restoreIpAddresses(string s) {
        backtracking(s, 0);
        return res;
    }
};
~~~
## 78.子集
求一个集合中的子集，就是组合问题只要求不重复
### 链接
[link](https://leetcode.cn/problems/subsets/description/)
### 关键词
回溯，组合
### 难点
求集合的子集也是一个求组和的问题，只是不要求和为定值，个数为定值的条件。只要求选取的元素不重复\
本题所给的元素集合本身就不包含重复的元素，而且也不是要求手动分割。startindex只需加1即可满足条件。\
由于本体不要求额外条件，每能够加入一个元素就需要将其push入结果数组中，只有在startIndex超过范围之后才会停止。
### 代码
~~~C++
class Solution {
private:
    vector<int> path;
    vector<vector<int>> res;
public:
    void backtracking(vector<int>& nums, int startIndex) {
        if (startIndex == nums.size()) {
            res.push_back(path);
            return;
        }
        res.push_back(path);
        for(int i = startIndex; i < nums.size(); i++) {
            path.push_back(nums[i]);
            backtracking(nums, i + 1);
            path.pop_back();
        }
        return;
    }
    vector<vector<int>> subsets(vector<int>& nums) {
        backtracking(nums, 0);
        return res;
    }
};
~~~
## 90.子集II
包含重复元素的子集问题
### 链接
[link](https://leetcode.cn/problems/subsets-ii/)
### 关键词
回溯，子集
### 难点
本题在子集的基础上添加了元素集合中存在重复元素的设定。既然存在重复元素而又不能要求重复组合，这就**需要先排序是相同的元素相邻，然后通过记录元素是否被使用过防止统一位置（统一回溯树树层）中不会出现相同的元素**\
### 代码
~~~C++
class Solution {
private:
    vector<vector<int>> res;
    vector<int> path;
public:
    void trasverse(vector<int>& nums, int startIndex, vector<bool>& used) {
        if (startIndex == nums.size()) {
            res.push_back(path);
            return;
        }
        res.push_back(path);
        for(int i = startIndex; i < nums.size(); i++) {
            if (i > 0 && nums[i - 1] == nums[i] && used[i - 1] == false) { 
                continue;
            }
            used[i] = true;
            path.push_back(nums[i]);
            trasverse(nums, i + 1, used);
            path.pop_back();
            used[i] = false;
        }
        return;
    }
    vector<vector<int>> subsetsWithDup(vector<int>& nums) {
        vector<bool> used(nums.size(), false);
        sort(nums.begin(), nums.end());
        trasverse(nums, 0, used);
        return res;
    }
};
~~~
