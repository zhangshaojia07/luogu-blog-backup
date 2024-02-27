# CF1650F Vitaly and Advanced Useless Algorithms

### Analysis

遵循「哪门作业先交就先写哪门」的原则，我们先将 Opt 按 $e$ 分类。

由于要到达的满额只有 $100$，我们在每种 $e$ 中跑 01 背包即可，让每门课写作业的总时间尽量少。

注意判断无法按时交作业的情况和记录背包的转移。

时间 $O(100(n+m))$。

### Code

[Link](https://codeforces.com/contest/1650/submission/149199429)