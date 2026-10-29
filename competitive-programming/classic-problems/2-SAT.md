---
tags:
  - graph
platform: luogu
source: https://www.luogu.com.cn/problem/P4782
date: 2026-10-28 15:57
---

# 1 Statement

给 $n$ 个布尔变量 $x_1​,\dots,x_n$​ 和 $m$ 个子句，每个子句是两个文字的 OR：
$$(a\vee b)\wedge (c\vee d)\wedge \cdots $$
文字可以是 $x_i$​ 或 $\neg x_i$。问是否存在赋值使所有子句同时为真；若有，输出一组解。

---
# 2 Solution

对于每个子句，如 $x_1 \vee x_2$，可以转换为蕴含 $\neg x_1\to x_2 \wedge \neg x_2\to x_1$。

对于变量 $x_i$，可以规定下标 $i$ 表示 $x_i$，下标 $i + n$ 表示 $\neg x_i$。

这样就可以把每个子句表示的蕴含用有向边的形式表示出来。

之后使用 Tarjan 算法求出 SCC，有解的情况即：
$$\forall i, \mathrm{scc}[i]\neq \mathrm{scc}[i+n]$$
每个布尔变量的取值即：`ans[i] = (scc[i] < scc[i + n]) ? 1 : 0`
