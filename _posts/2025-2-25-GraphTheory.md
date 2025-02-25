---
Title：GraphTheory09
Date：2025-2-25
---
# 算法随想录 | Dijaskal算法堆优化 | Bellman-Ford算法
## Dijskal算法堆优化
通过边的角度和堆的数据结构
### 难点
之所以会想到用堆和从边的角度优化Dijskal算法：是因为\
1、Dijaskal算法使用的是贪心思想，而Kruskal和prim算法也是使用贪心思想；
2、Dijaskal算法维护的mindist代表原点到各个节点的距离，而kruskal和Prim算法的mindist代表最小生成树到达各个节点的距离。二者相似
3、朴素的Dijaskal算法维护点集合，而Prim算法同样维护点集合；此时缺少Kruskal算法中针对边稀疏图而使用的维护边集合的方法。
4、Kruskal算法没有使用堆是因为Kruskal算法的适用对象是无向图，生成的结构是生成树，仅需要判断是否有环；而Dijskal算法求解原点到各个点的距离，且适用对象是有向图，每次找到一个节点，都需要动态地找出和这个节点相邻都可更新的节点。
5、堆可以使得不用遍历所有一个节点连接的边就可以找到距离最短的边。原理就是优先找距离近的节点，使得通过这个节点得到的更新也最近
### 代码
~~~C++
#include <iostream>
#include <vector>
#include <list>
#include <queue>
#include <climits>
using namespace std; 
// 小顶堆
class mycomparison {
public:
    bool operator()(const pair<int, int>& lhs, const pair<int, int>& rhs) {
        return lhs.second > rhs.second;
    }
};
// 定义一个结构体来表示带权重的边
struct Edge {
    int to;  // 邻接顶点
    int val; // 边的权重

    Edge(int t, int w): to(t), val(w) {}  // 构造函数
};

int main() {
    int n, m, p1, p2, val;
    cin >> n >> m;

    vector<list<Edge>> grid(n + 1);

    for(int i = 0; i < m; i++){
        cin >> p1 >> p2 >> val; 
        // p1 指向 p2，权值为 val
        grid[p1].push_back(Edge(p2, val));

    }

    int start = 1;  // 起点
    int end = n;    // 终点

    // 存储从源点到每个节点的最短距离
    std::vector<int> minDist(n + 1, INT_MAX);

    // 记录顶点是否被访问过
    std::vector<bool> visited(n + 1, false); 
    
    // 优先队列中存放 pair<节点，源点到该节点的权值>
    priority_queue<pair<int, int>, vector<pair<int, int>>, mycomparison> pq;


    // 初始化队列，源点到源点的距离为0，所以初始为0
    pq.push(pair<int, int>(start, 0)); 
    
    minDist[start] = 0;  // 起始点到自身的距离为0

    while (!pq.empty()) {
        // 1. 第一步，选源点到哪个节点近且该节点未被访问过 （通过优先级队列来实现）
        // <节点， 源点到该节点的距离>
        pair<int, int> cur = pq.top(); pq.pop();

        if (visited[cur.first]) continue;

        // 2. 第二步，该最近节点被标记访问过
        visited[cur.first] = true;

        // 3. 第三步，更新非访问节点到源点的距离（即更新minDist数组）
        for (Edge edge : grid[cur.first]) { // 遍历 cur指向的节点，cur指向的节点为 edge
            // cur指向的节点edge.to，这条边的权值为 edge.val
            if (!visited[edge.to] && minDist[cur.first] + edge.val < minDist[edge.to]) { // 更新minDist
                minDist[edge.to] = minDist[cur.first] + edge.val;
                pq.push(pair<int, int>(edge.to, minDist[edge.to]));
            }
        }

    }

    if (minDist[end] == INT_MAX) cout << -1 << endl; // 不能到达终点
    else cout << minDist[end] << endl; // 到达终点最短路径
}
~~~
### 思想
通过Prim和Kruskal算法的不同角度，推导出了Dijska算法的不同版本
## Bellman-Ford算法
解决两个节点之间的最小距离问题，其中边的权值可以为负值
### 难点
Bellman-Ford用于解决两个节点之间的最短距离的问题，如果重复n -  1次，就可以解决某个节点到达其余n-1个节点的最短距离。于Dijska算法不同的是，Bellman-Ford可以解决权值为负数的情况，而Dijska算法不行，Dijska算法不行是因为mindist的更新取决于到达某一个节点的顺序。
### 代码 
~~~c++
#include <iostream>
#include <vector>
#include <list>
#include <climits>
using namespace std;

int main() {
    int n, m, p1, p2, val;
    cin >> n >> m;

    vector<vector<int>> grid;

    // 将所有边保存起来
    for(int i = 0; i < m; i++){
        cin >> p1 >> p2 >> val;
        // p1 指向 p2，权值为 val
        grid.push_back({p1, p2, val});

    }
    int start = 1;  // 起点
    int end = n;    // 终点

    vector<int> minDist(n + 1 , INT_MAX);
    minDist[start] = 0;
    for (int i = 1; i < n; i++) { // 对所有边 松弛 n-1 次
        for (vector<int> &side : grid) { // 每一次松弛，都是对所有边进行松弛
            int from = side[0]; // 边的出发点
            int to = side[1]; // 边的到达点
            int price = side[2]; // 边的权值
            // 松弛操作 
            // minDist[from] != INT_MAX 防止从未计算过的节点出发
            if (minDist[from] != INT_MAX && minDist[to] > minDist[from] + price) { 
                minDist[to] = minDist[from] + price;  
            }
        }
    }
    if (minDist[end] == INT_MAX) cout << "unconnected" << endl; // 不能到达终点
    else cout << minDist[end] << endl; // 到达终点最短路径

}
~~~
### 思想
1、什么是松弛：松弛是对一条边的操作，如果经过这条边能够以更少的权重访问节点，那就对这条边进行松弛，更新它所连接的节点的mindist。
2、为什么需要松弛n-1次：一次松弛操作是对所有的边进行一次松弛，也就是说一次松弛是对一个节点，以及其由一条边连接的节点进行的操作。n-1次松弛就会对一个节点到其通过n-1条边连接的节点进行松弛。自然就能更新n-1条边。而通n个节点中的两个节点相连，最多只需要n-1条边。
