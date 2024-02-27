# CF1654D Potion Brewing Class

### Preface

场上正解 FST 了……现在重写了，代码较短。

### Analysis

发现这个取模 $998,244,353$ 和 $\gcd$ 无法同时进行。

我们先设 $a_1=1$，然后通过题目所给的条件 DFS 得出每个药剂的量 $a_i=2^{c_{i,1}}3^{c_{i,2}}5^{c_{i,3}}7^{c_{i,4}}\dots $ **注意 $c_{i,j}$ 可为负**。

我们需要给所有的 $a_i$ **同时乘上一个数**使得每一个 $c_{i,j}$ 都**不为负数**。（其实就是通分）

所以，在 DFS 时**带模求出** $a_1,a_2,\dots,a_n$，同时**维护当前节点（药剂）的** $\{c\}$，并记录 $d_j=-\min_{i=1}^nc_{i,j}$。

由于带模乘法具有**分配律**，最终结果即为

$$\left(\sum\limits_{i=1}^{n}a_i\right)\cdot2^{d_1}\cdot3^{d_2}\cdot5^{d_3}\cdot7^{d_4}\cdot\dots$$

对 $998,244,353$ 的模数。

时间 $O(n+\sum\limits_{(x,y)\in E}\Omega(x)+\Omega(y))$

其中 $\Omega$ 表示[素因数个数函数](https://baike.baidu.com/item/%E7%B4%A0%E5%9B%A0%E6%95%B0%E4%B8%AA%E6%95%B0%E5%87%BD%E6%95%B0/18898050)，$x,y$ 为输入的**比值**。

### Detail

要**线性筛**素数预处理，同时求出每个数的最小素因数以便于分解。

当然，由于要计算有关模意义下比率的 $a_i$，我们要预处理**阶乘和阶乘的逆元**，以便 $O(1)$ 求逆元。

### Code

[就是挺短的，快来看我](https://codeforces.com/contest/1654/submission/151329693)