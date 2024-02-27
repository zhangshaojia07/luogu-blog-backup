# CF1208G Polygons（欧拉函数妙题）

我的做法要分两类。

和别的题解一样，加入一个 $n$ 边形的代价为 $\varphi(n)$，原因是可以把 $n$ 的真因子边形扣在 $n$ 边形里面，这样新增的点数就是 $[1,n]$ 中与 $n$ 互质的数的个数了。

所以我们的**大体思路**就是找到这 $n-2$ 个 $\varphi$ 中的前 $k$ 小再求和即为答案。

但是这样有**两个问题**：

* 这样保证选了 $n$ 边形一定选了 $\forall d|n,d$ 边形吗？

保证，因为 $\forall d|n,\varphi(d)\le \varphi(n)$，可以用欧拉函数定义证明，或者
$$
\frac{\varphi(ab)}{\varphi(a)}=\frac{\varphi(a)\varphi(b)\gcd(a,b)}{\varphi(a)\varphi(\gcd(a,b))}\ge\frac{\gcd(a,b)}{\varphi(\gcd(a,b))}\ge 1
$$

* 不存在 $1$ 边形和 $2$ 边形咋办？

我们分两类：一类不存在 $2k$ 边形，另一类存在。

对于不存在的，我们初始在圆上随意选一个点作为“一边形”作为基底即可。

对于存在的，我们还需要增加一个“二边形”，即在选的点圆上对峙点再选一个。

[简短的代码](https://codeforces.com/contest/1208/submission/175236017)