# CF1470E Strange Permutation

### Analysis

先考虑总共的合法序列数 $\text{ways}(n,c)$。通过简单的组合计数得出 $\text{ways}(n,c)=\sum\limits_{i=0}^c\binom{n-1}{i}$，可以暴力计算。如果询问超过了这个数，直接 $-1$ 送走。

结论：如果最靠左的 Reverse 相同（$l,r$ 都相同），那么这些序列在字典序中是连续的。

由于 $p_{1\dots n}$ 互不相等，上述结论显然。

而且如果将序列以从左往右数第一个被改动过的下标标号，那么最终字典序的标号依次为：

$$1\times k_1,2\times k_2,3\times k_3,\dots,(n-1)\times k_{n-1},n\times k_n,$$
$$(n-1)\times k_{n+1},(n-2)\times k_{n+2},(n-3)\times k_{n+3},\dots,1\times k_{2n-1}$$

其中 $a\times b$ 表示 $a$ 连续出现 $b$ 次，$k_{\dots}$ 为非负整数。

很容易想到倒序遍历 $p$ 并维护字典序 Deque。

注意 Deque 中是按序列的字典序排的。

具体地，我们对于每一个固定的代价上限 $s\in[1,c]$，维护一个不超过该代价的 Deque。Deque 中每一个元素 $\{l,r,cnt\}$ 表示从左往右第一个翻转在 $[l,r]$，这样的合法序列有 $cnt$ 个（也就是用 $1$ 个来代表 $cnt$ 个），显然 Deque 里的元素不超过 $nc$ 个。

由于从 $i+1$ 遍历到 $i$，首个变动位置为 $i$ 的那些序列只可能插在前后（但是这些内部要排序），$c+1$ 个 Deque 构造的时间是 $O(c(nc)\log (nc))$ 的。

接下来就是对每个问题 $(pos,rk)$ 的求解。我们先求出排名 $rk$ 的排列 Reverse 了哪些区间，这样求出 $pos$ 位置上的数就简单至极了。

先在 $c$ 的 Deque 上二分找到排名 $rk$ 序列的第一个翻转 $[l_1,r_1]$，并将 $rk$ 减去那些因字典序靠前被排除掉的个数，再在 $c-1$ 的 Deque 上且首个变动位置 $>r_1$（Reverse 不交）中找到排名 $rk$，以此类推到 $0$。时间 $O(qc\log(nc))$。

这个过程好比查阅一本很厚的书。这本书的每一章节中间都有连续的一段是上一章的全部内容，而章节里每一个小节中间都有上一小节的全部内容……所以每一层级只要一张目录表，但是当你从第一章节和第三章节进来检索小节时，在小节目录里检索的范围不同而已。

总时间 $O(c(nc)\log (nc)+qc\log(nc))$。

### Code

可能我上面那段话有点难懂，所以我尽力把代码写漂亮了 qwq。

[Link](https://codeforces.com/contest/1470/submission/145640694)