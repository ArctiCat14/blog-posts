---
tags:
  - math
  - greedy
  - binary-search
platform: codeforces
source: https://codeforces.com/problemset/problem/2226/C
date: 2026-11-17 22:10
---

# 1 Statement

定义 $f(c)$ 为任意一个序列 $c=[c_1,c_2,\dots,c_m]$ 通过以下一次操作可以达到的最大的 $\operatorname{mex}(c)$ ：
- 选择一个序列 $b=[b_1, b_2,\dots, b_m]$ 满足 $\forall i\in [1,m], \ b_i \geq 1$
- 将 $c_i$ 变为 $c_i \bmod{b_i}\ ( 1\leq i \leq m)$

给你一个序列 $a$，求出 $f(a)$。

---
# 2 Solution

首先判断 $x \bmod y$ 可以达到的值：
1. 当 $y > x$，显然 $x\bmod y = x$；
2. 当 $y\leq x$，有 $x\bmod y < \frac{x}{2}$。

我们可以发现，对于 $f(a)$ 可能的答案 $k>1$ 如果成立，那么 $k-1$ 也必然成立。也就是说答案是否合法具有单调性。因此我们可以使用二分答案。

接下来我们需要检查答案 $k$ 是否合法。
对于一个值 $a_i$，他可以转化成的值即为 $a_i$ 或 $0\leq x < \frac{a_i}{2}$。

使用 multiset 维护未使用的值，从大到小枚举 $x,\ 0\leq x < k$，这个值只能通过上述两种方式取得：
1. 如果存在一个值等于 $x$，我们删去这个值。
2. 
