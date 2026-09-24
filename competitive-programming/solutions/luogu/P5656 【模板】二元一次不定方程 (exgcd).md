---
tags:
  - number-theory
platform: luogu
source: https://www.luogu.com.cn/problem/P5656
date: 2026-07-22T19:18:00
---

# 1 Solution

题目要求我们求出方程
$$ax+by=c$$
的正整数解($x>0, y>0$)。

用 [[欧几里得算法#2 拓展欧几里得算法|拓展欧几里得算法]]可以算出方程
$$ax+by=\gcd(a,b)=g$$
的一个解 $x',y'$，两边同乘以 $c$ 可得
$$acx'+bcy'=gc$$
同除以 $g$ 可得
$$a\frac{cx'}{g}+b\frac{cy'}{g}=c$$
则题目要求的方程的一个特解为
$$\begin{cases}x_0=\frac{cx'}{g} \\ y_0=\frac{cy'}{g}\end{cases}$$
设 $ax+by=c$，代入特解 $ax_0+by_0=c$ 可得
$$a(x-x_0)=b(y_0-y)$$
由于 $g$ 为 $a,b$ 的最大公因数，我们可以保证 $\frac{a}{g},\frac{b}{g}$ 互质。
两边同除以 $g$ 可得
$$\frac{a}{g}(x-x_0)=\frac{b}{g}(y_0-y)$$
也就是说，$\frac{b}{g} \mid (x-x_0)$，$\frac{a}{g}\mid (y_0-y)$。
我们设 $x,y$ 的步长为
$$\begin{cases}d_x=\frac{b}{g}\\ d_y=\frac{a}{g}\end{cases}$$
可以得到方程的通解
$$\begin{cases}x=x_0+kd_x\\ y=y_0-kd_y\end{cases}$$
我们可以得出 $x$ 的最小正整数解 $x_{\min}=x_0 \bmod{dx}$，通过代入方程可以求出此时 $y$ 的最大值
$$y_{\max}=\frac{c-ax_{\min}}{b}$$
若 $y_{\max}\leq 0$ 则该方程不存在正整数解。
同理 $y_{\min},x_{\max}$ 也可求出。

正整数解的数量即为 $\frac{x_{\max} - x_{\min}}{d_x}+1$。

```cpp
void solve(int cas) {
    ll a, b, c;
    cin >> a >> b >> c;
    ll x, y;
    ll g = exgcd(a, b, x, y);
    if (c % g != 0) {
        cout << -1 << '\n';
        return;
    }
    ll x_0 = x * c / g;
    ll y_0 = y * c / g;
    ll dx = b / g;
    ll dy = a / g;
    ll x_min = (x_0 % dx + dx) % dx;
    if (x_min == 0) x_min += dx;
    ll y_max = (c - a * x_min) / b;
    ll y_min = (y_0 % dy + dy) % dy;
    if (y_min == 0) y_min += dy;
    ll x_max = (c - b * y_min) / a;
    if (y_max <= 0) {
        cout << x_min << ' ' << y_min << '\n';
    }
    else {
        
        cout << (x_max - x_min) / dx + 1 << ' ' << x_min << ' ' << y_min << ' ' << x_max << ' ' << y_max << '\n';
    }
}
```

