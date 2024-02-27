# CF1562F题解

**Upd 2021-9-8** 修改了不严谨的表达。

此思路来自一位评论区小哥 [jeroenodb](https://codeforces.com/blog/entry/94278?#comment-833416)。

### 小范围暴力

多小呢？$n<86$。

因为 $\leqslant2e5$ 的数中相邻素数差最大为 $86$，也就是说小范围内不保证落有素数。

所以暴力枚举 $0<i<j\leqslant n$，皆求 $\operatorname{lcm}(a_i,a_j)$，即可得所有数的值。（注意 $n=3$ 时要特判）

$O(n^2\log n)$ 时间复杂度。

### 大范围随机

多大呢？$n\geqslant86$。

此时范围内保证落有素数，频率至少为 $\dfrac{n}{100}$。

显然，出现 $l\leqslant p\leqslant r \quad and \quad 2p > r \quad and \quad p\in prime$ 的频率至少为 $\dfrac{n}{200}$。

所以我们选 $\min(\dfrac{n}{2},5000)$ 个原序列的位置的 $pair$，进行询问。

找到所有回答中所有素因数最大的那个，设为 $p$，就假设它在原序列中存在，且就在那个位置上，且序列中不存在 $p$ 的其他倍数。（因为概率极高） 

那么由于 $\operatorname{lcm}(x,p)=x\times p \quad (p\in prime)$，所以可以通过 $n-1$ 次询问 $\operatorname{lcm}(i,p)\quad (1\leqslant i\leqslant n)$ 得到所有数值。

[非常不雅观的代码](https://codeforces.com/contest/1562/submission/128132975)