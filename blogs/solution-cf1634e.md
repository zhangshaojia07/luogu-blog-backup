# CF1634E Fair Share

## Preface

我考场上怎么也没想到这是一道图论题 qwq。

Algorithm(1) 是官方做法，但本人认为 Algorithm(2) 更加巧妙且好写。

## Algorithm(1)

### Analysis

如果有的值出现了奇数次，那显然是不行的。否则，我们下面构造一种分 $\text{L,R}$ 的方案来说明一定可行。

将数组和每种数值都看作图中的点，每次数值 $x$ 在第 $y$ 个数组出现，在 $x,y$ 分别对应的点之间连一条无向边（边上记得存位置，以便最终答案的输出）。这样构成的无向图（不一定简单，不一定连通），每个点的度都是偶数，所以每个连通分量必然存在一条欧拉回路。

欧拉回路的[知识点](https://baike.baidu.com/item/%E6%AC%A7%E6%8B%89%E8%B7%AF%E5%BE%84/23162222)和[算法](https://www.luogu.com.cn/problem/P7771)不再赘述。

将每条边按欧拉回路的方向定向。

这样，每个代表数值的点和代表数组的点的出入度相等了。我们只需要将 数组 $\to$ 数值 的那些位置放入 $\text{L}$，将 数值 $\to$ 数组 的那些位置放入 $\text{R}$ 即可。

### Detail

最方便的是前向星，因为要存反向边是否被遍历过。

### Code

[Link](https://codeforces.com/contest/1634/submission/145658088)

## Algorithm(2)

### Analysis

当然，通往广场的路不止一条。

有解的条件仍然是：每种值都出现偶数次。

我们将数组中的每一个数看作点。边有两种——值边 和 位边。

值边只连接两个值相同的点：想象着将每种值相同的位置存入数组 $\{p\}$（长度必定为偶数），将 $p_{2i-1},p_{2i}$ 之间连边。

![](https://cdn.luogu.com.cn/upload/image_hosting/c0wkixfd.png)

位边只连接在数组中位置相邻的两个点：设这个数组为 $\{a\}$（长度必定为偶数），将 $a_{2i-1},a_{2i}$ 之间连边。

![](https://cdn.luogu.com.cn/upload/image_hosting/a82swrj6.png)

发现最后这是一张二分图，因为不存在奇环。因为如果有奇环，必然有两个值边 或 两个位边 相邻（有公共端点）。而每个点有且仅有 一条值边 和 一条位边 连出去，矛盾，原命题得证。

这样，我们将这个二分图黑白染色，染黑的去 $\text{L}$，染白的去 $\text{R}$ 即可。

### Code

[Link](https://codeforces.com/contest/1634/submission/145800932)