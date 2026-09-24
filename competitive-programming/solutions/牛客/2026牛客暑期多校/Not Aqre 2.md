---
tags:
  - number-theory
  - nowcoder
date: 2026-07-25T18:55:00
---

# 1 Statement

- $n\times m$ 网格，每格填 0、1、2，相邻格子数字不同，
- 求合法填法数，对 $998244353$ 取模
- $1\leq n < 10, 1\leq m < 998244353$

---
# 2 Solution

- 首先，定义状态：对于一列 $a_1,a_2,\dots, a_n\ (a_i \neq a_{i + 1})$，当 $i\geq 3$ 时 $a_i$ 有两种状态
1. $a_i = a_{i-1}$
2. $a_i$ 为第三种数字
- 那么总的状态数量就为 $2^{n-2}$，可以使用二进制标记每种状态。
- 接下来，我们要构造转移矩阵，通过 dfs 生成所有可能的列序列，将两列的合法关系记录下来。
- dfs 的时候要固定前两个数字为 0 和 1，这样可以做到所有状态不重不漏。
- 第一列的前两个数字任选，总选法为 $3\times 2 = 6$。因此初始向量每项为 $6$。
- 答案为 $6\cdot \sum_{i,j}(M^{m-1})$。

```cpp
ll n, m;

vector<vector<int>> cols;
vector<int> col;

void dfs(ll dep) {
    if (dep == n) {
        cols.emplace_back(col);
        return;
    }
    for (int i = 0; i < 3; i++) {
        if (dep >= 1 && col[dep - 1] == i) {
            continue;
        }
        col.emplace_back(i);
        dfs(dep + 1);
        col.pop_back();
    }
}

ll state(const vector<int> &a) {
    ll state = 0;
    for (int i = 2; i < n; i++) {
        state <<= 1;
        state |= a[i] == a[i - 2];
    }
    return state;
}

bool check(const vector<int> &a, const vector<int> &b) {
    for (int i = 0; i < n; i++) {
        if (a[i] == b[i]) return false;
    }
    return true;
}

void solve() {
    cin >> n >> m;
    if (n == 1) {
        cout << 3 * qpow(2, m - 1) % MOD << '\n';
        return;
    }
    dfs(0);
    ll szM = 1 << (n - 2);
    matrix M(szM);
    for (int i = 0; i < sz(cols); i++) {
        if (cols[i][0] != 0 || cols[i][1] != 1) continue;
        for (int j = 0; j < sz(cols); j++) {
            if (check(cols[i], cols[j])) {
                M.data[state(cols[i])][state(cols[j])]++;
            }
        }
    }
    M = matrix::qpow(M, m - 1);
    ll ans = 0;
    for (int i = 0; i < szM; i++) {
        for (int j = 0; j < szM; j++) {
            ans = (ans + M.data[i][j]) % MOD;
        }
    }
    ans = ans * 6 % MOD;
    cout << ans << '\n';
}
```


