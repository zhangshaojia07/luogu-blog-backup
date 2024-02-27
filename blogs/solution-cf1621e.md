# CF1621E New School

### Preface

赛场上差点调出来 qwq。

### Analysis

显然，只有年长的 $m$ 位教师有用，所以只保留这些教师。 

如果在某一种情况下全体能够上课，则必定将教师和班级按照（平均）年纪分别排序，然后一一对应，也能够上课。

也就是说，我们只要考虑两者有序后是否能上课就行。

设 $a_1\le a_2\le\dots\le a_m$ 为教师年龄，$b_1\le b_2\le\dots\le b_m$ 为班级平均年龄。

能上课当且仅当 $\forall a_i\ge b_i$。

由于每次询问均只 remove 一个人，即改变一个班级 $x$ 的年龄平均值 $b_x$，这时这个班年龄平均值将在非降 $\{b\}$ 序列中重新定位，设为 $y$。

假设 $x>y$（该班级的平均值减小），则老师和班级的对应关系变为：

$$\begin{matrix}
a_1&\ \dots&\ a_{y-1}&\ \color{blue}{a_{y}}&\ \color{blue}{a_{y+1}}&\ \color{blue}{a_{y+2}}&\ \dots&\ \color{green}{a_{x}}&\ a_{x+1}&\ \dots&\ a_m
\\
b_1&\ \dots&\ b_{y-1}&\ \color{green}{b_x'}&\ \color{blue}{b_{y}}&\ \color{blue}{b_{y+1}}&\ \dots&\ \color{blue}{b_{x-1}}&\ b_{x+1}&\ \dots&\ b_m
\end{matrix}$$ 

其中 $b_x'$ 表示班级变化后的平均值。

$x<y$ 时同理。

发现就是中间一部分老师与学生错了 $1$ 位，可以预处理。

$$pre_x=[a_x\ge b_{x-1}]$$

$$now_x=[a_x\ge b_{x}]$$

$$suc_x=[a_x\ge b_{x+1}]$$

当然实际操作中这三个数组要用前缀和。

遍历每一个要退学的学生，计算即可。

时间复杂度 $O(n\log n+\log m \sum k)$，其中 $\log m$ 为二分寻找 $b_x$ 变换后的位置 $y$ 所致。

### Code

非常丑（借指本人），非必要不要看，不过跑得飞快。

[Link](https://codeforces.com/contest/1621/submission/141651719)