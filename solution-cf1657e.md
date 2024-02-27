# CF1657E Star MST

vp 自己写出来了，记录一下。

为方便，我们考虑将题面中的「最小生成树」改为「最大生成树」，显然这样方案数（即答案）是不变的。

由题可知，**以 $1$ 为中心的菊花图是这个完全图的最大生成树。** 进一步得到：
$$
\forall 2\le i,j\le n,w(i,j)\le\min(w(1,i),w(1,j))
$$
其中 $w(i,j)$ 表示两点间边权。

我们记
$$
A_i=w(1,i)\quad(2\le i\le n,1\le A_i\le k)
$$
则答案为
$$
\sum_{\{A\}}\prod_{2\le i<j\le n}\min(A_i,A_j)
$$
其中最外层的 $\sum$ 代表枚举共 $k^{n-1}$ 种序列 $\{A\}$。

发现这样好像不太好搞掉最外层的 $\sum$。

由于 $\{A\}$ 中的下标并没有顺序关系，我们只关心每种值的个数，所以我们把 $\{A\}$ 放入**桶**，记为 $B_1,B_2,\dots,B_k$（$B_i$ 表示 $\{A\}$ 中值为 $i$ 的元素数量）。

则答案改写为
$$
\sum_{B_1+\dots+B_k=n-1}\prod_{i=1}^k\binom{n-1-\sum_{j=i+1}^kB_j}{B_i}i^{B_i(B_i-1)/2+\sum_{j=i+1}^kB_j}
$$
（好好思考一下这个公式的具体含义）

这时候我们就可以 DP 了。

$dp(i,j)$ 表示我们已经计算好了 $B_i,B_{i+1},\dots,B_k$，且 $\sum_{x=i}^kB_x=j$ 的贡献。

根据上面答案的柿子，我们得到 $dp$ 的递推式
$$
dp(i,j)=\sum_{k=0}^j\binom{(n-1)-(j-k)}{k}dp(i+1,j-k)i^{k(k-1)/2+k(j-k)}
$$
$$
dp(k+1,0)=1
$$
答案就是
$$
dp(1,n-1)
$$
时间 $O(N^2K\log N)$。

```cpp
//We'll be counting stars.
#pragma GCC optimize("Ofast")
#include<bits/stdc++.h>
using namespace std;
#define For(i,j,k) for(int i=(j),i##_=(k);i<=i##_;i++)
#define Rof(i,j,k) for(int i=(j),i##_=(k);i>=i##_;i--)
#define ll long long
const ll mod=998244353;
inline ll pw(ll x,ll y){ll r=1;while(y){if(y&1)r=r*x%mod;x=x*x%mod;y>>=1;}return r;}
#define N 256
int n,K;
ll dp[N][N],f[N],ivf[N];
ll C(int x,int y){return f[x]*ivf[y]%mod*ivf[x-y]%mod;}
int main(){
	scanf("%d%d",&n,&K); 
	f[0]=1; For(i,1,n) f[i]=f[i-1]*i%mod;
	ivf[n]=pw(f[n],mod-2); Rof(i,n,1) ivf[i-1]=ivf[i]*i%mod;
	dp[K+1][0]=1;
	Rof(i,K,1)
		For(j,0,n-1)
			For(k,0,j)
				(dp[i][j]+=dp[i+1][j-k]*C(n-1-(j-k),k)%mod*pw(i,k*(k-1)/2+k*(j-k)))%=mod; 
	printf("%lld\n",dp[1][n-1]);
return 0;}
```