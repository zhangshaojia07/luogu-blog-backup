# CF1654E Arithmetic Operations

### Preface

场上一眼**根号分治**，然后写了个劣的 $n^{5/3}$ 死活卡不过去。（差一点点啊 qwq）

### Analysis

要让序列中变化的位置最少等价于不变的最多，我们求后者。$V=10^5$ 为值域。 

对**最终等差序列的公差** $D$ 进行分治。

若 $|D|<B$，枚举每一种 $D$，设 $b_i=a_i-i\cdot D$，则问题转化为求 $\{b\}$ 的众数。

若 $|D|\ge B$，枚举序列中第一个不用变化的位置 $x$，则只有在 $[x,x+\frac{V}{B}]$ 中才可能有不变化的位置，记录这些位置与 $x$ 构成的可能公差进行求众数。

时间

$$O(nB\log n+n\frac{V}{B}\log \frac{V}{B})$$

$B$ 大概取 $\sqrt{V}$ 即可，最终时间 $O(n\sqrt{V}\log (n+\sqrt{V}))$。

### Code

[Link](https://codeforces.com/contest/1654/submission/150708751)