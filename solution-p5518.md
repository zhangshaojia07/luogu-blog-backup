# P5518 [MtOI2019]幽灵乐团 / 莫比乌斯反演基础练习题

[my 完整题解](https://www.cnblogs.com/shaojia/p/16555685.html)

[帖子](https://www.luogu.com.cn/discuss/471977)

这里提供 [peterwuyihong](https://www.luogu.com.cn/user/100325) 的 [运用 exp/ln 解法](https://www.luogu.com.cn/blog/Peterprpr/solution-p5518) 的时间复杂度证明。

可知复杂度为整除分块内套了一个 $O(\sqrt n\log n)$，即：
$$
\sum_{\exists x\in\mathbb{N}^+,\lfloor n/x\rfloor=i}\sqrt i\log i
$$
我们考虑将 $i$ 按与 $\sqrt n$ 大小分类求时间（以下比较符号均代表时间复杂度 $O()$ 内的比较）：
$$
\begin{aligned}
Time&\le\sum_{i=1}^{\sqrt n}\sqrt i\log i+\sum_{i=1}^{\sqrt n}\sqrt \frac{n}{i}\log  \frac{n}{i}
\\&=\int_1^{\sqrt n}\sqrt x\log x\, \text{d}x+\int_1^{\sqrt n}\sqrt\frac{n}{x}\log  \frac{n}{x}\, \text{d}x
\\&\le \log n\int_1^{\sqrt n}\sqrt x\, \text{d}x+\sqrt n\log n\int_1^{\sqrt n}\sqrt\frac{1}{x}\, \text{d}x
\\&= \ x^{3/2}|_{1}^{\sqrt n}\log n+\ x^{1/2}|_{1}^{\sqrt n}\sqrt n\log n
\\&=n^{3/4} \log n+n^{3/4} \log n
\\&=n^{3/4} \log n
\end{aligned}
$$