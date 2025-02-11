---
Title: GraphTheory04
Date: 2024-02-11
---
# 算法训练营第五十三天 | 110. 字符串接龙 | 105 有向图的完全可达性 | 106 岛屿的周长
## 110 字符串接龙
字典 strList 中从字符串 beginStr 和 endStr 的转换序列是一个按下述规格形成的序列：
序列中第一个字符串是 beginStr。\
序列中最后一个字符串是 endStr。\
每次转换只能改变一个字符。\
转换过程中的中间字符串必须是字典 strList 中的字符串。\
给你两个字符串 beginStr 和 endStr 和一个字典 strList，找到从 beginStr 到 endStr 的最短转换序列中的字符串数目。如果不存在这样的转换序列，返回 0
### 难点
本题的主要难点有两个：其一是理解字符转化的方式其实就是图的连接：其二是理解广度优先搜索遍历解决最短路径的思想\
首先其一，字符串之间的转化可以视为无向图的连接，这是由于两个字符串是可以相互转化的，由于这是等长度字符串在字符层面上的转化，而不是字符的累计形成的字符串，因此本题作为图而不是树会更好理解。\
其二，广度优先搜索算法是对每个节点都的所有连接的节点进行访问过后，才进入到这些节点连接的节点，是一层一层访问的。也就是说在访问当前节点的所有连接节点的时候，这些节点到当前节点的距离都是一致的，位于同一层。
### 代码
~~~c++
#include <iostream>
#include <vector>
#include <string>
#include <unordered_set>
#include <unordered_map>
#include <queue>
using namespace std;
int main() {
    string beginStr, endStr, str;
    int n;
    cin >> n;
    unordered_set<string> strSet;
    cin >> beginStr >> endStr;
    for (int i = 0; i < n; i++) {
        cin >> str;
        strSet.insert(str);
    }

    // 记录strSet里的字符串是否被访问过，同时记录路径长度
    unordered_map<string, int> visitMap; // <记录的字符串，路径长度>

    // 初始化队列
    queue<string> que;
    que.push(beginStr);

    // 初始化visitMap
    visitMap.insert(pair<string, int>(beginStr, 1));

    while(!que.empty()) {
        string word = que.front();
        que.pop();
        int path = visitMap[word]; // 这个字符串在路径中的长度

        // 开始在这个str中，挨个字符去替换
        for (int i = 0; i < word.size(); i++) {
            string newWord = word; // 用一个新字符串替换str，因为每次要置换一个字符

            // 遍历26的字母
            for (int j = 0 ; j < 26; j++) {
                newWord[i] = j + 'a';
                if (newWord == endStr) { // 发现替换字母后，字符串与终点字符串相同
                    cout <<  path + 1 << endl; // 找到了路径 
                    return 0;
                }
                // 字符串集合里出现了newWord，并且newWord没有被访问过
                if (strSet.find(newWord) != strSet.end()
                        && visitMap.find(newWord) == visitMap.end()) {
                    // 添加访问信息，并将新字符串放到队列中
                    visitMap.insert(pair<string, int>(newWord, path + 1));
                    que.push(newWord);
                }
            }
        }
    }

    // 没找到输出0
    cout << 0 << endl;

}
~~~
### 思考
本题对于图的构建也是一个难点。很容易就想到先构建出图再进行路径查找的方法。但其实也可以一边构建图，一边进行广度搜索。
## 105 有向图的完全可达性
检查一个图从节点1到所有节点的可连通性
### 难点
需要熟练掌握对于邻接矩阵和邻接表两种形式的图的BFS和DFS。\
无论是哪种形式，都需要一个长度为u节点总数n的visited数组，记录是否已经访问过该节点。\
对于邻接矩阵，检查其是否连通，从第一个节点入手，检查graph[1]中的所有连接的节点。\
对于邻接表，也是从第一个节点入手，检查graph[1]中的相邻节点，再从这些节点进入深层节点。
### 代码
~~~c++
#include <iostream>
#include <vector>
#include <list>
using namespace std;

void dfs(const vector<list<int>>& graph, int key, vector<bool>& visited) {
    if (visited[key]) {
        return;
    }
    visited[key] = true;
    list<int> keys = graph[key];
    for (int key : keys) {
        // 深度优先搜索遍历
        dfs(graph, key, visited);
    }
}

int main() {
    int n, m, s, t;
    cin >> n >> m;

    // 节点编号从1到n，所以申请 n+1 这么大的数组
    vector<list<int>> graph(n + 1); // 邻接表
    while (m--) {
        cin >> s >> t;
        // 使用邻接表 ，表示 s -> t 是相连的
        graph[s].push_back(t);
    }
    vector<bool> visited(n + 1, false);
    dfs(graph, 1, visited);
    //检查是否都访问到了
    for (int i = 1; i <= n; i++) {
        if (visited[i] == false) {
            cout << -1 << endl;
            return 0;
        }
    }
    cout << 1 << endl;
}
~~~
## 106 岛屿的周长
给定一个由 1（陆地）和 0（水）组成的矩阵，岛屿是被水包围，并且通过水平方向或垂直方向上相邻的陆地连接而成的。\
你可以假设矩阵外均被水包围。在矩阵中恰好拥有一个岛屿，假设组成岛屿的陆地边长都为 1，请计算岛屿的周长。岛屿内部没有水域
### 难点
这道题与图的搜索算法的区别在于，这道题中只有一个岛屿，只需要遍历这个矩阵就能找到这个岛。而其他的计算孤岛的面积等题，由于存在多个岛屿，且孤岛需要通过其他岛屿进行判定，单纯的遍历矩阵无法确定岛屿的具体位置。
### 代码
每次找到一个块陆地，就往它的四周去寻找，如果这块陆地的四周超过边界（说明这块陆地在边界上），或四周为水域，则说明某一个方向存在长度为1的边。不需要visited数组是因为，矩阵的遍历对于每个元素只会遍历一次，不会重复。
~~~c++
#include <iostream>
#include <vector>
using namespace std;
int main() {
    int n, m;
    cin >> n >> m;
    vector<vector<int>> grid(n, vector<int>(m, 0));
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < m; j++) {
            cin >> grid[i][j];
        }
    }
    int direction[4][2] = {0, 1, 1, 0, -1, 0, 0, -1};
    int result = 0;
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < m; j++) {
            if (grid[i][j] == 1) {
                for (int k = 0; k < 4; k++) {       // 上下左右四个方向
                    int x = i + direction[k][0];
                    int y = j + direction[k][1];    // 计算周边坐标x,y
                    if (x < 0                       // x在边界上
                            || x >= grid.size()     // x在边界上
                            || y < 0                // y在边界上
                            || y >= grid[0].size()  // y在边界上
                            || grid[x][y] == 0) {   // x,y位置是水域
                        result++;
                    }
                }
            }
        }
    }
    cout << result << endl;

}
~~~
### 思考
如何区分使用图算法的题目和不使用图算法的题目。一般来说，如果只有一个节点，或者只对一个节点的性质进行分析，这些题目就不需要使用图算法。反之，对于考虑多个节点关系（边），节点与节点是否连通的题，就需要图算法。
