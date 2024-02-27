# CF1634F Fibonacci Additions

### Preface

比赛时还没写 D，瞄了一眼 F。“这不是[原题](https://www.luogu.com.cn/problem/CF446C)吗？”我想。然后花了 $10^9+7$ 年发现根本不是这样做的（甚至 $O(n)$）。

### Analysis

首先可以预处理出 Fib（斐波那契数列），记为 $F$。

我们记 $P_i=A_i-B_i$。题目要 $P$ 每次区间加（或减） Fib 后询问是否全为 $0$。

发现区间加 Fib 非常头痛。

由于 Fib 的递推式为 $F_n-F_{n-1}-F_{n-2}=0$，我们考虑以下不常规的差分（无意义的下标取值为 $0$）：

$$Q_i=P_i-P_{i-1}-P_{i-2}\quad(1\le i\le n)$$

这样 $P$ 区间加 Fib 相当于 $Q$ 的区间两端做少量加减。（区间中间部分满足：每一项 与 前两项和 的差保持不变）

具体地，若 $P$ 中 $[L,R]$ 加 Fib，则“少量加减”指：

$$\begin{aligned}
Q_L&+=F_1(=1)
\\
Q_{R+1}&-=F_{R-L}+F_{R-L+1}(=F_{R-L+2})
\\
Q_{R+2}&-=F_{R-L+1}
\end{aligned}$$

而

$$\forall i\in[1,n],P_i=0\iff\forall i\in[1,n],Q_i=0$$

所以每次记录 $Q$ 中 $0$ 的个数即可。

### Detail

别忘了取模。

输出居然卡 `endl`，不可思议。

### Code

[Link](https://codeforces.com/contest/1634/submission/145814935)