# CF1208F Bits And Pieces（高妙高维前缀和）

这道题很高。

对于每一个值 $x$，我们求出 $a_j\ \text{and}\ a_k\ (j<k)$ 包含 $x$ 的最大 $j$，设为 $p_x$。

这个是正常的高维前缀和维护了一个不寻常的东西（位置的最大值和次大值），具体看代码。

然后我们枚举每一个 $i$，通过二分搜索（在 trie 的视角的话就是每次往一个儿子走）找到 $x\ \text{or}\ a_i$ 最大的 $x$，满足 $p_x>i$。

最后求一个 $\max$ 即可。

复杂度 $O(c(n+2^c))$，$c$ 为二进制位数。

[Link](https://codeforces.com/contest/1208/submission/175250312)