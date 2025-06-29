---
Title: Atcoder_Beginner_412
Date: 2025-06-28
---
# C, D, E
## C
给定一个序列，维持最左侧和最右侧的数不变，中间部分的数保留最少的个数，使最后的数列满足：S(i+1) <= 2 * S(i)
输入格式为：N（case个数）
          S1
          。。。
          Sn
要求返回每个case的结果
### 解法
最开始我就想到了贪心，因为要求保留最少的个数，肯定是要优先选择值较大的数，因为要保证S(i + 1) <= 2*S(i)。\
前面的数越大，后面的数能取到的范围就越大，也就越有可能覆盖到最后一个数，从而提前完成数字的选取。\
但我陷入了一个误区，就是想要通过排序确定顺序。\
一是如何确定排序，从大到小排和从小到大排，都不能确定有多少数可取。\
二是这是一个选择最少的数以达到要求的题，我需要一个个确认数，保证其满足条件。\
根据题解，解题思路可以分为：\
首先确定尽可能取最大的数，越大的数就能有有越大的可能提前概括最后一个数，从而结束程序。\
固定一个需要比较的数，每次对比这个数满足条件的最大的数，标记为使用过。\
将这个数记录下来，用作下一次比较，同时这个数还会与最后一个数进行比较，查看是否满足条件。\
### 代码
~~~C++
#include <iostream>
#include <vector>
using namespace std;
void solve() {
  int N;
  cin >> N;
  vector<int> A(N);
  for (auto& a : A) cin >> a;
  vector<int> used(N);
  int ans = 1, last = 0;
  while (true) {
    if (A[last] * 2 >= A[N - 1]) {
      ans += 1;
      break;
    }
    int nxt = -1;
    for (int i = 1; i <= N - 1; i++) {
      if (used[i]) continue;
      if (A[last] * 2 >= A[i]) { // 是否满足条件
        if (nxt != -1 && A[nxt] >= A[i]) continue; // 找到较大的一个满足条件的值， 与A[last]比较
        nxt = i;
      }
    }
    if (nxt == -1 || A[nxt] <= A[last]) { // 找到的值一定要比比较的值大
      cout << -1 << endl;
      return;
    }
    ans++, last = nxt, used[nxt] = 1;
  }
  cout << ans << endl;
}
int main() {
  int T;
  cin >> T;
  while (T--) solve();
}
~~~
### 总结
相比普通贪心更难的题目，核心在于理解条件和越少越好的要求。
