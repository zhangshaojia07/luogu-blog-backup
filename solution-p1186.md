# P1186 玛丽卡 n^2 做法（ST 表下放标记）

抢到了最优解。

前面的做法同 [@囧仙](https://www.luogu.com.cn/blog/over-knee-socks/solution-p1186)。

维护最短路上区间 check min 时采用 ST 表。最终查最大值前统一下放标记，这样做到了 $O(1)$ 打标记，空间 $O(n\log n)$（但是边数都 $O(n^2)$ 了所以这个不用管）。

复杂度降为 $O(n^2)$。