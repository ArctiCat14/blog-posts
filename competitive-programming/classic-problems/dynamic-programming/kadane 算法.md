---
tags:
  - dynamic-programming
source: https://www.nitacm.com/problem_show.php?pid=1004
date: 2026-02-02T15:34:00
platform: nitacm
---

# 1 kadane 算法

kadane 算法是解决最大子数组和问题的算法。

```cpp
int n;
cin >> n;
vector<int> a(n);
for (int i = 0; i < n; i++) cin >> a[i];
int l, r, t, ans, cur;
l = r = t = cur = 0;
ans = a[0];
for (int i = 0; i < n; i++) {
    cur += a[i];
    if (ans < cur) {
        ans = cur;
        l = t;
        r = i;
    }
    if (cur < 0) {
        cur = 0;
        t = i + 1;
    }
}
cout << ans << ' ' << l << ' ' << r;
```

---
# 2 DP 写法

定义 $\mathrm{dp}[i]$ 为以 $i$ 为结尾的连续子数组的最大和，有状态转移方程
$$\mathrm{dp}[i]=\max\left( \mathrm{dp}[i - 1] + \mathrm{a}[i], \mathrm{a}[i] \right)$$

```cpp
int n;
cin >> n;
vector<int> a(n);
for (int i = 0; i < n; i++) cin >> a[i];
vector<int> dp(n);
dp[0] = a[0];
for (int i = 1; i < n; i++) {
    dp[i] = max(dp[i - 1] + a[i], a[i]);
}
cout << *max_element(dp.begin(), dp.end());
```

