---
Title: StackAndQueue 01
Date: 2024-10-09
---
# 代码随想录算法训练营第十天 | 232.用栈实现队列 | 225. 用队列实现栈 | 20. 有效的括号 | 1047. 删除字符串中的所有相邻重复项
## 232 用栈实现队列
本题的难度是用栈实现队列，如何使用栈后进先出的操作实现先进先出的队列\
如果使用栈实现队列，那就肯定需要不只一个栈
### 关键词
栈，队列，模拟
### 难点
不拘泥于使用一个栈，而是灵活使用两个栈，分别将其命名为输出栈和输入栈。\
想象一下，将两个栈的底部拼接起来，由于栈先进后出的特性，其中的一个栈用于存储进入的元素，元素由早到晚排列在栈中；另一个栈用于输出，元素排列顺序为先后进的在里面，先进的在外面，方便先进入的出栈\
如何决定什么时候元素被转移到输出栈时呢，答案自然是需要进行pop操作时。而如果输出栈中已经有元素了，则只需要进行输出栈的pop就可以了。
### 代码
~~~c++
class MyQueue {
public:
    stack<int> stIn;
    stack<int> stOut;
    /** Initialize your data structure here. */
    MyQueue() {

    }
    /** Push element x to the back of queue. */
    void push(int x) {
        stIn.push(x);
    }

    /** Removes the element from in front of queue and returns that element. */
    int pop() {
        // 只有当stOut为空的时候，再从stIn里导入数据（导入stIn全部数据）
        if (stOut.empty()) {
            // 从stIn导入数据直到stIn为空
            while(!stIn.empty()) {
                stOut.push(stIn.top());
                stIn.pop();
            }
        }
        int result = stOut.top();
        stOut.pop();
        return result;
    }

    /** Get the front element. */
    int peek() {
        int res = this->pop(); // 直接使用已有的pop函数
        stOut.push(res); // 因为pop函数弹出了元素res，所以再添加回去
        return res;
    }

    /** Returns whether the queue is empty. */
    bool empty() {
        return stIn.empty() && stOut.empty();
    }
};
~~~
## 225. 用队列实现栈
与前一题同理，只不过换了过来，使用队列来实现栈。
由于队列是先进先出，保证一定顺序的，所以不能像栈一样通过两个栈改变输出的顺序。另一个队列在这里只能作为暂时存储的位置
### 关键词
栈，队列，模拟
### 代码
~~~c++
~~~
## 20. 有效的括号  1047. 删除字符串中的所有相邻重复项
这两题的主要核心都是利用栈的后进先出概念，进行一个匹配后移出的过程。
括号只有在遇到反括号时才会和栈定元素进行查验匹配。
而相同字符每次进入一个字符都会进行匹配和查验。
### 关键词
栈，模拟
### 代码
~~~c++

~~~
