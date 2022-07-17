---
title: "Codeforces Round #808 (Div. 2)"
date: 2022-07-17T20:27:31+08:00
categories:
  - Codeforces
tags:
  - 贪心
  - 二分查找
  - 暴力

---

# A - Difference Operations

如果 $a_{2}$ 到 $a_{n}$ 都是 $a_{1}$ 的倍数，那么答案是 `YES`, 否则是 `NO`。

```cpp
//
//  a.cpp
//  2022-07-17 16:24
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
    for (int i = 1; i <= n; i++) {
      cin >> a[i];
    }
    int flag = true;
    for (int i = 2; i <= n; i++) {
      if (a[i] % a[1]) flag = false;
    }
    if (flag) cout << "YES" << endl;
    else cout << "NO" << endl;
  }


  return 0;
}
```

# B - Difference of GCDs

显然第 $i$ 位的 $\gcd$ 只能是 $i$。对于第 $i$ 位，看看 $i$ 的倍数在不在 $l$ 到 $r$ 的范围即可。

```cpp
//
//  b.cpp
//  2022-07-17 16:28
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
    int n, l, r; cin >> n >> l >> r;
    vector<int> a(n + 1);
    int flag = true;
    for (int i = 1; i <= n; i++) {
      a[i] = l / i * i;
      if (a[i] < l) a[i] += i;
      if (a[i] > r) flag = false;
    }
    if (!flag) {
      cout << "NO" << endl;
    } else {
      cout << "YES" << endl;
      for (int i = 1; i <= n; i++) {
        cout << a[i] << " ";
      }
      cout << endl;
    }
  }

  return 0;
}
```

# C - Doremy's IQ

可以反向考虑，当所有考试结束的时候，IQ 被用光是最好的。而我们又希望尽可能晚的第一次使用 IQ。所以最优方案是尽可能晚的使用 IQ，当使用了第一次IQ后，就一直使用IQ(除非不需要)，使得考完最后一个考试后 IQ 为 $0$ (不能提前为 $0$)。所以反向枚举考试，初始 IQ 是 $0$ 能用IQ就用（因为是反向的，所以 IQ $+ 1$），不能用就不管，直到 IQ 涨满。

```cpp
//
//  c.cpp
//  2022-07-17 16:31
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
    int n, limit; cin >> n >> limit;
    vector<int> a(n + 1);
    for (int i = 1; i <= n; i++) {
      cin >> a[i];
    }
    string s;
    int q = 0;
    for (int i = n; i >= 1; i--) {
      if (a[i] <= q) s += '1';
      else {
        if (q != limit) {
          q += 1;
          s += '1';
        } else s += '0';
      }
    }

    reverse(s.begin(), s.end());
    cout << s << endl;
  }

  return 0;
}
```

# D - Difference Array

模拟一下可以发现，相邻两个数的差会很快归零，归零后就不能复原。所以可以暴力模拟整个过程，但是不对等于 $0$ 的两个数做差，只是记录一下个数。

```cpp
//
//  d.cpp
//  2022-07-17 18:37
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
    for (int i = 0; i < n; i++) {
      cin >> a[i];
    }
    int st = 0;
    for (int i = 0; i < n - 1; i++) {
      vector<int> temp;  
      for (int j = st; j < n; j++) {
        if (j == i) continue;
        temp.push_back(a[j] - a[j - 1]);
      }
      sort(temp.begin(), temp.end());
      int p = 0;
      for (int j = st; j < n; j++) {
        if (j == i) {
          a[j] = 0;
          continue;
        }
        a[j] = temp[p++];
      }
      while (a[st] == 0) st++;
    }
    cout << a[n - 1] << endl;
  }

  return 0;
}
```
