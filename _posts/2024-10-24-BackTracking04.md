---
Title: BackTracking04
Date: 2024-10-24
---
# 算法训练营第二十五天 ｜ 491.递增子序列 ｜ 46.全排列 ｜ 47.全排列 II
## 491 递增子序列
递增子序列就是求子集，要求元素为递增序列子集中的子集，它与顺序无关。
### 链接
[link](https://leetcode.cn/problems/non-decreasing-subsequences/)
### 关键词
回溯，组合
### 难点
本体的难点相比于常规的求子集的问题多了：\
要求子集中全是非递减元素，可想象到集中将结果数组判断是否为非递增，也可以从在插入元素时判断是否为递增；\
本题还是后者的解决方法比较符合要求：一是时间复杂度低，只需要加入的元素与相邻元素做大小判断即可；二是符合backtracking的demo要求，要求判断之后再加入元素。\
本体容易中的陷阱是：在进行回溯之前就对集合元素进行排序，本题要求在集合元素顺序保持不变的条件下对其进行非递减顺序子集的查找。集合元素本来的顺序就决定了子集中非递减子集的个数。\
同时注意，本题中集合元素出现重复元素，求非重复组合（子集）的一般方法是排序后判断相邻相同元素是否出现在回溯输的同一层。而本题无法排序，因此需要一个集合记录每一树层使用的元素。
### 代码
～～～c++
class Solution {
private:
    vector<vector<int>> res;
    vector<int> path;
    void backtracking(vector<int>& nums, int start){
        if(path.size() >= 2){
            res.push_back(path);
            if(start == nums.size()){
                return;                
            }
        }
        unordered_set<int> uset; // can not be sorted, and it is a combination problem
        for(int i = start; i < nums.size(); i++){
            if ((!path.empty() && nums[i] < path.back())
                    || uset.find(nums[i]) != uset.end()) {
                    continue;
            }
            uset.insert(nums[i]);
            path.push_back(nums[i]);
            backtracking(nums, i + 1);
            path.pop_back();
        }
    }
public:
    vector<vector<int>> findSubsequences(vector<int>& nums) {
        backtracking(nums, 0);
        return res;
    }
};
～～～
## 46.全排列
排列与组合问题不同点在于：**组合与顺序无关，排列与顺序有关**，统一组合的不同排序就是排列
### 链接
[link](https://leetcode.cn/problems/permutations/)
### 关键词
回溯，排列
### 难点
本题是基础的求排列问题，用于**熟悉排列和组合的不同**\
求全排列中**下一个元素就不再会有自动➕1的要求**，因为下一个元素可以在没使用过的所有元素中选取。\
本题不存在重复的元素，因此**只需要判断是否使用过这个元素**
### 代码
～～～c++
lass Solution {
private:
    vector<vector<int>> res;
    vector<int> path;
public:
    void traverse(vector<int>& nums, vector<bool>& used) {
        if (path.size() == nums.size()) {
            res.push_back(path);
            return;
        }
        for(int i = 0; i < nums.size(); i++) {
            if (used[i]) {
                continue;
            }
            path.push_back(nums[i]);
            used[i] = true;
            traverse(nums, used);
            used[i] = false;
            path.pop_back();
        }
        return;
    }
    vector<vector<int>> permute(vector<int>& nums) {
        vector<bool> used(nums.size(), false);
        traverse(nums, used);
        return res;
    }
};
～～～
## 47.全排列 II
重复元素的集合的全排列
### 链接
[link](https://leetcode.cn/problems/permutations-ii/description/)
### 关键词
回溯，排列
### 难点
本题在[46全排列](https://leetcode.cn/problems/non-decreasing-subsequences/)的基础上添加了重复元素的条件。\
本题**与原生顺序无关，所以可以进行排序**。\
本题的核心还是在于排序后对**树层元素的去重**，相邻相同元素如果有前一个未被使用，则当前元素也无法使用，因为元素的选取是从左到右的顺序取的，前一个元素的选取先于后一个元素\
前一个元素如果没被选取，说明**它不是上一个树叉的元素，也就意味着它在同一层已经被选过了**
### 代码
～～～c++
class Solution {
private:
    vector<vector<int>> res;
    vector<int> path;
public:
    void traverse(vector<int>& nums, vector<bool>& used) {
        if (path.size() == nums.size()) {
            res.push_back(path);
            return;
        }
        for(int i = 0; i < nums.size(); i++) {
            if (i > 0 && nums[i] == nums[i - 1] && used[i - 1] == false) {
                continue;
            }
            if (!used[i]) {
                path.push_back(nums[i]);
                used[i] = true;
                traverse(nums, used);
                used[i] = false;
                path.pop_back(); 
            }
        }
        return;
    }
    vector<vector<int>> permuteUnique(vector<int>& nums) {
        res.clear();
        path.clear();
        vector<bool> used(nums.size(), false);
        sort(nums.begin(), nums.end());
        traverse(nums, used);
        return res;
    }
};
～～～
