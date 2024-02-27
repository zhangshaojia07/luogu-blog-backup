# CF1635F Closest Pair

### Preface

嘿，虽然我差了这道 F，但你想不想 AK 这场 Div 2？

### Analysis

首先有一个至关重要的结论：

**设**

$$\begin{aligned}
L_i&=\max\{j\ |\ j<i\ \land\ w_j\le w_i\}
\\
R_i&=\min\{j\ |\ i<j\ \land\ w_j\le w_i\}
\end{aligned}$$

**则最终答案选取的两个数一定是 $(i,R_i)$ 或 $(L_i,i)$ 的形式。**

**Proof**

若最优答案为取 $(i,j)$，且 $R_i\ne j\ \land\ L_j\ne i$，我们证明一定有更优解，以证明原命题。

若 $w_i\le w_j$，则 $L_j>i$，得到 $|x_{L_j}-x_i|<|x_j-x_i|$ 且 $w_i+w_{L_j}\le w_i+w_j$，所以 $(i,L_j)$ 是一组更优解。

$w_i>w_j$ 的情况与上文类似，不再赘述。$\blacksquare$

也就是说，我们把问题转化成：

**有 $2n$ 个数轴上的已知区间 $[l_i,r_i]$，分别带权 $c_i$。每次询问一个区间 $[ql,qr]$，求 $\min\{c_x\ |\ ql\le l_x\ \land\ r_x\le qr\}$。**

我们把询问**离线**下来，就可以用**树状数组**轻松维护了。

具体地，在每一个 $l_i$ 上挂一个 $(r_i,c_i)$ 的标记，在每一个 $ql$ 上挂一个 $(qr,id)$ 的标记。从后往前扫，树状数组维护最小值即可。

时间 $O((n+q)\log n)$。

### Code

[Link](https://codeforces.com/contest/1635/submission/147165337)