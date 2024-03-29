# CF1609E William The Oblivious 题解

### 题意简述

**注意：以下阐述中“字符”均在字符集 $\sum=\{\mathtt{"a","b","c"}\}$ 之内讨论。**

最初给一个长度为 $n$ 的字符串。

$q$ 次操作，每次先修改单个位置的字符，再询问全局最少要修改几个位置上的字符使得整个序列没有 $\mathtt{"abc"}$ 这个子序列。

### 题目分析

像这种**关于颜色模式子序列（颜色少，模式子序列短）**的题目一般想到**矩阵乘法**（不是一般的乘法，后面会说）。

具体地，用一个线段树维护整个序列，树上每一个节点存一个 $3\times 3$ 的矩阵：

$$\begin{pmatrix}
A&AB&ABC
\\
\infty&B&BC
\\
\infty&\infty&C
\end{pmatrix}$$

其中（下面的括号为断句）：

* $A$ 为使得（该线段树节点管辖的区间内）（没有 $\mathtt{"a"}$ 这个子序列）的（最小修改次数）。

* $AB$ 为使得（该线段树节点管辖的区间内）（没有 $\mathtt{"ab"}$ 这个子序列）的（最小修改次数）。

* $\dots$

* $C$ 为使得（该线段树节点管辖的区间内）（没有 $\mathtt{"c"}$ 这个子序列）的（最小修改次数）。

而且这里的矩阵乘法 $XY=Z$ 定义为：

$$z_{i,j}=\min\limits_{k=1}^{3}(x_{i,k}+y_{k,j})$$

发现她满足结合律（证明留给读者）。

对于一个线段树上的节点来说，设她的矩阵为 $F$，她的两个儿子的矩阵分别为 $L,R$，则：

$$F=LR$$

那我们就可以开开心心线段树合并了。

实时答案为线段树树根矩阵 $(1,3)$ 的值（即 $ABC$）。

时间复杂度：

* 单次修改 $O(|\sum|^3\log n)$

* 单次询问 $O(1)$

总共 $O(q|\sum|^3\log n)$。

### Details

我的矩阵用 `struct` 存储，每一维从 $0$ 开始标号。

### Code

[Link](https://codeforces.com/contest/1609/submission/137346877)