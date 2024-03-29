# P9371 [APIO2023] 序列 / sequence（线段树/中位数/双指针/二分答案）

一个值 $x$ 为序列 $\{a\}$ 的中位数当且仅当 $|H-L|\le S$，其中 $H$ 为序列中值 $>x$ 的个数，$L$ 为 $<x$，$S$ 为 $=x$。

拆成没有绝对值的形式
$$\begin{cases}
H-L+S\ge 0
\\ H-L-S\le 0
\end{cases}\ \ (*)$$
枚举中位数值 $V$，生成两个序列
$$
H_i=
\begin{cases}
-1&,a_i<V\\
1&,a_i\ge V
\end{cases}
$$
和
$$
L_i=
\begin{cases}
-1&,a_i\le V\\
1&,a_i>V
\end{cases}
$$
两者只在值 $=V$ 的位置有差别。

处理出 $=V$ 的下标序列 $p_1<p_2<\dots<p_k$。

对 $\{p\}$ 作双指针，扫左端点移右端点。

对于一个区间 $p[l,r]$，计算 $H[p_l,p_r]$ 内 $a_i\ne V$ 的和 $w$，不妨设 $w\le 0$。

那我们为了将这个和提高，找到 $H[1,p_l-1]$ 的最大后缀和 $x$ 与 $H[p_r+1,n]$ 的最大前缀 $y$（若 $w\ge 0$，则换成 $L$ 数组，最大缀改成最小缀）。

若 $x+y+w+(r-l+1)\ge 0$，则必然存在一个包含 $[p_l,p_r]$ 的区间满足 $(*)$ 不等式组（即 $\max(ans,r-l+1)\to ans$），因为：

> 一个变量初始值为负整数，通过不断地操作 $+1/-1$ 使其变为正整数，则必然有一个时刻变量 $=0$。

上述操作均可以用线段树维护，复杂度 $O(n\log n)$。