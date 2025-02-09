---
Title: GraphTheory02
Date: 2025-02-09
---
# 算法训练营第五十一天 | 99. 岛屿数量 深搜 | 岛屿数量 广搜 | 100.岛屿的最大面积 
## 99 岛屿数量深搜
给定一个由 1（陆地）和 0（水）组成的矩阵，你需要计算岛屿的数量。岛屿由水平方向或垂直方向上相邻的陆地连接而成，并且四周都是水域。你可以假设矩阵外均被水包围。
求矩阵中陆地组成的岛屿的数量。
### 难点
本题要求使用深度优先搜索，即从一个节点进入，通过递归不断向当前节点的下一个节点进发。直到所有的节点都被访问过。一般使用visited数组进行记录。
记录一个dfs的模板
~~~
void dfs(vector<vector<int>>& graph, vector<vector<int>>& visited, int row, int col) {
    int m = graph.size();
    int n = graph[0].size();
    if (visited[row][col]) {
        return;
    }
    visited[row][col] = true;
    for(int i = 0;i < 4; i++) {
        int next_row = row + direction[i][0];
        int next_col = col + direction[i][1];
        if ((next_row >= n) || (next_row < 0) || (next_col < 0) || (next_col >= m)) {
            continue;
        }
        dfs(graph, visited, next_row, next_col);
    }
}
~~~
c++
