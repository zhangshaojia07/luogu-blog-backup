# CF1634D Finding Zero

### Preface

自己想出来感觉好爽。

感觉本片题解比官方题解简单太多了……OvO

### Analysis

**对于集合 $S$ 极差定义：$\max\limits_{x\in S}x-\min\limits_{x\in S}x$**

**以下 $\text{que}(x,y,z)$ 表示一次询问（三个下标），并返回值。**

最后输出两个猜测的值，且每次询问只给三个数的极差。设序列最大值为 $mx$（并假设只有一个取到最大值），发现如果 $a_i:=mx-a_i$，每次询问给出的极差是不变的，而你仍然需要输出，且包含 $0$。这说明我们最后要输出整个序列的最大和最小值的下标（若多个随意，因为 $0$ 就一个）。

如果维护好了前 $i-1$ 个数的最大、最小值的下标 $x,y$（不知道具体对应关系）和 $len=|a_x-a_y|$，再随便选一个 $1\le z\le i-1$ 使得 $z$ 与 $x,y$ 不同。这时必定有 $\min(a_x,a_y)\le a_z\le\max(a_x,a_y)$。设 $dx=\text{que}(x,z,i),dy=\text{que}(y,z,i)$，这时如果 $\max(dx,dy)\le len$ 显然极差没有被更新。否则设 $dx\ge dy$，也就是说这时大小关系为 $a_x\le a_z\le a_y<a_i$ 或者全部反过来，那么将 $y:=i,len:=dx$ 即可。

发现这样每个位置询问两次正好，但是我们怎么起头（$i\le 4$）？

观察前四个，选三个数 $\text{que}$ 共 $4$ 次。设 $b1,b2,b3,b4$ 为 $1,2,3,4$ 的排列使得 $a_{b1}\le a_{b2}\le a_{b3}\le a_{b4}$ 这时取 $4$ 个 $\text{que}$ 的最大值为 $len$，$len$ 必定出现两次及以上（$\text{que}(b1.b2,b4),\text{que}(b1,b3,b4)$），就可以推出 $b1,b4$ 的值，$x=b1,y=b4,z=b2$ 或 $b3$ 即可。

前四个 $\text{que}$ 共 $4$ 次，后 $n-4$ 个共 $2(n-4)$ 次，共 $2n-4$ 次，比官方的最坏情况少两次。

### Code

[Link](https://codeforces.com/contest/1634/submission/145560281)