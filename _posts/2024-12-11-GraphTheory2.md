---
Title: GraphTheory02
Date: 2024-12-11
---
# 算法训练营第五十一天 | 99. 岛屿数量（深度搜索） | 99. 岛屿数量（广度搜索） | 100. 岛屿的最大面积
## 岛屿数量（深搜）
给定一个由 1（陆地）和 0（水）组成的矩阵，你需要计算岛屿的数量。岛屿由水平方向或垂直方向上相邻的陆地连接而成，并且四周都是水域。你可以假设矩阵外均被水包围。
### 关键词
图论，深度优先搜索
### 链接
[link](https://kamacoder.com/problempage.php?pid=1171)
### 难点
深度优先搜索遍历虽然也是采用回溯的思路，但是由于图的多连通性，它不像树一样严格区分进入和输出，图的节点可能还会通过其他路径返回入度节点。这样会使用visited数组。\
每次访问到一个节点，如果没有访问过，那就记作已经访问过，同时，这个标记不像树结构的回溯一样是需要删除的，因为树结构能保证访问过一个节点之后不会再次访问，而图结构不能。
### 代码
DFS有两种写法，一是不管条件，每次遇到一个节点就放入dfs栈内，由dfs判断并更新visited数组。二是在进入dfs之前先判断当前节点的条件，并修改visited数组，进入dfs后直接向下查找结点。\
以下是第一种方法。
~~~C++
#include<iostream>
#include<vector>
using namespace std;


int directions[4][2] = { 1, 0, 0, 1, -1, 0, 0, -1 };

void dfs(vector<vector<int>>& graph, vector<vector<int>>& visited, int row, int col) {
    if(visited[row][col] == 1  || graph[row][col] != 1){
        return;
    }
    visited[row][col] = 1;
    for(int i = 0; i < 4; i++) {
        int drow = row + directions[i][1];
        int dcol = col + directions[i][0];
        if (dcol < graph[0].size() && drow < graph.size() && drow >= 0 && dcol >= 0) {
            dfs(graph, visited, drow, dcol);
        }
    }
}

int main() {
    int M, N;
    cin>>N>>M;
    int islands = 0;
    vector<vector<int>> graph(N, vector<int>(M, 0));
    vector<vector<int>> visited(N, vector<int>(M ,0));
    for(int i = 0; i < N; i++) {
        for(int j = 0; j < M; j++) {
            cin>>graph[i][j];
        }
    }
    for(int i = 0; i < N; i++) {
        for(int j = 0; j < M; j++) {
            if(graph[i][j] == 1 && visited[i][j] == 0) {
                dfs(graph, visited, i, j);
                islands += 1;
            }
        }
    }
    cout<<islands<<endl;
    return 0;
}
~~~
## 100. 岛屿的最大面积
给定一个由 1（陆地）和 0（水）组成的矩阵，计算岛屿的最大面积。岛屿面积的计算方式为组成岛屿的陆地的总数。岛屿由水平方向或垂直方向上相邻的陆地连接而成，并且四周都是水域。你可以假设矩阵外均被水包围
### guan
