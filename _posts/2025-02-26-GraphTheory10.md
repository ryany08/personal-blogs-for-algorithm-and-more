---
Title: GraphTheory10
Date: 2025-02-26
---
# 算法随想录 | Bellman_ford 队列优化算法 | bellman_ford之判断负权回路
## Bellman-Ford队列优化
Bellman-Ford算法中，会对所有的边进行n-1次的松弛。但是不是所有的边都需要松弛，因为如果边的起始点的mindist为max，无论如何松弛都是无效的
### 难点
具体什么时候需要松弛？\
答案是：只有访问的节点所连接的边需要松弛，这些被访问过的节点对于原点的距离是确定的。\
相比于原生的朴素Bellman-Ford算法（需要对每一个边进行n-1次松弛），优化算法减少了对无用边的访问。这些无用边包括：起始节点到原点距离为max的点和不满足松弛条件的点\
在原生朴素Bellman-Ford算法中，访问边的顺序对于松弛的影响很大，如果访问的顺序得当，甚至只需要对所有边进行一次松弛就可以得到答案。因为边的访问顺序不确定，才需要对所有边都进行n-1次松弛。\
Bellman-Ford算法从对于自身距离为0的原点开始，只需要一步步对于原点连接的边，对连接原点的节点连接的边进行松弛即可，而由于每条边都是有方向的，就不需要判断一条边是否访问过。
### 代码
~~~C++
#include <iostream>
#include <vector>
#include <queue>
#include <list>
#include <climits>
using namespace std;

struct Edge { //邻接表
    int to;  // 链接的节点
    int val; // 边的权重

    Edge(int t, int w): to(t), val(w) {}  // 构造函数
};


int main() {
    int n, m, p1, p2, val;
    cin >> n >> m;

    vector<list<Edge>> grid(n + 1); 

    vector<bool> isInQueue(n + 1); // 加入优化，已经在队里里的元素不用重复添加

    // 将所有边保存起来
    for(int i = 0; i < m; i++){
        cin >> p1 >> p2 >> val;
        // p1 指向 p2，权值为 val
        grid[p1].push_back(Edge(p2, val));
    }
    int start = 1;  // 起点
    int end = n;    // 终点

    vector<int> minDist(n + 1 , INT_MAX);
    minDist[start] = 0;

    queue<int> que;
    que.push(start); 

    while (!que.empty()) {

        int node = que.front(); que.pop();
        isInQueue[node] = false; // 从队列里取出的时候，要取消标记，我们只保证已经在队列里的元素不用重复加入
        for (Edge edge : grid[node]) {
            int from = node;
            int to = edge.to;
            int value = edge.val;
            if (minDist[to] > minDist[from] + value) { // 开始松弛
                minDist[to] = minDist[from] + value; 
                if (isInQueue[to] == false) { // 已经在队列里的元素不用重复添加
                    que.push(to);
                    isInQueue[to] = true;
                }
            }
        }

    }
    if (minDist[end] == INT_MAX) cout << "unconnected" << endl; // 不能到达终点
    else cout << minDist[end] << endl; // 到达终点最短路径
}
~~~
### 思想
访问节点的顺序没有关系，但访问边的顺序有关，因此无论是队列还是栈，都可以以任意顺序访问节点（栈的情况下进入一个节点就需要立刻弹出，才能找到连接该节点的其他节点）。\
## bellman_ford之判断负权回路
Bellman-ford算法可以用来判断是否存在负权回路
### 难点
如果一个图中存在负权回路，那么即使松弛了n-1次，在第n次的时候，距离还是会变化，mindist还是会继续缩小（区别于有负权值和有负权回路，有负权值不一定n次松弛后mindist还会减少）。因此只需要判断第n次松弛是否将mindist减少即可。
### 代码
~~~C++
#include <iostream>
#include <vector>
#include <list>
#include <climits>
using namespace std;

int main() {
    int n, m, p1, p2, val;
    cin >> n >> m;

    vector<vector<int>> grid;

    for(int i = 0; i < m; i++){
        cin >> p1 >> p2 >> val;
        // p1 指向 p2，权值为 val
        grid.push_back({p1, p2, val});

    }
    int start = 1;  // 起点
    int end = n;    // 终点

    vector<int> minDist(n + 1 , INT_MAX);
    minDist[start] = 0;
    bool flag = false;
    for (int i = 1; i <= n; i++) { // 这里我们松弛n次，最后一次判断负权回路
        for (vector<int> &side : grid) {
            int from = side[0];
            int to = side[1];
            int price = side[2];
            if (i < n) {
                if (minDist[from] != INT_MAX && minDist[to] > minDist[from] + price) minDist[to] = minDist[from] + price;
            } else { // 多加一次松弛判断负权回路
                if (minDist[from] != INT_MAX && minDist[to] > minDist[from] + price) flag = true;

            }
        }

    }

    if (flag) cout << "circle" << endl;
    else if (minDist[end] == INT_MAX) {
        cout << "unconnected" << endl;
    } else {
        cout << minDist[end] << endl;
    }
}
~~~
### 思想
Bellman-Ford算法的核心是维护一个mindist数组，判断并记录从原点到达其他节点的最短距离。
## bellman_ford之单源有限最短路
Bellman-Ford升级版本，求经过最多k个节点，求从原点到固定结点的距离。
### 难点
由于bellman-ford需要对所有边进行n-1次的松弛，每i次松弛都是对起点距离为i的节点更新mindist。因此可以想到仅更新k + 1次的办法。但是由于每次松弛都是对所有的边进行松弛，所以会对其他节点的mindist进行更新，此时就忽略了这个限制，提前更新了下面的节点。
在更新当前节点的mindist时，需要基于之前的mindist来更新，保证其不受到所有边一次性松弛的影响
### 代码
~~~C++
// 版本二
#include <iostream>
#include <vector>
#include <list>
#include <climits>
using namespace std;

int main() {
    int src, dst,k ,p1, p2, val ,m , n;
    
    cin >> n >> m;

    vector<vector<int>> grid;

    for(int i = 0; i < m; i++){
        cin >> p1 >> p2 >> val;
        grid.push_back({p1, p2, val});
    }

    cin >> src >> dst >> k;

    vector<int> minDist(n + 1 , INT_MAX);
    minDist[src] = 0;
    vector<int> minDist_copy(n + 1); // 用来记录上一次遍历的结果
    for (int i = 1; i <= k + 1; i++) {
        minDist_copy = minDist; // 获取上一次计算的结果
        for (vector<int> &side : grid) {
            int from = side[0];
            int to = side[1];
            int price = side[2];
            // 注意使用 minDist_copy 来计算 minDist 
            if (minDist_copy[from] != INT_MAX && minDist[to] > minDist_copy[from] + price) {  
                minDist[to] = minDist_copy[from] + price;
            }
        }
    }
    if (minDist[dst] == INT_MAX) cout << "unreachable" << endl; // 不能到达终点
    else cout << minDist[dst] << endl; // 到达终点最短路径

}
~~~
### 思考
理清什么时候使用当前的mindist更新，由于本题中要求一定是从原点不断更新k+1次到达终点，因此如果采用当前的mindist更新，就会提前更新之后的节点，必须要用之前的mindist更新
