---
title: Codeforces 547C
date: '2022-07-15'
categories:
  - codeforces
tags:
  - 莫比乌斯反演

---

> https://codeforces.com/contest/547/problem/C

题意:
给 $n$ 个数字和 $q$ 次操作，列表初始为空，每次操作加入一个数字到列表或者删除一个列表里的数字。问每次操作后列表中 $gcd(a_{i}, a_{j}) = 1 (i < j)$ 的对数。

题解：
假设当前在列表中最大的数字是 $m$。

定义 $f(n)$ 表示列表中 $gcd(a_{i}, a_{j}) = n$ 的对数, 这里 $i$ 可以比 $j$ 大。也就是:

$$f(n) = \sum_{i=1}^{m} \sum_{j=1}^{m}\left[\operatorname{gcd}\left(a_{i}, a_{j}\right)=n\right]$$

$$F(n) = \sum_{n | d} f(d)$$
把 $F$ 反演得到 $f$：

$$F(n)=\sum_{n \mid d} f(d) \Leftrightarrow f(n)=\sum_{n \mid d} \mu\left(\frac{d}{n}\right) F(d)$$

其实我们只需要求 $f(1)$：
$$f(1)=\sum^{m}_{d=1} \mu\left(d\right) F(d)$$

现在的问题就是怎么求出 $F(d)$, 在序列中选两个数，这两个数的 $\gcd$ 是 $d$ 的倍数。 $F(d)$ 等于满足这个条件的对数。观察一下就可以知道:
$$F(d) = (\sum_{d|i} cnt(i)) ^ {2}$$

在题目中，每一次操作，只会增加或者删除一个数，受到影响的 $F(d)$ 只有增加或者删除的那个数的因数。因数最多只有 $\sqrt n$ 个，所以每次操作可以直接花 $\sqrt n$ 的时间把受影响的 $F(d)$ 更新一下。然后统计 $f(1)$ 。

```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;

struct Mobius {
  vector<int> is, prinum, miu;
  vector<int> cnt;
  vector<vector<int>> belong;
  int ans;

  Mobius(int n) : is(n + 1, 0), miu(n + 1, 1) {
    ans = 0;
    cnt.resize(n + 1, 0);
    belong.resize(n + 1);
    prinum.reserve(n + 1);
    for (int i = 2; i <= n; i++) {
      if (!is[i]) prinum.push_back(i), miu[i] = -1;  
      for (auto &it : prinum) {
        if (it * i > n) break;
        is[it * i] = 1;
        if (i % it == 0) {
          miu[it * i] = 0;
          break;
        } else {
          miu[it * i] = -1 * miu[i];
        }
      }
    }
    for (int i = 1; i <= n; i++) {
      for (int j = i; j <= n; j += i) {
        belong[j].push_back(i);
      }
    }
  }

  void update(int val, int o) {
    for (auto &it : belong[val]) {
      ans -= cnt[it] * cnt[it] * miu[it];
      cnt[it] += o;
      ans += cnt[it] * cnt[it] * miu[it];
    }
  }
};

signed main() {
  ios::sync_with_stdio(false); 
  cin.tie(0);
  
  int n, q; cin >> n >> q;
  vector<int> a(n + 1);
  for (int i = 1; i <= n; i++) {
    cin >> a[i]; 
  }

  Mobius T(5e5 + 10);

  vector<int> has(n + 1, 0);
  int cnt_1 = 0;

  for (int i = 1; i <= q; i++) {
    int id; cin >> id; 
    if (has[id]) {
      T.update(a[id], -1);
      if (a[id] == 1) cnt_1--;
    } else {
      T.update(a[id], 1);
      if (a[id] == 1) cnt_1++;
    }
    has[id] ^= 1;
    cout << (T.ans - cnt_1) / 2 << endl;
  }

  return 0;
}
```


