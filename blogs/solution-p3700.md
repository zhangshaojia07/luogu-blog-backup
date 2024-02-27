# P3700 [CQOI2017]小 Q 的表格（gcd/整除分块/分块）

这题黑？2.04s 抢到最优解。

题面给的两个条件就是辗转相除（减），进一步当 $\gcd(x',y')=\gcd(x,y)$，则
$$\frac{f(x,y)}{xy}=\frac{f(x',y')}{x'y'}$$
恒成立。

所以变量只有 $\forall x\in[1,n]\cap\mathbb Z,f(x,x)$。

对于询问有
$$\begin{aligned}
&
\sum_{i=1}^m\sum_{j=1}^mf(i,j)
\\=&
\sum_{i=1}^mf(i,i)\sum_{j=1}^{\lfloor m/i\rfloor}\sum_{k=1}^{\lfloor m/i\rfloor}jk[\gcd(j,k)=1]
\\=&
\sum_{i=1}^mf(i,i)\sum_{j=1}^{\lfloor m/i\rfloor}j^2\varphi(j)
\end{aligned}$$
后面可以线性筛线性预处理，前面按除 $m$ 的商整除分块。

$f(i,i)$ 用分块，记录块内前缀和，单点改复杂度为块长，区间查询复杂度为包含在区间内整块个数。

块长取 $\sqrt n$，复杂度 $O(n+m\sqrt n)$。