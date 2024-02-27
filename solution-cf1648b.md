# CF1648B Integral Array

### Preface

又是一道**枚举因数转化为枚举倍数**的好题。

### Analysis

若判断没有 $a_i=1$，直接 NO。

我们将 $\{a\}$ 去重，设为 $\{b\}$，并将 $\{b\}$ 入桶 $T$。

此时桶 $T$ **作前缀和**，以便于区间询问。

对于每一个 $b_i$，判断是否能成为 $\lfloor\frac{x}{y}\rfloor$ 中的 $y$ 使得 $\lfloor\frac{x}{y}\rfloor$ 不在 $\{b\}$ 中。

将 $>b_i$ 的数（桶中）划分成多个段，使得每个段中数 $\lfloor\frac{x}{b_i}\rfloor$ 的值相同。

若某一段中有值（区间询问），则判断是否有 $\lfloor\frac{x}{b_i}\rfloor$ 即可。

时间 $O(c\ln c)$，因为

$$\lim_{n\to\infty}\sum_{i=1}^{n}\frac{1}{i}=\ln n+\gamma$$

### Code

[Link](https://codeforces.com/contest/1648/submission/149199285)