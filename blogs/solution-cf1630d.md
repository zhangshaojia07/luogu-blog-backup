# CF1630D Flipping Range

### Analysis

**结论：以任意 $B$ 中元素为长度 Flip 一段区间任意次 等价于 $B$ 中元素的 $\gcd$ 为长度做任意次。**

**证明**：（懂了请跳过，证明略长）

设 $\gcd\limits_{i=1}^mb_i$ 为 $g$。

（始终记住，$B$ 是集合，$g$ 是数）

由裴蜀定理得到：存在一组 $\{c_1,\dots,c_m\}$ 使得 $\sum\limits_{i=1}^mb_ic_i=g$。

对于每个 $1\le i\le m$，将 $b_ic_i$ 平铺开，加入多重集合 $C$。 

这里平铺指

$$\begin{cases}
\begin{matrix}\underbrace{\{b_i,b_i,\dots,b_i\}}\\c_i \ times\end{matrix} & c_i\ge 0
\\
\begin{matrix}\underbrace{\{-b_i,-b_i,\dots,-b_i\}}\\|c_i| \ times\end{matrix} & c_i<0
\end{cases}$$

显然通过重排，我们可以使 $C$ 中任意前缀和 $\in[x,x+n-1]\quad(\forall x\in[1-n,0])$

原因是 $B$ 中元素值的上限是 $\left\lfloor\frac{n}{2}\right\rfloor$，每次在 $[x,x+n-1]$ 下半时选正的 $C$ 中元素，上半时选负的。

这样我们在 $\{a\}$ 中 Flip 一段长为 $g$ 的区间（因为 $|\{a\}|=n$）。

然而，$B$ 中每个元素都是 $g$ 的倍数，所以 $B$ 能完成的 $g$ 也能完成。

综上，我们构建了（$B$ 能完成的 Flip）和（$g$ 能完成的 Flip）之间的双射，**原命题得证**。

我们称以 $g$ 长度的 Flip 为 Glip。

发现两次错位为 $1$ 的 Glip 可以使两个间隔为 $g$ 的数同时取相反数。

所以我们将 $\{a\}$ 中元素按照下标 $\pmod{g}$ 划分成 $D_0,D_1,\dots,D_{g-1}$。

同一个 $D_i$ 中元素的“负号”是可以任意传递的，且 $2$ 个“负号”可以抵消。我们将每组“负号”抵消到不足 $2$ 个。

数组 $\{d_0,\dots,d_{g-1}\}$ 记录每组现在是（$1$）否（$0$）仍有“负号”，$\{e_0,\dots,e_{g-1}\}$ 记录每组元素**绝对值**最小值。

由于“负号”最优动到绝对值最小的地方，所以：

$$sum=\sum\limits_{i=1}^n|a_i|$$

$$ans_1=sum-2\sum\limits_{i=0}^{g-1}d_ie_i$$

不过再来任意一次 Glip，会让 $\{d\}$ 全部取反（bool 的取反），所以：

$$ans_2=sum-2\sum\limits_{i=0}^{g-1}(1-d_i)e_i$$

最终：

$$ans=\max\{ans_1,ans_2\}$$

快乐 AC！

### Code

[Link](https://codeforces.com/contest/1631/submission/144329498)