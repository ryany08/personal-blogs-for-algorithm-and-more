---
Title：GraphTheory05
Date：2025-02-17
---
# 算法随想录 | 查并集基础
## 查并集基础
查并集是一个用于查找和记录两个元素是否在同一个元素上的数据结构。通常使用一维数组的结构和递归的方法建立。\
并查集主要有两个功能：
将两个元素添加到一个集合中。
判断两个元素在不在同一个集合
### 难点
查并集的查找会递归向上找到连接的根节点。如果是将元素插入查并集中，就会首先分别查找需要插入的元素的根节点，找到根节点后，根据根节点的关系决定是否将其中一个元素当作另一个元素的根。
### 代码
~~~C++
int n = 1005; // n根据题目中节点数量而定，一般比节点数量大一点就好
vector<int> father = vector<int> (n, 0); // C++里的一种数组结构

// 并查集初始化
void init() {
    for (int i = 0; i < n; ++i) {
        father[i] = i;
    }
}
// 并查集里寻根的过程
int find(int u) {
    return u == father[u] ? u : father[u] = find(father[u]); // 路径压缩,把最终层层递归找到的根赋给最先的节点
}

// 判断 u 和 v是否找到同一个根
bool isSame(int u, int v) {
    u = find(u);
    v = find(v);
    return u == v;
}

// 将v->u 这条边加入并查集
void join(int u, int v) {
    u = find(u); // 寻找u的根
    v = find(v); // 寻找v的根
    if (u == v) return ; // 如果发现根相同，则说明在一个集合，不用两个节点相连直接返回
    father[v] = u;
}
~~~
107. 寻找存在的路径
给定一个包含 n 个节点的无向图中，节点编号从 1 到 n （含 1 和 n ）。
判断是否有一条从节点 source 出发到节点 destination 的路径存在
## 难点
在给定的一个无向图中寻找一条边，这条边的两端为所给的source和destination
## 代码
DFS版本，检查从start开始是否能够找到end
~~~C++
#include<iostream>
#include<vector>
#include<queue>
#include<list>
using namespace std;

int main() {
    int N, M;
    cin>>N>>M;
    vector<list<int>> graph(N + 1);
    vector<bool> visited(N + 1, false);
    queue<int> q1;
    for(int i = 0; i < M; i++) {
        int v1, v2;
        cin>>v1>>v2;
        graph[v1].push_back(v2);
        graph[v2].push_back(v1);
    }
    
    int start, end;
    cin>>start>>end;
    q1.push(start);
    while(!q1.empty()) {
        int key = q1.front();
        visited[key] = true;
        q1.pop();
        for(int k: graph[key]) {
            if (!visited[k]) {
                q1.push(k);
            }
        }
    }
    
    if (visited[end]) {
        cout<<1<<endl;
    }
    else {
        cout<<0<<endl;
    }
    return 0;
}
~~~
