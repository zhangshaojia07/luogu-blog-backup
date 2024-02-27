# CF1591F Non-equal Neighbours

[AtCoder相同题目（除范围）](https://atcoder.jp/contests/arc115/tasks/arc115_e)~~双倍经验~~

### Analysis

设 $f_n$ 表示前 $n$ 个数满足条件的方案数。

由容斥原理得（规定 $f_0=1$）：

$$f_n=\sum\limits_{i=0}^{n-1}(-1)^{n-i+1}f_i\min\limits_{j=i+1}^{n}a_j$$

其中 $f_i\min\limits_{j=i+1}^{n}a_j$ 表示 $b_y=b_{y+1}(x<y<i)$ 且 $b_y\not=b_{y+1}(y\leqslant x)$ 的方案数。

发现这不是一般的 DP 优化方法……

找到最大的 $k$ 使得 $0<k<n \land a_k\leqslant a_n$，特判 $k$ 不存在的情况，那么转移时当 $i\leqslant k-1$：

$$\min\limits_{j=i+1}^{n}a_j=\min\limits_{j=i+1}^{k}a_j$$

即：

$$\begin{aligned}
f_n &= a_n\sum\limits_{i=k}^{n-1}(-1)^{n-i+1}f_i+\sum\limits_{i=0}^{k-1}(-1)^{n-i+1}f_i\min\limits_{j=i+1}^{k}a_j
\\
&= a_n\sum\limits_{i=k}^{n-1}(-1)^{n-i+1}f_i+(-1)^{n-k}\sum\limits_{i=0}^{k-1}(-1)^{k-i+1}f_i\min\limits_{j=i+1}^{k}a_j
\\
&= a_n\sum\limits_{i=k}^{n-1}(-1)^{n-i+1}f_i+(-1)^{n-k}f_k
\end{aligned}$$

每一个位置的 $k$ 可以在 $O(N)$ 内用单调栈求出。

过程中求 $\{f\}$ 的正负交替的前缀和，即可 $O(1)$ 求出每一个 $\sum\limits_{i=k}^{n-1}(-1)^{n-i+1}f_i$，总时间 $O(N)$。

### Code

[Link](https://codeforces.com/contest/1591/submission/139476338)