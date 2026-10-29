---
tags:
  - graph
platform: luogu
source: https://www.luogu.com.cn/problem/P4782
date: 2026-10-28 15:57
---

# 1 2-SAT

给 $n$ 个布尔变量 $x_1​,\dots,x_n$​ 和 $m$ 个子句，每个子句是两个文字的 OR：
$$(a\vee b)\wedge (c\vee d)\wedge \cdots $$
文字可以是 $x_i$​ 或 $\neg x_i$。问是否存在赋值使所有子句同时为真；若有，输出一组解。

---
# 2 解法

