---
Title: BackTracking01
Date: 2024-10-21
---
# 算法训练营第二十二天 | 77题. 组合 | 216.组合总和III | 17.电话号码的字母组合
回溯的基本demo写法：\
函数参数包括当前的组合，约束的条件要求，寻找下个数的开始条件\
进入函数后首先需要判断组合是否满足要求，满足后就放入结果数组中。\
不满足就通过传入的下个数的开始条件进行下个数的寻找。
## 组合
在给定的范围内找满足条件的组合，就是回溯算法（back tracking）需要解决的问题之一。
### 链接
[link](https://leetcode.cn/problems/combinations/)
### 关键词
回溯，组和
### 难点
回溯其实就如同一颗树，在一个分叉父节点中的多个分叉子节点中选择其中一个；在**经历完当前分叉的所有内容后回到分叉父节点从下一分支开始的过程**，就是回溯。\
本题要求选出一定数量的节点组合（k个），**组合要求不分顺序，不能重复**。因此选定一个元素后，就只能在剩下没选定的元素中选择下一个。而为了保证顺序上不重复，只能向下找而不能往回找，因此需要一个值标志向下找的其实位置。\
回溯算法解决组合问题一般都需要一个index指引。
### 代码
~~~c++
class Solution {
private:
    vector<vector<int>> result;
    vector<int> path;

public:
    vector<vector<int>> combine(int n, int k) {
        backtracking(n, k, 1);
        return result;
    }

    void backtracking(int n, int k, int start){
        if(path.size() == k){
            result.push_back(path);
            return;
        }
        for(int i = start; i <= n; i++){
            path.push_back(i);
            backtracking(n, k, i + 1);
            path.pop_back();
        }

    }
};
~~~
## 组合总和III
本题在一般组合的基础上增加了和为定值的要求，仅需要在判断条件中加入即可
### 链接
[link](https://leetcode.cn/problems/combination-sum-iii/description/)
### 关键词
回溯，组合
### 难点
本题就是一般组合的更高要求，同时引入剪枝的概念。\
由于本题的结束条件有两个，只有同时满足两个条件才能被放入结果数组中。\
回溯函数中会优先判断其中一个条件是否成立，再判断剩下条件成立的情况。同时为了剪枝，减少不必要的开销，如果还未到达前面的条件但后面的条件已经到达，则也不满足。
### 代码
~~~C++
class Solution {
private:
    vector<vector<int>> result;
    vector<int> path;
    int pn = 0; // sum threshold
    int pk = 0; // size threshold
    int psum = 0; // the sum of the path vector

    void backtracking(int start, int pushed_num){
        if(path.size() == pk){ // the requirement for the size of the path
            if(psum == pn){
                result.push_back(path);
                return;
            }
            return;
        }
        if (psum >= pn) { // if the presum is larger than the sum threshold
            return;
        }
        for(int i = start; i <= (9 - pk) + 1 + pushed_num; i++){
            path.push_back(i);
            psum += i;
            backtracking(i + 1, pushed_num + 1);
            psum -= i;
            path.pop_back();
        }
    }
public:
    vector<vector<int>> combinationSum3(int k, int n) {
        pn = n;
        pk = k;
        backtracking(1, 0);
        return result;
    }
};
~~~
## 17.电话号码的字母组合
更复杂的求组合题目
### 链接
[link](https://leetcode.cn/problems/letter-combinations-of-a-phone-number/)
### 关键词
回溯，组合
### 难点
本题是求一个字符串中的每个字符可以被分别提换成不同的字符，求一共有多少种情况\
正如其他求组合的题目一样，本题也需要一个index记录从哪个字符开始求组合。\
由于固定了index，就固定了可以替换的字符的范围，因此就只能在指定的范围中取值。\
本体的结束条件是将所有的字符都转化完毕，即index与字符串长度相同，即到达字符串尾部。
### 代码
~~~c++
class Solution {
private:
    const string letterMap[10] = {
    "", // 0
    "", // 1
    "abc", // 2
    "def", // 3
    "ghi", // 4
    "jkl", // 5
    "mno", // 6
    "pqrs", // 7
    "tuv", // 8
    "wxyz", // 9
    };
    vector<string> result;
    string s = "";
    void backtracking(string digits, int idx){
        if(idx == digits.size()){
            result.push_back(s);
            return;
        }
        int digit = digits[idx] - '0'; // convert char to int
        for(int i = 0; i < letterMap[digit].size(); i++){
            s.push_back(letterMap[digit][i]);
            backtracking(digits, idx + 1);
            s.pop_back();
        }
    }

public:
    vector<string> letterCombinations(string digits) {
        if(digits.size() == 0){
            return result;
        }
        backtracking(digits, 0);
        return result;
    }
};
~~~

