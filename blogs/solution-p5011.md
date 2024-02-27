# P5011题解

有任何关于此篇题解的问题皆可私信[我](https://www.luogu.com.cn/user/101868)。

### 题目分析

期望题。

由题意可知**最后的威力等于使用的动作威力值之和加上相邻的动作贡献和**。

接下来有许多概率论术语~~不懂请出门右转百度一下~~。

翻开《概率论与数理统计》98 页。

- 设 $C$ 是常数，则有
$$E(C)=C.$$
- 设 $X$ 是一个随机变量，$C$ 是常数，则有
$$E(CX)=CE(X).$$
- 设 $X$ 和 $Y$ 是两个随机变量，则有
$$E(X+Y)=E(X)+E(Y).$$
- 设 $X$ 和 $Y$ 是两个**相互独立**的随机变量，则有
$$E(XY)=E(X)E(Y).$$

依照此题引入随机变量：
$$X_i=S(a_i+a_{i \ mod \ k+1}),i=1,2,\cdots,k$$
表示第 $i$ 个动作后紧接着第 $i \ mod \ k+1$ 个动作二元组合在搏击操中的期望贡献。

其中
$$S=\frac{n-1}{k^2}$$
表示第 $i$ 个动作后紧接着第 $(i \ mod \ k+1)$ 个动作二元组合在搏击操中出现的期望次数。

再引入随机变量：
$$Y_i=\frac{n}{k}a_i,i=1,2,\cdots,k$$
表示第 $i$ 个动作单个在搏击操中的期望贡献。

设最终答案的随机变量为 $A$。

所以最终答案的期望为：
$$E(A)=E(\sum_{i=1}^kX_i+\sum_{i=1}^kY_i)$$
开始用开头介绍的公式化简：
$$E(A)=E(\sum_{i=1}^kS(a_i+a_{i \ mod \ k+1})+\sum_{i=1}^k\frac{n}{k}a_i)$$
$$E(A)=E(2S\sum_{i=1}^ka_i+\frac{n}{k}\sum_{i=1}^ka_i)$$
$$E(A)=E((2S+\frac{n}{k})\sum_{i=1}^ka_i)$$
$$E(A)=E(2S+\frac{n}{k})\times E(\sum_{i=1}^ka_i)$$
$$E(A)=(2E(S)+\frac{n}{k})\sum_{i=1}^ka_i$$
设：
$$sum=\sum_{i=1}^ka_i$$
则：
$$E(A)=sum\times(2E(\frac{n-1}{k^2})+\frac{n}{k})$$
$$E(A)=sum\times(\frac{2n-2}{k^2}+\frac{n}{k})$$
$$E(A)=sum\times(2n-2+kn)\times inv(k^2)$$
其中 $inv(x)$ 表示 $x$ 的乘法逆元
### Details

别忘了开 `long long` 和取模。

$n\leq 10^{10^6}$ 但不用高精度，写个带模快读即可。

### ACcode
```cpp
#include<bits/stdc++.h>
using namespace std;
#define ll long long
#define FOR(i,j,k) for(register ll i=j;(j<k)?(i<=k):(i>=k);i+=(j<k)?1:(-1))
#define mod 19491001
ll read(){//带模快读 
	char k = getchar(); 
	ll x = 0;
	while(k < '0' || k > '9') k = getchar();
	while(k >= '0' && k <= '9') x = (x * 10 + k - '0') % mod ,k = getchar();
	return x;
}

ll n,k,sum=0,ans; 
ll pw(ll x,ll y){//快速幂（与题目给的略有不同） 
	if(y==0)return 1ll;
	ll res=pw(x,y/2);
	res=res*res%mod;
	if(y%2)res=res*x%mod;
	return res;
}
ll inv(ll x){//费马小定理求逆元 
	return pw(x,mod-2);
}
signed main(){
    n=read();
    scanf("%lld",&k);
    ll x;
    FOR(i,1,k){
    	scanf("%lld",&x);
    	sum=(sum+x)%mod;
	}
	ll ivk=inv(k);//inv of k
	ans=(n*k%mod+2*n-2)%mod*sum%mod*ivk%mod*ivk%mod;
	printf("%lld",ans);
    return 0;
}
```