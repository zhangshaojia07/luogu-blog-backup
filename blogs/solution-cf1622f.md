# CF1622F Quadratic Set

发现答案一定不小于 $n-3$。

以下设 $B=A-A'$。

**证明：**

$$
\prod\limits_{i=1}^{2k}i! =
2^kk!
\left(\prod\limits_{i=1}^{k}(2i-1)!\right)^2
$$

$n\equiv0\pmod{4}$ 时，取 $B=\{\frac{n}{2}\}$ 即可。

$n\equiv2\pmod{4}$ 时，取 $B=\{2,\frac{n}{2}\}$ 即可。

$n\equiv1\pmod{2}$ 时，去掉 $n!$ 这项后规模减小 $1$，成为偶数，变为上述两种情况。

注意上面的不一定是最优解。

所以我们先判断 $S=\prod\limits_{i=1}^{n}i!$ 是否为平方数。

若否，则判断是否 $\exists x ,\text{s.t. } \dfrac{S}{x!}$ 是平方数。

若再否，则判断是否 $\exists x,y,\text{s.t. }  \dfrac{S}{x!y!}$ 是平方数。

否则，答案 $=n-3$。

由于前面的判断与素因数个数的奇偶性有关，我们想到将素数赋随机 Hash 值，设 $f(x)$ 为 $x$ 分解素因数后每个素因数 Hash 值的异或和（多个相同素因数重复算）。

由于 $a \operatorname{xor} a=0$，$f(x)=0$ 当且仅当 $x$ 是平方数（在很高概率下）。

我们将 $f(x!)(1\le x\le n)$ 存入 `std::map` 匹配即可。

时间 $O(n\log n)$。

[Code](https://codeforces.com/contest/1622/submission/142751564)