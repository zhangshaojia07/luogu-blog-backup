# CF808G Anthem of Berland

### Preface

难度可以稍微降一降？至多蓝吧……

我只用了 Dp 啊，没用啥哈希、KMP……

### Analysis

**以下字符串下标从 $1$ 开始，$|s|=n,|t|=m,n\ge m$。**

时间显然是 $O(nm)$ 或 $O(m^2)$ 的。

如果不考虑 $t$ 重叠的情况，那 Dp 非常容易。设 $f_i$ 表示 $s$ 中 $t$ 当前最后匹配到 $s[i\dots(i+m-1)]$ 时的最大匹配次数。若 $s[i\dots(i+m-1)]$ 可以等于 $t$，那么：

$$f_i=1+\max\limits_{j=1}^{i-m}f_j$$

用前缀和优化即可。

考虑 $t$ 重叠的情况，重叠部分长度为 $len$ 的条件为 $t[1\dots len]=t[(m-len+1)\dots m]$（前缀 $=$ 后缀），我们可以 $O(m^2)$ 预处理出所有可行的 $len$ 存为集合 $S$。

那 Dp 转移改一下就能 $O(m(n+m))$ 过了：

$$f_i=1+\max\{\max\limits_{j=1}^{i-m}f_j,\max\limits_{j\in S}f_{i-m+j}\}$$

### Code

[Link](https://codeforces.com/contest/808/submission/146633288)