---
title: "Codeforces Round #807 (Div. 2)"
date: 2022-07-16T03:05:31+08:00
categories:
  - Codeforces
tags:
  - 数据结构
  - 贪心

---

# A - Mark the Photographer
排序后第一行 $1$ 到 $n$, 第二行 $n + 1$ 到 $n$。检查是否满足高度差大于等于 $x$。可以用反证法证明。

```cpp
//
//  a.cpp
//  2022-07-15 21:27
//
//  Created by liznb
//  

#include <bits/stdc++.h>
using namespace std;

#ifdef LOCAL
#include "/Users/liznb/algo/liznb.h"
#endif

#define int long long

signed main() {
  ios::sync_with_stdio(false); 
  cin.tie(0);
   
  int z; cin >> z; 
  while (z--) {
    int n, k; cin >> n >> k;
    vector<int> a(n * 2 + 1); 
    for (int i = 1; i <= n * 2; i++) {
      cin >> a[i];   
    }
    sort(a.begin() + 1, a.end());
    int flag = true; 
    for (int i = 1; i <= n; i++) {
      if (a[i + n] >= a[i] + k) continue;
      flag = false;
    }
    if (flag) cout << "YES" << endl;
    else cout << "NO" << endl;
  }


  return 0;
}
```

 
# B - Mark the Dust Sweeper
一次操作可以让一个 $0$ 被灰尘覆盖，如果所有 $0$ 都被灰尘覆盖至少一次，那么从左到右选择 $i$， $j$ 固定成 $n$, 显然就打扫完了。所以答案就是第一团灰尘右边所有 $0$ 的个数加上所有的灰尘个数(除去 $a_{n}$)。

```cpp
//
//  b.cpp
//  2022-07-15 21:44
//
//  Created by liznb
//  

#include <bits/stdc++.h>
using namespace std;

#ifdef LOCAL
#include "/Users/liznb/algo/liznb.h"
#endif

#define int long long

signed main() {
  ios::sync_with_stdio(false); 
  cin.tie(0);
   
  int z; cin >> z; 
  while (z--) {
    int n; cin >> n;
    vector<int> a(n + 1);
    int pos = -1;
    for (int i = 1; i <= n; i++) {
      cin >> a[i];
      if (a[i] && pos == -1) pos = i;
    }
    if (pos == -1) {
      cout << 0 << endl;
    } else {
      int cnt = 0;
      for (int i = pos + 1; i < n; i++) {
        if (a[i] == 0) cnt++;
      }
      for (int i = 1; i <= n - 1; i++) cnt += a[i];
      cout << cnt << endl;
    }
  }

  return 0;
}
```

# C - Mark and His Unfinished Essay
显然可以快速确定每一次询问的位置属于哪一次复制的哪一个位置。对于每一次复制后的位置，又可以快速定位到被复制的位置，所以不断递归确定询问的位置在对应的复制中的位置，直到确定在原数组的位置，输出答案即可。 

```cpp
//
//  c.cpp
//  2022-07-15 21:48
//
//  Created by liznb
//  

#include <bits/stdc++.h>
using namespace std;

#ifdef LOCAL
#include "/Users/liznb/algo/liznb.h"
#endif

#define int long long

signed main() {
  ios::sync_with_stdio(false); 
  cin.tie(0);
   
  int z; cin >> z; 
  while (z--) {
    int n, c, q; cin >> n >> c >> q;
    string s; cin >> s;
    vector<array<int, 2>> opt(c + 1);  
    vector<int> sum(c + 2, 0);
    sum[0] = n;
    for (int i = 1; i <= c; i++) {
      cin >> opt[i][0] >> opt[i][1];  
      sum[i] = sum[i - 1] + opt[i][1] - opt[i][0] + 1;
    }
    function<char(int x)> ask = [&] (int pos) {
      if (pos <= sum[0]) return s[pos - 1];
      for (int i = 1; i <= c; i++) {
        if (sum[i] >= pos) {
          pos -= sum[i - 1];
          pos = pos + opt[i][0] - 1;
          return ask(pos);
        }
      }
      assert(0);
    };

    for (int i = 1; i <= q; i++) {
      int x; cin >> x;
      cout << ask(x) << endl;
    }
  }

  return 0;
}

```

# D - Mark and Lightbulbs
一段连续的 $1$ 可以通过题目的操作向左或者向右把一个 $0$ 变成 $1$ (也可以反向操作), 但是没有办法把两个不连续的 $1$ 合并成一段连续的 $1$。所以如果 $s$ 和 $t$ 中 连续的 $1$ 的段数不相同，那么就无解，另一种情况是如果 $s$ 和 $t$ 首尾不相同也无解，因为这两个位置无法被修改。剩下的情况一定有解。最少的操作次数就是 $s$ 和 $t$ 中对应的连续的 $1$ 的一段的左端点之差的绝对值加上右端点之差的绝对值。

