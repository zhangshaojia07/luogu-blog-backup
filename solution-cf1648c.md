# CF1648C Tyler and Strings

### Analysis

我们先将 $\{s\}$ 中值相同的元素也看作不同来计算答案，最后答案再乘上

$$\left(\prod\limits_{i=1}^kc_i\right)^{-1}$$

即为最终答案，其中 $c_1,\dots,c_k$ 为 $\{s\}$ 每种元素出现的次数（共 $k$ 种）。

我们枚举 $s,t$ 公共前缀长度。同时用树状数组维护 $\{s\}$ 剩下的值。当前对答案的贡献为

$$(之前与 t 完全相同的方案数)\times(当前位比 t 小的方案数)\times(之后位乱放的方案数)$$

注意处理最后 $s,t$ 长度大小的细节。

### Code

[Link](https://codeforces.com/contest/1648/submission/149199297)