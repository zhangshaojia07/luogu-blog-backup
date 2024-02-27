# CF1628D1 Game on Sum (Easy Version)

[Hard Version 解法](https://www.luogu.com.cn/blog/101868/solution-cf1628d2)

### Analysis

首先显然 Bob 最终会恰好选 `+` 共 $m$ 次。

我们设 $f(n,m)$ 表示 $n$ 轮游戏中 Bob 选 `+` 共 $m$ 次最终的 score（初始 score 为 $0$）。

假设 Alice 第一轮选择 $x$，那么聪明绝顶~~秃头~~的 Bob 就会选择 `+` 和 `-` 中使得最终 score 最小的那个，即最终 score 成为 $\min\{f(n-1,m-1)+x,f(n-1,m)-x\}$，Alice 当然会选择让这个数最大，即 $x=\dfrac{f(n-1,m)-f(n-1,m-1)}{2}$。

即转移为：

$$f(n,m)=\dfrac{f(n-1,m-1)+f(n-1,m)}{2}$$

边界：

$$\begin{aligned}
f(n,n)&=nk
\\f(n,0)&=0
\end{aligned}$$

Dp 就好了。

时间 $O(nm)$。

### Code

自己动手试一试吧！