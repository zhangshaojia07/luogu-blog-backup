# CF1039D You Are Given a Tree

### Preface

人傻常数大，以后绝对不乱加 GCC 优化。

### Analysis

根号分治：长度 $len\ge\sqrt{n}$ 的路径最大数量一定 $\le\sqrt{n}$。

对于固定的路径长度 $len$，我们可以很轻松地树形 DP 搞定答案，由于过分简单，不再赘述。

然后我们选定分治点 $B$。$len\le B$ 直接暴力求；$len>B$ 时答案最多不超过 $\frac{n}{B}$，而且答案随 $len$ 递增而非升，我们对于每一种答案 $x$，二分最大的 $len$ 使得答案为 $x$ 即可。

时间为：

$$O(B+\frac{n\log n}{B})$$

$B$ 取 $\sqrt{n\log n}$ 最优。

### Optimize

~~其实这常数优化才是重点。~~

![](https://cdn.luogu.com.cn/upload/image_hosting/v8wmi9tz.png)

不要乱开 GCC 优化！不要乱开 GCC 优化！不要乱开 GCC 优化！

CF 上提交了 $33$ 次（头秃），证实了每次 DFS 递归求解绝对过不了，~~如果你过了我请你抽烟~~。

「那咋办呢？」

我们将树提前做好 DFS 序，之后在数组上非递归求解快不少。

「当然，快读快写是必要的……」

### Code

[My Fastest:1325 ms](https://codeforces.com/contest/1039/submission/149086644)