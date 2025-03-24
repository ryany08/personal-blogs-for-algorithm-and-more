# 算法随想录 | Floyd算法 | A*算法
## Floyd算法
寻找多源路径的最小值，求多个起点到多个终点的多条最短路径
### 难点
使用动态规划确定任意两个节点之间的路径。动态规划所使用的矩阵能够很好的对应两个节点之间目前的最短距离。使用三维的向量矩阵作为动态规划中的状态：dp[i][j][k]表示节点i到节点j经过1...k个节点集合中的一个节点的最短距离。\
状态转移方程为：dp[i][j][k] = min(dp[i][k][k-1] + dp[k][j][k-1], dp[i][j][k - 1],代表两种情况，一是从i到中间节点k在到j节点，二是继承自原来的节点。所要做的就是找到这两种情况的最小值。\
第二种情况也提示了需要把k放在i和j循环之外，因为不这样做就无法寻找第二种情况的最小值。
### 代码
~~~c++
#include <iostream>
#include <vector>
#include <list>
using namespace std;

int main() {
    int n, m, p1, p2, val;
    cin >> n >> m;

    vector<vector<vector<int>>> grid(n + 1, vector<vector<int>>(n + 1, vector<int>(n + 1, 10005)));  // 因为边的最大距离是10^4
    for(int i = 0; i < m; i++){
        cin >> p1 >> p2 >> val;
        grid[p1][p2][0] = val;
        grid[p2][p1][0] = val; // 注意这里是双向图

    }
    // 开始 floyd
    for (int k = 1; k <= n; k++) {
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= n; j++) {
                grid[i][j][k] = min(grid[i][j][k-1], grid[i][k][k-1] + grid[k][j][k-1]);
            }
        }
    }
    // 输出结果
    int z, start, end;
    cin >> z;
    while (z--) {
        cin >> start >> end;
        if (grid[start][end][n] == 10005) cout << -1 << endl;
        else cout << grid[start][end][n] << endl;
    }
}
~~~
### 思考
如何得出一个三维的dp并使用dp数组进行状态转化是一个问题
## A*算法
广度优先搜索的启发式算法
