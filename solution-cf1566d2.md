# CF1566D2 Seating Arrangements (hard version)题解

来一发清晰的题解~

相信大家一定会做 Easy Vision 了吧……（不就是个贪心嘛）

而若将 cinema 看成一条序列，则 Easy Vision ->  Hard Vision 的唯一区别是**人可以从序列里下标为 $m$ 的倍数的 seat 之后插入并右移至目标位置。**

e.g.

![](https://i.loli.net/2021/09/16/qZYpIbaKrcAEfs1.png)

于是构造一个二维序列 $row_i(j)$ 表示第 $i$ 个块（图中一个红色矩形为一块）中第 $j$ 个 seat 是否空闲（空闲为 $1$，否则为 $0$）。

通过预处理数据，可知一个人 $x$ 在最终 cinema 座位编号的范围 $[L(x),R(x)]$，而且**近视度数相同的人范围一定相同，近视度数不同的人范围一定不相交**。

所以从 $1$ 至 $nm$ 编号的人依次进入 cinema，依次贪心地安排 seat。

* 若 $L(x)$ 与 $R(x)$ 同 $row$，则同 Easy Vision 一样，选择最靠 $R(x)$ 的空闲的 seat 坐下。

* 若不同 $row$，则优先坐 $L(x)$ 那 $row$ 右边（因为之后可能有更多人插到这 $row$ 的左边），坐满则坐中间几 $row$ 的右边（因为这些 $row$ 都被同一近视度数的人包场了），否则只能坐 $R(x)$ 那 $row$ 最右边可能的位置（因为这样之前可以有更多人插到这 $row$ 的右边）。

用树状数组维护即可。

[Code](https://codeforces.com/contest/1566/submission/128992162)