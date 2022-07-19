---
title: "Codeforces Round #809 (Div. 2)"
date: 2022-07-20T01:33:00+08:00
categories:
  - Codeforces
tags:
  - 整除分块
  - 二分
  - 整函数
  - 贪心
  - 最小瓶颈路
  - LCA
---

# A - Another String Minimization Problem

模拟放置，先放靠前的位置，如果不能放再放靠后的位置。

```cpp
//
//  a.cpp
//  2022-07-18 22:38
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
    int n, m; cin >> n >> m;
    vector<int> a(n + 1);
    string s;
    for (int i = 1; i <= m; i++) s += 'B';
    for (int i = 1; i <= n;i ++) {
      cin >> a[i];
      int p1 =a[i] - 1; 
      int p2 = m + 1 - a[i] - 1; 
      if (p1 > p2) swap(p1, p2);

      if (s[p1] == 'B') s[p1] = 'A';
      else {
        if (s[p2] == 'B') s[p2] = 'A';
      }
    }
    cout << s << endl;
  }

  return 0;
}
```

# B - Making Towers

如果一个数字和它上一次出现的时候相差偶数个其他数字。那么就可以把塔叠高一层，如果相差奇数个数字，那么就把本次出现的数字当作其他数字，继续往后面找。

```cpp
//
//  b.cpp
//  2022-07-18 22:49
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
    vector<int> last(n + 1, 0);
    vector<int> ans(n + 1, 0), res(n + 1, 0);
    for (int i = 1; i <= n; i++) {
      cin >> a[i];
      if (last[a[i]] == 0) {
        res[a[i]] = 1;
        ans[a[i]] = max(res[a[i]], ans[a[i]]);
        last[a[i]] = i;
      } else if (abs(last[a[i]] - i) % 2) {
        res[a[i]] += 1;
        ans[a[i]] = max(res[a[i]], ans[a[i]]);
        last[a[i]] = i;
      } 
    }
    for (int i = 1; i <= n; i++) {
      cout << ans[i] << " ";
    }
    cout << endl;
  }

  return 0;
}
```

# C - Qpwoeirut And The City

对于 $n$ 为奇数显然答案是固定的，即上，下，上，下...。对于 $n$ 是偶数，必定有且只有一个位置会停一手，即...上，下，下，上,...。枚举这个位置即可。

```cpp
//
//  c.cpp
//  2022-07-18 23:02
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
    vector<int> add(n + 1, 0);
    for (int i = 1; i <= n; i++) {
      cin >> a[i];
    }
    for (int i = 2; i < n; i++) {
      if (a[i] > max(a[i - 1], a[i + 1])) add[i] = 0;
      else {
        add[i] = max(a[i - 1], a[i + 1]) - a[i] + 1;
      }
    }
    if (n % 2) {
      int ans = 0;
      for (int i = 2; i < n; i++) {
        if (i % 2 == 0) ans += add[i]; 
      }
      cout << ans << endl;
    } else {
      int ans = 0x3f3f3f3f3f3f3f3f;
      vector<int> odd(n + 2, 0), even(n + 2, 0);
      for (int i = 1; i < n; i++) {
        even[i] = even[i - 1];
        if (i % 2 == 0) even[i] += add[i];
      }
      for (int i = n; i > 1; i--) {
        odd[i] = odd[i + 1];
        if (i % 2) odd[i] += add[i];
      }
      for (int i = 1; i < n; i++) {
        if (i % 2 == 0) continue;
        int res = even[i] + odd[i + 1]; 
        ans = min(ans, res);
      }
      cout << ans << endl;
    }
  }

  return 0;
}
```

# D1 - Chopping Carrots (Easy Version)

枚举上界，二分下界，检查是否可行。

值得一提的是注意整函数在不等式中的运用。（似乎比 D2 的做法还难想）

