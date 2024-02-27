# CF1632E2 Distance Tree (hard version)

### Preface

这道题实属妙极，本蒟蒻确实做不出来。

### Analysis

**定义点 $x$ 的“受益”：指增加了那条边后 $d(x)$ 的变化量（减小值）。**

**定义 $dis(x,y)$：在树上 $x,y$ 间的距离。**

显然，贪心地增加这条边：一端选择为 $1$ 节点（设该边为 $(1,a)$），因为这样受益的节点更多，受益也更多。

我们先固定 $X$ 来求 $f(X)$。

假设 $f(X)\le F$，那么原树中深度 $=y(>F)$ 的点一定受益至少 $y-F$ 才行。

发现如果 $a$ 在相对“树干”上，那么有可能有些点的受益不够导致不可行；如果 $a$ 在相对“树枝”上，那么有可能有些点离 $a$ 太远导致不可行。

妙处：设 $S_F=\{x\ |\ dis(1,x)>F\}$（这些点需求受益，必须经过新增的边），寻找点集中在树上距离最大的两点，设为 $(u,v)$，距离为 $w$，这时如果 $\left\lceil\dfrac{w}{2}\right\rceil+X>F$，即 $a$ 取 $(u,v)$ 路径中点时仍然不行，判断出 $f(X)>F$。否则，$f(X)\le F$（Proof A）。

我们 $O(n)$ DFS 预处理出每个 $i$，$S_i$ 内的最大点对距离（只需 DFS 时处理每个节点 深度最大和次大的子树 即可，具体看代码）。

到此我们对于每个 $X\in[1,n]\cap\mathbb{Z}$ 可以 $O(\log n)$ 二分答案求出 $f(X)$，总时间 $O(n\log n)$。

观察到 $f(X)$ 随着 $X$ 递增而递增（非严格），且树的深度不超过 $n$，我们可以 $O(n)$ 做啦！

### Proof A

反证法：假设有一个点 $o$ 使得 $d(o)>F$，则:

$$dis(o,a)>F-X\ge\left\lceil\dfrac{w}{2}\right\rceil$$

将树假想成以 $a$ 为根定根，则 $u,v$ 必定在 $a$ 的两个不同的儿子子树内，$o$ 必然和其中一个不在同一个子树内，设是 $u$，得 $dis(o,u)=dis(o,a)+dis(a,u)\ge \left\lceil\dfrac{w}{2}\right\rceil+1+\left\lfloor\dfrac{w}{2}\right\rfloor>w$。

与 $w$ 的最大性矛盾，故得证。

### Code

[Link](https://codeforces.com/contest/1632/submission/144909024)