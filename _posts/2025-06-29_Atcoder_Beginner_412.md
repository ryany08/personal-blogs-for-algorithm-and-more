---
Title: Atcoder_Beginner_412
Date: 2025-06-28
---
# C, D, E
## C
给定一个序列，维持最左侧和最右侧的数不变，对中间部分的数保留最少的个数，使最后的数列满足：S(i+1) <= 2 * S(i)，也就是右边的多米诺满足一定的条件，即小于左边多米诺的两倍
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
  for (auto& a : A) cin >> a;//无引用无法赋值
  vector<int> used(N);
  int ans = 1, last = 0; // ans为最终结果，last为当前比较的数的下标
  while (true) {
    if (A[last] * 2 >= A[N - 1]) { //固定与最后一个数进行比较，因为最后一个数和最初的一个数需要被保留
      ans += 1;
      break;
    }
    int nxt = -1; // nxt，用于标记当前候选的数
    for (int i = 1; i <= N - 1; i++) {
      if (used[i]) continue; // 
      if (A[last] * 2 >= A[i]) { // 是否满足多米诺条件，只有满足多米诺条件才会向后走，last从0开始，是因为下标为0，即第一个数必须进行比较和保留
        if (nxt != -1 && A[nxt] >= A[i]) continue; // 找到较大的一个满足条件的值， 因为我们要求空间内尽可能少的数，则数一定要尽可能大
        nxt = i;
      }
    }
    if (nxt == -1 || A[nxt] <= A[last]) { // 找到的值一定要比比较的值大，如果找到的数甚至比前一个小，那就说明这个数有无法将最后一个多米诺推倒，因为last标记的数已经无法将最后一个多米诺。而如果想要让选中的数最终将最后一个多米诺推倒，选中的多米诺之后一定会再选中比当前的多米诺更高的数，何不选这个更高的呢？
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
## E
有LCM sequence
LCMsequence指的是An = 1到n之间n个数的最小公倍数。\
求给定区间内不同的LCM sequence 个数
例如：在4到12之间，有
A4=12
A5=60
A6=60
A7=420
A8=840
A9=2520
A10=2520
A11=27720
A12=27720，
这9个数中一共有6个不同的个LCMsequence，因此结果为6
### 解题思路
完全不会
按照题解是求出左范围L + 1到右范围R之间的所有素数幂，素数幂指的是单一的素数的幂。4，5，7等都是。因此只要想到要求素数幂就能解开本题。但是怎么求？怎么想到的？\
按照解说视频，是通过induction，但是并没有给出证明\
### 代码
~~~C++
#include <cmath>
#include <iostream>
#include <vector>
using namespace std;
vector<int> prime_enumerate(int N) {
  vector<bool> is_prime(N + 1, true);
  vector<int> primes;
  if (N < 2) return primes;
  is_prime[0] = is_prime[1] = false;
  for (int i = 2; i * i <= N; ++i) {
    if (is_prime[i]) {
      for (int j = i * i; j <= N; j += i) is_prime[j] = false;
    }
  }
  for (int i = 2; i <= N; ++i) {
    if (is_prime[i]) primes.push_back(i);
  }
  return primes;
}
int main() {
  long long L, R;
  cin >> L >> R;
  vector<int> vis(R - L);
  int ans = 1;
  for (int p : prime_enumerate(sqrt(R) + 100)) {
    for (long long x = (L / p + 1) * p; x <= R; x += p) {
      if (vis[x - (L + 1)]) continue;
      vis[x - (L + 1)] = 1;
      long long y = x;
      while (y % p == 0) y /= p;
      if (y == 1) ans++;
    }
  }
  for (int v : vis) ans += v == 0;
  cout << ans << "\n";
}
~~~
### 总结
完全不理解，需要时间消化
