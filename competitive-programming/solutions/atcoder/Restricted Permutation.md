---
tags:
  - solution
  - combinatorics
  - atcoder
source: https://atcoder.jp/contests/abc468/tasks/abc468_g
---

---
# 1 Statement

- 给一个整数 $N$ 和由 `o` 和 `x` 组成的长度为 $N$ 的字符串 $S$。
- 找到满足以下条件的由 $(1, 2, \dots, N)$ 构成的排列 $P$ 的数量 $\pmod{998244353}$。
- 对于 $k = 1, 2, \dots, N$，以下两条件等价：
1. $S_k =$`o`。
2. $P$ 包含 $(1, 2, \dots, k)$ 的一个排列作为**连续**子序列。

---
# 2 Solution

- 排列 $P$ 总是包含 $(1)$ 和 $(1,2,\dots,N)$ 作为连续子序列，所以对于 $S_1=$`x` 和 $S_N=$`x` 答案为 $0$
- 设长度为 $n$ 的字符串 $S$ 形如 `oxx...xo` 的答案为 $d_n$。
- 设 $S_i=$`o` 时的 $i$ 的全集为 $1=A_1<A_2<\cdots<A_M=N$。
- 考虑到对于相邻的每个 `oxx...xo` 串，它们的答案是相互独立的。
>[!proof]
>设 $S$ 为形如 `oxx...xo` 的字符串，符合它的排列为 $P$。
>对于一个 $S_i$，以它结尾时的字符串总长度就为 $A_i$。
>符合它的排列 $P_i$ 一定包含 $(P_{i-1})$ 和 $(P_{i-1}, A_{i-1}+1, \dots, A_i)$ 作为连续子序列。
>因此，对于 $S_i$，它的答案为 $d_{A_i-A_{i-1} + 1}$。
- 整体的答案就为 $\prod_{i=2}^{M}d_{A_{i}-A_{i-1}+1}$。
- $(1,2,\dots,N)$ 的排列 $P$ 共有 $N!$ 个，我们可以根据包含 $(1, 2, \dots, k)$ 作为连续子序列的最小值 $k\ (k\geq 2)$ 对 $P$ 进行分类。
- 对于满足该最小值为 $k$ 的序列 $P$，其个数为 $d_k(N - k + 1)!$。
- 因此 $N!=\sum_{k=2}^{N}d_k(N-k+1)!$ 成立。根据该等式可以求出所有的 $d_2,d_3,\dots,d_N$。

```cpp
void solve() {
    int n;
    cin >> n;
    string s;
    cin >> s;
    if (s[0] == 'x' || s[n - 1] == 'x') {
        cout << "0\n";
        return;
    }
    vector<ll> d(n + 1);
    d[1] = 1;
    for (int i = 2; i <= n; i++) {
        d[i] = fact[i];
        for (int j = 2; j < i; j++) {
            d[i] = ((d[i] - d[j] * fact[i - j + 1] % MOD) % MOD + MOD) % MOD;
        }
    }
    ll ans = 1;
    int prev = 0;
    for (int i = 1; i < n; i++) {
        if (s[i] == 'o') {
            ans = ans * d[i - prev + 1] % MOD;
            prev = i;
        }
    }
    cout << ans << '\n';
}
```

---
