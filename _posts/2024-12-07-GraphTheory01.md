---
Title: GraphTheory01
Date: 2024-12-7
---
# 算法训练营第五十天 | 深度优先搜索理论基础 | 98. 所有可达路径 | 广度优先搜索理论基础
## 深度优先搜索
图的深度优先搜索（dfs），是一种对图进行遍历的搜索方法。
### 难点
图的深度优先搜索遍历有点像树的深度遍历，区别在于：图的节点可能会有重复遍历的可能而树节点不会。因此需要使用一个数组来标注是否已经遍历过。\
图的深度优先搜索具体代码有点像回溯，进入一个节点后用for遍历与这个节点相连的所有节点，如果该节点尚未遍历过，就进入该节点。
### 代码
~~~C++
vector<vector<int>> result; // 保存符合条件的所有路径
vector<int> path; // 起点到终点的路径
void dfs (图，目前搜索的节点) {
if (终止条件) {
    存放结果;
    return;
}
for (选择：本节点所连接的其他节点) {
    处理节点;
    dfs(图，选择的节点); // 递归
    回溯，撤销处理结果
}
}
~~~
## 98. 所有可达路径
给定一个有 n 个节点的有向无环图，节点编号从 1 到 n。请编写一个函数，找出并返回所有从节点 1 到节点 n 的路径。每条路径应以节点编号的列表形式表示
### 链接
[link](https://kamacoder.com/problempage.php?pid=1170)
### 关键词
图，图的深度优先搜索
### 难点
常规的深度优先搜索，注意开始和技术的条件，其一是遍历的节点到达n时结束，需要注意特殊情况：即节点只有一个时，因此需要加上路径至少为1的时候；其二，对于路径打印的问题，如果dfs当前输入的节点直接被放入path，那么在判断的时候
就会出现最后一个元素也在输出之前被放入路径数组的情况，会比较处理。
### 代码
~~~C++
# include<iostream>
# include<vector>
using namespace std;

int paths = 0;    
int N, M;

void dfs(vector<vector<int>>& graph, int node, vector<int>& visited, vector<int>& path) {
    if(node == (N - 1) && path.size() > 0) {
        paths += 1;
        for(int el:path) {
            cout<<el + 1<<' ';
        }
        cout<<node + 1<<endl;
        return;
    }
    visited[node] = 1;
    path.push_back(node);
    for(int i = 0; i < N; i++) {
        if (graph[node][i] != 0 && visited[i] == 0) {
            dfs(graph, i, visited, path);
        } 
    }
    visited[node] = 0;
    path.pop_back();
}

int main() {
    cin>>N>>M;
    vector<int> visited(N, 0);
    vector<int> path;
    vector<vector<int>> graph(N, vector<int>(N, 0));
    for(int i = 0; i < M; i++) {
        int start, end;
        cin>>start>>end;
        graph[start - 1][end - 1] = 1;    
    }
    dfs(graph, 0, visited, path);
    if(paths == 0) {
        cout<<-1<<endl;
    }
    return 0;
}
~~~
## 广度优先搜索理论基础
广度优先搜索是图的一种遍历算法
### 难点
广度优先搜索理论类似于树的层次遍历，依赖于队列，每一个节点在输出时就会把所有没有访问过的节点放入队列中。直到所有的节点完成。
### 代码
~~~C++
int dir[4][2] = {0, 1, 1, 0, -1, 0, 0, -1}; // 表示四个方向
// grid 是地图，也就是一个二维数组
// visited标记访问过的节点，不要重复访问
// x,y 表示开始搜索节点的下标
void bfs(vector<vector<char>>& grid, vector<vector<bool>>& visited, int x, int y) {
    queue<pair<int, int>> que; // 定义队列
    que.push({x, y}); // 起始节点加入队列
    visited[x][y] = true; // 只要加入队列，立刻标记为访问过的节点
    while(!que.empty()) { // 开始遍历队列里的元素
        pair<int ,int> cur = que.front(); que.pop(); // 从队列取元素
        int curx = cur.first;
        int cury = cur.second; // 当前节点坐标
        for (int i = 0; i < 4; i++) { // 开始想当前节点的四个方向左右上下去遍历
            int nextx = curx + dir[i][0];
            int nexty = cury + dir[i][1]; // 获取周边四个方向的坐标
            if (nextx < 0 || nextx >= grid.size() || nexty < 0 || nexty >= grid[0].size()) continue;  // 坐标越界了，直接跳过
            if (!visited[nextx][nexty]) { // 如果节点没被访问过
                que.push({nextx, nexty});  // 队列添加该节点为下一轮要遍历的节点
                visited[nextx][nexty] = true; // 只要加入队列立刻标记，避免重复访问
            }
        }
    }

}
~~~