```cpp
//
//  d1.cpp
//  2022-07-18 23:23
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
    vector<int> a(n + 1);
    int maxn = 0;
    for (int i = 1; i <= n; i++) {
      cin >> a[i];
      maxn = max(maxn, a[i]);
    }
    int ans = 0x3f3f3f3f;

    auto solve1 = [&](int a, int b) {
      int l = 1, r = a;
      while (l <= r) {
        int mid = (l + r) / 2;
        if (a / mid <= b) r = mid - 1;
        else l = mid + 1;
      }
      return l;
    };

    auto solve2 = [&](int a, int b) {
      assert(a >= b);
      int l = 1, r = a + 1;
      while (l <= r) {
        int mid = (l + r) >> 1;  
        if (a / mid < b) r = mid - 1; 
        else l = mid + 1;
      }
      return r;
    };

    for (int i = maxn / k; i <= maxn; i++) {
      int l = 0, r = i;  
      while (l <= r) {
        int mid = (l + r) >> 1;  
        int flag = true;
        for (int j = 1; j <= n; j++) {
          if (a[j] < mid) flag = false;
          if (a[j] > i) {
            int least;
            if (i == 0) least = 0x3f3f3f3f;
            else {
              least = a[j] / (i + 1) + 1;
            }

            int high;
            if (mid == 0) high = 0x3f3f3f3f;
            else high = a[j] / mid;

            if (least > high) flag = false;
            if (least > k) flag = false;  
            if (high == 0) flag = false;
          }
        }
        if (flag) l = mid + 1;
        else r = mid - 1;
      }
      ans = min(ans, i - r);
    }
    cout << ans << endl;
  }


  return 0;
}
```

# D2 - Chopping Carrots (Hard Version)

对每个数能整除到的数枚举一遍，记录下界取到的每一个位置至少需要的上界是多少。

```cpp
//
//  d2.cpp
//  2022-07-18 23:23
//
//  Created by liznb
//  

#include <bits/stdc++.h>
using namespace std;

#ifdef LOCAL
#include "/Users/liznb/algo/liznb.h"
#endif


signed main() {
  ios::sync_with_stdio(false); 
  cin.tie(0);
   
  int z; cin >> z;
  while (z--) {
    int n, k; cin >> n >> k;
    vector<int> a(n + 1);
    int maxn = 0; 
    for (int i = 1; i <= n; i++) {
      cin >> a[i];
      maxn = max(maxn, a[i]); 
    }
    vector<int> b(maxn + 2, 0);
    for (int i = 1; i <= n; i++) {
      int last = 0x3f3f3f3f; 
      for (int j = 1, nxt; j <= min(k, a[i]); j = nxt) {
        nxt = a[i] / (a[i] / j) + 1;
        int val = a[i] / j; 
        b[val + 1] = max(last, b[val + 1]); 
        last = val;
      }
      b[0] = max(b[0], last);
    }
    int ans = 0x3f3f3f3f;
    for (int i = 0; i <= maxn; i++) {
      if (i != 0)
        b[i] = max(b[i - 1], b[i]);      
      ans = min(b[i] - i, ans);
    }
    cout << ans << endl;
  }


  return 0;
}
```

# E - Qpwoeirut and Vertices

把边权当作边的编号。两点之间需要的最小 $k$ 就是最小瓶颈路上的最大边权。对于多点求最小瓶颈路，用线段树维护区间最值即可。（很好的最小瓶颈路板题）

