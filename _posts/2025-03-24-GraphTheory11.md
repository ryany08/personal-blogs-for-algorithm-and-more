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
### 难点
如何理解启发式算法：启发式算法提供了一个高效的算法，它不一定是最优的算法，但是是有效率的算法。\
Astar 关键在于 启发式函数， 也就是 影响 广搜或者 dijkstra 从 容器（队列）里取元素的优先顺序。一般的广度优先搜索算法都是一圈一圈向外寻找，把所有路径的权值都同等看待，而启发式算法通过不同权重衡量不同的路径的价值。\
这就需要一个函数对路径的权值进行计算，一般采用欧氏距离或曼哈顿距离。由于需要计算权值，使得Astar只能用于直到终点的题目，只有直到终点才能计算所需要的权重。\
每次输入一个节点，就循环计算它可能走的下一个节点，计算这些节点的权重，同时按照权重大小将这些下一步的节点排序，优选走权重大的节点。
### 代码
~~~C++
#include<iostream>
#include<queue>
#include<string.h>
using namespace std;
int moves[1001][1001];
int dir[8][2]={-2,-1,-2,1,-1,2,1,2,2,1,2,-1,1,-2,-1,-2};
int b1, b2;
// F = G + H
// G = 从起点到该节点路径消耗
// H = 该节点到终点的预估消耗

struct Knight{
    int x,y;
    int g,h,f;
    bool operator < (const Knight & k) const{  // 重载运算符， 从小到大排序
     return k.f < f;
    }
};

priority_queue<Knight> que;

int Heuristic(const Knight& k) { // 欧拉距离
    return (k.x - b1) * (k.x - b1) + (k.y - b2) * (k.y - b2); // 统一不开根号，这样可以提高精度
}
void astar(const Knight& k)
{
    Knight cur, next;
	que.push(k);
	while(!que.empty())
	{
		cur=que.top(); que.pop();
		if(cur.x == b1 && cur.y == b2)
		break;
		for(int i = 0; i < 8; i++)
		{
			next.x = cur.x + dir[i][0];
			next.y = cur.y + dir[i][1];
			if(next.x < 1 || next.x > 1000 || next.y < 1 || next.y > 1000)
			continue;
			if(!moves[next.x][next.y])
			{
				moves[next.x][next.y] = moves[cur.x][cur.y] + 1;

                // 开始计算F
				next.g = cur.g + 5; // 统一不开根号，这样可以提高精度，马走日，1 * 1 + 2 * 2 = 5
                next.h = Heuristic(next);
                next.f = next.g + next.h;
                que.push(next);
			}
		}
	}
}

int main()
{
    int n, a1, a2;
    cin >> n;
    while (n--) {
        cin >> a1 >> a2 >> b1 >> b2;
        memset(moves,0,sizeof(moves));// 给moves数组快速赋值0，memset函数第一个参数位变量的地址， 第二位位需要填入的整数，注意这个整数会被先化成二进制后被填入，第三个参数表示填入的空间的大小，以字节为单位
        Knight start;
        start.x = a1;
        start.y = a2;
        start.g = 0;
        start.h = Heuristic(start);
        start.f = start.g + start.h;
		astar(start);
        while(!que.empty()) que.pop(); // 队列清空
		cout << moves[b1][b2] << endl;
	}
	return 0;
}
~~~
### 思考
Astar问题解决的是近似最有路线，而不是最优路线，通过更改计算权值的函数，可以得到不同的路线。
Astar通常可以被用在寻找近似最短最有路径，尤其是用在游戏和路径规划中
