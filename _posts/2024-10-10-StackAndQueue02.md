---
Title: StackAndQueue02
Date: 2024-10-10
---
# 代码随想录算法训练营第十天 | 150. 逆波兰表达式求值 | 239. 滑动窗口最大值 | 347.前 K 个高频元素
## 150 逆波兰表达式
本题和昨日的后两题一样，都是考察对栈后进先出的特性\
同时本体也考察了对逆波兰表达式的理解，这是计算机的一种思考方式
### 链接
https://leetcode.cn/problems/evaluate-reverse-polish-notation/
### 关键词
栈，模拟
### 难点
由于本题是进行int之间的计算，而所给出的是string数组，也就是说需要先把string转化成int才能进行栈的操作。
### 额外复习
C++中进行string和int相互转换的函数\
**string -> int**
int stoi() 
**string -> long**
long stol()
**string -> long long**
long long stoll()
**int, long, long long -> string**
string to_string()
### 代码
~~~c++
class Solution {
public:
    int evalRPN(vector<string>& tokens) {
        stack<int> myStack;
        for(auto s = tokens.begin(); s != tokens.end(); s++) {
            if (*s == "+" || *s == "-" || *s == "*" || *s == "/") {
                int op1, op2;
                if (myStack.size() >= 2) {
                    op2 = myStack.top();
                    myStack.pop();
                    op1 = myStack.top();
                    myStack.pop();
                    myStack.push(getResult(op1, op2, *s));
                    continue;
                }
                return 0;
            }
            else {
                myStack.push(stoi(*s));
            }
        }
        return myStack.top();
    }

    int getResult(int op1, int op2, string symbol) {
        if (symbol == "+") {
            return op1 + op2;
        }
        else if (symbol == "-") {
            return op1 - op2;
        }
        else if (symbol == "*") {
            return op1 * op2;
        }
        else {
            return op1 / op2;
        }
    }
};
~~~
## 239 滑动窗口最大值
最开始的思路是使用双指针记录滑动窗口，同时记录最大值，每次出入都会更新最大值。\
但是发现如果最大值出去了，就需要遍历窗口重新寻找最大值，极端情况下会出现quadratic time的时间复杂度。
在没想出来的情况下，通过讲解得知了使用单调队列的解法
### 链接
https://leetcode.cn/problems/sliding-window-maximum/
### 关键词
单调队列
### 难点
以单调队列为基础，进行扩展思考：\
1、如何建立单调队列\
2、单调队列的处理逻辑\
答案：
1、建立单调队列的目的是通过保证滑动窗口内的元素按从大到小的情况排列，而方便找出最大值。\
最开始的时候也想过监控整个队列，使得队列里一直存储着窗口中的已经排列好的元素。实际上保证窗口所有元素都被排序并存储在队列中无法在线性时间内做到\
因此正确的办法是只需要**存储区间内相对递减的数**就可以了。这样就能一直保证在区间内是递减的，单调的。\
2、push和pop都需要输入相应的元素进行。这样才能知道pop是否将最大的元素去除。\
pop在只有去除的元素为当前最大的元素是才能去除，其余保持不变。因为其余情况表示最大元素即头部元素还在区间内。\
push为保证区间内相对单调的特性，每进来一个元素，在找到自己的位置后，就把比自己小的所有原先的元素排除，因为这些元素无论如何都无法被认为是区间内较大的元素了，有新来的元素比它们大。
### 代码
~~~c++
class Solution {
private:
    class MyQueue { //单调队列（从大到小）
    public:
        deque<int> que; // 使用deque来实现单调队列
        // 每次弹出的时候，比较当前要弹出的数值是否等于队列出口元素的数值，如果相等则弹出。
        // 同时pop之前判断队列当前是否为空。
        void pop(int value) {
            if (!que.empty() && value == que.front()) {
                que.pop_front();
            }
        }
        // 如果push的数值大于入口元素的数值，那么就将队列后端的数值弹出，直到push的数值小于等于队列入口元素的数值为止。
        // 这样就保持了队列里的数值是单调从大到小的了。
        void push(int value) {
            while (!que.empty() && value > que.back()) {
                que.pop_back();
            }
            que.push_back(value);

        }
        // 查询当前队列里的最大值 直接返回队列前端也就是front就可以了。
        int front() {
            return que.front();
        }
    };
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        MyQueue que;
        vector<int> result;
        for (int i = 0; i < k; i++) { // 先将前k的元素放进队列
            que.push(nums[i]);
        }
        result.push_back(que.front()); // result 记录前k的元素的最大值
        for (int i = k; i < nums.size(); i++) {
            que.pop(nums[i - k]); // 滑动窗口移除最前面元素
            que.push(nums[i]); // 滑动窗口前加入最后面的元素
            result.push_back(que.front()); // 记录对应的最大值
        }
        return result;
    }
};
~~~
## 347.前 K 个高频元素
本题看似简单（使用map，再排序即可，时间复杂度nlogn），但这不符合时间复杂度要求小于nlogn。\
说明一般的map排序不可行。\
想到一个可以自动保持元素相对顺序的数据结构，即堆（单调队列），
由于堆会自动找到当前最大或最小的元素，只需要不断pop就可以拿到Top K个元素
### 链接
https://leetcode.cn/problems/top-k-frequent-elements/
### 关键词
堆，小顶堆
### 难点
1、确认使用大顶堆还是小顶堆\
答案：\
如果使用大顶堆，则需要吧所有元素都放进堆中，时间复杂度就会变成nlogn\
如果使用小顶堆，则只需要保证前k个大的元素在内，即一旦大小超过k，就开始pop出最小的元素，容器的大小不会大于k。时间复杂度为nlogk，满足题意
### 代码
~~~c++
ass Solution {
public:
    // 小顶堆
    class mycomparison {
    public:
        bool operator()(const pair<int, int>& lhs, const pair<int, int>& rhs) {
            return lhs.second > rhs.second;
        }
    };
    vector<int> topKFrequent(vector<int>& nums, int k) {
        // 要统计元素出现频率
        unordered_map<int, int> map; // map<nums[i],对应出现的次数>
        for (int i = 0; i < nums.size(); i++) {
            map[nums[i]]++;
        }

        // 对频率排序
        // 定义一个小顶堆，大小为k
        priority_queue<pair<int, int>, vector<pair<int, int>>, mycomparison> pri_que;

        // 用固定大小为k的小顶堆，扫面所有频率的数值
        for (unordered_map<int, int>::iterator it = map.begin(); it != map.end(); it++) {
            pri_que.push(*it);
            if (pri_que.size() > k) { // 如果堆的大小大于了K，则队列弹出，保证堆的大小一直为k
                pri_que.pop();
            }
        }

        // 找出前K个高频元素，因为小顶堆先弹出的是最小的，所以倒序来输出到数组
        vector<int> result(k);
        for (int i = k - 1; i >= 0; i--) {
            result[i] = pri_que.top().first;
            pri_que.pop();
        }
        return result;

    }
};
~~~
