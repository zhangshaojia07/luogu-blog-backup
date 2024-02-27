# CF1083D The Fair Nuts getting crazy（线段树）

\*3500？\*1500！

离散化，求出前驱（$pre$，没有则为 $0$）后继（$suc$，没有则为 $n+1$），枚举中间交的区间。

对于每个左端点 $l$，我们可以轻松求出最大的 $r$ 使得 $[l,r]$ 区间内每个数最多出现一次。

倒序枚举 $l$，设 $r$ 上限为 $rlim$，则贡献为
$$
\sum_{r=l}^{rlim}(l-1-\max(pre_l,\dots,pre_r))(\min(suc_l,\dots,suc_r)-r-1)
$$
由于倒序枚举，尝试维护
$$
p_i=\max(pre_l,\dots,pre_i)
$$
$$
s_i=\min(suc_l,\dots,suc_i)
$$
则原式
$$
\begin{aligned}
=&-\frac{(l-1)(l+rlim+2)(rlim-l+1)}{2}+\sum_{r=l}^{rlim}p_r(r+1)+s_r(l-1)-p_rs_r
\\=&C+\sum_{r=l}^{rlim}p_r(r+1)+(l-1)\sum_{r=l}^{rlim}s_r-\sum_{r=l}^{rlim}p_rs_r
\end{aligned}
$$
$l$ 向左移动后，我们找到 $pre_i>pre_l,i>l$ 的最小 $i$，将 $[l,i)$ 的 $p$ 全赋值为 $pre_l$，$suc$ 同理。

要维护：

* 区间内 $\sum (i+1)p_i$。

* 区间内 $\sum s_i$。

* 区间内 $\sum p_is_i$。

* $p$ 区间赋值。

* $s$ 区间赋值。

线段树即可，虽然标记有点多。

复杂度 $O(n\log n)$，好写的，不压主体 80 多行。