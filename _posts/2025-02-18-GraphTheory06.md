---
Title: GraphTheory06
Date: 2025-02-18
---
# 算法训练营 | 108 冗杂路径 I,II
## 108 冗杂路径I
有一个图，它是一棵树，他是拥有 n 个节点（节点编号1到n）和 n - 1 条边的连通无环无向图，在这个基础上添加一条边，使之成为无向有环图，找出这条边
### 难点
查并集可以用来解决两个点是否在同一集合内的问题以及判断两个点是否在同一个集合上。若其余的边都组成了一个无向树，则这集合点都会同属于一个集合。额外的一条边，如果边上了两个节点已经同属于一个节点了，则说明这条边是多余的。
### 代码
~~~C++
#include <iostream>
#include <vector>
using namespace std;
int n; // 节点数量
vector<int> father(1001, 0); // 按照节点大小范围定义数组

// 并查集初始化
void init() {
    for (int i = 0; i <= n; ++i) {
        father[i] = i;
    }
}
// 并查集里寻根的过程
int find(int u) {
    return u == father[u] ? u : father[u] = find(father[u]);
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

int main() {
    int s, t;
    cin >> n;
    init();
    for (int i = 0; i < n; i++) {
        cin >> s >> t;
        if (isSame(s, t)) {
            cout << s << " " << t << endl;
            return 0;
        } else {
            join(s, t);
        }
    }
}
~~~
### 思考
这道题只需要判断第一个遇见的，拥有同属于一个集合的两点的边即可：因为：这是一个无向图，没有方向规定边的父子归属。除了这条边外，其余的节点已经形成了一个平衡，多加入这个点必定会变成一个有环图。\
而有向图就必须考虑输入的先后顺序，因为两个点的边可以有两个方向，必须要知道这两条边的输入顺序才能删掉后一条边，如下图:
![image](https://github.com/user-attachments/assets/d4efee0d-106f-4295-a648-97b0c439e782)
这张图的双向边必须要通过输入顺序才能直到删除哪条边
## 108 冗杂路径II
前一题的有向图版本
### 难点
由于这道题新增加了方向的设定，就不能向前一题那样通过直接判断两个节点是否已经位于一个集合中的办法了，如下图所示：\
![image](https://github.com/user-attachments/assets/4fffa727-797b-4c9b-af68-2b3eac29d0c8)\
该图由1-3，1-2，2-3的顺序输入时，会出现2-3也成功输入的情况。原因是方向限制了路径压缩，方向使得路径压缩只能限制在一个方向上，而不是双向的。无向图就没有这个限制。此时就需要其他的策略：\
树的特点是一棵树每个节点只有最多一个入度。此时如果一个节点出现两个入度说明有一条该节点的边需要被删除。\
同时这条边的删除也有讲究，如果删除了唯一连到某一个节点的边则就会使得图出现非连通的情况。如下面这张图：\
![image](https://github.com/user-attachments/assets/54919152-973e-43d7-a019-749c6dcaea31)。
同时，如果不存在有两个入度的节点，则说明存在一个有向环。\
![image](https://github.com/user-attachments/assets/205082de-c044-45b2-a339-c8e8c02754ee)
因此，整体来说需要编写一个函数判读一个边是否需要删除，以及针对无入度为2且有环的图的找到环的函数。
### 代码
~~~C++
#include <iostream>
#include <vector>
using namespace std;
int n;
vector<int> father (1001, 0);
// 并查集初始化
void init() {
    for (int i = 1; i <= n; ++i) {
        father[i] = i;
    }
}
// 并查集里寻根的过程
int find(int u) {
    return u == father[u] ? u : father[u] = find(father[u]);
}
// 将v->u 这条边加入并查集
void join(int u, int v) {
    u = find(u);
    v = find(v);
    if (u == v) return ;
    father[v] = u;
}
// 判断 u 和 v是否找到同一个根
bool same(int u, int v) {
    u = find(u);
    v = find(v);
    return u == v;
}

// 在有向图里找到删除的那条边，使其变成树，只用于无入度为2的图的或有有向环的情况下去边
void getRemoveEdge(const vector<vector<int>>& edges) {
    init(); // 初始化并查集
    for (int i = 0; i < n; i++) { // 遍历所有的边
        if (same(edges[i][0], edges[i][1])) { // 构成有向环了，就是要删除的边
            cout << edges[i][0] << " " << edges[i][1];
            return;
        } else {
            join(edges[i][0], edges[i][1]);
        }
    }
}

// 删一条边之后判断是不是树
bool isTreeAfterRemoveEdge(const vector<vector<int>>& edges, int deleteEdge) {
    init(); // 初始化并查集
    for (int i = 0; i < n; i++) {
        if (i == deleteEdge) continue;
        if (same(edges[i][0], edges[i][1])) { // 构成有向环了，一定不是树
            return false;
        }
        join(edges[i][0], edges[i][1]);
    }
    return true;
}

int main() {
    int s, t;
    vector<vector<int>> edges;
    cin >> n;
    vector<int> inDegree(n + 1, 0); // 记录节点入度
    for (int i = 0; i < n; i++) {
        cin >> s >> t;
        inDegree[t]++;
        edges.push_back({s, t});
    }

    vector<int> vec; // 记录入度为2的边（如果有的话就两条边）
    // 找入度为2的节点所对应的边，注意要倒序，因为优先删除最后出现的一条边
    for (int i = n - 1; i >= 0; i--) {
        if (inDegree[edges[i][1]] == 2) {
            vec.push_back(i);
        }
    }
    // 情况一、情况二
    if (vec.size() > 0) {
        // 放在vec里的边已经按照倒叙放的，所以这里就优先删vec[0]这条边
        if (isTreeAfterRemoveEdge(edges, vec[0])) {
            cout << edges[vec[0]][0] << " " << edges[vec[0]][1];
        } else {
            cout << edges[vec[1]][0] << " " << edges[vec[1]][1];
        }
        return 0;
    }

    // 处理情况三
    // 明确没有入度为2的情况，那么一定有有向环，找到构成环的边返回就可以了
    getRemoveEdge(edges);
}
~~~
### 思考
本题比上一题难，不仅要考虑查并集的情况，还需要考虑节点的入度。因为方向性的引入，在递归找到其中一个节点的根时情况就会不同。

