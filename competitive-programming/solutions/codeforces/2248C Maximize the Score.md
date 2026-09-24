---
tags:
  - dynamic-programming
platform: codeforces
source: https://codeforces.com/problemset/problem/2248/C
date: 2026-08-02T12:43:00
---

# 1 Statement

给你一个长度为 $2n$ 的数组 $a$。从 $1$ 到 $n$ 的每个整数在 $a$ 中正好出现两次。
最初，您的得分是 $0$。
当 $a$ 为非空时，您可以重复执行以下操作：
- 选择一个在 $a$ 中存在的整数 $x$。
- 假设 $l$ 和 $r$ 分别是当前数组中 $x$ 最左边和最右边的索引。如果 $x$ 只出现一次，则为 $l=r$。
- 将 $(r−l+1)^2$ 加入您的分数。
- 删除 $a$ 中的 $a_l,a_{l+1},\dots,a_r$ 元素。其余元素在不改变顺序的情况下进行连接，并从 $1$ 开始重新索引。
将数组清空后，求最大可能得分。

---
# 2 Solution

考虑两个长度分别为 $A,B\ (A> B)$ 的区间 $I_1,I_2$，$I_1$ 完全包含 $I_2$。
考虑先删除 $I_2$ 再删除 $I_1$ 和直接删除 $I_1$ 两种情况，可以得到两者收益的关系
$$(A-B)^2+B^2\leq A^2$$
因此只需要尽量删除那些原始区间而不是被缩短的区间就可以使收益最大化。

设 $f_i$ 为前缀 $a_1,\dots,a_i$ 的最大收益，有状态转移方程
$$f_i=\begin{cases}
\max\left\{f_{i-1}+1,f_{\mathrm{ls}[i]-1}+(i-\mathrm{ls}[i]+1)^2\right\} & \mathrm{ls}[i]\text{ exists}\\
f_{i - 1}+1 & (\text{otherwise})
\end{cases}$$
其中 $\mathrm{ls}[i]$ 为 $a[i]$ 首次出现的位置。
