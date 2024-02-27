# P7992 [USACO21DEC] Convoluted Intervals S

我们先将 $a,b$ 的值分别开桶 $ta,tb$。

由于 $M\leqslant5000$，我们 $O(M^2)$ 枚举 $ta$ 的某对值 $(x,y)$，将 $ans[x+y]+=ta[x]\times ta[y]$，表示不比 $x+y$ 小的 $k$ 可取的区间对(pair)增加了这么多。

再 $O(M^2)$ 枚举 $tb$ 的某对值 $(x,y)$，将 $ans[x+y+1]-=tb[x]\times tb[y]$，表示比 $x+y$ 大的 $k$ 可取的区间对（pair）减少了这么多。

发现我们维护的 $ans$ 是一个差分序列，$O(M)$ 还原序列后输出即可。

代码就不贴了~~太简单了~~。