# P8380 Two Hypercubes

哥我尝试阅读了一下你的 P8380 题解，看了一整天了，真的好抽象，，完全看不懂，您这个思路虽然很简单但是我真的看不懂，式子是怎么推的。。我还是去写不高明的做法了

$$\begin{aligned}
&\sum_{x=1}^A\sum_{y=1}^B\sum_{z=1}^C[y^x=x^z]
\\=&
C+\sum_{d>1}\sum_{x=1}^{\log_dA}\sum_{y=1}^{\log_dB}[x\perp y]\sum_{z=1}^C[yd^x=xz]
\\=&
C+\sum_{d>1}\sum_{x=1}^{\log_dA}\sum_{y=1}^{\log_dB}[x\perp y][x|d^x][y\frac{d^x}{x}\le C]
\\=&
C+\sum_{x=1}^{\log_2A}\sum_{y=1}^{\log_2B}[x\perp y]\sum_{d>1}[d^x\le\min(A,\frac{xC}{y}),d^y\le B][x|d^x]
\end{aligned}$$

$x=1$ 时内部为
$$
\sum_{y=1}^{\log_2B}\sum_{d>1}[d\le\min(A,\frac{C}{y}),d^y\le B]
$$

$x=2$ 时内部为
$$
\sum_{y=1}^{\log_2B}[2\nmid y]\sum_{d>1,2|d}[d^2\le\min(A,\frac{2C}{y}),d^y\le B]
$$

$x\ge 3$ 时，$d\le 10^6$，预处理
$$
f^{(x)}(c)=\sum_{d=2}^c[x|d^x]
$$
即可。

快速处理 $d^a\le b$ 的 $d$ 的上界可以对每个 $a$ 预处理 $d^a$ 数组查询时二分即可。

复杂度 $O(n^{1/3}\log+T\log^2\cdot\log\log)$，其中 $\log\log$ 的由来：
$$
\sum_{i=3}^{\log}\log(n^{1/i})=\log \cdot \sum_{i=1}^{\log}\frac{1}{i}=\log\cdot\log\log
$$