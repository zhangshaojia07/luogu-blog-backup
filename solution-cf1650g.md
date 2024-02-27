# CF1650G Counting Shortcuts

### Preface

赛场上被 Hack 了 qwq。

### Analysis

设最短路长度为 $D$，以及 $dis_i$ 表示 $s\to i$ 的最短路径长度。首先，$s\to t$ 的长度为 $D+1$ 的路径一定不会有点被多次经过。

引理 A：每条边 $(x,y)$ 必然满足 $|dis_x-dis_y|\le 1$。

**证明：**

反证，假设有一条路径（其实只能算通道了）经过的点集为 $\{s,p_1,p_2,\dots,p_D,t\}$ 且存在 $u<v$ 使得 $p_u=p_v$。由引理可得 $dis_{p_u}\le u$，即 $dis_{p_v}\le u$，此时得出 $p_v\to t$ 的最短路径长度一定 $\ge D-u$，而 $\{p_v,p_{v+1},\dots,p_D,t\}$ 的长度为 $D-v<D-u$，矛盾，原命题得证。$\blacksquare$

由这个证明我们也得到：$s\to t$ 的长度为 $D+1$ 的路径包含且仅包含一条边 $(u,v)$ 使得 $dis_u=dis_v$。我们枚举这条特殊的边，分别预处理出 $s\to u$ 和 $v\to t$ 的最短路径条数相乘即可。

时间 $O(n+m)$。

### Code

[Link](https://codeforces.com/contest/1650/submission/149199434)