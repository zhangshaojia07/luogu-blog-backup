# CF1622E Math Test

### Analysis

初中数学告诉我们，遇到绝对值要分类。

考虑最终答案中每一个人的贡献是 $x_i-r_i$ 还是 $r_i-x_i$，发现这可以状压解决。

对于一种，记 $c_1,c_2,\dots,c_n(c_i\in\{-1,1\})$ 表示第 $i$ 名学生的贡献为 $c_i(x_i-r_i)$。

则惊喜值为（其中 $a_1,\dots,a_m$ 表示最终每题的分值，为未知量）：

$$\begin{aligned}
S&=\sum\limits_{i=1}^nc_i(x_i-r_i)
\\&=\sum\limits_{i=1}^nc_ix_i-\sum\limits_{i=1}^nc_ir_i
\\&=\sum\limits_{i=1}^nc_ix_i-\sum\limits_{i=1}^nc_i\sum\limits_{j=1}^ma_jp_{i.j}
\\&=\sum\limits_{i=1}^nc_ix_i-\sum\limits_{i=1}^nc_i\sum\limits_{j=1}^ma_jp_{i.j}
\\&=\sum\limits_{i=1}^nc_ix_i-\sum\limits_{j=1}^m\left(\sum\limits_{i=1}^nc_ip_{i.j}\right)a_j
\end{aligned}$$

记（表示第 $j$ 题在最终答案中的系数）：

$$d_j=\sum\limits_{i=1}^nc_ip_{i.j}$$

得：

$$S=\sum\limits_{i=1}^nc_ix_i-\sum\limits_{i=1}^md_ia_i$$

由于 $\{a\}$ 是一个 $1\to m$ 的排列，将 $d_i$ 更小的 $a_i$ 赋更大的值将使 $S$ 最大。（[排序不等式](https://baike.baidu.com/item/%E6%8E%92%E5%BA%8F%E4%B8%8D%E7%AD%89%E5%BC%8F/7775728)）

对于每一种 $\{c\}$（状压），求出答案后取 $\max$ 即可。

### Detail

细心的小可爱们肯定发现了问题：**会把错解算进去啊。**

具体地，有可能最终某些人的贡献 $c_i(x_i-r_i)<0$（类比于绝对值 $|x|$ 分类 $x>0$ 后解得 $x=-2$ 一样矛盾）。

但是**一定有正解把错解刷掉**。

具体地，对于下标集合 $W=\{i\ |\ c_i(x_i-r_i)<0\}$，我们在枚举 $\{c\}$ 时枚举到 $W$ 中的 $c_i$ 全部取反（相反数）时，答案一定比这种情况要优。

### Code

时间 $O(nm2^n)$。（跑 $10^8$ 能过，CF 机子快)

[Link](https://codeforces.com/contest/1622/submission/142377050)