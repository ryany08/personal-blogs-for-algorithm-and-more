---
Title: GraphTheory07
Date: 2025-02-22
---
# 算法随想录 | Prim算法 | Kruskal算法
今日的两题都是用于解决无向图中的最小生成树，即所有结点的最小连通子图
## Prim算法
Prim算法需要解决的问题是找出一个图中，节点相互连接的最短路径，也就是最小生成树，也就是所有节点的最小连通子图。\
Prim算法的核心是贪心，即每次都更新连接一个节点的最小路径，一共需要更新节点数-1次，因为最后一个节点在最后一次更新中就被记录下来了。
### 难点
每次循环，将选中一个节点，这个节点要求（不在构建的生成树中，保证与连接树的距离最小）。\
需要准备两个数组：1个数组用于记录节点是否位于生成树中，另一个数组作为纪录到达各个节点的距离，这个数据会随着每次更新节点而更新。\
### 代码
~~~c++
#include<iostream>
#include<vector>
#include <climits>

using namespace std;
int main() {
    int v, e;
    int x, y, k;
    cin >> v >> e;
    // 填一个默认最大值，题目描述val最大为10000
    vector<vector<int>> grid(v + 1, vector<int>(v + 1, 10001));
    while (e--) {
        cin >> x >> y >> k;
        // 因为是双向图，所以两个方向都要填上
        grid[x][y] = k;
        grid[y][x] = k;

    }
    // 所有节点到最小生成树的最小距离
    vector<int> minDist(v + 1, 10001);

    // 这个节点是否在树里
    vector<bool> isInTree(v + 1, false);

    // 我们只需要循环 n-1次，建立 n - 1条边，就可以把n个节点的图连在一起
    for (int i = 1; i < v; i++) {

        // 1、prim三部曲，第一步：选距离生成树最近节点
        int cur = -1; // 选中哪个节点 加入最小生成树
        int minVal = INT_MAX;
        for (int j = 1; j <= v; j++) { // 1 - v，顶点编号，这里下标从1开始
            //  选取最小生成树节点的条件：
            //  （1）不在最小生成树里
            //  （2）距离最小生成树最近的节点
            if (!isInTree[j] &&  minDist[j] < minVal) {
                minVal = minDist[j];
                cur = j;
            }
        }
        // 2、prim三部曲，第二步：最近节点（cur）加入生成树
        isInTree[cur] = true;

        // 3、prim三部曲，第三步：更新非生成树节点到生成树的距离（即更新minDist数组）
        // cur节点加入之后， 最小生成树加入了新的节点，那么所有节点到 最小生成树的距离（即minDist数组）需要更新一下
        // 由于cur节点是新加入到最小生成树，那么只需要关心与 cur 相连的 非生成树节点 的距离 是否比 原来 非生成树节点到生成树节点的距离更小了呢
        for (int j = 1; j <= v; j++) {
            // 更新的条件：
            // （1）节点是 非生成树里的节点
            // （2）与cur相连的某节点的权值 比 该某节点距离最小生成树的距离小
            // 很多录友看到自己 就想不明白什么意思，其实就是 cur 是新加入 最小生成树的节点，那么 所有非生成树的节点距离生成树节点的最近距离 由于 cur的新加入，需要更新一下数据了
            if (!isInTree[j] && grid[cur][j] < minDist[j]) {
                minDist[j] = grid[cur][j];
            }
        }
    }
    // 统计结果
    int result = 0;
    for (int i = 2; i <= v; i++) { // 不计第一个顶点，因为统计的是边的权值，v个节点有 v-1条边
        result += minDist[i];
    }
    cout << result << endl;

}
~~~
### 思考
注意，Prim算法初始化时，记录到达每个结点的数组不能初始化为INT_MAX,否则无法选中第一个节点，也不能使用最外层（节点数-1）的标记进行循环，因为节点之间连接的权重不一定是按节点顺序分布的，需要额外的变量记录选中的节点。
## Kruskal算法
Kruskal算法会记录边的顺序，通过对边进行排序，从最小的边一直记录到最大的边。\
其核心也是贪心算法，也需要从最短的路径开始。\
### 难点
将边根据权值进行排序后，就可以从最小的权值出发，进行查找了。每选取一条边，都需要查看这个边的两个节点是否处于同一个集合，如果出于同一个集合，则会导致加入这条边变成一个环。需要这两个节点都不属于一个集合才能加入这条节点。\
引入并查集的目的就是快速检查两个节点是否处于同一集合。
### 代码
~~~C++
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

// l,r为 边两边的节点，val为边的数值
struct Edge {
    int l, r, val;
};

// 节点数量
int n = 10001;
// 并查集标记节点关系的数组
vector<int> father(n, -1); // 节点编号是从1开始的，n要大一些

// 并查集初始化
void init() {
    for (int i = 0; i < n; ++i) {
        father[i] = i;
    }
}

// 并查集的查找操作
int find(int u) {
    return u == father[u] ? u : father[u] = find(father[u]); // 路径压缩
}

// 并查集的加入集合
void join(int u, int v) {
    u = find(u); // 寻找u的根
    v = find(v); // 寻找v的根
    if (u == v) return ; // 如果发现根相同，则说明在一个集合，不用两个节点相连直接返回
    father[v] = u;
}

int main() {

    int v, e;
    int v1, v2, val;
    vector<Edge> edges;
    int result_val = 0;
    cin >> v >> e;
    while (e--) {
        cin >> v1 >> v2 >> val;
        edges.push_back({v1, v2, val});
    }

    // 执行Kruskal算法
    // 按边的权值对边进行从小到大排序
    sort(edges.begin(), edges.end(), [](const Edge& a, const Edge& b) {
            return a.val < b.val;
    });

    // 并查集初始化
    init();

    // 从头开始遍历边
    for (Edge edge : edges) {
        // 并查集，搜出两个节点的祖先
        int x = find(edge.l);
        int y = find(edge.r);

        // 如果祖先不同，则不在同一个集合
        if (x != y) {
            result_val += edge.val; // 这条边可以作为生成树的边
            join(x, y); // 两个节点加入到同一个集合
        }
    }
    cout << result_val << endl;
    return 0;
}
~~~
### 思考
kruskal记录边的集合而prim算法记录点的集合。一个是通过从最小边开始记录两个节点，一个是取一个最近的节点并更新这个节点的权值。
