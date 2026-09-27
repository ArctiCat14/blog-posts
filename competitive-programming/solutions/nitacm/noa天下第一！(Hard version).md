---
tags:
  - dynamic-programming
platform: nitacm
source: https://www.nitacm.com/problem_show.php?pid=608
date: 2026-09-27T15:10:00
---

# 1 Statement

给一棵 $n$ 个节点的树，和一个常数 $k$。  
对于每个节点 $x$，定义受其影响的节点为：与 $x$ 距离不超过 $k$ 的节点。  
一个受影响节点 $y$ 的**拥挤程度**定义为：所有受影响节点到 $x$ 的路径中经过 $y$ 的次数。

对于每个 $x$，输出：
1. 受 $x$ 影响的节点的数量；
2. 所有受 $x$ 影响的节点的拥挤程度的乘积，对 $10^9 + 7$ 取模。


---
# 2 Solution

这是一道换根 DP。
定义 $\mathrm{dp1}_{u\to v,r}$ 为删去 $u\to v$ 后在 $v$ 的连通分量中能到达 $v$ 的距离不大于 $r$ 的点的个数(即 $v$ 的拥挤程度)
有状态转移方程
$$\mathrm{dp1}_{u\to v,r}=1+\sum_{w\in \mathrm{adj}(v)}^{w\neq u}\mathrm{dp1}_{v\to w,r-1}$$
对于每个顶点 $u$，受 $u$ 影响的节点的数量为
$$\mathrm{ans1}[u]=1+\sum_{v\in\mathrm{adj}(u)}\mathrm{dp1}_{u\to v,k-1}$$

定义 $\mathrm{dp2}_{u\to v, r}$ 为删去 $u\to v$ 后在 $v$ 的连通分量中能到达 $v$ 的距离不大于 $r$ 的点的拥挤程度的乘积
有状态转移方程
$$\mathrm{dp2}_{u\to v, r}=\mathrm{dp1}_{u\to v,r}\cdot\prod_{w\in \mathrm{adj}(v)}^{w\neq u}\mathrm{dp2}_{v\to w, r-1}$$
对于每个顶点 $u$，受 $u$ 影响的节点的拥挤程度的乘积为
$$\mathrm{ans2}[u]=\mathrm{ans1}[u]\cdot\prod_{v\in\mathrm{adj}(u)}\mathrm{dp2}_{u\to v,k-1}$$

```cpp
const int MAXN = 100000 + 7;
const ll MOD = 1e9 + 7;

struct edge {
    int v, next;

} edges[MAXN * 2];

int heads[MAXN];
int tot = 0;

void addEdge(int u, int v) {
    edges[tot].v = v;
    edges[tot].next = heads[u];
    heads[u] = tot++;
}

void init() {
    
}

void solve() {
    int n, k;
    cin >> n >> k;
    vector<int> deg(n + 1);
    tot = 0;
    for (int i = 1; i <= n; i++) {
        heads[i] = -1;
    }
    for (int i = 0; i < n - 1; i++) {
        int u, v;
        cin >> u >> v;
        addEdge(u, v);
        addEdge(v, u);
        deg[u]++;
        deg[v]++;
    }
    if (k == 0) {
        for (int i = 1; i <= n; i++) {
            cout << 1 << " \n"[i == n];
        }
        for (int i = 1; i <= n; i++) {
            cout << 1 << " \n"[i == n];
        }
        return;
    }
    stack<int> st;
    st.emplace(1);
    vector<int> pa(n + 1, -1), pe(n + 1, -1);
    vector<int> order;
    order.reserve(n);
    while (st.size()) {
        int u = st.top();
        st.pop();
        order.emplace_back(u);
        for (int e = heads[u]; ~e; e = edges[e].next) {
            int v = edges[e].v;
            if (v == pa[u]) continue;
            pa[v] = u;
            pe[v] = e;
            st.emplace(v);
        }
    }
    auto mul = [&](ll& a, const ll& b) -> void {
        a *= b;
        a %= MOD;
    };
    vector<vector<ll>> dp1(k, vector<ll>(tot, 1LL));
    vector<vector<ll>> dp2(k, vector<ll>(tot, 1LL));
    for (int i = n - 1; i >= 0; i--) {
        int u = order[i];
        if (pa[u] == -1) continue;
        for (int r = 1; r < k; r++) {
            for (int e = heads[u]; ~e; e = edges[e].next) {
                int v = edges[e].v;
                if (v == pa[u]) continue;
                dp1[r][pe[u]] += dp1[r - 1][e];
                mul(dp2[r][pe[u]], dp2[r - 1][e]);
            }
            mul(dp2[r][pe[u]], dp1[r][pe[u]]);
        }
    }

    for (int i = 0; i < n; i++) {
        int u = order[i];
        vector<int> es(deg[u] + 1);
        vector<ll> pre(deg[u] + 2, 1LL), suf(deg[u] + 2, 1LL);
        for (int i = 1, e = heads[u]; i <= deg[u]; i++, e = edges[e].next) {
            es[i] = e;
        }
        for (int r = 1; r < k; r++) {
            ll full_sum = 0;

            for (int i = 1; i <= deg[u]; i++) {
                full_sum += dp1[r - 1][es[i]];
                pre[i] = pre[i - 1] * dp2[r - 1][es[i]] % MOD;
                int j = deg[u] + 1 - i;
                suf[j] = suf[j + 1] * dp2[r - 1][es[j]] % MOD;
            }

            for (int i = 1; i <= deg[u]; i++) {
                int e = es[i];
                int v = edges[e].v;
                if (v == pa[u]) continue;
                dp1[r][e ^ 1] += full_sum - dp1[r - 1][e];
                mul(dp2[r][e ^ 1], pre[i - 1] * suf[i + 1] % MOD);
                mul(dp2[r][e ^ 1], dp1[r][e ^ 1]);
            }
        }
    }

    vector<ll> ans2(n + 1, 1LL);
    for (int u = 1; u <= n; u++) {
        ll ans1 = 1;
        for (int e = heads[u]; ~e; e = edges[e].next) {
            ans1 += dp1[k - 1][e];
            mul(ans2[u], dp2[k - 1][e]);
        }
        mul(ans2[u], ans1);
        cout << ans1 << " \n"[u == n];
    }
    for (int i = 1; i <= n; i++) {
        cout << ans2[i] << " \n"[i == n];
    }
}
```
