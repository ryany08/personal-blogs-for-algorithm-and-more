---
Title: GraphTheory08
Date: 2025-02-24
---
# 算法训练营 | 拓扑排序精讲 | dijkstra（朴素版）精讲
## 拓扑排序
某个大型软件项目的构建系统拥有 N 个文件，文件编号从 0 到 N - 1，在这些文件中，某些文件依赖于其他文件的内容，这意味着如果文件 A 依赖于文件 B，则必须在处理文件 A 之前处理文件 B （0 <= A, B <= N - 1）。请编写一个算法，用于确定文件处理的顺序
### 难点
拓扑排序的思想就是检查一个有向图是否存在环，使得这个图能够输出节点的顺序。它和生成树的区别在于，拓扑结构的节点可以有两个以上的入度，而生成树只能有一个入度。\
拓扑排序的过程，其实就两步:\
找到入度为0 的节点，加入结果集\
将该节点从图中移除\
循环以上两步，直到 所有节点都在图中被移除了
### 代码
~~~C++
#include <iostream>
#include <vector>
#include <queue>
#include <unordered_map>
using namespace std;
int main() {
    int m, n, s, t;
    cin >> n >> m;
    vector<int> inDegree(n, 0); // 记录每个文件的入度

    unordered_map<int, vector<int>> umap;// 记录文件依赖关系
    vector<int> result; // 记录结果

    while (m--) {
        // s->t，先有s才能有t
        cin >> s >> t;
        inDegree[t]++; // t的入度加一
        umap[s].push_back(t); // 记录s指向哪些文件
    }
    queue<int> que;
    for (int i = 0; i < n; i++) {
        // 入度为0的文件，可以作为开头，先加入队列
        if (inDegree[i] == 0) que.push(i);
        //cout << inDegree[i] << endl;
    }
    // int count = 0;
    while (que.size()) {
        int  cur = que.front(); // 当前选中的文件
        que.pop();
        //count++;
        result.push_back(cur);
        vector<int> files = umap[cur]; //获取该文件指向的文件
        if (files.size()) { // cur有后续文件
            for (int i = 0; i < files.size(); i++) {
                inDegree[files[i]] --; // cur的指向的文件入度-1
                if(inDegree[files[i]] == 0) que.push(files[i]);
            }
        }
    }
    if (result.size() == n) {
        for (int i = 0; i < n - 1; i++) cout << result[i] << " ";
        cout << result[n - 1];
    } else cout << -1 << endl;
}
~~~
### 思想
既然需要每次找到入度为0的节点，且把这个节点从图中移除。这就形成了一个循环，这个循环类似于队列的先进先出，取出队首的元素相当于消除当前入度为0的节点，紧接着消除这个节点连接的其他节点，便可以放入入度为0的节点。
## dijkstra（朴素版）精讲
求有向图中，原点到其他节点的最小距离，其中节点与节点的距离不全为1
### 难点
由于节点与节点的距离不完全为1，说明这是一个加权的有向图，不能使用DFS求最短距离。\
Dijkstra算法要求权值不能为负数。\
Dijkstra算法步骤：\
1、找到距离最近的，且没有被访问过的节点，标记为访问过；\
2、通过找到的节点，更新从原点到其他节点的距离，要求（mindist[cur] + graph[cur][v] < mindist[v]，其中v是其他节点，cur是选中的节点)。
### 代码
~~~C++
#include <iostream>
#include <vector>
#include <climits>
using namespace std;
int main() {
    int n, m, p1, p2, val;
    cin >> n >> m;

    vector<vector<int>> grid(n + 1, vector<int>(n + 1, INT_MAX));
    for(int i = 0; i < m; i++){
        cin >> p1 >> p2 >> val;
        grid[p1][p2] = val;
    }

    int start = 1;
    int end = n;

    // 存储从源点到每个节点的最短距离
    std::vector<int> minDist(n + 1, INT_MAX);

    // 记录顶点是否被访问过
    std::vector<bool> visited(n + 1, false);

    minDist[start] = 0;  // 起始点到自身的距离为0

    for (int i = 1; i <= n; i++) { // 遍历所有节点

        int minVal = INT_MAX;
        int cur = 1;

        // 1、选距离源点最近且未访问过的节点
        for (int v = 1; v <= n; ++v) {
            if (!visited[v] && minDist[v] < minVal) {
                minVal = minDist[v];
                cur = v;
            }
        }

        visited[cur] = true;  // 2、标记该节点已被访问

        // 3、第三步，更新非访问节点到源点的距离（即更新minDist数组）
        for (int v = 1; v <= n; v++) {
            if (!visited[v] && grid[cur][v] != INT_MAX && minDist[cur] + grid[cur][v] < minDist[v]) {
                minDist[v] = minDist[cur] + grid[cur][v];
            }
        }

    }

    if (minDist[end] == INT_MAX) cout << -1 << endl; // 不能到达终点
    else cout << minDist[end] << endl; // 到达终点最短路径

}
~~~
### 思考
dijskal算法和prim算法类似，中心思想都是利用贪心算法。不同的是，prim算法的mindist指的是节点到最小生成树的距离，更新mindist就是更新最小生成树到达结点的距离，初始化不能为最大值，且只用于无向有权图。\
dijskal算法用于有向有权图，mindist代表节点到达原点的距离，每次更新都是通过中转节点比较通过这个节点和不通过这个节点的距离。
