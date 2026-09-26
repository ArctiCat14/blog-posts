---
tags:
  - greedy
  - dynamic-programming
date: 2026-09-09T22:19:00
---

# 1 最长上升子序列

给定长度为 $n$ 的序列 $a$,最长上升子序列(longest increasing subsequence, LIS)指从中选出一个下标严格递增的子序列,且元素值严格递增,求它的最大长度。

## 1.1 dp 解法

设 $dp[i]$ 表示**以 $a[i]$ 结尾**的最长上升子序列长度。

转移:枚举前一个元素的位置 $j$,
$$dp[i] = \max\left(1,\ \max_{j<i,\ a[j]<a[i]}(dp[j]+1)\right)$$

答案为 $\max_{1\le i\le n}dp[i]$。

```cpp
int LIS(vector<int>& a) {
    int n = a.size();
    vector<int> dp(n, 1);
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < i; j++) {
            if (a[j] < a[i]) {
                dp[i] = max(dp[i], dp[j] + 1);
            }
        }
    }
    return *max_element(dp.begin(), dp.end());
}
```

时间复杂度 $O(n^2)$。

## 1.2 贪心解法

$O(n^2)$ 的 dp 在 $n$ 较大时不可接受。注意到 dp 转移时只关心"末尾元素",可以贪心地维护:

设 $d[i]$ 表示长度为 $i$ 的上升子序列的最小末尾元素。$d$ 单调递增(长度越长,末尾必须越大)。

从左到右扫描 $a$,对每个 $a[i]$,在 $d$ 中找到第一个 $\ge a[i]$ 的位置 $k$ 并把它替换成 $a[i]$;若 $d$ 中所有元素都 $<a[i]$,则把 $a[i]$ 追加到 $d$ 末尾。

正确性(交换论证):
- 末尾元素越小,后面越容易接上新元素,所以把某个长度的最小末尾替换成更小的 $a[i]$ 不会使答案变差;
- 当 $a[i]$ 比所有长度的最小末尾都大时,答案长度 $+1$。

因为 $d$ 单调递增,查找位置用二分,总复杂度 $O(n\log n)$。C++中直接用 `lower_bound`:

```cpp
int LIS(vector<int>& a) {
    vector<int> d;
    for (int x : a) {
        auto it = lower_bound(d.begin(), d.end(), x);
        if (it == d.end()) {
            d.push_back(x);
        } else {
            *it = x;
        }
    }
    return d.size();
}
```

注意:
- 这里求的是**严格上升**。求**不降**子序列时,把 `lower_bound` 换成 `upper_bound` 即可;
- $d$ 数组本身并不是最终的 LIS,要还原 LIS 需要额外记录前驱。

[[Dilworth定理]](最小链覆盖等于最长反链)的应用:把序列划分成最少的不升子序列,所需的条数等于最长上升子序列的长度,如导弹拦截的第二问。

---
# 2 LCS 转 LIS

当其中一个序列的元素**互不相同**时,最长公共子序列(LCS)可以转化为最长上升子序列(LIS)求解。

设 $a$ 中元素互不相同,$b$ 任意。把 $a$ 的每个元素映射为它的下标 $1,2,\dots,n$,再把 $b$ 中每个元素替换成它在 $a$ 中的下标(不在 $a$ 中的元素直接丢弃),得到序列 $c$。

则 $\operatorname{LCS}(a,b)=\operatorname{LIS}(c)$:
- 公共子序列必须按 $a$ 中的下标顺序出现;
- $a$ 中元素互不相同,所以 $b$ 的每个元素至多对应一个下标;
- 于是 $b$ 的一个子序列是公共子序列,当且仅当它对应的下标序列严格递增。

```cpp
int LCS(vector<int>& a, vector<int>& b) {
    int n = a.size();
    vector<int> pos(n + 1); // 假设值域为[1,n],0表示未出现
    for (int i = 0; i < n; i++) pos[a[i]] = i + 1;
    vector<int> c;
    for (int x : b) {
        if (pos[x]) c.push_back(pos[x]);
    }
    return LIS(c); // 贪心版,O(n log n)
}
```

复杂度从 $O(nm)$ 降到 $O((n+m)\log n)$。

若 $a$ 存在重复元素,可以把 $b$ 的每个元素 $x$ 替换成 $x$ 在 $a$ 中的所有下标并按**降序**排列,再求 LIS。同一组下标降序排列,保证一个严格上升的子序列不会重复选取同一个位置,此时仍有 $\mathrm{LCS}(a,b)=\mathrm{LIS}(c)$。

---
# 3 例题

模板：[B3637 最长上升子序列 - 洛谷](https://www.luogu.com.cn/problem/B3637)
[P1020 [NOIP 1999 提高组] 导弹拦截 - 洛谷](https://www.luogu.com.cn/problem/P1020)
LCS 转 LIS 模板：[P1439 两个排列的最长公共子序列 - 洛谷](https://www.luogu.com.cn/problem/P1439)

