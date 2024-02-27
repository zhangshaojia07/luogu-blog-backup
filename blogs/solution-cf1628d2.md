# CF1628D2 Game on Sum (Hard Version)

[Easy Version 解法](https://www.luogu.com.cn/blog/101868/solution-cf1628d1)

### Analysis

$m\le n\le 10^6$ 这意味着线性。

发现 Dp 的状态数就崩了……那么只能数学了。

我们考虑 $f(i,i)=ik\quad(1\le i\le m)$ 对 $f(n,m)$ 的贡献，累加起来就是答案。

发现 $f(x,y)$ 对 $f(x+1,y)$ 和 $f(x+1,y+1)$ 分别有 $\frac{1}{2}$ 倍的贡献（由 Dp 转移）（无意义和 $x=y$ 的状态不被贡献），所以 $f(i,i)$ 的贡献为以下图 $(i,i)\to(n,m)$ 的路径数量再乘上路径上“半衰”的 $2^{-(n-i)}$。

![](https://cdn.luogu.com.cn/upload/image_hosting/t21w8ruv.png)

由组合计数可知，路径共 $\binom{n-i-1}{m-i}$ 条。

所以最终答案为：

$$\sum\limits_{i=1}^m\dfrac{ik}{2^{n-i}}\binom{n-i-1}{m-i}$$

时间 $O((n+m)+Tm)$，分别是 阶乘及其逆元的预处理 和 求和 的时间。

### Detail

$n=m$ 时要特判。

### Code

[Link](https://codeforces.com/contest/1628/submission/143969941)