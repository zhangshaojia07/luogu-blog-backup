# CF1654C Alice and the Cake

### Preface

典中典了属实是。

### Analysis

首先，每次合并最小的两个是错的，Hack：

```
6
1 1 1 1 1 1
```

发现我们很难合并，那我们就拆最终的数，判断是否能够拆成目前的这些数。

具体地，我们将一开始的数存入 `std::map` 并求和，将和进行拆分。当对于一个数 $x$ 拆分时，判断是否在 map 里：若有直接用掉，否则拆成 $\lfloor\frac{x}{2}\rfloor$ 和 $\lceil\frac{x}{2}\rceil$ 递归拆分。 

时间 $O(n\log n)$

### Code

[Link](https://codeforces.com/contest/1654/submission/150703782)