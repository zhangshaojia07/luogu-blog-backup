# CF1656E Equal Tree Sums

### Preface

$\text{E<D}$ 实锤，$4$ 分钟写掉 $\text{E}$。

### Analysis

无向无根树？首先想到**黑白间隔染色**。

设 $deg_i$ 表示节点 $i$ 在树上的度。

**构造：** 对于节点 $x$，若为黑节点，值设为 $deg_x$，否则设为 $-deg_x$。

显然任意一棵树这样权值和为 $0$。

考虑删掉一个节点后形成多个连通块。每个连通块的权值和一定相同，且为 $\pm1$。（证明留给读者 doge）

### Code

[Link](https://codeforces.com/contest/1656/submission/150891549)