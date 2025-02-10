---
Title: GraphTheory03
Date: 2025-02-10
---
# 算法训练营第五十二天 | 101. 孤岛的总面积 | 102. 沉没孤岛 | 103. 水流问题 | 104.建造最大岛屿 
## 101. 孤岛的总面积
给定一个由 1（陆地）和 0（水）组成的矩阵，岛屿指的是由水平或垂直方向上相邻的陆地单元格组成的区域，且完全被水域单元格包围。孤岛是那些位于矩阵内部、所有单元格都不接触边缘的岛屿。需要计算所有孤岛的总面积，岛屿面积的计算方式为组成岛屿的陆地的总数
### 难点
本体的难点在于如何判断一座岛屿是否为孤岛。**第一种想法**：传入一个布尔值，判断是否有接触到边缘，同时返回计算这个岛屿的面积，最终加起来就是孤岛的面积。
### 代码
第一种想法代码：
~~~c++
#include<iostream>
#include<vector>
using namespace std;

int direction[4][2] = {0, -1, 1, 0, 0, 1, -1, 0};
// for saving storage, use &
void dfs(vector<vector<int>>& graph, vector<vector<bool>>& visited, int row, int col, int& size, bool& isnotalone) {
    int n = graph.size();
    int m = graph[0].size();
    if (visited[row][col] || graph[row][col] == 0) {
        return;
    }
    visited[row][col] = true;
    if ((row == 0) || (row == n - 1) || (col == 0) || (col == m - 1)) { // to the edge of the grid
        isnotalone = true;
    }
    size += 1;
    for(int i = 0;i < 4; i++) {
        int next_row = row + direction[i][1];
        int next_col = col + direction[i][0];
        if ((next_row >= n) || (next_row < 0) || (next_col < 0) || (next_col >= m)) { // can not let it surpass grid
            continue;
        }
        dfs(graph, visited, next_row, next_col, size, isnotalone);
    }
}

int main(){
    int n, m;
    cin >> n >> m;
    vector<vector<int>> grid(n, vector<int>(m, 0));
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < m; j++) {
            cin >> grid[i][j];
        }
    }
    
    vector<vector<bool>> visited(n, vector<bool>(m, false));
    
    int results = 0; // the number of islands
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < m; j++) {
            int result = 0;
            bool isnotalone = false;
            dfs(grid, visited, i, j, result, isnotalone); // 将与其链接的陆地都标记上 true
            if (!isnotalone) {
                results += result;
            }                
        }
            
    }
    cout<<results<<endl;
    return  0;
}
~~~
### 解析
本题的第二种思想：使用visited数组，既然visited数组可以记录访问过的元素，那就让其优先将边缘的元素访问，留下的元素组成的岛屿就是孤岛，这种方法就**不会刻意去访问孤岛**。
~~~c++
#include<iostream>
#include<vector>
#include<cmath>
using namespace std;
 
 
int directions[4][2] = { 1, 0, 0, 1, -1, 0, 0, -1 };
 
void dfs(vector<vector<int>>& graph, vector<vector<int>>& visited, int row, int col, int& temp) {
    if(visited[row][col] == 1  || graph[row][col] != 1){
        return;
    }
    visited[row][col] = 1;
    temp += 1;
    for(int i = 0; i < 4; i++) {
        int drow = row + directions[i][1];
        int dcol = col + directions[i][0];
        if (dcol < graph[0].size() && drow < graph.size() && drow >= 0 && dcol >= 0) {
            dfs(graph, visited, drow, dcol, temp);
        }
    }
}
 
