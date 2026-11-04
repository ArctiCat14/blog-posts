---
tags:
  - dynamic-programming
platform: vjudge
source: https://vjudge.net/problem/UVA-10003
date: 2026-11-04 21:36
---

# 1 Statement

一根长度为 $L$ 的木棍，需要在 $n$ 个指定位置 $c_1 < c_2 < \cdots < c_n$ 个位置被切开。
每次切割的代价为当前被切割的那段木棍的长度，切割顺序可以任意选择。
求切完所有指定位置所需的最小总代价。

---
# 2 Solution

经典的区间 DP 问题。
可以在原有的位置中再添加两个位置 $c_0 = 0, c_{n + 1} = L$。

定义状态 $\mathrm{dp}[i][j]\ (i<j)$ 表示处理完区间 $[c_i, c_j]$ 所需的最小总代价，有状态转移方程：
$$\mathrm{dp}[i][j]=\min_{i<k<j}\left(\mathrm{dp}[i][k]+\mathrm{dp}[k][j]+c_j-c_i\right)$$

要注意区间 DP 是按区间长度从小到大开始转移的。
