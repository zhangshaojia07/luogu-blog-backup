# CF232C Doe Graphs（Fib/二分图/簇）

$|D_i|$ 的大小是斐波那契数列，指数增长，所以 $n$ 可以向 $80$ 取 $\min$。

考虑在 $D_i$ 中同构于 $D_{i-1}$ 和 $D_{i-2}$ 子图内部的最短路状态，发现外界对其影响至多为增加了一条编号最小和最大的点之间（以下称为头和尾）的路径。

在 $D_n$ 中不断 zoom in 找到同时包含 $a,b$ 两点的最小 Doe 图，此过程同时维护外界对其影响的路径长度（在 zoom in 时按进入 $D_{i-1}$ 还是 $D_{i-2}$ 分类维护长度）。

设该子图为 $D_{m}$，头尾之间外界路径长度为 $len$（没有则设为 $\infty$），不妨设 $a$ 属于其 $D_{m-1}$ 部分，$b$ 则属于 $D_{m-2}$，分别维护出 $a/b$ 在 $D_{m-1}/D_{m-2}$ 中到头和尾的最短路 $a_0,a_1,b_0,b_1$（此过程可递归求解）。

最后答案即为 $\min(a_0+b_0+1,a_1+b_0+1,a_0+b_1+len,a_1+b_1+2+len)$。

代码很短，但是要考虑的细节不少。