```cpp
//
//  d.cpp
//  2022-07-15 22:01
//
//  Created by liznb
//  

#include <bits/stdc++.h>
using namespace std;

#ifdef LOCAL
#include "/Users/liznb/algo/liznb.h"
#endif

#define int long long

signed main() {
  ios::sync_with_stdio(false); 
  cin.tie(0);
   
  int z; cin >> z; 
  while (z--) {
    int n; cin >> n;
    string s, t; cin >> s >> t;
    int flag = true;
    
    if (s[0] != t[0]) {
      flag = false;
    }
    if (s.back() != t.back()) {
      flag = false;
    }

    if (!flag) {
      cout << -1 << endl;
      continue;
    }

    int cnt0 = 1, cnt1 = 1; 
    vector<array<int, 2>> res0, res1; 
    for (int i = 1; i < n; i++) {
      if (s[i] != s[i - 1]) {
        cnt0++;
        if (res0.size() == 0) res0.push_back({0, i - 1});
        else res0.push_back({res0.back()[1] + 1, i - 1});
      }
    }

    if (res0.size() == 0) res0.push_back({0, n - 1});
    else res0.push_back({res0.back()[1] + 1, n - 1});

    for (int i = 1; i < n; i++) {
      if (t[i] != t[i - 1]) {
        cnt1++;
        if (res1.size() == 0) res1.push_back({0, i - 1});
        else res1.push_back({res1.back()[1] + 1, i - 1});
      }
    }

    if (res1.size() == 0) res1.push_back({0, n - 1});
    else res1.push_back({res1.back()[1] + 1, n - 1});

    if (cnt0 != cnt1) flag = false;
    if (!flag) {
      cout << -1 << endl;
      continue;
    }

    int ans0 = 0, ans1 = 0;

    for (int i = 0; i < (int) res0.size(); i += 2) {
      ans0 += abs(res0[i][0] - res1[i][0]) + abs(res0[i][1] - res1[i][1]); 
    }
    for (int i = 1; i < (int) res0.size(); i += 2) {
      ans1 += abs(res0[i][0] - res1[i][0]) + abs(res0[i][1] - res1[i][1]); 
    }
  
    cout << min(ans0, ans1) << endl;
  }

  return 0;
}

```

# E - Mark and Professor Koro

考虑把所有的数分解成 $1$ 后再合成最大的数。

最大的数就是把所有的 $1$ 相加后的数的二进制最高位的位数加 $1$ 。

考虑每一个数对这个二进制数的贡献，显然贡献就是把这个二进制数加上一个二的幂。所以模拟二进制加减法，计算二进制最高位即可。

模拟方法有很多：
1. 加减法：ODT 区间覆盖，查询：从 $2e5 + 20$ 向后跳到最高位 $1$。注意跳完之后，需要把最高位 $+1$ 到 $2e5 + 20$ 覆盖成 $0$。这样才不会超时。实测比较快。
2. 加减法：线段树/树状数组 + 二分，查询：二分最高位。 $O(n\log^{2}n)$ ，线段树优化一下可以卡过，树状数组常数很小，可以直接过。
3. 加减法/查询：线段树内二分定位。 $O(n\log n)$。

```cpp
//
//  eodt.cpp
//  2022-07-16 00:22
//
//  Created by liznb
//  

#include <bits/stdc++.h>
using namespace std;

#ifdef LOCAL
#include "/Users/liznb/algo/liznb.h"
#endif

// 珂朵莉树
// 1. 记住 split 返回包含x的右半区间
// 2. split 的 x 只能在初始化的范围内

struct ODT {
  struct Node {
    int l, r;
    mutable int val;
    Node(const int &a, const int &b, const int &c) : l(a), r(b), val(c) {}
    bool operator < (const Node & oth) const { return l < oth.l; }
  };

  set<Node> odt;

  ODT(int l, int r, int val) {
    odt.insert({l, r, val}); 
  }

  // 把包含 x 的区间 split 成[l, x) [x, r], 返回 [x, r]
  auto split(int x) { 
    auto it = --odt.upper_bound((Node) {x, 0, 0});
    if (it->l == x) return it;
    int l = it->l, r = it -> r, val = it->val;
    odt.erase(it);
    odt.insert((Node) {l, x - 1, val});
    return odt.insert((Node) {x, r, val}).first;
  }

  // split 必须先右后左, 因为 split 右端点可能会让已经 split 的左端点失效
  void assign(int l, int r, int val) {
    auto itr = split(r + 1);  
    auto itl = split(l);
    odt.erase(itl, itr);
    odt.insert((Node) {l, r, val});
  }

  void add(int x) {
    auto it = split(x);    
    while (it->val == 1) it++;
    int l = it->l;
    if (x <= l - 1) assign(x, l - 1, 0); 
    assign(l, l, 1);
  }

  void sub(int x) {
    auto it = split(x);    
    while (it->val == 0) it++;
    int l = it->l;
    if (x <= l - 1) assign(x, l - 1, 1); 
    assign(l, l, 0);
  }

  int query() {
    auto it = odt.end();
    it--;
    int limit = it->r;
    while (it->val == 0) {
      it--;
    }
    assign(it->r + 1, limit, 0);
    return it->r;
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

  ODT T(0, 2e5 + 100, 0);

  for (int i = 1; i <= n; i++) {
    T.add(a[i]);
  }

  for (int i = 1; i <= q; i++) {
    int pos, val; cin >> pos >> val;
    T.sub(a[pos]); 
    a[pos] = val;
    T.add(a[pos]);
    cout << T.query() << endl; 
  } 

  return 0;
}
```