int main() {
    int N, M;
    cin>>N>>M;
    int size = 0;
    vector<vector<int>> graph(N, vector<int>(M, 0));
    vector<vector<int>> visited(N, vector<int>(M ,0));
    for(int i = 0; i < N; i++) {
        for(int j = 0; j < M; j++) {
            cin>>graph[i][j];
        }
    }
     for (int i = 0; i < N; i++) {
        int temp = 0;
        dfs(graph, visited, i, 0, temp);
        dfs(graph, visited, i, M - 1, temp);
    }
    // 从上边和下边 向中间遍历
    for (int j = 0; j < M; j++) {
        int temp = 0;
        dfs(graph, visited, 0, j, temp);
        dfs(graph, visited, N - 1, j, temp);
    }
    for(int i = 0; i < N; i++) {
        for(int j = 0; j < M; j++) {
            int temp = 0;
            dfs(graph, visited, i, j, temp);
            size += temp;
        }
    }
    cout<<size<<endl;
    return 0;
~~~
## 102. 沉没孤岛
### 难点
本题也是和101孤岛总面积一样，难点在于寻找孤岛和修改元素值使其沉没。第一种想法：直接在原来的grid中沉没孤岛，此时就不能使用寻找孤岛的第一种想法了，因为这会导致提前将岛屿沉没。使用第二种方法从四周开始访问岛屿就正好。
### 代码
~~~c++
#include<iostream>
#include<vector>
#include<cmath>
using namespace std;
 
 
int directions[4][2] = { 1, 0, 0, 1, -1, 0, 0, -1 };
 
void dfs(vector<vector<int>>& graph, vector<vector<int>>& visited, int row, int col, bool isToChange) {
    if(visited[row][col] == 1  || graph[row][col] != 1){
        return;
    }
    if (isToChange) {
        graph[row][col] = graph[row][col] == 1 ? 0 : 0;
    }
    visited[row][col] = 1;
    for(int i = 0; i < 4; i++) {
        int drow = row + directions[i][1];
        int dcol = col + directions[i][0];
        if (dcol < graph[0].size() && drow < graph.size() && drow >= 0 && dcol >= 0) {
            dfs(graph, visited, drow, dcol, isToChange);
        }
    }
}
 
int main() {
    int N, M;
    cin>>N>>M;
    int change = false;
    vector<vector<int>> graph(N, vector<int>(M, 0));
    vector<vector<int>> visited(N, vector<int>(M ,0));
    for(int i = 0; i < N; i++) {
        for(int j = 0; j < M; j++) {
            cin>>graph[i][j];
        }
    }
     for (int i = 0; i < N; i++) {
        dfs(graph, visited, i, 0, change);
        dfs(graph, visited, i, M - 1, change);
    }
    // 从上边和下边 向中间遍历
    for (int j = 0; j < M; j++) {
        dfs(graph, visited, 0, j, change);
        dfs(graph, visited, N - 1, j, change);
    }
    for(int i = 0; i < N; i++) {
        for(int j = 0; j < M; j++) {
            dfs(graph, visited, i, j, !change);
        }
    }
    for (int i = 0; i < N; i++) {
        for (int j = 0; j < M; j++) {
            cout << graph[i][j] << " ";
        }
        cout << endl;
    }
    return 0;
}
~~~
### 思考
因为从四周访问和最终遍历可以分为两块，因此在最终遍历的dfs中添加原地修改的逻辑可，这个逻辑可以使用一个布尔值作为开关。
## 104.建造最大岛屿
给定一个由 1（陆地）和 0（水）组成的矩阵，你最多可以将矩阵中的一格水变为一块陆地，在执行了此操作之后，矩阵中最大的岛屿面积是多少
### 难点
主要的难点在于如何判断需要将哪个水填平组成新的陆地。暴力解法通过遍历所有的水判断形成的陆地的最大面积，荣誉的地方在于，每次填满一个水都需要重新计算所有陆地的面积一次，就带来了不便。如果能够一次就计算出所有的陆地面积并存储下来，同时还能方便搜索面积，就能把这段时间节省下来。
### 代码
~~~c++
#include <iostream>
#include <vector>
#include <unordered_set>
#include <unordered_map>
using namespace std;
int n, m;
int count;

int dir[4][2] = {0, 1, 1, 0, -1, 0, 0, -1}; // 四个方向
void dfs(vector<vector<int>>& grid, vector<vector<bool>>& visited, int x, int y, int mark) {
    if (visited[x][y] || grid[x][y] == 0) return; // 终止条件：访问过的节点 或者 遇到海水
    visited[x][y] = true; // 标记访问过
    grid[x][y] = mark; // 给陆地标记新标签
    count++;
    for (int i = 0; i < 4; i++) {
        int nextx = x + dir[i][0];
        int nexty = y + dir[i][1];
        if (nextx < 0 || nextx >= n || nexty < 0 || nexty >= m) continue;  // 越界了，直接跳过
        dfs(grid, visited, nextx, nexty, mark);
    }
}

int main() {
    cin >> n >> m;
    vector<vector<int>> grid(n, vector<int>(m, 0));

    for (int i = 0; i < n; i++) {
        for (int j = 0; j < m; j++) {
            cin >> grid[i][j];
        }
    }
    vector<vector<bool>> visited(n, vector<bool>(m, false)); // 标记访问过的点
    unordered_map<int ,int> gridNum;
    int mark = 2; // 记录每个岛屿的编号
    bool isAllGrid = true; // 标记是否整个地图都是陆地
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < m; j++) {
            if (grid[i][j] == 0) isAllGrid = false;
            if (!visited[i][j] && grid[i][j] == 1) { // 为了count的计算
                count = 0;
                dfs(grid, visited, i, j, mark); // 将与其链接的陆地都标记上 true
                gridNum[mark] = count; // 记录每一个岛屿的面积
                mark++; // 记录下一个岛屿编号
            }
        }
    }
    if (isAllGrid) {
        cout << n * m << endl; // 如果都是陆地，返回全面积
        return 0; // 结束程序
    }

    // 以下逻辑是根据添加陆地的位置，计算周边岛屿面积之和
    int result = 0; // 记录最后结果
    unordered_set<int> visitedGrid; // 标记访问过的岛屿
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < m; j++) {
            count = 1; // 记录连接之后的岛屿数量
            visitedGrid.clear(); // 每次使用时，清空
            if (grid[i][j] == 0) {quanwei
                for (int k = 0; k < 4; k++) {
                    int neari = i + dir[k][1]; // 计算相邻坐标
                    int nearj = j + dir[k][0];
                    if (neari < 0 || neari >= n || nearj < 0 || nearj >= m) continue;
                    if (visitedGrid.count(grid[neari][nearj])) continue; // 添加过的岛屿不要重复添加
                    // 把相邻四面的岛屿数量加起来
                    count += gridNum[grid[neari][nearj]];
                    visitedGrid.insert(grid[neari][nearj]); // 标记该岛屿已经添加过
                }
            }
            result = max(result, count);
        }
    }
    cout << result << endl;

}
~~~
### 思考
注意到0和1分别表示水和陆地，可以使用2，3，4等数记录每一块岛屿，这就能轻松计算和存储岛屿的面积。还需要注意的是：所给的grid可能全为陆地，就需要额外判断这一种情况。
