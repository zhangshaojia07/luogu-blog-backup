# CF1284C题解

有任何关于此篇题解的问题皆可私信[我](https://www.luogu.com.cn/user/101868)。

### 题目分析

其实也就是 **$p_l$ 到 $p_r$ 内是一组连续的数的集合**呗。

接下来我们来算贡献，考虑每一个长度的区间对答案的贡献。

对一个长度为 $k$ 的连续区间，我们选这个区间**在原序列的位置有 $n-k+1$ 种选法**，将这 $k$ 个数**打乱有 $k!$ 种方法**。

然后合法的方案要求选择的区间内元素连续，所以**把这个区间看成一个整体元素**，和其他 $n-k$ 个数做个排列，有 $(n-k+1)!$ 种方法。

例如：

![](https://i.loli.net/2021/07/25/qkofHQxUzw8jECc.png)

统计每种长度的区间，所以**答案就是 $\sum_{k=1}^n(n-k+1)!k!(n-k+1)$**。

时间复杂度 $O(n)$

### Details

写给像我一样的数论新手：**预处理**出每个数的阶乘的模数。

### ACcode
```cpp
#include<bits/stdc++.h>
using namespace std;
#define ll long long
#define FOR(i,j,k) for(register int i=j;(j<k)?(i<=k):(i>=k);i+=(j<k)?1:(-1))
#define IO ios::sync_with_stdio(0),cin.tie(0),cout.tie(0)
#define N 255000

ll n,mod;
ll f[N];//带模阶乘 
ll sum=0;//ans求和 
void init(){
	f[0]=1;
	FOR(i,1,n) f[i]=f[i-1]*i%mod;
}
signed main(){
    IO;
    cin>>n>>mod;
    init();
    FOR(i,1,n) sum=(sum+f[n-i+1]*f[i]%mod*(n-i+1)%mod)%mod;
	cout<<sum<<endl;
    return 0;
}
```