```cpp
//
//  e.cpp
//  2022-07-19 01:32
//
//  Created by liznb
//  

#include <bits/stdc++.h>
using namespace std;

#ifdef LOCAL
#include "/Users/liznb/algo/liznb.h"
#endif


// 快速读入输出
// 1. From tourist 
// 2. 唯一的修改, 增加 #define endl '\n'
// 3. 支持 __int128
// 4. 其他用法 cin cout 一样

#define endl '\n'
static struct FastInput {
  static constexpr int BUF_SIZE = 1 << 20;
  char buf[BUF_SIZE];
  size_t chars_read = 0;
  size_t buf_pos = 0;
  FILE *in = stdin;
  char cur = 0;

  inline char get_char() {
    if (buf_pos >= chars_read) {
      chars_read = fread(buf, 1, BUF_SIZE, in);
      buf_pos = 0;
      buf[0] = (chars_read == 0 ? -1 : buf[0]);
    }
    return cur = buf[buf_pos++];
  }
 
  inline void tie(int) {}

  inline explicit operator bool() {
    return cur != -1;
  }

  inline static bool is_blank(char c) {
    return c <= ' ';
  }

  inline bool skip_blanks() {
    while (is_blank(cur) && cur != -1) {
      get_char();
    }
    return cur != -1;
  }
 
  inline FastInput& operator>>(char& c) {
    skip_blanks();
    c = cur;
    return *this;
  }
  
  inline FastInput& operator>>(string& s) {
    if (skip_blanks()) {
      s.clear();
      do {
        s += cur;
      } while (!is_blank(get_char()));
    }
    return *this;
  }
 
  template <typename T>
  inline FastInput& read_integer(T& n) {
    // unsafe, doesn't check that characters are actually digits
    n = 0;
    if (skip_blanks()) {
      int sign = +1;
      if (cur == '-') {
        sign = -1;
        get_char();
      }
      do {
        n += n + (n << 3) + cur - '0';
      } while (!is_blank(get_char()));
      n *= sign;
    }
    return *this;
  }

  template <typename T>
  inline typename enable_if<is_integral<T>::value, FastInput&>::type operator>>(T& n) {
    return read_integer(n);
  }
  
  #if !defined(_WIN32) || defined(_WIN64)
  inline FastInput& operator>>(__int128& n) {
    return read_integer(n);
  }
  #endif

  template <typename T>
  inline typename enable_if<is_floating_point<T>::value, FastInput&>::type operator>>(T& n) {
    // not sure if really fast, for compatibility only
    n = 0;
    if (skip_blanks()) {
      string s;
      (*this) >> s;
      sscanf(s.c_str(), "%lf", &n);
    }
    return *this;
  }
} fast_input;

#define cin fast_input

static struct FastOutput {
  static constexpr int BUF_SIZE = 1 << 20;
  char buf[BUF_SIZE];
  size_t buf_pos = 0;
  static constexpr int TMP_SIZE = 1 << 20;
  char tmp[TMP_SIZE];
  FILE *out = stdout;

  inline void put_char(char c) {
    buf[buf_pos++] = c;
    if (buf_pos == BUF_SIZE) {
      fwrite(buf, 1, buf_pos, out);
      buf_pos = 0;
    }
  }

  ~FastOutput() {
    fwrite(buf, 1, buf_pos, out);
  }

  inline FastOutput& operator<<(char c) {
    put_char(c);
    return *this;
  }

  inline FastOutput& operator<<(const char* s) {
    while (*s) {
      put_char(*s++);
    }
    return *this;
  }
 
  inline FastOutput& operator<<(const string& s) {
    for (int i = 0; i < (int) s.size(); i++) {
      put_char(s[i]);
    }
    return *this;
  }
 
  template <typename T>
  inline char* integer_to_string(T n) {
    // beware of TMP_SIZE
    char* p = tmp + TMP_SIZE - 1;
    if (n == 0) {
      *--p = '0';
    } else {
      bool is_negative = false;
      if (n < 0) {
        is_negative = true;
        n = -n;
      }
      while (n > 0) {
        *--p = (char) ('0' + n % 10);
        n /= 10;
      }
      if (is_negative) {
        *--p = '-';
      }
    }
    return p;
  }

  template <typename T>
  inline typename enable_if<is_integral<T>::value, char*>::type stringify(T n) {
    return integer_to_string(n);
  }

  #if !defined(_WIN32) || defined(_WIN64)
  inline char* stringify(__int128 n) {
    return integer_to_string(n);
  }
  #endif

  template <typename T>
  inline typename enable_if<is_floating_point<T>::value, char*>::type stringify(T n) {
    sprintf(tmp, "%.17f", n);
    return tmp;
  }

  template <typename T>
  inline FastOutput& operator<<(const T& n) {
    auto p = stringify(n);
    for (; *p != 0; p++) {
      put_char(*p);
    }
    return *this;
  }
} fast_output;

#define cout fast_output
#define int long long

// 最小瓶颈路
// 1. 定义: 两点之间所有路径中最大边权最小的路径。
//    定理: 最小生成树上两点之间在树上的路径是一个最小瓶颈路。
//
// 2. 一般只能处理正边权，但是可以修改后处理负边权,
//    更改 max_edge 初始化最小值( 默认是0 )
//
// 3. 使用方法：
//    a. 初始化为点数 n
//    b. add 加边 u, v, w, 自动无向
//    c. build 建树, 不需要传参数
//    d. query(a, b) 查询 a <-> b 最小瓶颈路上的最大边权。
struct Bottleneck {
  
  struct DSU {
    vector<int> f, dep, size;   
    DSU(int n) : f(n + 1), dep(n + 1, 0), size(n + 1, 1) { iota(f.begin(), f.end(), 0); }
  
    inline int find(int x) {
      while (x != f[x]) x = f[x] = f[f[x]];
      return x;
    }
  
    inline bool merge(int x, int y) {
      x = find(x), y = find(y); 
  
      if (x == y) return false;
      if (dep[x] > dep[y]) swap(x, y);
      if (dep[x] == dep[y]) dep[y]++;
  
      f[x] = y;    
      size[y] += size[x];
      return true;
    }
  };

  // LCA·改
  // 求出 lca 的同时求出两点路径上的边权最大值。
  // p[i][j][0] 是边权(最大值)
  // G[i][j][0] 是边权
  struct LCA {
    vector<vector<array<int, 2>>> G, p;
    vector<int> d;
    int n, s; 
  
    LCA(int _n, int _s) : G(_n + 1), d(_n + 1, 1), n(_n), s(_s) {
      p.resize(_n + 1);
      int level = 0;
      while ((1 << level) <= _n) level++;
      for (int i = 0; i <= _n; i++) {
        p[i].resize(level + 1);
      }
    }
  
    int flag = false;
  
    void add(int u, int v, int w) {
      G[u].push_back({w, v});
      G[v].push_back({w, u});
    }
    
    void dfs(int pos, int fa) {
      p[pos][0][1] = fa;
      for (auto &[w, v] : G[pos]) {
        if (v == fa) continue;
        d[v] = d[pos] + 1;
        p[v][0][0] = w;
        dfs(v, pos);
      }
    }
  
    void init() {
      flag = true; 
      dfs(s, s); 
      for (int level = 1; (1 << level) <= n; level++) {
        for (int i = 1; i <= n; i++) {
          p[i][level][1] = p[p[i][level - 1][1]][level - 1][1];
          p[i][level][0] = max(p[i][level - 1][0], p[p[i][level - 1][1]][level - 1][0]);
        }
      }
    }
    // 返回 [0] 最大边权 [1] lca ，如果最大边权不存在，返回0
    array<int, 2> lca(int a, int b) {
      assert(flag);
      if (d[a] < d[b]) swap(a, b); 
      int i, j;
      for (i = 0; (1 << i) <= d[a]; i++);  
      i--; 
      int max_edge = 0;
      
      for (j = i; j >= 0; j--) {
        if (d[a] - (1 << j) >= d[b]) {
          max_edge = max(max_edge, p[a][j][0]);
          a = p[a][j][1];
        }
      }
      if (a == b) return {max_edge, b}; 
      for (j = i; j >= 0; j--) {
        if (p[a][j][1] != p[b][j][1]) {
          max_edge = max(max_edge, p[a][j][0]);
          max_edge = max(max_edge, p[b][j][0]);
          a = p[a][j][1];
          b = p[b][j][1];
        }
      }
      max_edge = max(max_edge, p[a][0][0]);
      max_edge = max(max_edge, p[b][0][0]);
      return {max_edge, p[a][0][1]};
    }
  };

  vector<array<int, 3>> edges;
  DSU T1;
  LCA T2; 

  Bottleneck(int n): T1(n), T2(n, 1) {}
  
  void add(int u, int v, int w) {
    edges.push_back({w, u, v});
  }

  void build() {
    sort(edges.begin(), edges.end());
    for(auto &[w, u, v] : edges) {
      if (T1.merge(u, v)) {
        T2.add(u, v, w);
      }
    }
    T2.init(); 
  }

  int query(int a, int b) {
    if (T1.find(a) != T1.find(b)) return 0;
    return T2.lca(a, b)[0]; 
  }
};

struct Segment_Tree {
  
  #define tl tree[pos].l 
  #define tr tree[pos].r 
  #define tv tree[pos].val
  struct Info {
    long long sum, maxi, mini; 
  };

  struct Tree {
    int l, r; 
    Info val;
  };  
  vector<Tree> tree;
  vector<long long> lazy;

  Segment_Tree(int n) {
    tree.resize(n * 4 + 10);  
    lazy.resize(n * 4 + 10);
  }

  // 合并两个儿子
  Info merge(Info a, Info b) {
    Info res;
    res.sum = a.sum + b.sum;
    res.maxi = max(a.maxi, b.maxi);
    res.mini = min(a.mini, b.mini);
    return res;
  }

  void build(int pos, int l, int r, vector<int> &arr) {
    tl = l; tr = r;    
    lazy[pos] = 0ll;

    int m = l + (r - l) / 2;
    if (l == r) {
      tv.sum = arr[l]; 
      tv.maxi = arr[l];
      tv.mini = arr[l];
      return;
    } 
    build(pos << 1, l, m, arr);
    build(pos << 1 | 1, m + 1, r, arr);
    tv = merge(tree[pos << 1].val, tree[pos << 1 | 1].val);
  }

  // 对树上一个位置进行修改
  void add(int pos, long long val) {
    lazy[pos] += val;
    tv.sum += (tr - tl + 1) * val;
    tv.maxi += val;
    tv.mini += val;
  }

  // 传递懒人标记
  void push_down(int pos) {
    if (lazy[pos] == 0) return; 
    add(pos << 1, lazy[pos]); 
    add(pos << 1 | 1, lazy[pos]);
    lazy[pos] = 0;
  } 

  void modify(int pos, int l, int r, long long val) {
    if (tl >= l && tr <= r) {
      add(pos, val);
      return;
    }  
    push_down(pos);
    int m = tl + (tr - tl) / 2;
    if (l <= m) modify(pos << 1, l, r, val);
    if (r > m) modify(pos << 1 | 1, l, r, val);
    tv = merge(tree[pos << 1].val, tree[pos << 1 | 1].val);
  } 

  Info query(int pos, int l, int r) {
    if (tl >= l && tr <= r) {
      return tv;
    }   
    push_down(pos);
    int m = tl + (tr - tl) / 2;
    Info res;

    if (l <= m && r > m) {
      res = merge(query(pos << 1, l, r), query(pos << 1 | 1, l , r));
    } else if (l > m) {
      res = query(pos << 1 | 1, l, r);
    } else if (r <= m) {
      res = query(pos << 1, l, r);
    }

    tv = merge(tree[pos << 1].val, tree[pos << 1 | 1].val);
    return res;
  }
  #undef tl
  #undef tr
  #undef tv
};

signed main() {
  ios::sync_with_stdio(false); 
  cin.tie(0);
   
  int z; cin >> z;  
  
  while (z--) {
    int n, m, q; cin >> n >> m >> q;
    Bottleneck T(n);    
    for (int i = 1; i <= m; i++) {
      int u, v; cin >> u >> v;    
      T.add(u, v, i);
    }
    T.build();
    Segment_Tree T1(n);
    vector<int> arr(n + 1, 0);

    for (int i = 1; i < n; i++) {
      arr[i] = T.query(i, i + 1);
    }
    cout << endl;

    T1.build(1, 1, n - 1, arr);

    for (int i = 1; i <= q; i++) {
      int l, r; cin >> l >> r;
      if (l <= r - 1)
        cout << T1.query(1, l, r - 1).maxi << " ";
      else 
        cout << 0 << " ";
    }
    cout << endl;
  }

  return 0;
}
```